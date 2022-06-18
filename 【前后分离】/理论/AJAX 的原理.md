# AJAX 的原理(Async JavaScript And XML)

> js三座大山之Ajax\
> **Ajax: 用js控制浏览器发请求和收响应**

**背景(原理)**\
**1.Ajax是浏览器推出的一个功能**\
浏览器可以**发请求，收响应**\
浏览器在window上加了一个XMLHttpRequest全局函数\
用这个构造函数(类)可以构造出一个对象\
JS通过它实现**发请求，收响应**

```
typeof window.XMLHttpRequest
'function'
```

实际上跟window.Object是一样的。\
window.Object用来创建普通对象，window.XMLHttpRequest用来创建XMLRequest对象。\
XMLHttpRequest也是一个构造函数，可以构造出一个对象。\
**2.准备一个服务器，用来接收请求**\
使用[server.js](https://github.com/lixueqin-4119/nodejs-test/blob/master/server.js)作为我们的服务器\
启动node server.js 8888\
添加index.html/main.js两个路由\
**工具** [node-dev](https://github.com/fgnass/node-dev) 是一个node.js开发工具，当文件被修改时会自动重启node进程。

```
Installation安装:yarn global add node-dev
Usage使用: node-dev src/server.js
```

用node-dev代替node方便开发，node-dev修改时会自动保存重启Restarting\
⚠️node-dev 已经出 bug 没人修理了，不要使用，请直接使用 node 并手动重启。\
**添加index.html/main.js两个路由**

```js
http://localhost:8888/index.html 注意浏览器路径要一致！

 if (path === '/index.html') { //1.添加index.html
        response.statusCode = 200
        response.setHeader('Content-Type', 'text/html;charset=utf-8')
        response.write(`\n
    <!DOCTYPE html>
    <html>
    <head>
      <title>Ajax</title>
    </head>
    <body>
      <h1>Ajax demo</h1>
      <script src="main.js"></script>
    </body>
    </html>
    `)
        response.end()
    } else if (path === '/main.js') { //2.添加main.js
        response.statusCode = 200
        response.setHeader('Content-Type', 'text/javascript;charset=utf-8')
        response.write(`console.log('我是js')`)
        response.end()
    } else { ... }
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/437327eb06374dc98624935f7069c386~tplv-k3u1fbpfcp-zoom-1.image)

**模块化**[示例代码](https://editor.csdn.net/md/?articleId=122872618)\
把html、css、js单独写到文件里\
1.write接收字符串，[把文件变字符串](https://www.liaoxuefeng.com/wiki/1022910821149312/1023025763380448)

```js
const string = fs.readFileSync('public/index.html') //html,注意不能有空格
response.write(string)
response.write(`fs.readFileSync('public/main.js')`) //js
```

对node.js来说文件就是字符串

这个服务器可以用js来控制你访问什么路径。\
**注意，这(判断语句)是服务器端的js(node.js),不是ajax.它也可以用java或者php写，跟ajax无关**。

### []()挑战1 加载CSS

以前我们用`<link rel=stylesheet href="1.css"/>`\
今天用AJAX加载css,**4步走**

**1’不用ajax怎么做？**\
首先新建style.css写好样式,然后index.html里引入css`<link rel="styleheet" href="/style.css">`,最后添加路由,代码如下：

```js
else if (path === '/style.css') {
  response.statusCode = 200
  response.setHeader('Content-Type', 'text/css;charset=utf-8')
  response.write(fs.readFileSync('public/style.css'))
  response.end()
}
```

**2’用ajax怎么做？**\
在js里请求Ajax\
**4步骤**\
**1.创建HttpRequest对象(全称是XMLHttpRequest)**\
**2.调用对象的open方法**\
**3.监听对象的unload & onerror事件(成功/失败)**\
**4.调用对象的send方法(发送请求)**

**1.创建HttpRequest对象(全称是XMLHttpRequest)**

```js
const request=new XMLHttpRequest()
```

**2.调用对象的open方法**

```
request.open('GET','./style.css')
```

🔍[mdn XMLHttpRequest open](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/open)\
语法xhrReq.open(method, url);
\
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/39a569c8ca1246b89584ac1a6588f852~tplv-k3u1fbpfcp-zoom-1.image)

**注意:专业前端只用前面2个! 后面的基本没人会用**\
method:get/post

**3.监听对象的unload & onerror事件(成功/失败)**\
专业前端会改用onreadystatechange事件\
在事件处理函数里操作CSS文件内容\
**4.调用对象的send方法(发送请求)**\
具体代码打开MDN用CRM大法搞定

```js
request.onload = () => {
    console.log("成功了")
}
request.onerror = () => {
    console.log("失败了")
}
request.send()
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6839fa51186b4bb6acb29eb0016ceda6~tplv-k3u1fbpfcp-zoom-1.image)

拿到内容request.response\
添加button请求css，方便查看

```js
getCSS.onclick = () => {
  const request = new XMLHttpRequest()
  request.open('GET', './style.css')
  request.onload = () => {
    console.log('request.response' + request.response)
    console.log("成功了")
  }
  request.onerror = () => { console.log("失败了") }
  request.send()
}
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/952f4208c5b94d23839c0705ef955456~tplv-k3u1fbpfcp-zoom-1.image)

**有内容后如何生效？**\
创建style标签，填写style内容，插到头里面

```js
const style = document.createElement('style')
style.innerHTML = request.response
document.head.appendChild(style)
```

### []()挑战2 加载js

以前我们用`<script src="2.js"/></script>`\
今天用AJAX加载js\
1’不用ajax\
首先新建2.js写好代码,然后html里引入js`<script src="2.js"></script>`,最后添加路由,代码如下：

```js
else if(path==='/2.js'){
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/javascript;charset=utf-8')
    response.write(fs.readFileSync('public/2.js'))
    response.end()
  }
```

**2’用ajax**\
添加个button请求js,方便查看`<button id="getJS">请求JS</button>`

```js
getJS.onclick = () => {
  const request = new XMLHttpRequest()
  request.open('GET', './2.js')
  request.onload = () => {
    //console.log('request.response:' + request.response)
    const script = document.createElement('script')
    script.innerHTML = request.response
    document.body.appendChild(script)
  }
  request.onerror = () => { console.log("失败了") }
  request.send()
}
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eea1ed16249b49a89f735568b9e565f4~tplv-k3u1fbpfcp-zoom-1.image)

