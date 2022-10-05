> 文档：[Node.js中文档](http://nodejs.cn/api/)、[Set-Cookie响应头](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies#set-cookie%E5%93%8D%E5%BA%94%E5%A4%B4%E9%83%A8%E5%92%8Ccookie%E8%AF%B7%E6%B1%82%E5%A4%B4%E9%83%A8)\
> 内容：动态服务器

### 什么是动态服务器?

静态服务器(网页) VS 动态服务器(网页)\
判断依据\
是否请求了数据库。\
没有请求数据库，就是静态服务器。**请求了数据库，就是动态服务器。**

今天直接用json文件当作数据库。\
**步骤** [查看代码](https://github.com/lixueqin-4119/js-demo34/blob/master/test.js)\
**1.新建目录db和文件users.json**

```json
[
    {"id":1,"name":"李白","password":"xxx","age":18},
    {"id":2,"name":"李宇春","password":"yyy","age":20}
]
```

**2.读数据库**\
新建test.js用来测试数据\
fs用来读文件的，要想获取文件数据，首先要引入fs。

```js
const fs = require('fs') //引入fs
const usersString = fs.readFileSync('./db/users.json').toString()
console.log(usersString)
```

toString() 方法返回一个表示该对象的字符串。\
运行:`node test.js`

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ca5a12db38f4284ac059214e5ee57a1~tplv-k3u1fbpfcp-zoom-1.image)

把字符串变成数组

```js
const usersArray = JSON.parse(usersString)
console.log(typeof usersString)
console.log(usersString)
console.log(typeof usersArray)
console.log(usersArray)
console.log(usersArray instanceof Array)
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f30927fada37474c9755b5e90e93bd9b~tplv-k3u1fbpfcp-zoom-1.image)

**3.写数据库**

```js
const user3 = { id: 3, name: '李明', password: 'zzz', age: 25 }
usersArray.push(user3)
```

**存到数据库**。我们的数据库是文件，文件只能存字符串

```js
console.log(typeof user3) //object
const string = JSON.stringify(usersArray)
fs.writeFileSync('./db/users.json', string)
```

每运行一次**node test.js**就会写一次user3\
**知识点：**\
1.**JSON.stringify()序列化/字符串化**\
序列化就是将一个js对象或值转换为符合JSON语法字符串。\
**JSON.parse()反序列化/反字符串化**\
反序列化就是解析JSON字符串，构造由字符串描述的js值或对象。\
**2.json文件清空的话必须留个[]，不然会报错**\
3.UTF-8的编码怎么变成字符串？\
用`Buffer.concat(）`

```
例子:const string=Buffer.concat(array).toString()
```

**4.js数组使用JSON.stringify()和toString()的区别**


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b55a34e0d29a43bfb1767c7b22571633~tplv-k3u1fbpfcp-zoom-1.image)

toString()是将**数组**转化成字符串，因此不带[]\
JSON.stringify()将一个js对象或值(一般是对象)转换为符合JSON语法字符串,是带[]的。\
另外JSON.parse()是解析JSON字符串，构造由字符串描述的js值或对象(一般是对象)
**经验**\
**1.读数据时**需要先将json数据文件toString()转化为字符串，符合JSON语法的字符串,toString也会**反**UTF-8编码。然后再解析JSON字符串JSON.parse()构造由字符串描述的js值或对象。


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f24947ca7ae24555acb55c3a85a51d1a~tplv-k3u1fbpfcp-zoom-1.image)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c5ceb9c1f95246f1a59ca77bcca78bf1~tplv-k3u1fbpfcp-zoom-1.image)

`JSON.parse()//[object Object],[object Object]`\
**2.写数据时**，需要将数据JSON.stringify()将一个js对象或值转换为符合JSON语法字符串,是带[]的。因为我们的数据库是文件，文件只能存字符串。\
**总结**\
/db/users.json\
**结构：一个数组**

```json
[
  { id: 1, name: '李白', password: 'xxx', age: 18 },
  { id: 2, name: '李宇春', password: 'yyy', age: 20 }
]
```

**读users数据**\
先fs.readFileSync('./db/users.json).toString()\
然后JSON.parse一下(反序列化)，得到数组\
**写users数据**\
先JSON.stringify一下(序列化)，得到字符串\
然后fs.writeFileSync('./db/users.json',data)

### 目标1.[实现用户注册功能](https://github.com/lixueqin-4119/js-demo35/tree/master/public)

用户提交用户名和密码\
users.json里就新增了一行数据\
**思路**\
前端写一个form,让用户填写name和password\
前端监听submit事件\
前端发送post请求，数据位于请求体\
后端接收post请求\
后端获取请求体中的name和password\
后端存储数据

**步骤**\
首先，先质空users.json只留下[]\
1.新建注册页面register.html\
2.重置meta:vp能够在手机上运行\
3.引入jQuery:我使用的是[BootCDN](https://www.bootcdn.cn/jquery/)，选择/jquery.min.js，点击复制`<script>`标签。当然你也可以使用一些更高级的写法(比如npm)\
必须先引入jquery,然后才能使用。\
4.监听表单的submit事件\
表单有默认事件，第一步就是要阻止默认事件，不阻止的话会自动刷新页面\
5.拿到name和password后要**提交**下，怎么提交呢？\
**发一个ajax请求**

```html
<form id="registerForm">
  <div>
    <label>用户名<input type="text" name="name"></label>
  </div>
  <div>
    <label>密码<input type="password" name="password"></label>
  </div>
  <div>
    <button type="submit">注册</button>
  </div>
</form>
<script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
<script>
const $form = $('#registerForm')
  $form.on('submit', (e) => {
    e.preventDefault() //阻止默认事件
    const name = $form.find('input[name=name]').val()
    const password = $form.find('input[name=password]').val()
    console.log(name, password)
    $.ajax({
      url: '/register',
      data: JSON.stringify({
      name, //name:name 冗余可缩写,
      password ,
    })
  })
})
</script>
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ac4719a1393645dfabec35fef49816b6~tplv-k3u1fbpfcp-zoom-1.image)

**GET请求只能把数据放到url上面，改为POST请求。**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a8e06e82f06a42eca6c2a77cd245bf3a~tplv-k3u1fbpfcp-zoom-1.image)

**我们上传给服务器的是json字符串，那服务器是怎么知道的呢？**\
设置contentType

```js
$.ajax({
  method: 'POST',
  url: '/register',
  contentType: 'text/json;charset=UTF-8',
  data: JSON.stringify({ name,password })
})
```

### 获取post数据

**1.首先先处理/register.html请求**\
server.js

```js
if (path === '/register' && method === "POST") {
  response.setHeader('Content-Type', 'text/html;charset=UTF-8')
  response.end("你好")
} else { ...}
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b8a5ce6a33584d0797ebb7417b3e7341~tplv-k3u1fbpfcp-zoom-1.image)

**2.服务器如何获取name和password呢？**\
GET请求可以通过query拿到，POST请求怎么拿到？\
**思路**\
**(1)声明一个空数组用来装数据。** 因为数据有可能是**分段**上传的。\
就两条数据为什么这么点数据也要分段一个一个上传？\
因为不能确定你的数据长度是多少。也许你只传个name、password，但也许你也可能传个100兆的文件。100兆的文件上传是一点一点上传的，所以我们在获取时也要一条一条获取。\
**(2)监听请求上的数据(data上传事件)**\
server.js
```js
const array = []
request.on('data', (chunk) => {
  array.push(chunk) //先上传name再上传password
})
request.on('end', () => { //结束后做的事情
  console.log(array)
  const string=Buffer.concat(array).toString()
  console.log(string)
  response.end('很好')
})
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f5e1b17e3a7c496ea98f8712b3cd4242~tplv-k3u1fbpfcp-zoom-1.image)

**这就是UTF-8的编码，怎么变成字符串？**\
用`Buffer.concat(）`


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d90cb63b93c34a0189e3673c4bc6e0f6~tplv-k3u1fbpfcp-zoom-1.image)



别忘了JSON.parse()解析JSON字符串，构造由字符串描述的js值或对象(一般是对象)。\
server.js

```js
request.on('end', () => {
  console.log(array)
  const string = Buffer.concat(array).toString()
  console.log(string)
  const obj = JSON.parse(string) 
  console.log(obj.name)
  console.log(obj.password)
  response.end('很好')
})
```

js、node.js、浏览器都有全局方法JSON\
现在服务器就可以获取我的name和password了。\
**如何获取当前id呢？**\
思路：先读再写。先读出JSON数据库中id最大的，一般是最后一个。然后再写\
server.js

```js
if (path === '/register' && method === "POST") {
  const userArray = JSON.parse(fs.readFileSync('./db/users.json').toString())
  request.on('end', () => {
    const lastUser = userArray[userArray.length - 1]
    const newUser = {
      //id为最后一个用户的id+1
       id: lastUser ? lastUser.id + 1 : 1, //如果是空的就给个保底值id:1
       name: obj.name,
       password: obj.password
     }
      userArray.push(newUser)
     fs.writeFileSync('./db/users.json', JSON.stringify(userArray))
     response.end()
        })
  }
}
```

**注册成功后跳转首页**\
新建登录页sign_in.html\
register.html

```js
$.ajax({
  ...
}).then(() => {
  alert("注册成功")
  //window.open()
  location.href = '/sign_in.html'
  }, () => {})
})
```

then的第1个参数是成功，第2个参数是失败\
代码其实很简单，只需要弄懂http协议。**记住流程比代码更重要，最好把每一步的流程记下来！**

[CSDN用明文存储密码？意外还是故意？](https://www.zhihu.com/question/19982659)\
用md5加密是门外汉的行为

### 目标2.[实现用户登录功能](https://github.com/lixueqin-4119/js-demo38)

首页home.html,已登录用户可看到自己名\
登录页sign_in.html,提交用户名和密码\
输入的用户名和密码如果是匹配的，就自动跳转首页\
**sign_in.html思路**\
前端写一个form,让用户填写name和password\
前端监听submit事件\
前端发送post请求，数据位于请求体\
后端接收post请求\
后端获取请求体中的name和password\
后端读取数据，看是否有匹配的name和password\
如果匹配，后端应标记用户已登录，可是怎么标记？

**步骤**\
1.新建home.html\
如果已登录就显示:李雪芹 已登录。如果未登录就显示:未登录，请登录

```html
<p>你好,{{user.name}}</p> //html不能读取数据库，只能用占位
<p><a href="sign_in.html">登录</a></p>
```

**{{user.name}}怎么替换？**  单独写个home的server

```js
if (path = "/home.html") {
  //当前用户无法得知，写不出来
} else if (path === '/register' && method === "POST") {...}
```

**2.编写sign_in.html，登录思路与注册register.html思路基本一致**

```html
<form id="signInForm">
  <div>
    <label>用户名<input type="text" name="name"></label>
  </div>
  <div>
    <label>密码<input type="password" name="password"></label>
  </div>
  <div>
    <button type="submit">登录</button>
  </div>
</form>
<script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
  <script>
    const $form = $('#signInForm')
    $form.on('submit', (e) => {
      e.preventDefault()//阻止默认事件
      const name = $form.find('input[name=name]').val()
      const password = $form.find('input[name=password]').val()
      console.log(name, password)
      $.ajax({
        method: 'POST',
        url: '/sign_in',
        contentType: 'text/json;charset=UTF-8',
        data: JSON.stringify({ //把name、password对象变成字符串上传给服务器
          name,
          password
        })
    }).then(() => {
      alert("登录成功")
      location.href = '/sign_in.html'
      }, () => {})
    })
  </script>
```

**3.编写后端逻辑**

```js
server.js

if (path === '/sign_in' && method === 'POST') {
  const userArray = JSON.parse(fs.readFileSync('./db/users.json').toString())
  const array = []
  request.on('data', (chunk) => {
    array.push(chunk)
  })
  request.on('end', () => {
    const string = Buffer.concat(array).toString()
    const obj = JSON.parse(string) //js、node.js浏览器都有JSON，name、password
    //find()查找一个数组里有没有符合要求的
    const user = userArray.find((user) => user.name === obj.name && user.password === user.password)
      if(user === undefined){ //如果undefined说明用户还没有注册。设置400并提示不匹配
        response.statusCode = 400
        //response.end('name password 不匹配')
        response.setHeader('Content-Type', 'text/json;charset=UTF-8')
        response.end(`{"errorCode":4001}`)
      } else {
        response.statusCode = 200
        response.end()
      }
   })
} else if (path = "/home.html") { 
  //写不出来。读取当前用户，但不知道当前用户是谁。
  response.end('home')//直接返回一个home
} else if (path === '/register' && method === "POST") { ... }
} else { ... }             
```

直接拷贝地址(未通过身份验证)为undefined\
如果是undefined就说明user是空的，用户未注册。设置状态码400并提示报错的内容\
**一般请求字符串没什么用，最好是能返回个JSON然后给它个错误码。**

```js
response.setHeader('Content-Type', 'text/html;charset=UTF-8') //告诉浏览器我是JSON
response.end(`{"errorCode":4001}`)
```

**为什么4001就是"用户名密码不匹配"呢？**\
例子:4001表示是400的第1种错误。\
这个是你自己在文档里写的，你愿意写4001表示用户名密码不匹配，那4001就是。\
每个公司都有自己的errorCode编码，如果没有那就说明公司很挫。\
**未注册时：**


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9006e026770240008e1dc9b573e534a4~tplv-k3u1fbpfcp-zoom-1.image)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d47d0226f66b4f9987b3356c00443db3~tplv-k3u1fbpfcp-zoom-1.image)

**知识点**\
1.[🔍mdn array find](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/find)查找数组里有没有符合要求的。\
浏览器兼容性：IE不支持，node.js支持就行了。\
用法
```js
const array1 = [5, 12, 8, 130, 44];
const found = array1.find(element => element > 10);
console.log(found); // expected output: 12
```

find() 方法返回数组中满足提供的测试函数的第一个元素的值。否则返回 undefined。\
**例子**\
find里面写个函数,函数内容:满足条件就返回true,如果有这么个对象就返回函数的第1个元素值。

```js
const user=userArray.find((user) => user.name === obj.name && user.password === obj.password)
```

**2.400,4开头的响应statusCode表示都是错的**\
response.statusCode = 400\
**3.等待状态**

当没编写(path = "/home.html"的逻辑时，浏览器就会一直处于等待状态，必须写点内容。
没有response.end()也会导致一直处于"请求状态",记得end

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3466a8eefc71480caf8c787c80a5f662~tplv-k3u1fbpfcp-zoom-1.image)

总结：目标2受阻，目标太大了，目标应该尽量小。\
把目标调小点，你能想到的目标与你的能力不匹配，所以你需要有经验的人给你定目标。\
改一下，这次不要显示用户名了，标记用户已经登录。

### 目标3.[标记用户已登录](https://github.com/lixueqin-4119/js-demo39)

> 使用Cookie标记用户

```html
home.html
<p>{{loginStatus}}</p> 
<p><a href="sign_in.html">登录</a></p>
```

把loginStatus变成true(登录)或false(未登录)\
**如何识别一个用户是否登录？**  使用Cookie标记用户

#### Cookie

**定义**\
Cookie是服务器下发给浏览器的一段字符串\
浏览器必须保存这个Cookie(除非用户删除)\
之后发起**相同二级域名**请求(任何请求)时，浏览器必须附上Cookie\
**举例说明**\
假如你是公园检票员，你怎么知道谁能进谁不能？有票能进，没票不能进。\
Cookie就是门票\
有Cookie就是登录了，没Cookie就没登录\
那后端给浏览器下发一个Cookie不就完事了嘛\
代码

```js
response.setHeader('Set-Cookie','logined=1')//发票，1是true
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a58b26a50d4146c788fa99fd4591043d~tplv-k3u1fbpfcp-zoom-1.image)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c496e35d96164153a9a63e9c48838e96~tplv-k3u1fbpfcp-zoom-1.image)


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/13eb11c50e604649a910b6e2274c861a~tplv-k3u1fbpfcp-zoom-1.image)

