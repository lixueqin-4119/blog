
# Hooks 原理解析
> **内容**\
> 1.分析useState原理和源码\
> 2.useRef的作用\
> 3.useContext的作用\
> 4.Vue3 对比 React

## 分析useState原理
```js
import React from "react";
import ReactDOM from "react-dom";
const rootElement = document.getElementById("root");

function App() {
  console.log("App 运行了")
  const [n, setN] = React.useState(0);
  console.log(`n:${n}`);
  return (
    <div className="App">
      <p>{n}</p>
      <p>
        <button onClick={() => setN(n + 1)}>+1</button>
      </p>
    </div>
  );
}
ReactDOM.render(<App />, rootElement); 
```
**点击button后会发生什么？**

首次渲染`render <App/>`

调用App()

得到一个**对象**(虚拟DIV)

React就会把这个虚拟DIV变成页面中真实的`<div>`

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6fd1fc3a90dc4dec9c5129062079630a~tplv-k3u1fbpfcp-zoom-1.image)

用户点击button就会调用onClick函数,执行setN(n+1)

再次`render <App/>`,调用App()

验证:添加`console.log("App 运行了")`看看App运行了几次

第1次得到0时“App 运行了”，点button又执行“App 运行了”

证明:这个函数每更新一次UI就会运行一次。

会再次得到一个**对象**(虚拟DIV)

把`新的虚拟DIV`跟`旧的虚拟DIV`对比，看哪里有变化DOM Diff，然后**局部**更新真`<div>`

每次调用App(),都会执行`useState(0)`

第1次运行与第2次运行结果是一样的吗？

验证:添加`console.log(`n:${n}`);`

点刷新n=0,点`+1`n=1

证明:同样一句话`const [n, setN] = React.useState(0);`每次执行时，n的值都不一样。

**useState到底做了什么，让每次n的值都不同？**

执行setN的时候会发生什么？重新渲染UI

n会变吗？setN不会改变n

App()会重新执行吗？当然会

既然App()会重新执行，那么`useState(0)`的时候，n每次的值会有不同吗？会不同

**分析**

**1.setN**\
setN一定会修改`某个数据x`,将n+1存入`某个数据x里`\
setN一定会触发<App/>重新渲染(re-render)\
**2.useState**\
useState肯定会从`某个数据x`读取n的最新值\
**3.x**\
每个组件有自己的数据x,我们将其命名为state

### 实现React.useState

1.初次尝试实现`React.useState(0);`

n是变量，setN是函数

```js
import React from "react";
import ReactDOM from "react-dom";
const rootElement = document.getElementById("root");

let _state;
const myUseState = (initicalValue) => {
  console.log("myUseState run");
  _state = _state === undefined ? initicalValue : _state;
  const setState = (newValue) => {
    console.log("setState run");
    _state = newValue;
    render();
  };
  return [_state, setState];
};
//教学需要,render只是简化，不用在意render的实现
const render = () => ReactDOM.render(<App />, rootElement);

function App() {
  const [n, setN] = myUseState(0);
  return (
    <div className="App">
      <p>{n}</p>
      <p>
        <button onClick={() => setN(n + 1)}>+1</button>
      </p>
    </div>
  );
}
ReactDOM.render(<App />, rootElement);
```
**因为myUseState会将state重置**

我们需要一个不会被myUseState重置的变量

这个变量`_state`只要声明在myUseState外面即可

### 如何让两个useState不冲突

**如果一个组件用了两个useState怎么办？**

第一次使用myUseState时会把0赋值给state,第二次再使用myUseState时又把0赋值给state，那这个state到底是n还是m?

```js
const [n, setN] = myUseState(0);
const [m, setM] = myUseState(0);
```

由于所有数据都放在_state,所以会冲突。

**改进思路**

**把_state做成一个对象**

比如`_state={n:0,m:0}`

不行，因为useState(0)并不知道变量叫n还是m

**把_state做成数组**(_state数组方案)

比如_state=[0,0]

貌似可行，试试看
```js
//多个useState
import React from "react";
import ReactDOM from "react-dom";
const rootElement = document.getElementById("root");

let _state = [];
let index = 0;
function myUseState(initialValue) {
    const currentIndex = index //保存当前的index
  //console.log('currentIndex:'+currentIndex)
    _state[currentIndex] = _state[currentIndex] === undefined ? initialValue : _state[currentIndex];
    const setState = (newState) => {
        _state[currentIndex] = newState;
      //console.log("_state:" + _state)
        render();
    }
    index += 1
  //console.log("currentIndex:" + currentIndex + "index:" + index)
    return [_state[currentIndex], setState];
}

const render = () => {
    index = 0 //重置index
    ReactDOM.render(<App />, rootElement)
};

function App() {
    const [n, setN] = myUseState(0);
    const [m, setM] = myUseState(0);
    return (
        <div className="App">
            <p>{n}</p>
            <p>
                <button onClick={() => setN(n + 1)}>+1</button>
            </p>
            <p>{m}</p>
            <p>
                <button onClick={() => setM(m + 1)}>+1</button>
            </p>
        </div>
    );
}
ReactDOM.render(<App />, rootElement);
```
**解析**

