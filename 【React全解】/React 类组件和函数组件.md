---
theme: juejin
---
# React 类组件和函数组件

> **目录**
> 
> 1.类组件和**函数组件**
> 
> 2.如何使用 props 和 state
> 
> 3.如何绑定事件
> 
> 4.复习 this+ 两个面试题

## 组件component
### **一.概念**
**Element VS Component (元素与组件)**
```js
//不成文的约定:元素小写，组件大写
const div=React.createElement('div',...)
这是一个React元素(小写)
const Div=()=>React.createElement('div',...) 
这是一个React组件(大写)
```
**什么是组件?**

能跟其他物件组合起来的物件，就是组件，组件并没有明确的定义。
就目前而言，一个返回值是React元素的函数就是组件

Vue里，`data、methods、render`组合成的一个对象(构造选项)就可以表示一个组件

**React两种组件**

**1.函数组件**
```js
function Welcome(props){
  return <h1>Hello,{props.name}</h1>
}
使用方法: <Welcome name="frank"/>
```
**2.类组件**
```js
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>
  }
}
使用方法: <Welcome name="frank"/>
```
**`<Welcome />`会被翻译成什么？**

`<div />`翻译为`React.createElement('div')` div是元素
`<Welcome />`翻译为`React.createElement(Welcome)` Welcome是函数

工具: [babel online](https://babeljs.io/repl#?browsers=&build=&builtIns=false&corejs=3.21&spec=false&loose=false&code_lz=DwEwlgbgBAxgNgQwM5IHIILYFMC8AiAJyxDwD4A7YAenAlKA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=false&targets=&version=7.17.9&externalPlugins=&assumptions=%7B%7D)把标签翻译成JS

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6263c53ee4e4492ab29e6af62287da5d~tplv-k3u1fbpfcp-zoom-1.image)

 **React.createElement的逻辑**
 
`React.createElement`目前接收几种参数:

1.如果传入一个**字符串**'div',则会创建一个div(虚拟DOM元素)

2.如果传入一个**函数**,则会调用该函数，获取其**返回值**

3.如果传入一个**类**,则在类前面加个new(这会导致执行
constructor),获取一个组件**对象**，然后调用对象的**render方法，获取其返回值**

```js
class Welcome extends React.Component{ 
  constructor(){
    super()
    this.state={n:0}
  }
  render(){
    return <div>hi</div>
  }
}
使用类
new Welcome()
```

### 二.使用React的2种组件 
React2种组件的书写方式: `class`类组件和`function`函数组件。

例子
```js
import React from "react";
import ReactDOM from "react-dom";

function App() { 
  return ( 
    <div className="App"> 
      爸爸
      <Son /> 
    </div>
  );
}

class Son extends React.Component { 
  constructor() { 
    super(); 
    this.state = { 
      n: 0
    };
  }
  add() {
    // this.state.n += 1 为什么不行
    this.setState({ n: this.state.n + 1 }); 
  }
  render() {
    return ( 
      <div className="Son">
        儿子 n: {this.state.n}
        <button onClick={() => this.add()}>+1</button> 
        <Grandson />
      </div>
    );
  }
}

const Grandson = () => {
//声明一个state初始值为0,用n代表0，用setN对0进行修改，每次setN时都会得到一个新的n(不是改变原有的n)
  const [n, setN] = React.useState(0); //析构写法
  return (
    <div className="Grandson">
      孙子 n:{n}
      <button onClick={() => setN(n + 1)}>+1</button>
    </div>
  );
};

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

### 如何使用 props(外部数据)？
**类组件和函数组件使用 props:**

**类组件**直接读取**属性**`this.props.xxx`

**函数组件**直接读取**参数**`props.xxx`

外部数据props不能写，只能读。

**一.class类组件添加外部数据props**

**1' 传字符串**

传: 接收方添加`messageForSon="字符串"`

接收: `{this.props.messageForSon}`

**2' 传变量**

传: 接收方添加`messageForSon={1+1}`

接收: `{this.props.messageForSon}`

例子:爸爸传props给Son
```js
function App() {
const a=10
  return (
    <div className="App">
      爸爸
      <Son messageForSon="儿子你好" /> 
    //<Son messageForSon={a} />
    </div>
  );
}
class Son extends React.Component {
  render() {
    return (
      <div className="Son">
        我是儿子，我爸对我说「{this.props.messageForSon}」
        <Grandson messageForGrandson="孙贼你好" />
      </div>
    );
  }
}
```
**二.函数组件**

同样的，要么使用`""`表示字符串，要么使用`{}`表示JS的表达式。

**1' 传字符串**

传: 接收方添加`messageForGrandson="字符串"`

接收: 需要传一个参数props 和 接收`{props.messageForGrandson}`

React会自动把`messageForGrandson="字符串"`变成一个对象然后放到第一个参数props里，props可自定义,叫x也行，一般叫props。

**2' 传变量**

传: 接收方添加`messageForGrandson={a}`

接收: `{props.messageForGrandson}`

例子:儿子传props给Grandson
```js
class Son extends React.Component {
  render() {
    var a=10
    return (
      <div className="Son">
        我是儿子，我爸对我说「{this.props.messageForSon}」
        <Grandson messageForGrandson="孙贼你好" /> 
      //<Grandson messageForGrandson={a} /> 添加messageForGrandson
      </div>
    );
  }
}
const Grandson = props => { 
  var a=10
  return (
    <div className="Grandson">
      我是孙子，我爸对我说「{props.messageForGrandson}」//接收string或变量
    </div>
  );
};
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4c7030c413bc4055a61bd0f92758c55e~tplv-k3u1fbpfcp-zoom-1.image)


