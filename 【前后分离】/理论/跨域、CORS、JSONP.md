# 跨域、CORS、JSONP(必考)

> ajax和Promise的跨域问题：跨域一般用2种方案JSONP和CORS

### 跨域

> 前端与后端的交互。

1.同源策略 浏览器故意设计的一个功能限制\
2.CORS 突破浏览器限制的一个办法\
3.JSONP IE时代的妥协

#### 一.同源策略

**1.同源定义**\
**源**\
控制台输入window.origin或location.origin可以得到当前源\
源=协议+域名+端口号\
如果两个url的协议、域名、端口号完全一致，那么这两个url就是同源的。\
**举例**
```js
https://qq.com、https://www.baidu.com不同源
https://baidu.com、https://www.baidu.com不同源
```
完全一致才算同源

**2.同源策略定义**\
**浏览器规定**\
如果JS运行在源A里就只能获取源A的数据，不能获取源B的数据，即**不允许跨域**。\
举例(省略http协议)\
假设diaoyu.com/index.html引用了cdn.com/1.js\
那么就说1.js**运行在源diaoyu.com**里。注意这跟cdn.com没关系，虽然1.js从它那**下载**\
所以1.js就只能获取diaoyu.com的数据

**1.js运行在哪个域名里就只能访问这个域名的数据**\
**这是浏览器的功能，浏览器故意要这样设计的。**\
浏览器这么做的目的是为了**保护用户隐私**。

怎么保护的？(假设没有同源策略,所有的js可以访问所有的网站的数据)\
**以QQ空间为例**\
源为https://user.qzone.qq.com\
假设，当前用户已登陆(登陆信息保存在浏览器Cookie里)，现在可以查看好友列表。\
好友信息是通过Ajax请求/friends.json获取到的好友列表\
假设，Ajax请求/friends.json可获取用户好友列表\
目前为止都很正常\
**黑客来了**\
假设你的女神(黑客)分享https://qzone-qq.com 给你，实际是钓鱼网站。\
你点开这个网页，这个网页也请求你的好友列表\
https://user.qzone.qq.com/friends.json\
请问，你的好友列表是不是就被黑客偷走了？是的

**问题的根源**
第1次是正常的请求，第2次是黑客的请求。几乎没有区别除了**referrer**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8b9afeb0283242eca3d6518c6aebe563~tplv-k3u1fbpfcp-zoom-1.image)

**控制台的XHR是XMLHttpRequest的缩写也就是ajax的缩写**

**同源策略：不同源的页面之间，不准互相访问数据**

## []()实践 [完整代码](https://editor.csdn.net/md/?not_checkout=1&articleId=122953054)

> 做2个网站来演示下

**步骤**\
1.创建2个目录qq-com和diaoyu-com\
qq-com目录新建server.js,模拟QQ空间\
diaoyu-com目录新建server.js,模拟坏人空间\
**先打开第1个目录，然后将第2个目录添加到工作区**

```js
安装：yarn global add node-dev 
使用：node-dev server.js 8888 //qq-com
     node-dev server.js 9999 //diaoyu-com
```

2.**qq-com**\
/index.html是首页\
/qq.js是JS脚本文件\
/friends.json是模拟的好友数据\
端口监听为8888，访问http://127.0.0.1:8888

**操作步骤**\
编写path=’/index.html’路由,然后新建index.html。server.js会读取index.html。\
编写path === '/qq.js’路由,然后新建qq.js。server.js会读取qq.js。\
编写path === '/friends.json’路由,然后新建friends.json。server.js会读取friends.json。

