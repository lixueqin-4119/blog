# 异步与Promise(必考)

> AJAX(Async JavaScript And XML)\
> 内容:Ajax异步编程在js里的统一解决方案(js异步编程模型) **Promise**

### 什么是异步？什么是同步？

**同步：能直接拿到结果**\
比如你在医院挂号，拿到号才会离开窗口。\
同步任务可能消耗10ms,也可能需要3s\
总之不拿到结果你是不会离开的

**异步：不能直接拿到结果**\
比如你在餐厅门口等位，拿到号可以去逛街\
什么时候才能真正吃饭呢？\
你可以每10min去餐厅问一下(轮询)\
你也可以扫码用微信接收通知(回调)\
异步通常指"异步加回调"\
**举例**\
**1.以ajax为例**\
request.send()之后，并不能直接得到response\
不信console.log(request.response)试试\
必须等到readState变为4后，**浏览器回头调用**\
request.onreadystatechange函数\
我们才能得到request.response\
这跟餐厅给你发送微信提醒的过程是类似的

**补充：在js中发一个网络请求并得到响应大概要几百ms~1/2s**


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8c5f8d86d4b44ecfba91713d146c1099~tplv-k3u1fbpfcp-zoom-1.image)


```js
getJSON.onclick = () => {
  ...
  request.send()
  console.log(request.response) //不能直接得到response
  setTimeout(() => { //2s后得到response
    console.log(request.response)
  }, 2000)
}
下载完成后浏览器会回调request.onreadystatechange函数
所以上面的代码等价于
 request.onreadystatechange = () => {
   if (request.readyState === 4 && request.status == 200) {
       console.log(request.response)
       ...
   }
打印出response后就是3s后
```

**2.回调callback**\
你写给自己用的函数，不是回调\
**你写给别人用的函数，就是回调**\
request.onreadystatechange就是**我写给浏览器调用**的\
意思就是浏览器回头调一下这个函数\
「回头」也有「将来」的意思，如「我回头请你吃饭」\
**举例**\
**1.把函数1给另一个函数2**

```js
function f1(){}
function f2(fn){
  fn()
}
f2(f1)
```

我调用f1没有？没有\
我把f1传给f2了没有？传了\
f2调用f1了没有？调了\
那么f1是不是我写给f2调用的函数？是，所以f1是回调\
没有调用、传给别人了、别人调用了\
**补充**

```js
//request.setCallback(onreadystatechange)
request.onreadystatechange
```

是不是参数这个问题并不大，直接写一样的效果\
不过一般不推荐放到对象上，最好作为参数传给它，防止别人不知道

**抬杠1，如果我传给f2的参数不是函数呢？**\
会报错:fn不是一个函数。看到报错你不就知错了\
**抬杠2**

```js
function f1(x){
  console,log(x)
}
function f2(fn){
  fn('你好')
}
f2(f1)
```

f1怎么会有一个x参数\
fn('你好')中的fn就是f1对吧\
fn('你好')中的'你好'会被赋值给参数x对吧\
所以x就是'你好'。x表示第1个参数而已

### 异步和回调的关系

**回调就是我把一个函数传给你 或者传到全局函数request上**\
**关联**\
异步任务需要在得到结果时通知js来拿结果\
怎么通知呢？\
可以让js留一个函数地址(电话号码)给浏览器\
异步任务完成时浏览器调用该函数地址即可(拨打电话)\
同时把结果作为参数传给该函数(电话里说可以来吃了)\
这个函数是我写给浏览器调用的，所以是回调函数\
**区别**\
异步任务常常用到回调函数来通知结果，但不一定非要用回调。也可以用轮循\
回调函数也不一定只用在异步任务里，也可以用到同步任务里\
`array.forEach(n=>console.log(n))`就是同步回调\
**array里有多少个元素forEach就会调用函数n多少次**

### 判断同步异步

**怎么区分一个函数是同步还是异步？**\
根据特征或文档\
**如果一个函数的返回值处于这3个东西内部，那么这个函数就是异步函数**\
setTimeout\
Ajax(即XMLHttpRequest)\
AddEventListener\
等下，我听说Ajax可以设置为同步的\
傻X前端才把Ajax设置为同步的，这样会使请求期间页面卡住。