### 如何使用 state(内部数据)？
**类组件和函数组件使用 state:**

**类组件**用`this.state`读，`this.setState`写
**函数组件**用`useState`返回数组，第一项读，第二项写

**一.类组件如何使用内部数据state？**

关注数据时关注它的3个条件：1.如何初始化？2.如何读？3.如何写？
```js
class Son extends React.Component {
  constructor() {
    super();
    this.state = { //1.初始化
      n: 0
    };
  }
  add() {
 // this.state.n += 1 为什么不行？
    this.setState({ n: this.state.n + 1 }); //2.写
  }
  render() {
    return (
      <div className="Son">
        儿子 n: {this.state.n} //3.读
        <button onClick={() => this.add()}>+1</button>
        <Grandson />
      </div>
    );
  }
}
```
**`this.state.n += 1`为什么不行？**

因为Vue会监听n,而React根本不会监听n,所以就算n变化了React也不知道.

**那怎么才能通知React我修改了数据呢？**

用`setState`。
数据不可变的思维:不要改变以前的对象，要改的话你就产生一个新的对象。React理念就是数据不可变，你如果要改变就产生一个新的对象，新的对象去容纳新的东西，然后传给setState。
```js
add() {
  //this.state.n +=1
  //this.setState(this.state); 
  //这种虽然也生效但不要用，违反了数据不可变的思维
    this.setState({ n: this.state.n + 1 }); 
  }
```
这就是在类组件里，如何使用state:

1.在constructor的super下面进行初始化

2.读的时候用`this.state.n`

3.写的时候用`this.setState`,最好用一个新的对象，而不是在原有的对象上修改。

**牛X的前端用`setState(函数)`，他一般会写成一个函数。**
```js
add() {
  //this.setState({ n: this.state.n + 1 }); 
  //console.log(this.state.n) 
  //打印出0,setState不会马上改变n的值，而是等一会console执行完后才去覆盖n的值。
  //所以说，setState是异步的更新UI的过程，不会立即执行而是过一会执行，新手不理解易bug,老手为了避开这个问题这样写,避免混淆新的、旧的state。
    this.setState( state => { //旧的n
      const n=state.n+1
      console.log(n)
      return { n } //新的n
      }
    )  
  }
```
这种写法能更好的理解旧state、新state，而且不容易出错，避免异步造成的误解。
使用state时注意一个事情，setState不会马上改state，最好使用函数去读新的值，它会让你更容易区分新旧state。

**二.函数组件如何使用内部数据state？**

关注数据时关注它的3个条件：1.如何初始化？2.如何读？3.如何写？
```js
const Grandson = () => {
  const [n, setN] = React.useState(0); //1.初始值
  //你返回的这个数组的第1项就是用来读的，第2项就是用来写的。一个读接口一个写接口，就2项。名字可写但一般就叫n、setN。
  return (
    <div className="Grandson">
      孙子 n:{n} //2.读
      <button onClick={() => setN(n + 1)}>+1</button> 
    //3.写,setN永远不会改变n,它会产生一个新的n。
    </div>
  );
};
```
类组件的setState会等一会再改变state，函数组件的setN永远都不会改变n。

**函数组件注意事项**

**1.跟类组件类似的地方**

也要通过setX(新值)来更新UI

**2.跟类组件不同的地方**

没有this,一律用参数和变量，搞不懂this的就可以用函数组件。

### Vue 对比 React

**两种编程模型**

Vue: 我对数据的修改会直接映射到UI上。Vue监听了n的变化，当n变化时，Vue就把n出现的地方全部都变一遍。

React: 一开始给我个state`{n:0}`,我把这个state变成一个UI。
如果你想改变n,要注意不能修改之前的n。应该直接新建一个新的对象，对应一个新的UI。

