## Node基础第二讲

### 常用模块

#### BUFFER

> …JavaScript 语言没有读取或操作二进制数据流的机制。 `Buffer` 类被引入作为 Node.js API 的一部分，使其可以在 TCP 流或文件系统操作等场景中处理二进制数据流。Buffer其实是二进制数据模块。

buffer.from

```javascript
// Buffer.from(array) 返回一个新的 Buffer，其中包含提供的八位字节数组的副本
// Buffer.from(string[, encoding]) 返回一个新的 Buffer，其中包含提供的字符串的副本

const buf = Buffer.from([1,2,3])

const buf1 = Buffer.from('test')
```

buffer.alloc

```javascript
// Buffer.alloc(size[, fill[, encoding]]) 返回一个指定大小的新建的的已初始化的Buffer。
// 此方法比 Buffer.allocUnsafe(size) 慢，但能确保新创建的 Buffer 实例永远不会包含可能敏感的旧数据。如果 size 不是数字，则将会抛出 TypeError。

const buf3 = Buffer.alloc(10)
```

buffer其他常用方法：

> buf.compare,buf.copy,buf.equals,buf.includes
>
> buf.indexOf.buf.length,buf.toJSON,buf.toString

[其他](https://www.jianshu.com/p/e3f14cdf78f1)

#### FS

##### 读取文件

```javascript
const fs = require('fs')

// 异步读取
fs.readFile('./index.txt', 'utf8', (err, data) => {
  console.log(data) //  Hello Nodejs
})

// 同步读取
const data = fs.readFileSync('./index.txt', 'utf8')

console.log(data) //  Hello Nodejs

// 创建读取流
const stream = fs.createReadStream('./index.txt', 'utf8')

// fs.createReadStream用到了events eventEmitter.on() 方法来监听事件
stream.on('data', data => {
  console.log(data) // Hello Nodejs
})
```

> 如果读取小文件，我们可以使用fs读取，fs读取文件的时候，是将文件一次性读取到本地内存。而如果读取一个大文件，一次性读取会占用大量内存，效率很低，这个时候需要用流来读取。流是将数据分割段，一段一段的读取，可以控制速率,效率很高,不会占用太大的内存。gulp的task任务，文件压缩，和http中的请求和响应等功能的实现都是基于流来实现的。

##### 写入/修改文件

写入文件时,如果文件不存在,则会创建并写入,如果文件存在,会覆盖文件内容.

```javascript
const fs = require('fs')
// 异步写入
fs.writeFile('./write.txt', 'Hello Nodejs', 'utf8', err => {
  if (err) throw err
})
// 同步写入
fs.writeFileSync('./writeSync.txt', 'Hello Nodejs')
// 文件流写入
const ws = fs.createWriteStream('./writeStream.txt', 'utf8')
ws.write('Hello Nodejs')
ws.end()
```

##### 删除文件

```javascript
// 异步删除文件
fs.unlink('./delete.txt', err => {
  if (err) throw err
})

// 同步删除文件
fs.unlinkSync('./deleteSync.txt')
```

##### 删除文件夹

```javascript
// 异步删除文件夹
fs.rmdir('./rmdir', err => {
  if (err) throw err
})

// 同步删除文件夹
fs.rmdirSync('./rmdirSync')
```



##### 创建文件夹

```javascript
// 异步创建文件夹
fs.mkdir('./mkdir', err => {
  if (err) throw err
})

// 同步创建文件夹
fs.mkdirSync('./mkdirSync')
```



##### 重命名文件/文件夹

```javascript
const fs = require('fs')

// 异步重命名文件
fs.rename('./rename.txt', './rename-r.txt', err => {
  if (err) throw err
})

// 同步重命名文件夹
fs.renameSync('./renameSync', './renameSync-r')
```

##### 重命名文件/文件夹

```javascript
const fs = require('fs')

// 异步重命名文件
fs.rename('./rename.txt', './rename-r.txt', err => {
  if (err) throw err
})

// 同步重命名文件夹
fs.renameSync('./renameSync', './renameSync-r')
```

##### 复制文件/文件夹

```javascript
const fs = require('fs')

// 异步复制文件
fs.copyFile('./copy.txt', './copy-c.txt', err => {
  if (err) throw err
})

// 同步复制文件夹
fs.copyFileSync('./null', 'null-c')
```

##### 文件夹状态- 文件/文件夹

```javascript
const fs = require('fs')

// 异步获取文件状态
fs.stat('./dir', (err, stats) => {
  if (err) throw err
  // 是否是文件类型
  console.log(stats.isFile()) // false
  // 是否是文件夹类型
  console.log(stats.isDirectory()) // true
})

// 同步获取文件状态
const stats = fs.statSync('./stats.txt')

// 是否是文件类型
console.log(stats.isFile()) // true
// 是否是文件夹类型
console.log(stats.isDirectory()) // false
```

#### HTTP

> http 模块是 Node.js 中非常重要的一个核心模块。通过 http 模块，你可以使用其 http.createServer 方法创建一个 http 服务器

##### 创建http服务

```javascript
const http = require('http');
/**
 * @param {Object} req 是一个http.IncomingMessag实例
 * @param {Object} res 是一个http.ServerResponse实例
 */
const server = http.createServer((req, res) => {
    res.end('hello')
});

server.listen(3030);
```

##### req常用属性

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
    console.log(req.method);
    console.log(req.url);
    console.log(req.headers);
    res.end('hello')
});

server.listen(3030);

/*
访问localhost:3030
GET
/
{
    host: 'localhost:3030',
    connection: 'keep-alive',
    'cache-control': 'max-age=0',
    'upgrade-insecure-requests': '1',
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.97 Safari/53
    7.36',
    accept: 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*!/!*;q=0.8,application/signed-exchange;v=
    b3;q=0.9',
    'sec-fetch-site': 'none',
    'sec-fetch-mode': 'navigate',
    'sec-fetch-user': '?1',
    'sec-fetch-dest': 'document',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'zh-CN,zh;q=0.9',
    cookie: '_ga=GA1.1.2093560688.1565968868; Webstorm-eb136ecf=8140b201-31c3-42b6-a55d-f07bdea5ab89; Webstorm-63a56db3=fcf3d381-
cf44-49c5-ad1a-502399f674be'
}
*/
```



##### res常用属性

> 当使用 response.setHeader() 设置响应头时，它们将与传给 response.writeHead() 的任何响应头合并，其中 response.writeHead() 的响应头优先。

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
    res.setHeader('Content-Type', 'text/html');
    res.setHeader('X-Foo', 'bar');
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello Node');
});

server.listen(3030);
```