**1.需求:实现index+1的功能。**

思路: 添加中间量存储当前值

因为这个index会+1，currentIndex我们不会去改它。然后你再对index+1时就不会影响"当前的index"了。

**2.`+1`没反应**

调试`console.log("_state:" + _state)`

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/efeb881c62f747729564e3ec339e9d95~tplv-k3u1fbpfcp-zoom-1.image)

**什么改变了下标的值？**

看看currentIndex是不是超过了1？

`console.log("currentIndex:" + currentIndex)`

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/86a47fe5d1dd4c0f9030446f8a59572b~tplv-k3u1fbpfcp-zoom-1.image)

怎么会超过1呢？这就是为什么state会变长。

**原因:** 因为你一直在不停的运行App。

第1次设置n运行结果是0、1，第2次设置m结果是2、3，所以导致了index一直往前加。

```js
function App() {
  const [n, setN] = myUseState(0);
  const [m, setM] = myUseState(0);
}
```
**解决方法:** 每次在渲染App之前，就应该重置`index=0`

**上面_state数组方案的缺点**

**useState调用顺序**

若第一次渲染时n是第一个，m是第二个，k是第三个

则第二次渲染时必须保证顺序完全一致

所以React不允许出现如下代码，会打乱顺序并出现bug

useState不能写在if里

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e0f6646c6af248768f52e48b79fac2ce~tplv-k3u1fbpfcp-zoom-1.image)

```js
function App(){
  const [n,setN]=React.useState(0)
  let m,setM
  if(n % 2 === 1){ //只在某个时候才去用它
    [m,setN]=React.useState(0)
  }
  ...
}
```
题外话:Vue 3克服了这个问题

**现在的代码还有问题**

**App用了_state和index,那其它组件用什么？**

解决方法:给每个组件创建一个_state和index

**又有问题:放在全局作用域里重名了咋整？**

解决方法:放在组件对应的虚拟节点对象上

![请添加图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/70cb3450730e4a9f87b810e72520fe72~tplv-k3u1fbpfcp-zoom-1.image)

虚拟DOM是函数组件运行得出来的，函数组件运行中会使用useState(),useState的_state和index放在虚拟DOM上，一开始虚拟DOM是空的，运行后就不是空的了。但这个虚拟DOM一开始就有_state和index这2个值，用来存储App组件里的state。

得到`App1`组件后就可以渲染到虚拟DOM树上，这个DOM树就会映射到div上。在用户点击button后就会执行set(N+1)，set(N+1)会再次执行App组件，App组件再次执行useState就会再次读取_state和index得到`App2`。

`App1`和`App2`的区别就是n不同，做个Diff，把`App1`和`App2`差别之处做成一个对象，这个对象叫Patch。把这个Patch再次运行到刚才的div上，那它就知道刚才n=0,现在n=1,那我就会更新n的值。

其实它的模型非常简单，就是我去改这个虚拟组件，我所有的东西都放在虚拟组件的上面。函数每次执行都会得到虚拟组件的对象`App1`，这个对象`Patch`可以去更新这个虚拟组件。
每一个组件都有自己的虚拟节点，每一个虚拟节点都会存储_state和index。

**总结**\
**1.每个函数组件对应一个React节点***\
**2.每个节点保存着state和index**\
**3.每个函数里用useState会读取对应节点的state[index]**\
**4.index由useState出现的顺序决定**\
第一次调用useState index等于0.第二次调用index等于1，以此类推。\
**5.setState会修改state，并触发更新**\
useState会得到setState，得到n和setN，setN会修改n的对应的state,这个state是存在节点上的，修改之后会触发更新。

> **注意:** 这篇文章对React的实现做了简化，React节点应该是FiberNode,_state的真实名称为memorizedState,index的实现则用到了链表，有兴趣的可以[自行学习](https://juejin.cn/post/6844903704437456909)

### 纠正下多数人对useState错误的理解。

**错误1:误以为setN会改变n,实际上setN不会改变n**

```js
import React from "react";
import ReactDOM from "react-dom";
const rootElement = document.getElementById("root");

function App() {
  const [n, setN] = React.useState(0);
  const log = () => setTimeout(() => console.log(`n: ${n}`), 3000);
  return (
    <div className="App">
      <p>{n}</p>
      <p>
        <button onClick={() => setN(n + 1)}>+1</button>
        <button onClick={log}>log</button>
      </p>
    </div>
  );
}
ReactDOM.render(<App />, rootElement);
```
**两种操作**

一.先+1后log,无bug

二.先log后+1，有bug

问题:为什么log出了旧数据？

**因为有多个n**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1d6f67f22594b2b9263b7338540bfef~tplv-k3u1fbpfcp-zoom-1.image)