React就会将这2个UI进行**对比**(DOM diff)区别，然后进行局部更新。

## 复杂 state 怎么处理
所谓复杂就是state是个对象，如果state是个复杂的对象怎么办？

如果state里不止有n怎么办？

**一.类组件里有`n`和`m`**

1.类组件的setState**会自动合并第一层属性**:

当类组件里有`n`和`m`时，setState时可以只setState一部分，因为它会自动延用之前的属性。

2.类组件的setState**不会合并第二层属性**。

**二.函数组件里有`n`和`m`**

函数组件的setState**不会自动合并**，当多个setState放一起时要记得用`...操作符`把之前的复制过来。函数组件的setState完全不会帮你自动合并，不管你是第一层还是第二层，要合并只能自己用...操作符。

### **一.类组件里有`n`和`m`**
**1.类组件的setState`会自动合并第一层属性`**

例子
```js
function App() {
  return (
    <div className="App">
      爸爸
      <Son />
    </div>
  );
}

class Son extends React.Component {
  constructor() {
    super();
    this.state = {
      n: 0,
      m: 0
    };
  }
  addN() {
    this.setState({ n: this.state.n + 1 });
    // m 会被覆盖为 undefined 吗？
  }
  addM() {
    this.setState({ m: this.state.m + 1 });
    // n 会被覆盖为 undefined 吗？
  }
  render() {
    return (
      <div className="Son">
        儿子 n: {this.state.n}
        <button onClick={() => this.addN()}>n+1</button>
        m: {this.state.m}
        <button onClick={() => this.addM()}>m+1</button>
        <Grandson />
      </div>
    );
  }
}

const Grandson = () => {
  const [n, setN] = React.useState(0);
  return (
    <div className="Grandson">
      孙子 n:{n}
      <button onClick={() => setN(n + 1)}>+1</button>
    </div>
  );
};

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6f882eed6ba34c04aa3d4ea53e2008a8~tplv-k3u1fbpfcp-zoom-1.image)

总结:类组件里的setState,如果你对其中一部分进行修改那么其他的部分会自动延用上一次的值，而不会被`undefined`覆盖。

```js
addN() {
    this.setState({ n: this.state.n + 1 });
/*相当于写
  this.setState({...this.state, n: this.state.n + 1 });
  当只设置n时，m会自动延用上一次的值，m不会被覆盖为undefined
  React会自动帮你写`...this.state`:把之前所有值拷过来再将n变成n+1
*/
  }
```
#### 2.类组件的setState不会合并第二层属性
类组件的setState`会自动合并第一层属性`，但是不会合并第二层属性。

**解决方法:** 使用`Object.assign`或者`...操作符`，二选一。

**1' 使用`...操作符`(推荐)**
```js
class Son extends React.Component {
  constructor() {
    super();
    this.state = {
      n: 0, //第1层，设置n时不要管m
      m: 0,
      user: {
        name: "frank", //第2层，设置name时必须管age
        age: 18
      }
    };
  }
  ...
  changeUser() {
    this.setState({
    //m和n不会被置空
      user: {
        ...this.state.user, //防止age被置空的解决方法
        name: "jack"
      //坑:age会被置空，解决方法:`...this.state.user`
      }
    });
  }
  render() {
    return (
      <div className="Son">
        儿子 n: {this.state.n}
        <button onClick={() => this.addN()}>n+1</button>
        m: {this.state.m}
        <button onClick={() => this.addM()}>m+1</button>
        <hr />
        <div>user.name: {this.state.user.name}</div>
        <div>user.age: {this.state.user.age}</div>
        <button onClick={() => this.changeUser()}>change user</button>
        <Grandson />
      </div>
    );
  }
}
```
`...操作法`是React经常会用到的技巧

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7a40562ebc504dcd9f977794ba66f417~tplv-k3u1fbpfcp-zoom-1.image)

**2' 使用`Object.assign`**
```js
class Son extends React.Component {
  constructor() {
    super();
    this.state = {
      n: 0,
      m: 0,
      user: {
        name: "frank",
        age: 18
      }
    };
  }
  changeUser() {
  //Object.assign把之前user的所有属性复制到新对象上
    const user = Object.assign({}, this.state.user); 
  //等价于const user={...this.state.user}
    user.name = "jack";
    this.setState({
      // m 和 n 不会被置空
      user: user
    });
  }
  render() {
    return (
      <div className="Son">
        儿子 n: {this.state.n}
        <button onClick={() => this.addN()}>n+1</button>
        m: {this.state.m}
        <button onClick={() => this.addM()}>m+1</button>
        <hr />
        <div>user.name: {this.state.user.name}</div>
        <div>user.age: {this.state.user.age}</div>
        <button onClick={() => this.changeUser()}>change user</button>
        <Grandson />
      </div>
    );
  }
}