**读票**\
🔍nodejs request cookie

```js
const cookie = request.headers['cookie']
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/db48e7efd52d45f18fdf8abf099ea588~tplv-k3u1fbpfcp-zoom-1.image)

**如果登录过了logined=1，没登录就undefined。**

```js
else if (path === '/home.html') {
  //console.log(request.headers)
  const cookie = request.headers['cookie']
  if (cookie === 'logined=1') {
    const homeHtml = fs.readFileSync('./public/home.html').toString()
    const string = homeHtml.replace('{{loginStatus}}', '已登陆')
    response.write(string)
    //console.log(cookie)
  } else {
    const homeHtml = fs.readFileSync('./public/home.html').toString()
    const string = homeHtml.replace('{{loginStatus}}', '未登陆')
    response.write(string)
  }
```

**知识点**\
1.[response.setHeader的各种用法](https://blog.csdn.net/junmoxi/article/details/76976692)\
2.[🔍mdn set cookie](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies#set-cookie%E5%93%8D%E5%BA%94%E5%A4%B4%E9%83%A8%E5%92%8Ccookie%E8%AF%B7%E6%B1%82%E5%A4%B4%E9%83%A8)\
语法:`Set-Cookie:<cookie-name>=<cookie-value>`\
使用:登录成功时请求头cookie

```js
response.setHeader('Set-Cookie','logined=1') //1是true，Set-Cookie:name=value
```

**Set-Cookie的其它参数:**\
Expires=<date>设置一个过期的时间，时间一过期，票就不会到你身上了。\
Max-Age最大时间，过了这段时间票也不会到你身上了。\
Domain域名、Path路径、Secure是否只在https时附带\
**HttpOnly**不准前端碰我的cookie,前端能改说明用户就能改,后端要坚决杜绝前端修改一定要加HttpOnly

```js
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```

3.request.headers、request.getHeader、request.getHeaders、request.getHeaderNames区别?

```js
request.headers['Cookie'];
request.getHeader("Accept-Encoding");获取单个请求头name对应的value值
request.getHeaders("Accept-Encoding")获取多个同名请求头对应的一组value值，因此返回枚举类型数据
request.getHeaderNames();获取请求头的所有name值,返回的数据也是一个枚举类型的数据，
将枚举中的元素依次遍历出来，根据name获取对应的value值，即可得到Http请求头的所有信息
```

4.**注意：fs.readFileSync读文件后最好toString,因为默认不是一个String**\
返回值可能是String，也可能是**Buffer类型**，默认不是string\
.toString()能确保是String类型，后面的JSON.parse才能正常处理\
5.cookie是否只能后端设置？\
不是，但是建议永远只在后端设置cookie，前端js千万不要碰cookie。\
cookie是否能伪造？

### 使用Cookie记录user id

**home.html怎么知道登录的是谁呢？**\
通过cookie里面存的id就知道了。\
思路:把logined改成user_id，user_id去拿这个user，user存在就把这个userName替换到页面中。\
这样home就知道了当前登录的用户是谁了

```js
if (path === '/sign_in' && method === 'POST') {
   request.on('end', () => {
      if(user === undefined){ 
        ... 
      }else{
        response.statusCode = 200
        response.setHeader('Set-Cookie', `user_id=${user.id}; HttpOnly`)
        response.end()
      }
   }
}
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9259a72af7ae4bdb833bbc24997ead21~tplv-k3u1fbpfcp-zoom-1.image)

**获取userId**

```js
else if (path === '/home.html') {
  const cookie = request.headers["cookie"]
  console.log(cookie)
  let userId
  try { //没有userId可能会报错
    userId = cookie.split(';').filter(s => s.indexOf('user_id=') >= 0)[0].split('=')[1]
} catch (error) { }
```

```js
解析：[ 'logined=1', ' user_id=2' ]
     cookie.split(';').filter(s => s.indexOf('user_id=') < 0) //[ 'logined=1' ]
     cookie.split(';').filter(s => s.indexOf('user_id=') >= 0)[0]因为是数组，所以取第0个，user_id=2
```

**现在只需要判断是否有userId即可**

```js
if (userId) {
  const userArray = JSON.parse(fs.readFileSync('./db/users.json'))
  const user = userArray.find(user => user.id.toString() === userId)
  let string
  if (user) {
    string = homeHtml.replace('{{loginStatus}}', '已登陆').replace('{{user.name}}', user.name)
  } else { }
    response.write(string)
  } else {
    const homeHtml = fs.readFileSync('./public/home.html').toString()
    const string = homeHtml.replace('{{loginStatus}}', '未登陆').replace('{{user.name}}', '')
    response.write(string)
  }
```

**user可能有也可能没有。**\
如果有user说明已登录，如果没有user说明没登录。\
如果已登录时将用户名告诉我，如果未登录把user.name给删掉

**显示用户名**\
home.html渲染前获取user信息\
如果有user,则将{{user.name}}替换成user.name\
如果无user,则显示登录按钮

```html
home.html
<p>
  {{user.name}}
  {{loginStatus}}
</p>
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/21a7e0a7cf1c4dc4a3ef5e42b2ed64d7~tplv-k3u1fbpfcp-zoom-1.image)

### 目标4.[防篡改user_id](https://github.com/lixueqin-4119/js-demo40/blob/master/server.js)

**Bug:用户可以篡改user_id**\
如果是HttpOnly只能用开发者工具改，如果没有HttpOnly那JS就能改。\
如何解决用户篡改cookie的问题？\
**思路1**，把信息隐藏在服务器(推荐)\
用随机数\
把用户信息放在服务器的X里，再给信息一个随机id\
把随机id发给浏览器\
后端下次读取到id时，通过x[id]获取用户信息\
想想为什么用户无法篡改id(因为id很长，而且随机)\
x是什么？是文件。不能用内存，因为断电内存就清空\
这个x又被叫做session(会话)\
**步骤**\
1.新建文件session.json(用什么形式无所谓)\
**session.json默认是个对象**\
如果我给浏览器这个数字(0.05239442708348885),意思就是我给了你这个对象(user_id是2)，表驱动编程。

```
{
  // "0.05239442708348885":{"user_id":2} 记得清空，内容是自动生成的无需手写。
}
```

**2.使用js自带的随机数,还要把随机数记到我的文件session里面**

```js
request.on('end', () => {
  if (user === undefined) {
    ...
  } else {
    response.statusCode = 200;
    const random = Math.random()
    const session = JSON.parse(fs.readFileSync('./session.json').toString())
    session[random] = { user_id: user.id }
    fs.writeFileSync('./session.json', JSON.stringify(session))
    response.setHeader("Set-Cookie", `session_id=${random}; HttpOnly`);
  }
  response.end()
});
```

把id给浏览器，浏览器并不知道id对应的啥，那我怎么知道呢？我的session.json记录了。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d16dde5039554d948163e9db771acb01~tplv-k3u1fbpfcp-zoom-1.image)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/87f282ba81e24a56ad25119520a22753~tplv-k3u1fbpfcp-zoom-1.image)

**3.home怎么获取user_id?**

```js
//let userId
let sessionId
try {
  sessionId=cookie.split(';').filter(s => s.indexOf('sessionId=') >= 0)[0].split('=')[1]
} catch (error) { }   
const session = JSON.parse(fs.readFileSync('./session.json').toString())
if (sessionId && session[sessionId]) {
  const userId = session[sessionId].user_id
  const userArray = JSON.parse(fs.readFileSync("./db/users.json"));
  const user = userArray.find(user => user.id === userId);
  const homeHtml = fs.readFileSync("./public/home.html").toString();
  let string = ''
  if (user) {
    string = homeHtml.replace("{{loginStatus}}", "已登录")
    .replace('{{user.name}}', user.name)
  }
  response.write(string);
  } else {
    const homeHtml = fs.readFileSync("./public/home.html").toString();
    const string = homeHtml.replace("{{loginStatus}}", "未登录")
    .replace('{{user.name}}', '')
    response.write(string);
    }
  response.end()     
```

**session的时效性:**  数据通过文件操作时可用性很强，因为数据是在服务器上的，Session保存在服务器的文件中。可以随时删掉有问题的sesstionId。服务器一般会将Session id放到Cookie中，发放给浏览器。\
**加密**数据是在用户那边的，无法修改。
  
\    
**思路2**，加密\
将user_id加密发送给前端，后端读取user_id时解密，此法可行但有安全漏洞。\
漏洞：加密后的内容可以无限期使用**没办法注销掉**，如果被窃听者拿到了加密的user_id，他就可以一直用这个加密的user_id复制到他的浏览器上，然后不停的模拟你登录。\
解决办法：JWT

\
**注销功能一般如何实现?**\
安全起见，不能用JS删Cookie，应该使用HttpOnly的Cookie，然后JS发请求让服务器删 Cookie\
安全起见，除了删除浏览器端的Cookie，还需要把对应的Session数据删掉