**知识点：** [fs文档](http://nodejs.cn/api/fs.html)、[fs模块-阮一峰](https://javascript.ruanyifeng.com/nodejs/fs.html)推荐[网道](https://wangdoc.com/javascript/)\
Node.js文件系统封装在fs模块中，它提供了文件的读取、写入、更名、删除、遍历目录、链接等POSIX文件系统操作\
fs模块的基本使用：

```js
var fs = require('fs') //引用fs
response.write(fs.readFileSync('./public/index.html'))
```

**3.diaoyu-com**\
操作步骤同上\
/index.html是首页\
/diaoyu.js是JS脚本文件\
补充：diaoyu-com不需要写路由/friends.json\
端口监听为9999，访问http://127.0.0.1:9999

4.让qq.js**成功**访问自己的json(friends.json)数据\
让diaoyu.js**不能成功**访问qq的json

能不能做到域名也不同?设置本地域名映射,修改hosts\
让qq.com映射到127.0.0.1,就可以访问 http://qq.com:8888/index.html 了\
让diaoyu.com映射到127.0.0.1,就可以访问 http://diaoyu.com:9999/index.html 了

### []()跨域AJAX

> 演示跨域被阻止

**1.qq-com**\
首先index.html应用js`<script src="qq.js"></script>`\
然后qq.js发送请求

```js
const request = new XMLHttpRequest()
request.open('GET', './friends.json')
request.onreadystatechange = () => {
    if (request.readyState === 4 && request.status === 200) {
        console.log(request.response)
    }
}
request.send()
```

2.diaoyu-com\
操作同上\
首先index.html应用js`<script src="./diaoyu.js"></script>`\
然后qq.js发送请求

```js
const request = new XMLHttpRequest()
request.open('GET', 'http://qq.com:8888/friends.json')//请求qq.com的json
request.onreadystatechange = () => {
    if (request.readyState === 4 && request.status === 200) {
        console.log(request.response)
    }
}
request.send()
```

diaoyu-com不需要/friends.json路由

```js
提示：从源“http://diaoyu.com：9999”访问“http://qq.com：8888/friends.json”的XMLHttp请求已被CORS策略阻止：请求的资源上不存在“访问-控制-允许源”头。
```

response拿不到qq.com的数据，因为**CORS策略阻止了**。\
这就是跨域，跨域会被阻止的。浏览器会限制跨域，故意不给数据。浏览器需要CORS。

**其它疑问**\
**1.为什么a.qq.com访问qq.com也算跨域？**\
因为历史上出现过不同公司共用域名。a.qq.com和qq.com不一定是同一个网站。浏览器谨慎起见，认为这是不同的源。\
**2.为什么不同端口也算跨域？**\
原因同上。曾经由于服务器比较贵，很多公司共用同一个服务器，这导致了一个端口一个公司，这两个公司数据是不共享的。记住安全链条的强度取决于最弱的一环，任何安全相关的问题都要谨慎对待。\
**3.为什么两个网站的IP是一样的，也算跨域？**\
原因同上。IP都一样说明在同一台服务器上。很早的时候服务器很贵的，所以一台服务器挂了20个不同网站的网页都是有可能的。所以由于**IP可以共用**，实际上它们是不同的公司。\
**4.为什么可以跨域使用CSS、JS、图片？**

**面试题**
同源策略限制的是数据访问，我们引用CSS、JS、图片的时候，其实并不知道其内容，我们只是在引用。能引用但不能读取。不信我问你，你能知道CSS的第1个字符是什么吗？

虽然理论上要求不同网站不能共享数据，不允许跨域，但实际工作或者面试中经常会问**如何跨域共享数据**。

### []()实现跨域的2种方法

**方法一：CORS 跨域资源共享**\
**问题根源**\
浏览器默认不同源之间不能互相访问数据。\
但是qq.com和diaoyu.com其实都是我的网站,我就是想要两个网站互相访问，浏览器为什么阻止。\
好吧，用CORS\
浏览器说，如果要共享数据，需要提前声明！\
**怎么声明？**\
浏览器说，qq.com在响应头里写diaoyu.com可以访问\
**设置响应头**`Access-Control-Allow-Origin: http://foo.example`\
[CORS文档](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS#%E7%AE%80%E5%8D%95%E8%AF%B7%E6%B1%82) CORS分为简单请求和复杂请求具体看文档\
示例代码

```js
else if (path === '/friends.json') {
  response.statusCode = 200
  response.setHeader('Content-Type', 'text/json;charset=utf-8')
  response.setHeader('Access-Control-Allow-Origin', 'http://diaoyu.com:9999')
  response.write(fs.readFileSync('./public/friends.json'))
  response.end()
  }
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/577b1d8bb1f241e8ae5129f8711c2507~tplv-k3u1fbpfcp-zoom-1.image)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f57e2199a33c4c90a35486e3a597b9d5~tplv-k3u1fbpfcp-zoom-1.image)

**如果有2个网站都想请求qq.com呢？**\
思路：来一个网站读取一个网站,不是将这2个网站都写上去。\
console.log(request.headers[‘referer’])\
将当前正在请求的网站记录下来，然后重新记录到Access-Control-Allow-Origin后面\
⚠️**注意：IE不支持CORS,要想兼容IE只能用第2种方法**

**方法二：JSONP(兼容IE)**

> JSONP和JSON半毛钱关系都没有

记不记得我们可以任意引用JS\
虽然我们不能访问qq.com:8888/friends.json，但是我们能引用qq.com:8888friends.js啊！\
**这有什么用？**\
JS又不是数据\
我们让JS包含数据不就好了，把数据写到js文件里\
**qq创建包含数据的js**，diaoyu.com访问qq.com\
**步骤**\
**1.qq.com将数据写到/friends.js**\
qq新建文件friends.js,代码

```js
{ { data } }  //占位
```

**2.后台写路由**\
后台获取js内容、获取数据json，把数据填到js内容。然后再返回给浏览器。

```js
else if (path === '/friends.js') {
  response.statusCode = 200
  response.setHeader('Content-Type', 'text/javascript;charset=utf-8')
  const string = fs.readFileSync('./public/friends.js').toString()
  const data = fs.readFileSync('./public/friends.json').toString()
  //默认不是string要toString
  const string2 = string.replace('{{data}}', data)
  //把data塞到string里。用数据data把占位{{data}}替换掉
  response.write(string2)
  response.end()
} 
```

**补充:js文件中光有数据是不合法的需要赋值**

```js
window.xxx = { { data } } 
```

**3.diaoyu.com用script标签引用/friends.js,js动态引用**\
diaoyu.js

```js
const script = document.createElement('script')
script.src = 'http://qq.com:8888/friends.js'
document.body.appendChild(script)
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7661ae406a7849948435095f93ffba6e~tplv-k3u1fbpfcp-zoom-1.image)

那怎么确定数据是否拿到呢？监听script的onload事件

```js
const script = document.createElement('script') script.src = 'http://qq.com:8888/friends.js' script.onload = () => { console.log(window.xxx) } document.body.appendChild(script)
```
**4.js文件中光有数据是不合法的需要赋值,但是xxx可能被占用，可以用函数**

```js
//window.xxx={{data}}
window.xxx({{ data }})
```

diaoyu.js\
**不用再监听onload了。因为只要执行成功就可以调用函数window.xxx了**

```js
window.xxx = (data) => {
    console.log(data)
}
const script = document.createElement('script')
script.src = 'http://qq.com:8888/friends.js'
document.body.appendChild(script)
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e9a72b17415645a0a7088879164bc444~tplv-k3u1fbpfcp-zoom-1.image)

回调：我定义一个函数给别人调用，别人调的时候给我个数据。\
**window.xxx本质就是一个回调**\
我定义了但是不调用，等着qq.com的脚本js文件(friends.js)来调用。调的时候就会把数据作为第1个参数传给我。

### []()JSONP referer检查限制访问者

CORS可以指定谁访问者，JSONP没办法指定，那JSONP的js不是所有网站都可以用了吗？\
是的，但是**JSONP可以做referer检查限制访问者。**

```js
else if (path === '/friends.js') {
  if (request.headers['referer'].indexOf('http://diaoyu.com:9999') === 0) {
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/javascript;charset=utf-8')
    //console.log(request.headers['referer']) //http://diaoyu.com:9999
    const string = fs.readFileSync('./public/friends.js').toString()
    const data = fs.readFileSync('./public/friends.json').toString()
    const string2 = string.replace('{{ data }}', data)
    response.write(string2)
    response.end()
  } else {
    response.statusCode = 404
    response.end()
   }
} 
```

[indexOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/indexOf)\
即使这样还是会有安全隐患，一旦我信任的网站被攻陷(diaoyu.com)，那qq.com也就完了。\
还有更严格的限制以后会讲(涉及Cookie、Token)

### 优化JSONP

**一.xxx能不写死吗？**\
假如我有多个接口，除了"好友列表"，还有"最近访问的人"等，这明显不能满足我们的需求。其实名字不重要，只要diaoyu.com定义的函数名和qq.com/friends.js执行的函数名是同一个即可。那就把名字传给/friends.js\
**1.思路：自动生成**

```js
const random = Math.random()
//console.log(random) 0~1的小数，所有的key都可以是字符串，只要是字符串就行
window[random] = (data) => { //window['0.023216364']=函数
  console.log(data)
}
```

**2.如何把随机数给后台？查询参数`?functionName`**

```js
diapyu.js
script.src = `http://qq.com:8888/friends.js?functionName=${random}`
```

**传进去后怎么渲染到js里?**

```js
qq.com
else if (path === '/friends.js') {
  if (request.headers['referer'].indexOf('http://diaoyu.com:9999') === 0) {
  ...
  console.log(query.functionName) //query?
  }
}
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7ec0314fb4e244ed9344f18129147425~tplv-k3u1fbpfcp-zoom-1.image)