const Grandson = () => {
  const [n, setN] = React.useState(0);
  return (
    <div className="Grandson">
      孙子 n:{n}
      <button onClick={() => setN(n + 1)}>+1</button>
    </div>
  );
};
```

### **二.函数组件里有`n`和`m`**
**同样的需求下，用函数组件更简便，类组件已经过时了，尽量用函数组件**。
例子
```js
//第1种写法
const Grandson = () => {
  const [n, setN] = React.useState(0);
  const [m, setM] = React.useState(0);
  return (
    <div className="Grandson">
      孙子 n:{n}
      <button onClick={() => setN(n + 1)}>n+1</button>
      m:{m}
      <button onClick={() => setM(m + 1)}>m+1</button>
    </div>
  );
};
```
函数组件另一种不推荐的写法，你会发现m被质空:
```js
//不要这样写，当你setState`n`时，`m`就会`undefined`.
const Grandson = () => {
  const [state, setState] = React.useState({
    n:0,m:0
  });
  return (
    <div className="Grandson">
      孙子 n:{state.n}
      <button onClick={() => setState({n:state.n + 1})}>n+1</button>
      m:{state.m}
      <button onClick={() => setState({m:state.m + 1})}>m+1</button>
    </div>
  );
};
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d26a994208db469f926509da744c50e2~tplv-k3u1fbpfcp-zoom-1.image)

如果你要用函数组件的setState就不要搞个对象在这里。如果你非要弄个对象那每次赋值时要记得先拷贝之前的。
```js
//第2种写法
const Grandson = () => {
  const [state, setState] = React.useState({
    n:0,m:0
  });
  return (
    <div className="Grandson">
      孙子 n:{state.n}
      <button onClick={() => setState({...state,n:state.n + 1})}>n+1</button>
      m:{state.m}
      <button onClick={() => setState({...state,m:state.m + 1})}>m+1</button>
    </div>
  );
};
```
**总结:
类组件会自动合并第1层，不会合并第2层。函数组件不会自动合并。所以不要依赖自动合并。**

## 事件绑定
> React组件里事件绑定的各种写法:onClick,onKeyPress...

**一.类组件的事件绑定**

`<button onClick={()=>this.addN()}>`

示例
```js
//最终写法:这种写法this不会变成window
class Son extends React.Component {
  addN=()=> this.setState({n:this.state.n+1}) //绑定
//上面的写法是下面的语法糖,2种写法完全等价
//constructor(){
//  this.addN = () => this.setState({n:this.state.n+1})
//}
  render(){
    <button onClick={this.addN()}> //使用
  }
}
```
constructor里的this`this.addN `指的是当前对象。
这种写法函数会被定义到对象本身身上，这意味着每个Son组件都有自己的addN,如果有两个Son,就有2个addN。
```js
//补充
//1.箭头函数上的this是不会变的，因为箭头函数不支持this
addN=()=>{this.setState({n:this.state.n+1})}

//2.constructor里的this`this.addN `指的是当前对象Son。
class Son extends React.Component {
  constructor(){
    this.addN = () => this.setState({n:this.state.n+1})
  }
}
```

### 复习this
[1.this的值到底是什么？](https://zhuanlan.zhihu.com/p/23804247)给this定性

[2.你怎么还没搞懂this?](https://zhuanlan.zhihu.com/p/25991271) 如何确定this

[3.JS里为什么会有this?](https://zhuanlan.zhihu.com/p/30164164) 为什么要设计this

**为什么this会变/不会变？**

所有函数的this都是参数，由调用决定，所以可变

唯独箭头函数的this不变，因为箭头函数不接受this

React的特点:能不做的，我都不做。

Vue的特点:能帮你做的，都帮你做了。

**this面试题1**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d86c48365feb45369d75a43e45aec262~tplv-k3u1fbpfcp-zoom-1.image)


**this面试题2**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5885e804ccf6426b8640b43cba02a2d2~tplv-k3u1fbpfcp-zoom-1.image)

**总结**

React是面向类和函数，Vue更像是面向对象。

**React提供的是JSX语法(2点):**

1.普通属性用标签

2.非普通属性也就是跟JS相关的，都写在{}里。

{}之外都是标签，{}之内是JS。

**面试题**

**React与Vue的区别？**

**相同点**

1.都是对视图的封装，React是用类和函数表示一个组件，而Vue是通过构造选项构造一个组件。

2.都提供了createElement的XML简写，React提供的是JSX语法，而Vue是提供的是template模版写法(语法巨多)

**不同点**

React是把HTML放在JS里写(HTML in JS),而Vue是把JS放在HTML里写(JS in HTML)