**第2条线:**

**先点log**,第一次App()

n和setN不变，`console.log(n)`会先读取n的值,然后在3s后打印`0`

**再点+1**,第二次App()

会生成一个新的n,这2次渲染`n=0`和`n=1`是同时存在内存的。

### 那我希望有一个贯穿始终的状态，怎么做？

> 题外话:Vue 3可以实现。只有一个n,每次改的都是同一个n。

**三种方法**

**1' 全局变量**\
用window.xxx即可

**2' useRef**\
useRef不仅可以用于div,还能用于任意数据

**3' useContext**\
useContext不仅能贯穿始终，还能贯穿不同组件

**useRef例子**\
因为只有一个n,只有一个current值。\
但是有个bug,useRef不会让App重新渲染，所以**页面上的n不会实时更新**。

**题外话:** Vue 3可以做到当你修改useRef值时，自动渲染App。
```js
function App() {
  const nRef = React.useRef(0); //{current:0}
  const log = () => setTimeout(() => console.log(`n: ${nRef.current}`), 1000);
  return (
    <div className="App">
      <p>{nRef.current} 这里并不能实时更新</p>
      <p>
        <button onClick={() => (nRef.current += 1)}>+1</button>
        <button onClick={log}>log</button>
      </p>
    </div>
  );
}
```
useRef不会让App重新渲染，React不支持。

但是理论上还是可以造出一个手动触发App更新的API,代码如下。
```js
function App() {
  const nRef = React.useRef(0); //{current:0}
  const update = React.useState()[1];
  const log = () => setTimeout(() => console.log(`n: ${nRef.current}`), 1000);
  return (
    <div className="App">
      <p>{nRef.current} 这里并不能实时更新</p>
      <p>
        <button onClick={() => ((nRef.current += 1), update(nRef.current))}>
          +1
        </button>
        <button onClick={log}>log</button>
      </p>
    </div>
  );
}
```
这种情况不如直接学Vue3。

Vue3\
 1.借鉴了hook这个思想\
 2.不使用useState 而是使用useRef，并把useRef用到了极致。\
 3.自创:当你对`useRef.current`的值进行变更时，它会自动的去update。

**useContext例子：** 全局切换主题

useRef只是贯穿一个组件的前中后，useContext不仅能贯穿始终还能贯穿所有组件。

**什么是上下文？**

上下文就是一个全局变量，只不过它是局部的全局变量。

```js
import React from "react";
import ReactDOM from "react-dom";
import "./styles.css";
const rootElement = document.getElementById("root");

const themeContext = React.createContext(null);//初始化一个上下文themeContext

function App() {
  const [theme, setTheme] = React.useState("red");
  return (
    <themeContext.Provider value={{ theme, setTheme }}>
      <div className={`App ${theme}`}>
        <p>{theme}</p>
        <div>
          <ChildA />
        </div>
        <div>
          <ChildB />
        </div>
      </div>
    </themeContext.Provider>
  );
}

function ChildA() {
  const { setTheme } = React.useContext(themeContext);
  return (
    <div>
      <button onClick={() => setTheme("red")}>red</button>
    </div>
  );
}

function ChildB() {
  const { setTheme } = React.useContext(themeContext);
  return (
    <div>
      <button onClick={() => setTheme("blue")}>blue</button>
    </div>
  );
}

ReactDOM.render(<App />, rootElement);
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cf7ab4ea61194d98a49bd1b7fcc350eb~tplv-k3u1fbpfcp-zoom-1.image)

**详解**

1.`value={{ theme, setTheme }}`

`value={}`表示里面是JS，theme是`theme:theme`的缩写，setTheme也是缩写。
表示我们一开始就把这个全局变量的初始值赋值为一个对象，这个对象有theme、setTheme两个属性。

2.`<themeContext.Provider>`标签
```js
<themeContext.Provider>
  //标签内是上下文全局变量themeContext的作用域。
</themeContext.Provider>
```
**3.如何在点击button时调用全局变量setTheme？**

用全局变量,从themeContext读取setTheme

```js
const { setTheme } = React.useContext(themeContext);
```

一般是用一个函数放在它身上，这样我们就可以在任何一个子组件/子孙组件里调用最上面的那个组件的全局方法。useContext非常适用于切换主题。

大部分时候用useRef，useRef用多了你就会想用Vue3。

**总结**\
**1.组件每次重新渲染，组件函数就会再次执行**\
**2.对应的所有state都会出现分身**\
新旧n会同时存在，如果你没有setTimeout,旧的n就会自动消失，会被垃圾回收掉。\
如果你有setTimeout，它就会setTimeout之后再被垃圾回收掉。\
**3.如果你不希望出现分身,可以用useRef/useContect 或者Vue3**




