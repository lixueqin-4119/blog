# 静态服务器Static Server
> 优化server.js

优化前
```js
var http = require('http')
var fs = require('fs')
var url = require('url')
var port = process.argv[2]

if (!port) {
    console.log('请指定端口号好不啦？\nnode server.js 8888 这样不会吗？')
    process.exit(1)
}

var server = http.createServer(function (request, response) {
var parsedUrl = url.parse(request.url, true)
var pathWithQuery = request.url
var queryString = ''
if (pathWithQuery.indexOf('?') >= 0) { queryString = pathWithQuery.substring(pathWithQuery.indexOf('?')) }
var path = parsedUrl.pathname
var query = parsedUrl.query
var method = request.method

/******** 从这里开始看，上面不要看 ************/
console.log('有个傻子发请求过来啦！路径（带查询参数）为：' + pathWithQuery)

  if (path === '/') {
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/html;charset=utf-8')
    response.write(`二哈`)
    response.end()
  } else if (path === '/x') {
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/css;charset=utf-8')
    response.write(`body{color: red;}`)
    response.end()
  } else {
    response.statusCode = 404
    response.setHeader('Content-Type', 'text/html;charset=utf-8')
    response.write(`你输入的路径不存在对应的内容`)
    response.end()
  }

/******** 代码结束，下面不要看 ************/
})
server.listen(port)
console.log('监听 ' + port + ' 成功\n请用在空中转体720度然后用电饭煲打开 http://localhost:' + port)
```

```
安装：yarn global add node-dev 
运行：node-dev server.js 8888
```
1.如果访问/index.html就要读取index文件，访问/style.css就要读取style文件。
每次读取新文件我都要写代码，如果有100个文件难道要写100个if else吗，**如何简化？**
```js
response.statusCode = 200
response.setHeader('Content-Type', 'text/html;charset=utf-8')
const x = path
response.write(fs.readFileSync(`./public${x}`))
response.end()
```
**2.当读取不存在的文件时浏览器就奔溃了，怎么解决?**

思路：try catch下,如果报错了就把这个错处理一下。如果文件不存在，就返回404。
```js
response.statusCode = 200
response.setHeader('Content-Type', 'text/html;charset=utf-8')
const filePath = path
let content
try { //有可能会报错的代码
  content = fs.readFileSync(`./public${filePath}`)
} catch (error) { //错误处理
  content = '文件不存在'
  response.statusCode = 404
}
response.write(content)
response.end()
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/46fc1a14255a4a0c90a6d5189d51e96c~tplv-k3u1fbpfcp-zoom-1.image)

**如果是根目录就默认为/index.html**
```
const filePath = path === '/' ? '/index.html' : path
```
实际上很多浏览器都会默认加上这句话。

**3.如何替换Content-Type？**

怎么知道文件请求的是html/css/js? 

思路：通过获取用户请求路径的后缀。
```js
console.log(filePath.lastIndexOf('.'))
const index = filePath.lastIndexOf('.')
const suffix = filePath.substring(index)
console.log(suffix)
```
**index是.出现的位置。从/开始算，/是第0个**

当请求的是/main.js时，此时的index等于5，suffix等于.js

得到后缀后，利用数据结构里的哈希表一一映射
```js
const fileType = {
  '.html': 'text/html',
  '.css': 'text/css',
  '.js': 'text/javascript',
  '.png': 'image/png',
  '.jpg': 'image/jpeg'
}
response.setHeader('Content-Type', `${fileType[suffix] || 'text/html'};charset=utf-8`)
```
当suffer等于.js时，Content-Type值为'text/javascript'

**如果不是html/css/js中的任意一个就设置保底值为'text/html'**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6710db15c86d4a8583e94a035b07f4dc~tplv-k3u1fbpfcp-zoom-1.image)

**知识点:** try catch
```
try {
  //有可能会报错的代码
} catch (error) {
  //错误处理
}
```

### 优化后 [完整代码](https://github.com/lixueqin-4119/js-demo33/blob/master/server.js)
> 自动查找对应文件，即**静态文件服务器**

```js
/******** 从这里开始看，上面不要看 ************/
console.log('有个傻子发请求过来啦！路径（带查询参数）为：' + pathWithQuery)

response.statusCode = 200
//默认首页
const filePath = path === '/' ? '/index.html' : path
console.log(filePath.lastIndexOf('.'))
const index = filePath.lastIndexOf('.')
const suffix = filePath.substring(index) //suffix后缀
console.log(suffix)
const fileType = {
  '.html': 'text/html',
  '.css': 'text/css',
  '.js': 'text/javascript',
  '.png': 'image/png',
  '.jpg': 'image/jpeg'
    }
response.setHeader('Content-Type', `${fileType[suffix] || 'text/html'};charset=utf-8`)

let content
try { //有可能会报错的代码
  content = fs.readFileSync(`./public${filePath}`)
} catch (error) {
  content = '文件不存在'
  response.statusCode = 404
}
response.write(content)
response.end()
/******** 代码结束，下面不要看 ************/
```
**server.js文件可用来代替http server**