**为什么插入就可以执行？**\
原理,只要浏览器看到了`<script>`标签，它就会执行里面的内容，没看到就不会执行。\
在学习**DOM操作跨线程**时说过“js引擎与浏览器的关系”。浏览器包括很多部分，其中就包括js引擎、渲染引擎。只有把js标签放到渲染引擎里才会触发。

### 挑战3 加载html

以前我们不会加载3.html [示例代码](https://github.com/lixueqin-4119/js-demo29)

添加个button请求html,方便查看\
**步骤**\
1.新建文件3.html写好要展示的html\
2.添加button方便查看`<button id="getHtml">请求html</button>`\
添加路由,代码如下：

```js
else if (path === '/3.html') { 
  response.statusCode = 200
  response.setHeader('Content-Type', 'text/html;charset=utf-8')
  response.write(fs.readFileSync('public/3.html'))
  response.end()
} 

getHtml.onclick = () => {
  const request = new XMLHttpRequest()
  request.open('GET', './3.html')
  request.onload = () => {
    console.log('request.response:' + request.response)
    const div = document.createElement('div')
    div.innerHTML = request.response
    document.body.appendChild(div)
  }
  request.onerror = () => { console.log("失败了") }
  request.send()
}
```

**应用场景:**  可以把页面拆成各个不同的部分，当用户点击时再请求这部分，这在以前是做不到的。\
Ajax可以做到轻量级的请求。你想请求什么都可以，只要http支持就可以。

### []()onreadystatechange 事件

**什么时候会触发onerror呢？**\
假设故意将路径写错成3.htm，这时只会触发404而不是onerror。

```js
else {
  response.statusCode = 404
  response.setHeader('Content-Type', 'text/html;charset=utf-8')
  response.write(`你输入的路径不存在对应的内容`)
  response.end()
}
```

因为onerror并没有很好的匹配ajax，因为onerror是先发明的，ajax是后发明的。\
onerror一开始是用来检测请求图片失败了，没有料想到会去监听ajax失败。\
那这个怎么解决呢？\
专业的前端不会使用 onload & onerror事件(太简陋了)，会改用[onreadystatechange](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/onreadystatechange)事件。\
只要[readyState](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/readyState)属性发生变化，就会调用触发相应的处理函数。readyState值如下图所示:\
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/67cad00b1a6b48ea9bd1285f4931e134~tplv-k3u1fbpfcp-zoom-1.image)

