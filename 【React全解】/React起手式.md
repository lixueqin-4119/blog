
# React起手式
> **目录**\
> 1.如何引入React\
> 2.函数的本质--延迟   如何使用React里的函数？\
> 3.JSX的用法\
> 4.条件判断与循环

### 引入React(3种方法)
**1' CDN引入**(不推荐)
> 需要引入2个库：先引react 后引react-dom,注意顺序

```js
<body>
  <div id="app"></div>
  <script src="https://cdn.bootcss.com/react/16.10.2/umd/react.development.js"></script>
  <script src="https://cdn.bootcss.com/react-dom/16.10.2/umd/react-dom.development.js"></script>

  <script src="src/index.js"></script> //引入之后就可以使用你自己的js了
</body>
```
**测试是否引用成功**\
本地运行`parcel src/index.html`\
⚠️注意:CDN引入的都会变成全局变量window

```js
console.log(window.React); //打印出一个对象
console.log(window.React.createElement); //打印出一个函数
console.log(window.ReactDOM); //对象
console.log(window.ReactDOM.render); //函数
```
这种方法没人用，如果想升级就需要重新复制版本号，麻烦。

**其它**

1.cjs和umd的区别？(**优先用umd**)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/94fd41b162c347ae9f8ade72f8b7a543~tplv-k3u1fbpfcp-zoom-1.image)

cjs全称CommonJS,是Node.js支持的模块规范。

模块规范指"如何声明一个模块",平时我们是`const x = {...}`然后导出`export default x`就声明一个可以被别人导入的模块。Node.js有自己的模块定义方法，这个方法就叫cjs。

umd是统一模块定义，兼容各种模块规范(含浏览器)
理论上优先使用umd,同时兼容Node.js上的模块和浏览器模块。
最新的模块规范是使用`import`和`export`关键字。

**2' 通过webpack引入React**(不推荐)

这种方法需要配置webpack，也麻烦，不用。

除webpack外，rollup、parcel也支持上面写法。这2个工具都支持，把浏览器不支持的JS变成浏览器支持的JS。

**3' 用命令行`create-react-app` (推荐)**
> `create-react-app`跟`vue-cli`是同一个级别的东西。\
> 新手用`create-react-app`,老手用`webpack/rollup`