**补充** [url.parse](https://blog.csdn.net/weixin_39037804/article/details/102467141?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0.pc_relevant_default&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

```js
var parsedUrl = url.parse(request.url, true)
var query = parsedUrl.query

语法：第2个参数(可省)传入一个布尔值，默认为false，为true时，返回的url对象中，query的属性为一个对象。
url.parse("http://user:pass@host.com:8080/p/a/t/h?query=string#hash",true);
返回值：
 {
  protocol: 'http:',
  slashes: true,
  auth: 'user:pass',
  host: 'host.com:8080',
  port: '8080',
  hostname: 'host.com',
  hash: '#hash',
  search: '?query=string',
  query: { query: 'string' },
  pathname: '/p/a/t/h',
  path: '/p/a/t/h?query=string',
  href: 'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'
 }
 将第2个参数设置为true时，query属性为 “名称/值”对的集合,即json格式。
```

**将随机数插入到字符串里**\
friends.js

```js
window['{{xxx}}']({{ data }})
```

**再将{{xxx}}替换成随机数**

```js
const string2=string.replace('{{ data }}', data).replace('{{xxx}}',query.functionName)
```

**还可以更复杂点：加个前缀**

```js
const random = 'diaoyuJSONPCallbackName' + Math.random()
```

**二.执行完后删掉`<script>`标签**\
每发一次请求就会增加一个`<script>`标签，页面也会变的臃肿。

```js
const random = 'diaoyuJSONPCallbackName' + Math.random()
console.log(random)
window[random] = (data) => {
    console.log(data)
}
const script = document.createElement('script')
script.src = `http://qq.com:8888/friends.js?functionName=${random}`
script.onload = () => { //删掉<script>标签
    script.remove()  
}
document.body.appendChild(script)
```

只要执行完了，`<script>`就没用了，可以删掉

**三.封装jsonp()**\
封装成jsonp(‘url’).then(f1,f2)

```js
function jsonp(url) {
  return new Promise((resolve, reject) => {
    const random = 'diaoyuJSONPCallbackName' + Math.random()
    window[random] = (data) => {//成功调用resolve
      resolve(data)
    }
    const script = document.createElement('script')
    //script.src = `${url}?functionName=${random}`
    script.src = `${url}?callback=${random}`
    script.onload = () => {
      script.remove()
     }
    script.onerror = () => {//失败调用reject
      reject()
    }
    document.body.appendChild(script)
  })
}

jsonp('http://qq.com:8888/friends.js').then((data) => {
  console.log(data)
})
```

jsonp与ajax对比的弱点，**jsonp只能知道成功/失败，不能拿到状态码。**

**⚠️注意：jsonp约定查询参数不能叫functionName应该叫callback**`?callback`

**四.删除文件friends.js**\
friends.js的`window['{{xxx}}']({{ data }})`这句代码其实可以直接写在后台。

```js
//const string = fs.readFileSync('./public/friends.js').toString()
const string = `window['{{xxx}}']({{ data }})`           
```

**JSONP就是创建一个`<script>`请求js，js把数据夹带过来**\
**跨域还有其他方案可以自己搜**

**面试题**\
**JSONP是什么？**\
1.跨域时由于当前浏览器不支持CORS，所以必须使用另外一种方式实现跨域。\
于是我们就请求一个js文件，这个js文件会执行一个回调，回调里有我们的数据。\
**追问，你这个回调的名字是什么？**\
回调的名字是可以随机生成的1个随机数，我们把这个名字以callback的参数传给后台，后台会把这个函数再次返回给我们并执行。\
2.JSONP优点:(1)兼容IE (2)可以跨域\
3.JSONP缺点:\
(1)由于是`<script>`标签,所以它读不到ajax那么精确的状态。状态码、响应头都不知道，只知道成功和失败。\
(2)由于是`<script>`标签只能发get请求,**JSONP不支持post**。