**例子:异步1个结果的处理**\
1s后返回1～6的随机数

```js
function 摇骰子(){ 
  setTimeout(()=>{ 
    return parseInt(Math.random() * 6 ) + 1 
  },1000)
  //return undefined
}

const n=摇骰子()
console.log(n) //undefined
```

摇骰子()没有写return,那就是return undefined\
箭头函数里有return，返回真正的结果\
**注意这2个return属于不同的函数**\
所以这是一个异步函数/任务

**那怎么才能拿到异步结果(1～6的随机数)？**\
可以用回调。写个函数，然后把函数地址给它

```js
function f1(x){console.log(x)}
摇骰子(f1)
```

然后我要求**摇骰子函数**得到结果后**把结果作为参数**传给f1

```js
function 摇骰子(fn){ 
  setTimeout(()=>{ 
    fn(parseInt(Math.random() * 6 ) + 1) //得到结果后传给fn
  },1000)
}
```

将结果传给fn，这时候就能输出结果了。

简化为箭头函数。f1声明后只用了一次，所以可以删掉f1\
**优化技巧：**  函数声明后只用了一次时，可以简化为匿名函数

```js
function f1(x){console.log(x)}
摇骰子(f1)
改为
摇骰子(x=>{
  console.log(x)
})
再简化为
摇骰子(console.log)
```

**如果参数个数不一致就不能这样简化，有个面试题**

```js
摇骰子(x,y=>{
  console.log(x)
})
```

**面试题**

```js
const array=['1','2','3'].map(parseInt)
console.log(array)
输出结果:[1, NaN, NaN]
还原
const array=['1','2','3'].map((item,i,arr)=>{
  return parseInt(item,i,arr)
  //parseInt('1',0,arr)=>1
  //parseInt('2',1,arr)=>NAN 一进制没有2所以得到NAN
  //parseInt('3',2,arr)=>NAN 二进制没有3所以得到NAN
})
console.log(array)
输出结果:[1, NaN, NaN]
```