🔍[create react app](https://github.com/facebook/create-react-app#quick-overview)  
```js
//打开终端工具iTerm
yarn global add create-react-app 
cd ~/Desktop 
create-react-app react-demo //新建项目react-demo
cd react-demo
yarn start //启动服务
中断control+c，用VSCode打开项目
yarn start //启动服务
```
**测试是否引用成功:**`console.log(React)`


**补充**\
1.`npx`在windows上兼容性不好，用`yarn`\
2.启动失败的话就运行`yarn run eject`让配置文件可见，然后再`yarn start`

### React初体验

例子:用React实现+1
```js
//用codesandbox
console.log(window.React);
console.log(window.React.createElement);
console.log(window.ReactDOM);
console.log(window.ReactDOM.render);

const React = window.React;
const ReactDOM = window.ReactDOM;

let n = 0;
const App = () => React.createElement("div", null, [
    n,
    React.createElement(
      "button",
      {
        onClick: () => {
          n += 1;
          console.log(n); //这一句是精髓
          ReactDOM.render(App(), document.querySelector("#app")); // 为什么还是不能重新渲染
        }
      },
      "+1"
    )
  ]);

ReactDOM.render(App(), document.querySelector("#app"));
```
**详解**

**1.用React创建div**

```js
const App = React.createElement("div", {className:'red'},n}

意思是创建:
<div class='red'> n </div>
```
JS里设置`className`就是在设置`class`，class是关键字在以前不能用，现在能用了，但是延续了这个习惯。

**2.CDN引入的都会变成全局变量window，测试是否引用成功**
`console.log(window.React)`

**3.实现`+1`功能**

**React不会监听自动刷新页面，只能自己去刷新:**

方法:需要刷新页面时再次执行render`ReactDOM.render(App, document.querySelector("#app"))`

还是不能实现`+1`，失败是因为`App=React.createElement(...)`只执行了一次。

**如何让它重新执行，以获取n的最新值？**

方法:通过把App变成**函数**解决。记不记得6个6，函数可以获取最新值。
```js
App = React.createElement(...)
改为App = ()=> React.createElement(...)
//App等于一个函数，这个函数执行之后才会返回这个对象。
//App必须执行了才会去取n的值。
```
所以第1次render时要render返回的这个div,`ReactDOM.render(App(),root)`,第2次也要`App()`。函数运行时会重新读n的值，因此会得到最新的n。

**面试题**
```js
let i
for(i=0;i<6;i++){
  setTimeout(()=>{console.log(i)},1000)
}
1s后会打印6个6，因为函数执行时遇到外部变量会读取最新值。
```
**解决方法:打印0～5**
```js
1' for(i=0;i<6;i++){ 
     let j=i
     setTimeout(()=>{console.log(i)},1000)
   }
 //let的作用域在{}内,每次进来时都是一个新的作用域，所以j每次的取值都是新的，所以j的取值是0～5。
   
2' for(let i=0;i<6;i++){ 
     setTimeout(()=>{console.log(i)},1000)
   } 
  // 每次进来时就会声明一个新的i
   
   除了用let还可以用立即执行函数,利用形参和实参值会复制的特点
3' for(var i=0;i<6;i++){
     !function(j){ //这个语法会报错,!用来阻止报错
        setTimeout(()=>{console.log(j)},1000)
      }(i) //实参i会赋值给j形参
    }
    
4'  for(var i=0;i<6;i++){
     !function(i){ //i和j是复制值的关系，所以也可以改成同名的。
       setTimeout(()=>{console.log(i)},1000)
     }(i)
    }
```

### 函数与延迟执行
**对比普通代码与函数区别**

这是普通代码`let b = 1 + a`

这是函数(不讨论参数) 

`let f = () => 1+a`

`let b = f()`

普通代码**立即求值**，读取a的**当前**值

函数会等调用时再求值，即**延迟求值**。且求值时才会读取a的**最新**值

函数的特点：函数是延迟执行的代码

**对比React元素和函数组件**

`App1 = React.createElement("div", null, n)`

App1是一个React元素

`App2 = () => React.createElement("div", null, n)`

App2是一个React函数组件

**启示**

函数App2是**延迟执行**的代码,会在**被调用的时候**执行

注意我说的是**代码执行的时机**，没有在说同步和异步

同步和异步关注的是得到结果的时机

**总结**

**React元素**

`React.createElement("div", null, n)`用来创建一个元素，createElement的返回值element可以**代表**一个div,但本身不是一个div对象，也就是说你不能把这个对象直接插到页面里。element并不是真正的div(DOM对象)。所以我们一般称element为**虚拟DOM**对象。

**() => React元素**

1.返回element的函数，也可以代表一个div

2.这个函数可以**多次执行**，每次得到最新的虚拟div

3.React会对比两个虚拟div,找出不同，局部更新视图

**4.找不同的算法叫做DOM Diff算法**

那么找出2个虚拟DOM**虚拟`div`** 不同的地方，然后得到一个最小化的结果局部更新视图，这个找不同的过程就叫做DOM Diff算法。

### JSX
> 现在的代码很丑，比Vue的写法复杂多了，别急有办法。
> 
> JSX弥补React代码太丑而发明的语言。
> 
> X表示扩展，所以JSX就是JS扩展版。

React的开发者不用装任何东西就可以愉快开发了。
因为babel-loader被webpack内置了。

**使用JSX**(3种方法)

**1' 用CDN**(不要用)

**语法:所有的标签用标签形式写，如果需要插入JS变量或者JS函数，就把那个东西用{}包起来。**

也不需要在函数后面加()了，因为App是一个函数组件，组件就直接当标签用就可以了，它会自动去调用你的App这个函数的。`<App />`
```js
html
</body>
  <div id="app"></div>
  <script src="https://cdn.bootcss.com/react/16.10.2/umd/react.development.js"></script>
  <script src="https://cdn.bootcss.com/react-dom/16.10.2/umd/react-dom.development.js"></script>
  <script src="https://cdn.bootcss.com/babel-standalone/7.0.0-beta.3/babel.min.js"></script>
  <script type="text/babel">
      let n = 0;
      const App = () => (
        <div> //创建div
          {n} //插入变量
          <button onClick={() => { //插入函数
              n += 1;
              render();
            }}
            >
            +1
          </button>
        </div>
      );
      const render = () =>
        ReactDOM.render(<App />, document.querySelector("#app"));
      render();
  </script>
</body>
```
**用CDN bable.js的原理是**，bable.js会把页面上浏览器不认识的bable代码翻译成浏览器认识的JS代码，然后把JS代码放到页面里，去替换之前浏览器不认识的。

**2' webpack + babel-loader**

新手跳过，配置太麻烦。

**3' 使用create-react-app**(推荐)

跟@vue/cli用法类似
```js
yarn global add create-react-app //全局安装
create-react-app react-demo //初始化目录
cd react-demo
yarn start //开发
```
示例
```js
inde.jtml
<div id="root"></div>
  
App.js
import React from "react"

const App = () => {
    return ( //这里默认使用了jsx语法
        <div>App组件</div> //相当于React.createElement()
    )
}
export default App

index.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.js'
//console.log(React);
ReactDOM.render(<App />, document.getElementById('root'))
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/423167e379d145529797c92cce21df90~tplv-k3u1fbpfcp-zoom-1.image)

现在Vue和React打平了，都可以写HTML:

Vue写在.vue文件的`<template>`里

React把HTML混在JS/JSX文件里

### 条件判断 与 循环控制
**一.条件判断**

在JSX里如何使用条件判断`if...else...`？
```js
在Vue里
<template>
  <div>
    <div v-if="n%2===0">n是偶数</div>
    <span v-else>n是奇数</span>
  </div>
</template>
```
**JSX的条件判断**
```js
在React里
第1种
const Component = () => {
  return n%2 === 0 ? <div> n是偶数 </div> : <span> n是奇数 </span>
}
//如果需要外面的div，可以写成
const Component = () => {
    return (
        <div>
         //⚠️别忘了用{ }包起来，只要是js东西就必须用{ }包起来！
            {n % 2 === 0 ? <div> n是偶数 </div> : <span> n是奇数 </span>}
        </div>
    )
}

第2种
cconst Component = () => {
    const content = (
        <div>
            {n % 2 === 0 ? <div>n是偶数</div> : <span>n是奇数</span>}//别忘了用{ }包起来
        </div>
    )
    return content
}

第3种
const Component = () => {
    const inner = n % 2 === 0 ? <div>n是偶数</div> : <span>n是奇数</span>
    const content = (
        <div>
            {inner} //别忘了{ }将inner包起来
        </div>
    )
    return content
}
第4种
const Component = () => {
    let inner
    if (n % 2 === 0) {
        inner = <div>n是偶数</div>
    } else {
        inner = <span>n是奇数</span>
    }
    const content = (
        <div> {inner} </div>
    )
    return content
}
```
结论:在Vue里只能用Vue提供的语法写条件判断，在React里你想怎么写就怎么写，你就是在写JS而已。这就是Vue与React最明显的区别。

**二.循环语句loop**

在Vue里可以遍历数组和对象
```js
<template>
  <div>
    <div v-for="(n, index) in numbers" :key="index">
      下标{{index}},值为{{n}}
    </div>
  </div>
</template>
```
**在React里**
```js
const Component = (props) => {
    return props.numbers.map((n, index) => {
        return <div>下标{index}值为{n}</div>
    })
}
//如果你需要外面的div，可以写成
const Component = (props) => {
    return (
        <div>
            {props.numbers.map((n, index) => {
                return <div> {index} {n}</div>
            })}
        </div>)
}

还可以这样写(麻烦不推荐)
const Component = (props) => {
  const array = []
  for(let i=0;i<props.numbers.length;i++){
    array.push(<div>下标为{i}值为
      { props.numbers[i]} </div>)
   }
   return <div>{ array }</div>
}
```
