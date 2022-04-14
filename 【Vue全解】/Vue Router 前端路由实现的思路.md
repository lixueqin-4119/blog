# Vue Router 前端路由实现的思路
**路由器(面试题)**

**一.前端路由是什么？**

**二.hash模式? history模式? memory模式?**

  三.[VueRouter源码](https://github.com/vuejs/vue-router/blob/dev/dist/vue-router.js)

## 一.前端路由是什么？
**路由是什么？**

路由器,如果你家里有1个以上的人要上网就需要路由器。

你要上网就需要连接到路由器上，路由器要连接到电信/联通公司，电信公司会出售一些网络IP或端口，百度就会购买这些IP(大概会购买几千个IP)作为它的服务器，腾讯也会购买它的IP，抖音同样也要购买它的IP。

你连路由器，路由器看你想连哪里，比如你想连百度，它就会把包发给百度。当你打开抖音时，它就会把你这个信号发到抖音。**只要满足一对多的关系就叫分发，** 分别发送到各个地方。**路由就是分发请求，路由器就是分发请求的东西。**

[实例1:](https://codesandbox.io/s/sweet-northcutt-v6vqtm?file=/src/index.js:656-700)实现路由router

需求:根据用户URL后接的hash是#x 比如`http://127.0.0.1/#1`以此来判断用户想去的界面，界面用div代表。#1代表界面1`Div1`
```js
index.html
<a href="#1">go to 1</a>
<a href="#2">go to 2</a>
<a href="#3">go to 3</a>
<a href="#4">go to 4</a>
<div id="app"></div>
<div id="div1" style="display: none;">1</div>
<div id="div2" style="display: none;">2</div>
<div id="div3" style="display: none;">3</div>
<div id="div4" style="display: none;">4</div>
<div id="div404" style="display: none;">你要找的内容被狗吃了</div>

index.js
//console.log(window.location.hash) //#1
let number = window.location.hash.substr(1);
number = number || 1
//console.log(number); //1
let div = document.querySelector(`#div${number}`);
//console.log(div);
let app = document.querySelector("#app");
if (div) {
  div.style.display = "block";
} else {
  div = document.querySelector("#div404");
  div.style.display = "block";
}
if (app) app.appendChild(div);
window.addEventListener("hashchange", () => {
  console.log("hash变了");
  const number = window.location.hash.substr(1);
  let div = document.querySelector(`#div${number}`);
  if (div) {
    div.style.display = "block";
  } else {
    div = document.querySelector("#div404");
    div.style.display = "block";
  }
  const app = document.querySelector("#app");
  app.children[0].style.display = "none"; //先把旧的设为不可见
  document.body.appendChild(app.children[0]); //再将旧的放回body里
  app.appendChild(div);
});
```
**细节**

**1.在URL后添加`#1`，用`window.location.hash`获取添加的内容。**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ad595be04d0a4609b04789646be8ad8d~tplv-k3u1fbpfcp-zoom-1.image)

**2.实现切换页面:监听URL的变化**

🔍[hashchange MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/HashChangeEvent#%E5%9F%BA%E6%9C%AC%E7%A4%BA%E4%BE%8B)

**优化代码**
```js
function route() {
  let number = window.location.hash.substr(1);
  let app = document.querySelector("#app"); 
  number = number || 1;
  let div = document.querySelector(`#div${number}`);
  if (!div) {
    div = document.querySelector("#div404");
  }
  div.style.display = "block"; 
  
  if (app.children.length > 0) { 
    app.children[0].style.display = "none";
    document.body.appendChild(app.children[0]);
  }
  app.appendChild(div);
}

route();
window.addEventListener("hashchange", () => {
  console.log("hash变了");
  route();
});
```
#### 路由包含的概念
**1.路由**

**2.hash**:就是#1

**3.默认路由**
```js
number = number || 1 //如果number不存在就给个保底址1
```
**4.保底路由**:404路由
```js
if(div){
  div.style.display="block"
}else{
  div=document.querySelector("#div404")
  div.style.display="block"
}
```
**概念5:嵌套路由**

**需求:**`https://rllh2y.codesandbox.io/#1/3` 表示要跳转到子路由1.3
接收一个参数container，表示要替换的根。要route时说一下，要route到哪个目录下，加一个参数container，表示根在哪里。

**思路:** 用/分成2部分，比如将1/1分成[1,1]。第1个1用第一层路由去渲染，得到div1。第2个1用第二层路由去渲染，得到div11，**路由可以再分层**。

### 路由表
自定义hash与div的关系
```js
const div1 = document.createElement("div"); //JS创建div
div1.innerHTML = "1";
const div2 = document.createElement("div");
div2.innerHTML = "2";
const div3 = document.createElement("div");
div3.innerHTML = "3";
const div4 = document.createElement("div");
div4.innerHTML = "4";
const routeTable = {
  "1": div1,
  "2": div2,
  "3": div3,
  "4": div4
};
function route() {
  let number = window.location.hash.substr(1);
  let app = document.querySelector("#app");
  number = number || 1;
  let div = routeTable[number.toString()]; //得到'1'
  if (!div) {
    div = document.querySelector("#div404");
  }
  div.style.display = "block";
  app.innerHTML = ""; //置空
  app.appendChild(div);
}
route();
window.addEventListener("hashchange", () => {
  console.log("hash变了");
  route();
});
```

## 二.hash模式? history模式? memory模式?
路由有3种模式[hash模式](https://codesandbox.io/s/x3nxq950ko)、[history模式](https://codesandbox.io/s/oqjvqm6w05)、[memory模式](https://codesandbox.io/s/936269l69o)

目前是通过hash`#1`,能不能去掉`#`？比如`https://rllh2y.codesandbox.io/1`

**1.hash** 

**任何情况下你都可以用hash做前端路由，但是SEO不友好。**

**缺点:SEO不友好。** 服务器收不到hash，因为浏览器是不会把#之后的内容发送给服务器。所以不管你是访问`https://www.baidu.com/#1`还是`https://www.baidu.com/#frank`对百度来说都是同一个内容，永远只展示默认路由`https://www.baidu.com`。根本没法收录你的内容，这就叫SEO不友好。
但也不是绝对的,google的**hashbang**可以让google能识别#,需要你的服务器做些配置:加`!`,这样google就会认为是不同的页面。例如`https://www.baidu.com/#!1`

**2.history**

**条件1**.后端将所有前端路由都渲染同一个页面(不能是404)

**条件2**.不需要兼容IE8以下。

只要后端能实现需求:不管你请求`https://www.baidu.com/#1`还是`https://www.baidu.com/#frank`都得到同一个页面`https://www.baidu.com`，而且不需要兼容IE8以下，那你就可以使用history模式。

### history实例
`window.location.pathname`**默认会有`/`**
```js
index.html
<a class="link" href="/1">go to 1</a>
<a class="link" href="/2">go to 2</a>
<a class="link" href="/3">go to 3</a>
<a class="link" href="/4">go to 4</a>
<div id="app"></div>
<div id="div404" style="display: none;">你要找的内容被狗吃了</div>

index.js
const app = document.querySelector("#app");
const div1 = document.createElement("div");
div1.innerHTML = "1";
const div2 = document.createElement("div");
div2.innerHTML = "2";
const div3 = document.createElement("div");
div3.innerHTML = "3";
const div4 = document.createElement("div");
div4.innerHTML = "4";
const routeTable = {
  "/1": div1, //将路径名改为/开头
  "/2": div2,
  "/3": div3,
  "/4": div4
};
function route(container) {
  let number = window.location.pathname; //获取路径名，路径名一定会以/开头
  console.log("number: " + number);//number: / 

  if (number === "/") {
    number = "/1";
  }
  let div = routeTable[number.toString()];
  if (!div) {
    div = document.querySelector("#div404");
  }
  div.style.display = "block";
  container.innerHTML = "";
  container.appendChild(div);
}

const allA = document.querySelectorAll("a.link");//获取所有a标签
//遍历，每次点击时篡改它的行为:阻止所有a标签的默认动作,不要自动刷新
for (let a of allA) { 
  a.addEventListener("click", e => {
    e.preventDefault();
    const href = a.getAttribute("href");//每次点击时获取href值
    window.history.pushState(null, `page ${href}`, href);//在不刷新页面情况下变更URL
    //通知
    onStateChange(href);//把最新的路径给他
  });
}
route(app);
function onStateChange() {
  console.log("state 变了");
  route(app);
}
```
**细节**

**1.每次点都要重新渲染，体验感很差，于是浏览器出了一个新的API `history`**

2.如果是通过`document.querySelectorAll`得到的allA是不能直接**map**的，可以用这种方法`for(let a of allA){}`遍历。

**3.在不刷新页面的情况下，改URL**

🔍[history mdn](https://developer.mozilla.org/zh-CN/docs/Web/API/History/replaceState)
```js
用法
var stateObj = { foo: "bar" };
history.pushState(stateObj, "", "bar.html");
```
```js
项目实例
const href = a.getAttribute("href");//每次点击时获取href值
window.history.pushState(null, `page ${href}`, href);//改URL
```
**4.监听state变化**

点击链接时跳转，怎么监听变化了呢？

在push时进行**通知**:就是调用一个函数`onStateChange(href)`
知道它变了后就可以render了。

**5.history模式为什么需要所有的页面指向同一个页面呢？**

因为用户喜欢刷新。比如说当前页面是`/4`，点击刷新，如果4没有定位到该页面就会到404页面，那这可不行。

#### memory模式(一般不用)
memory模式既不用hash也不用history，用一个对象来存储你的东西。

hash、history模式都是把数据存在URL，URL一变，页面就要变。memory是存到localStorage里。
```js
const app = document.querySelector("#app");
const div1 = document.createElement("div");
div1.innerHTML = "1";
const div2 = document.createElement("div");
div2.innerHTML = "2";
const div3 = document.createElement("div");
div3.innerHTML = "3";
const div4 = document.createElement("div");
div4.innerHTML = "4";
const routeTable = {
  "/1": div1,
  "/2": div2,
  "/3": div3,
  "/4": div4
};
function route(container) {
  let number = window.localStorage.getItem("xxx");//把所有路径信息都存到xxx里面
  if (!number) {
    number = "/1";
  }
  let div = routeTable[number.toString()];
  if (!div) {
    div = document.querySelector("#div404");
  }
  div.style.display = "block";
  container.innerHTML = "";
  container.appendChild(div);
}
const allA = document.querySelectorAll("a.link");
for (let a of allA) {
  a.addEventListener("click", e => {
    e.preventDefault();
    const href = a.getAttribute("href");
    window.localStorage.setItem("xxx", href);//路径信息都存到xxx里面
    onStateChange(href);
  });
}
route(app);
function onStateChange() {
  console.log("state 变了");
  route(app);
}
```
把路径存在用户看不见的地方，没有路径
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1a3c8c7a8a344544be6c3dfdd48237cc~tplv-k3u1fbpfcp-zoom-1.image)


**memory优点:** 这种模式适合非浏览器，比如你想在APP里想做路由，APP是没有路径的，不是网页就没有路径。

**memory适用于:react native**和**weex**(Vue手机端的方案)，有市场但前端一般不用。

### **hash、history、memory三者区别**

`hash、history`都是把路径存在url上，只不过一个是URL的哈希，一个是URL的路径。memory不用URL，前端一般放在localStorage，移动端APP放在本地数据库里面。

memory缺点是没有URL,只对单机有效，是单机版的路由，分享无效。`hash、history`可以记录你的信息，所以你可以分享你的URL。你的页面显示的是几，用户看到的就是几，所以是可分享的路由。