**助解图**\
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/290abb77ad754f06ae0b17f7ec3e625b~tplv-k3u1fbpfcp-zoom-1.image)

将路径故意写错为styl.css,用onreadystatechange改写。\
不管结果是成功还是失败都会打印出**下载完成**(1个请求)

```js
getCSS.onclick = () => {
  const request = new XMLHttpRequest()
  request.open('GET', './styl.css') //readyState=1
  request.onreadystatechange = () => {
    //下载完成(一个请求完成)，但不知道是成功2xx还是失败4xx 5xx
    console.log(request.readyState) 
    if (request.readyState === 4) {
      console.log("下载完成")
      console.log(request.status) //打印http状态码
      if (request.status >= 200 && request.status < 300) { //当状态码以2开头才算成功
        const style = document.createElement('style')
        style.innerHTML = request.response
        document.head.appendChild(style)
      } else {
        alert('加载css失败')
      }
    }
  }
  request.send() //readyState=2
}
```

**补充：http状态码以2开头的都表示成功**，一般不用管300多，因为3开头会再次发一个新的请求。\
**不管是成功还是失败都会打出下载完成**\
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2a5223627a4c4cd6bb99d9af313f9824~tplv-k3u1fbpfcp-zoom-1.image)

### []()挑战4 加载 XML

**步骤**\
1.新建文件4.xml写好要展示的[xml](https://developer.mozilla.org/zh-CN/docs/Web/XML/XML_Introduction#xml_.e7.a4.ba.e4.be.8b_2)

```js
<?xml version="1.0" encoding="UTF-8"?>
<message>
    <warning>
         Hello World
    </warning>
</message>
```

2.添加button方便查看`<button id="getXML">请求XML</button>`\
添加路由,代码如下：

```js
else if (path === '/4.xml') {
  response.statusCode = 200
  response.setHeader('Content-Type', 'text/xml;charset=utf-8')
  response.write(fs.readFileSync('public/4.xml'))
  response.end()
}
```

补充：**当new后面没有参数时可以省略()**

```js
const request = new XMLHttpRequest()
没有参数时可以省略()
const request = new XMLHttpRequest
```

**request.responseXML可以直接将字符串变成dom对象**\
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1200564a69324a36baf66120f5d721f4~tplv-k3u1fbpfcp-zoom-1.image)

**DOM对象不止是用于html,还可以用于xml文档**

```js
getXML.onclick = () => {
  const request = new XMLHttpRequest()
  request.open('GET', './4.xml')
  request.onreadystatechange = () => {
    if (request.readyState === 4 && request.status === 200) {
      //console.log(request.responseXML)
      //console.log(request.responseXML)
      const dom = request.responseXML
      const text = dom.getElementsByTagName('warning')[0].textContent
        console.log(text.trim())
    }
  }
  request.send()
}
```

**注意:**getElementsByTagName得到的是伪数组必须加[0]否则**undefined**

**Ajax最初就是为了让你使用responseXML，只不过发展2年不到，一个新的语言替代了xml，它就是json。**
### 总结