[map接收3个参数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map#parameters)\
把2作为1进制数进行解析，2进制数只有0和1，1进制数只有0，10进制只有0～9。因为一进制没有2所以得到的结果不是一个数字NAN。

正确简化

```js
const array=['1','2','3'].map((item,i,arr)=>{
  return parseInt(item)
})
console.log(array)
输出结果:[1, 2, 3]
简写
const array=['1','2','3'].map((item=>parseInt(item))
console.log(array)
```

**总结**\
异步任务不能拿到结果，于是我们传一个回调给异步任务\
异步任务完成时调用回调，调用的时候把结果作为参数\
异步为什么会用到回调？为了拿到不能直接拿到的结果，必须用回调/轮询

### [Promise的用法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

**Promise是前端解决异步问题的统一方案，异步2个结果(成功、失败)的处理**\
**如果异步任务有2个结果成功和失败，怎么办？**\
方法1:回调接受两个参数(node.js就是用的这个方案,接收两个参数)

```js
fs.readFile('./1.txt',(error,data)=>{//2个参数:失败的错误，成功的结果
  if(error){console.log('失败'); return}
  console.log(data.toString()) //成功
)
```

方法2:搞两个回调

```js
ajax('Get','./1.json',data=>{},error=>{})
//前面函数是成功回调，后面函数是失败回调
或者
ajax('Get','./1.json',{
  success:()=>{},fail:()=>{}
})
//接受一个对象，对象有两个 key 表示成功和失败
```

不管方法1还是方法2都有问题，有3个缺点(**回调的3个问题**)\
1.不规范没有成文的规定。名称五花八门,有人用success+error,有人用success+fail或者done+fail\
2.容易出现**回调地狱**，代码变的看不懂\
3.很难进行**错误处理**\
回调地狱举例

```js
getUser(user=>{
  getGroups(user,(groups)=>{
    groups.forEach((g)=>{
      g.filter(x=>x.ownerId===user.id).forEach(x=>console.log(x))
    })
  })
})
这还只是四层回调，你能想象20层回调吗？
```

**有什么办法能解决这3个问题？用promise**\
规范回调的名字或顺序\
拒绝回调地狱，让代码可读性更强\
很方便地捕获错误

promise思想是在1976年提出的，后来被前端抄袭的。\
**promise是什么？1976年的一种设计模式(写得好的代码取了个名)**\
以ajax的封装为例，解释promise的用法\
示例：写一个回调的封装

```js
//1.ajax的定义
ajax=(method,url,options)=>{
  const {success,fail}=options //析构赋值，从options里拿到success和fail这2个回调函数
  const request=new XMLHttpRequest() //全局变量request
  request.open(method,url)
  request.onreadystatechange = () => {
    if (request.readyState === 4) {
    //成功就调用success,失败就调用fail
      if(request.status < 400){
      //一般来说不会出现300的，300不应该出现在这里，300会再发一个请求
        success.call(null,request.response)
      }else if(request.status >= 400){
        fail.call(null,request,request.status)
      }
    }
  }    
    request.send()  
}
//2.ajax的使用
ajax('Get','/xxx',{
  success(response){},fail:(request,status)=>{}
  //ES6语法：左边是function缩写，右边是箭头函数
})
```

**ES 6语法：析构赋值**

```js
const {success,fail}=options //析构赋值，从options里拿到success和fail这2个回调函数
  //等价于
  //const success=options.success
  //const fail=options.fail
```

Promise说这代码太傻了，我们改成promise写法

```js
ajax('Get','/xxx',{
  success(response){},fail:(request,status)=>{}
})
//上面用到了两个回调，还使用了success和fail.
//改成promise写法
ajax('Get','/xxx').then((response)=>{},(request)=>{})
```

虽然也是回调，但是不需要记success和fail了\
then的第1个参数就是success,第2个参数就是fail\
**promise规定了成功失败都只能返回一个参数**\
**ajax()返回了个啥？**\
返回了一个含有.then()方法的对象\
**那如何得到这个含有.then()的对象呢？**\
那就要改造ajax的源码了

```js
ajax=(method,url,options)=>{
  return new Promise((resolve,reject)=>{ //1处
    const {success,fail}=options 
    const request=new XMLHttpRequest()
    request.open(method,url)
    request.onreadystatechange = () => {
      if (request.readyState === 4) {
        if(request.status < 400){
          resolve.call(null,request.response) //2处
        }else if(request.status >= 400){
          reject.call(null,request,request)   //3处
        }
      }
    }    
      request.send()  
  })
}
```

符合promise规范的ajax调用，让回调的异步函数变成promise的异步函数\
**步骤**\
**第1步(定义时)，**  加上return new Promise((resolve,reject)=>{...})\
任务成功调resolve(result)，失败调reject(error)\
resolve和reject会再去调用成功和失败函数\
**第2步(使用时)，**  使用.then(success,fail)传入成功和失败函数\
**如何使用Promise?**\
背下来5个词`return new promise((resolve,reject)=>{...}`

### 总结

**背下来：** `return new promise((resolve,reject)=>{成功时调用resolve，失败时调用reject}`\
//return 构造函数(参数)\
**关于异步**\
1.如果 JS 不能直接拿到一个函数的结果，可以先去执行别的代码，等结果到了再取结果，这就是异步\
2.异步的结果可以通过轮询获取，轮询就是定时去询问结果拿到了没有\
3.异步的结果可以通过回调获取，一般来说结果会被作为回调的第一个参数\
4.异步的好处是可以把用来等待的时间拿去做别的事情\
**关于回调**\
1.满足某些条件的函数才被称为回调，比如我写一个函数 A，传给另一个函数 B 调用，那么函数 A 就是回调\
2.回调可以用于同步任务，不一定非要用于异步任务\
3.有的时候回调还可以传给一个对象，如 request.onreadystatechange，等待浏览器来调用\
**关于Promise**\
1.Promise不是前端发明的，是目前前端解决异步问题的统一方案。\
2.window.Promise 是一个全局函数，可以用来构造 Promise 对象\
3.使用return new Promise((resolve, reject)=> {})就可以构造一个Promise对象\
构造出来的Promise对象含有一个 .then()函数属性

**补充：注意Promise不可取消。**  为了解决取消promise这个问题,axios自己想了个办法就是用CancleToken。\
原理就是将请求编号，假设发了10个promise，其中9个不要了。就把id为1～9的promise对应的ajax请求终止。promise还是做，只不过ajax不要了。axios取消的不是promise而是请求。`axios.CancleToken`

**关于return new Promise((resolve, reject)=>{...})中的resolve和reject**\
1.resolve 和 reject 可以改成任何其他名字，不影响使用，但一般就叫这两个名字。\
2.任务成功的时候调用resolve，失败的时候调用reject\
3.resolve和reject都只接收一个数据，而且this是空，不应该用this\
4.resolve和reject并不是 .then(success, fail)里面的success和fail，resolve会去调用 success，reject会去调用fail

**我们封装的ajax的缺点**\
1.post无法上传数据 `request.send(这里可以上传数据)`\
2.不能设置请求头 `request.setRequestHeader(key,value)`\
**怎么解决呢？**\
**1.使用axios (推荐)**\
2.使用[jQuery.ajax](https://www.jquery123.com/jQuery.ajax/)\
我们需要掌握jQuery.ajax吗？不用，写篇博客罗列下功能，就可以忘掉jQuery了\
3.自己花时间把ajax写到完美

### axios库

[中文文档](http://www.axios-js.com/zh-cn/docs/) 、[英文原文](https://kapeli.com/cheat_sheets/Axios.docset/Contents/Resources/Documents/index)

> Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。\
> 目前最新的Ajax库，Vue、React都在用它。\
> 这个库比jQuery逼格高，**现在专业前端都在用axios**，它抄袭了jQuery的封装思路。\
> 推荐通过写博客来学习一个库[axios速查表](https://juejin.cn/post/7063572834143961125)

代码示例

```js
axios.get('/5.json')
  .then(response=>
    console.log(response)
)
```

**axios**\
这是一个专门用于操作 AJAX 的库\
axios.get('/xxx') 返回一个 Promise 对象\
axios.get('/xxx').then(s, f) 在请求成功的时候调用 s，失败的使用调用 f

**axios高级用法**\
**1.JSON自动处理**\
axios如何发现响应的Content-Type是json\
就会自动调用JSON.parse\
所以说正确设置Content-Type是好习惯\
**2.请求拦截器**

> 你可以在所有请求里加些东西，比如加查询参数

只要在这个函数里对config做一些修改，那你发的请求就会全部被你自己篡改。\
比如说，我们要在请求里统一加个参数，不管这个请求是怎样的都要加一个参数。\
那么就可以加个拦截器。

```js
// Add a request interceptor
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });
```

**3.响应拦截器**

> 你可以在所有响应里加些东西，甚至改内容

function(response){ return response;}得到原始响应数据。如果你对数据不满意，可以对它进行修改。这样你就可以对它进行一些测试。

```js
// Add a response interceptor
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });
```

**4.可以生成不同实例(对象)**\
不同实例可以设置不同的配置，用于复杂场景

instance就是axios的复制品

```js
var instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```

**封装！封装！封装！**\
初级程序员学习API(包括Vue/React的API)\
中级程序员学习如何封装\
高级程序员造轮子

**如何使用Axios? 设置下引用即可使用**\
打开[BootCDN](https://www.bootcdn.cn/)搜索axios-> 选择axios.min.js，点击复制`<script>`标签\


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/37ff93bdfd44420db6a5146c823e16fa~tplv-k3u1fbpfcp-zoom-1.image)

[查看示例](http://js.jirengu.com/siwudecedi/1/edit?html,output)\
console.log(axios)控制台打印出了函数，说明axios存在，这样就引用成功了(引用脚本)。\
axios.get('/xxx')请求发送成功了(由于没写内容所以是404)\


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1302848a6351423b89d466f9d39826ba~tplv-k3u1fbpfcp-zoom-1.image)

**面试题**\
为什么我们要用promise啊，能不能讲讲原因？\
思路:promise能解决回调的3个问题\
1.规范回调的名字或顺序\
2.拒绝回调地狱，让代码可读性更强\
3.很方便地捕获错误