**HTTP是个框，什么都能往里装**\
可以装HTML、CSS、JS、XML…\
记得设置正确的Content-Type,这是好习惯\
只要你知道怎么解析这些内容，就可以使用这些内容\
**解析方法**\
得到css后生成style标签\
得到js后生成script标签\
得到html之后使用innerHTML和DOM API\
得到xml之后使用responseXML和DOM API\
不同类型的数据有不同类型的解析办法

### []()什么是JSON？

**JS对象标记语言JavaScript Object Notation**\
**JSON是一门语言**\
跟HTML、CSS、XML、JS一样，是一门独立的语言\
**JSON不是编程语言是标记语言**\
跟HTML、XML、Markdown一样，用来展示数据\
[中文官网](https://www.json.org/json-en.html)\
一页纸就说明了JSON的所有语法\
你会看铁轨图就会用JSON\
例子：\
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8bf66166472148b19bfba72320ed3533~tplv-k3u1fbpfcp-zoom-1.image)

对象的语法是{ }空对象 或者 {“name”:“li”,“age”:18}\
**支持的数据类型**\
**string** 只支持双引号，不支持单引号和无引号\
**number** 支持科学记数法\
**object**\
**array**\
bool true和false\
null 没有undefined\
就这6种，注意跟JS的七种数据类型区别开来\
不支持函数，不支持变量(所以也不支持引用)

**JSON抄袭JS，把js糟粕删掉只取了好的部分，好的这部分就是JSON的全部内容。**

### []()挑战5 加载json

我们需要加载5.json

**步骤**\
1.新建文件5.json写好要展示的数据

```
{
    "name":"李",
    "age":18,
    "xxx":null
}
```

2.添加button方便查看`<button id="getJSON">请求JSONL</button>`\
添加路由,代码如下：

```js
else if (path === '/5.json') {
        response.statusCode = 200
        response.setHeader('Content-Type', 'text/json;charset=utf-8')
        response.write(fs.readFileSync('public/5.json'))
        response.end()
    }
```

```js
getJSON.onclick = () => {
    const request = new XMLHttpRequest()
    request.open('GET', './5.json')
    request.onreadystatechange = () => {
        if (request.readyState === 4 && request.status == 200) {
            console.log(request.response)
            const object = JSON.parse(request.response)
            console.log(object)
        }
    }
    request.send()
}
```

**如何把字符串变成js对象？**\
**JSON.parse()可以把符合JSON语法的字符串变成对应的对象或者是其它东西。**\
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/689c1ae413304c75abe40f9cafb68d72~tplv-k3u1fbpfcp-zoom-1.image)\
ajax把json代替xml作为数据请求，JSON.parse()可以解析它。\
我们可以对数据进行任意操作，用处很广泛。\
例如，进入网站时会提示 “欢迎，李”

```js
index.htm
<h1>ajax hello<span id="myName"></span></h1>

main.js
getJSON.onclick = () => {
...
 myName.textContent = object.name
}
```



### 总结

window.JSON是全局对象,它有2个方法。\
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7a5738dc364e493faadc7017828a5340~tplv-k3u1fbpfcp-zoom-1.image)\
**JSON.parse**\
将符合JSON语法的字符串转化成JS对应类型的数据\
JSON字符串 => JS数据\
由于JSON只有6种类型，所以转成的数据也只有6种\
如果不符合JSON语法，则直接抛出一个Error对象\
一半用try catch捕获错误\
**JSON.stringify**\
是JSON.parse的逆运算\
JS数据 => JSON字符串\
**由于JS的数据类型比JSON多，所以不一定能成功**\
如果失败就抛出一个Error对象\
json不支持函数\
**经验：使用JSON.parse时一定要try catch!**

```js
JSON.parse(`{'name':'lixueqin'}`) //不能用单引号，必须是双引号
Uncaught SyntaxError: Unexpected token ' in JSON at position 1

let object
try{ //把要写的代码放try里
  object=JSON.parse(`{'name':'lixueqin'}`) //成功的话把值赋给object
}catch(error){
  console.log('出错了，错误详情是')
  console.log(error)
  object={'name':'no name'} //失败的话就用默认的值
}
console.log(object)
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/68e06ff6c728492e9f0f4563279d77de~tplv-k3u1fbpfcp-watermark.image?)

**注意:**\
如果你把错误捕获了，object={‘name’:‘no name’}执行完后还是会执行console.log(object)的，因为错误被你消化了。\
对代码来说，try catch里是没有错误的。\
捕获的意思是我要把这个错误遮住，不能被别人看到了。自己把错误偷偷的干掉。\
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a481720e1b3b4e6eaca499c4bbbf84b0~tplv-k3u1fbpfcp-zoom-1.image)

不报错，直接把函数忽略了。\
因为JSON不支持函数，所以没办法转。

### []()综合应用 加载分页

Ajax最典型的应用**加载分页**\
**步骤**\
1.把数据放到数据库里。\
新建目录db，3个文件page1.json、page2.json、page3.json\
page1.json内容为[{“id”:1}, … {“id”:10}]\
page2.json内容为[{“id”:11}, … {“id”:20}]\
page3.json内容为[{“id”:21}, … {“id”:30}]\
2.替换

```js
<div>
  {{page1}}
</div>
```

渲染首页时把page1改为page1的内容。

```js
let string = fs.readFileSync('public/index.html').toString()
const page1 = fs.readFileSync('db/page1.json')  //获取内容
string = string.replace('{{page1}}', page1)  //将拿到的数据放到页面里      
```

**知识点:**\
1.[replace()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)方法会产生一个新字符串\
2.调用.toString()可以强制变string。\
当报错时(TypeError: string.replace is not a function)可以用这个方法解决。

总结：这叫做前后端不分离的渲染技术。

**示例：我们也可以将id变成li**\
从数据库里拿到数据，然后拼接字符串.

```js
let string = fs.readFileSync('public/index.html').toString()
  const page1 = fs.readFileSync('db/page1.json')
  //string = string.replace('{{page1}}', page1)
  const array = JSON.parse(page1)
  const result = array.map(item => `<li>${item.id}</li>`).join('')
  string = string.replace('{{page1}}', `<ul id="xxx">${result}</ul>`)
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/35389adecdb346438fdd2d0d413906f3~tplv-k3u1fbpfcp-zoom-1.image)

### []()请求下一页[完整代码](https://github.com/lixueqin-4119/js-demo30/blob/master/public/main.js)

加个按钮请求下一页`<button id="getPage">请求下一页</button>`，然后添加监听。

```js
getPage.onclick = () => {
    const request = new XMLHttpRequest()
    request.open('GET', '/page2')
    request.onreadystatechange = () => {
        if (request.readyState === 4 && request.status == 200) {
            console.log(request.response)
        }
    }
    request.send()
}
```

添加后台响应

```js
else if (path === '/page2') {
  response.statusCode = 200
  response.setHeader('Content-Type', 'text/json;charset=utf-8')
  response.write(fs.readFileSync('db/page2.json'))
  response.end()
} 
```

用js把page2放到page1后面
```js

let n = 1 //值会变化时用let
getPage.onclick = () => {
  const request = new XMLHttpRequest()
  request.open('GET', `/page${n + 1}`)
  request.onreadystatechange = () => {
    if (request.readyState === 4 && request.status == 200) {
      const array = JSON.parse(request.response) //把json字符串变成js数组
      array.forEach(item => {//将每个元素叫做item
        const li = document.createElement('li')
        li.textContent = item.id
        xxx.appendChild(li) //<ul id="xxx">
      });
        n += 1
    }
  }
  request.send()
}
```

xxx.appendChild(li)\
解析:window.idxxx可以直接idxxx 或者 document.querySelector(’#idxxx’)

添加page3路由代码略
