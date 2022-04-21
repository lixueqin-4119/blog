# Class 组件详解
> **目录**
> 
> 一.class组件的创建方式
> 
> 二.props和state
> 
> 三.生命周期Lifecycle

# 创建 class 组件
```js
两种方式
第1种.ES5方式(过时)
import React from 'react'
const A = React.createClass({
  render() {
    return ( <div>hi</div> )
  }
})
export default A
// 由于ES5不支持class,才会有这种方式

第2种.ES6方式(推荐)
import React from 'react';
class B extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <div>hi</div>
    )
  }
}
export default B;
```
**浏览器不支持ES6怎么办？**

用webpack+babel将ES6翻译成ES5即可

# props
对组件来说组件内就是内部，组件外就是外部。

**1.传入props给B组件**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d75749e8513841c5a06b0af2f0abc077~tplv-k3u1fbpfcp-zoom-1.image)

(1)外部数据一般都来自父元素的state，**state是怎么作为props的？**

把`this.state.name`作为B的外部属性。

(2)把外面的onClick函数传给onClick属性,这个onClick不是state，就是类的一个方法。传的是地址，对象只会拷贝地址。

**(3)外部数据被包装为一个对象**

name、onClick会变成对象`{name:'frank',onClick:...,children:'hi'}`
children就是hi,如果里面写了2个东西，就会变成一个数组放在children里。

(4)onClick就是回调

**2.props如何初始化**
```js
class B extends React.Component {
  constructor(props) { //这个props是从外部父组件接收来的
    super(props); //super会把props放到this上
  //this.state={n:0} 初始化，如果写了初始化那必须写constructor
  }
  render(){}
}
要么不初始化，即不写constructor
要么初始化，且必须写全套(不写super直接报错)
```
**效果**

这么做了之后，this.props就是外部数据**对象的地址**了。

**3.读取props**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/48d6da3ab97046159fdffad86d1a9714~tplv-k3u1fbpfcp-zoom-1.image)

把外部传进来的函数 复制给**div的onClick** props
直接展示`{this.props.name}`  

用对象的语法来读取并展示`{this.props.children}`

通过`this.props.xxx`读取

**4.怎么写props？**

**永远不准对props进行任何的改写，不准写props!**

```js
//不要这样写
this.props={ 另一个对象}
this.props.xxx='hi'
```
**相关钩子**

[componentWillReceiveProps](https://codesandbox.io/s/withered-feather-wgczm)钩子(**被弃用**)

当组件接收新的props时，会触发此钩子

**面试题**

**Props的作用**

**1.接受外部数据**

只能读不能写，外部数据由父组件传递

**2.接收外部函数**

在恰当的时机，调用该函数。比如说当某个div被点击时调用

该函数一般是父组件的函数

# State(读) & setState(写)

**1.初始化**
```js
class B extends React.Component{
  constructor(props) {
    super(props);
    this.state = { //初始化
      user: {name:'frank', age:18}
      }
  }
    render() {  ...  }
  }
}
```
**2.读写State**

**读用this.state**
```js
this.state.xxx.yyy.zzz
```
**写用this.setState(???,fn)**

**第1个参数有2种情况:**
```js
1' this.setState(newState,fn)
⚠️注意:setState不会立即改变this.state,会在当前代码运行完后，再去更新this.state,从而触发UI更新

2' this.setState((state,props)=>newState,fn)
这种方式的state反而更易于理解
fn会在写入成功后执行
```
**写时会shallow merge**

setState会自动将新state与旧state进行一级合并

**详解**

**(1)setState不会立即改变this.state**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/76b3121ed63948a38edf4973417849f9~tplv-k3u1fbpfcp-zoom-1.image)

所以当前setState后，`this.state.x`仍为1

**这就导致了一个问题:** 2次setState后，x是2不是3。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c6d9b5ad9c87496c8aa70bf36a61853c~tplv-k3u1fbpfcp-zoom-1.image)

**解决方法:**

1' 只用一次setState

2' 不用对象的形式而是**用函数的形式**

**JS中`{}`有个bug,如果你要返回一个对象，最好用`()`扩起来。**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2f566865871440c8b3f3b2aa33cd596e~tplv-k3u1fbpfcp-zoom-1.image)

**(2)第2个参数fn**(用的少)

第2个参数`fn`，成功的回调，在setState成功后会执行的操作。

可以用fn实现2次`+1`，但一般不会这么用。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ae73fbe8903f4e809c1451a2745334cd~tplv-k3u1fbpfcp-zoom-1.image)

**(3)不守规矩:修改this.state的属性值**

`this.state.n +=1`

`this.setState(this.state)`

不推荐用，但能用(因为React没法阻止你这么写)

# React的生命周期
生命周期

类比如下代码

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/880ee634c2f54046aa4ba26de5a1f7b9~tplv-k3u1fbpfcp-zoom-1.image)

create创建、mount挂载、update更新、unmount卸载,这四个过程就叫**生命周期**

**React有如下生命周期:**

```js
1.constructor() 构造，在这里初始化state 
2.shouldComponentUpdate() 是否应该更新，return false阻止更新 
3.render() 渲染，创建虚拟DOM
4.componentDidMount() 组件已挂载(已出现在页面)
5.componentDidUpdate() 组件已更新，当组件更新之后就会执行该函数
6.componentWillUnmount() 当组件将要卸载/死亡时就会执行该函数
```
下面逐个了解这6个函数是什么时候执行的。

### **一.constructor**

构造，在这里初始化state 

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f389d90a6c4a4485acbb7f2d3aec0be2~tplv-k3u1fbpfcp-zoom-1.image)

如果代码只是初始化，那么props可省略不用写。初始化state时才需要写全constructor。

### 二.shouldComponentUpdate

> 是否应该更新，return false阻止更新 
> 
> PureComponent是shouldComponentUpdate的优化，**推荐用PureComponent**。

**用途**

返回true表示不阻止UI更新

返回false表示**阻止**UI更新


**示例:**`+1``-1`数值不变，render仍旧会执行
```js
class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = { n: 1 }
  }
  onClick = () => {
    this.setState(state => ({
      n: state.n + 1
    }))
    this.setState(state => ({
      n: state.n - 1
    }))
 }
//{n:1}和{n:1}不是同一个对象。
//对React来说，你换了对象我就要更新数据，对象就是数据。
//只不过页面上的元素是没有更新的
```
补充：JS中`{}`有个bug,如果你要返回一个对象，最好用`()`扩起来。

render是每次都执行了，重复执行。有没有办法阻止render不必要的重复执行？用`shouldComponentUpdate`

🔍[React.shouldComponentUpdate ](https://zh-hans.reactjs.org/docs/optimizing-performance.html#shouldcomponentupdate-in-action)
```js
import React from "react"
class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      n: 1
    }
  }
  onClick = () => {
    this.setState(state => ({
      n: state.n + 1
    }))
    this.setState(state => ({
      n: state.n - 1
    }))
  }
  shouldComponentUpdate(newProps, newState) {
    if (newState.n === this.state.n) {
      return false
    } else {
      return true
    }
  }
  render() {
    console.log('render了一次')
    return (
      <div>App
        <div>
          {this.state.n}
          <button onClick={this.onClick}>+1</button>
        </div>
      </div>
    )
  }
}
export default App
```
**解析**

1.newProps就算用不到也不能删,占位的，删掉的话newState就变成第1个参数了。
```js
shouldComponentUpdate(newProps, newState){
  if (newState.n === this.state.n) {
    return false
  } else {
    return true
  }
}
可以这样写
shouldComponentUpdate(){ //arguments的用法
  if (arguments[1].n === this.state.n) {
    return false
  } else {
    return true
  }
}
```
**PureComponent的用法(推荐)**
> PureComponent是shouldComponentUpdate的优化

**用法:只需要修改继承React.PureComponent**
```js
class App extends React.PureComponent{ ... }
```
React提供了PureComponent给你优化，可代替shouldComponentUpdate。
PureComponent 会在 render 之前对比`新旧state`的每一个key，以及`新旧props`的每一个key。如果所有key的值全都一样就不会render，如果有任何一个key的值不同，就会 render。

**面试题**

**请问 shouldComponentUpdate 有什么用？**

这个钩子/生命周期允许我们手动判断是否要进行组件更新，我们可以根据应用场景灵活的设置返回值，以避免不必要的更新。

### 三.生命周期之 render
> 渲染，创建虚拟DOM

**用途详解**

**功能1.return一个虚拟DOM**

表示DOM元素的对象旧就叫做虚拟DOM，return返回的`<div>`是一个虚拟DOM元素的对象。虚拟DOM就是个对象。

**虚拟DOM长啥样？**
```js
render() {
  const x = (
    <div>App
      <div>
        {this.state.n}
        <button onClick={this.onClick}>+1</button>
       </div>
     </div>
   )
   console.log(x)
   return x
}
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b2e190fd8eb04d61b898e1d3114922cf~tplv-k3u1fbpfcp-zoom-1.image)

**功能2.只能有一个根元素**

**如果有2个元素就要用`<React.Fragment>`包起来。**

`<React.Fragment></React.Fragment>`**可以简写为**`<></>`
```js
render() {
  return (
    <>
      <div>div1</div>
      <div>div2
        <div>
          {this.state.n}
          <button onClick={this.onClick}>+1</button>
        </div>
      </div>
    </>
 )}
```
好处:只是做占位的，渲染的时候并不存在。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f0d140fad1264de5bb1b5b56e71d8a1f~tplv-k3u1fbpfcp-zoom-1.image)

**技巧详解**

**1.render里面可以写`if...else`、`?:`表达式**

可以写`if...else...`、`?:`表达式(推荐)、`&&`

```js
render() {
  let message
  if (this.state.n % 2 === 0) {
    message = <div>偶数</div>
  } else {
    message = <div>奇数</div>
  }
  return (
    <>
      {message}
      <button onClick={this.onClick}>+1</button>
    </>
  )
}
//也可以直接这样
render() {
  return (
    <>
      {this.state.n % 2 === 0 ? <div>偶数</div> :<div>奇数</div>}
    //{this.state.n % 2 === 0 && <div>偶数</div>}
      <button onClick={this.onClick}>+1</button>
    </>
  )
}
```
**2.render中循环的写法**

render里面不能直接写for循环，需要用数组，因为for循环没有返回值，所以只会循环一次。

一般会用map实现循环。

**循环方法:1' 用变量  2' 用map**

```js
1' 用变量
render() {
  let result = []
  for (let i = 0; i < this.state.array.length; i++) {
    result.push(this.state.array[i])
  }
  return result
}
2' 用array.map
render() {//给每个元素包个span
  return this.state.array.map(n => <span key={n}> {n} </span>)
}
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e60828af50de4ba0b7f3bf22d48461ea~tplv-k3u1fbpfcp-zoom-1.image)

**⚠️注意:所有的循环都需要绑定key**

### 四.生命周期之 componentDidMount 
> 当组件已挂载(已出现在页面)就会执行这个函数

**获取当前div的宽度**

**1' 用id**

```js
import React from "react"

class App extends React.PureComponent {
    constructor(props) {
        super(props)
        this.state = {
            n: 1,
            width: undefined
        }
    }
    onClick = () => {
        this.setState(state => ({
            n: state.n + 1
        }))
    }
    componentDidMount() {
        const div = document.getElementById("xxx")
        const { width } = div.getBoundingClientRect()//析构写法
        this.setState({ width })
    }
    render() {
        return (
            <div id="xxx">当前div宽度是：{this.state.width}</div>
        )
    }
}
export default App
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b3db4c74f3184ec4a9f958299e69b645~tplv-k3u1fbpfcp-zoom-1.image)

在元素插入页面后执行代码，这些代码依赖DOM。如果你想获取div的高度，就最好在`componentDidMount`里写。

**2' 除了用id还可以用Ref**

React提供了一种更方便的方式来**获取div**

🔍[react ref](https://react.docschina.org/docs/refs-and-the-dom.html#creating-refs)

```js
class App extends React.PureComponent {
    divRef = undefined //先声明，告诉后来的程序员说，我会动态创建divRef的东西。直接写不利于阅读。
    constructor(props) {
        super(props)
        this.state = {
            n: 1,
            width: undefined
        }
        const divRef = React.createRef //第1步.创建引用
    }
    onClick = () => {
        this.setState(state => ({
            n: state.n + 1
        }))
    }
    componentDidMount() {//第3步.使用this.divRef获取div
        const div = this.divRef.current
        const { width } = div.getBoundingClientRect()
        this.setState({ width })
    }
    render() {
        return (//第2步.将创建的引用放到div上
            <div ref="{this.divRef}">当前div宽度是:{this.state.width}</div>
        )
    }
}
```
好处:不用担心div会冲突，因为用的是内部属性。

componentDidMount还可以发起"加载数据的AJAX请求",**官方推荐**写在这里。比如我要获取当前用户信息，既可以在`constructor`里写，也可以在`componentDidMount`里写，但是**官方推荐**写到`componentDidMount`。

**首次渲染一定会执行此钩子。**

**`componentDidMount(){}`没有参数，要获取什么东西只能通过this取。**

### 五.生命周期之 componentDidUpdate()
> 当组件更新之后就会执行该函数,首次渲染不会执行此钩子。

**1.在视图更新后执行代码。首次渲染不会执行此钩子，因为没有更新任何东西。**

2.componentDidUpdate()可以发起AJAX请求，用于[更新数据](https://zh-hans.reactjs.org/docs/react-component.html#componentdidupdate)
**经验:** 很少在componentDidUpdate发请求，一般都是在componentDidMount里。

3.如果在这个钩子里面再去setState可能会引起无限循环。
因为它们两个会互相调用，除非做一些条件判断。比如说，n是奇数时才会调用componentDidUpdate

4.如果阻止更新UI，componentDidUpdate返回false,则不触发此钩子。

5.[参数](https://zh-hans.reactjs.org/docs/react-component.html#componentdidupdate)
```js
componentDidUpdate(prevProps, prevState, snapshot)
```

### 六.生命周期之 componentWillUnmount()
> 当组件将要卸载/死亡时就会执行该函数

1.组件从页面中移除并且从内存里干掉，才会触发并执行componentWillUnmount
**移除页面**就是从页面跑到内存里面。**销毁**就是从内存里干掉它。

2.unmount过的组件不能再吃unmount

**举例**

1.如果你在挂载时监听了window scroll事件，那你应该在组件要死时取消监听，因为如果你不取消那你监听有什么用呢。

2.如果你在挂载时创建了计时器Timer，那么组件要死时就要删掉。

3.如果你在挂载时创建了AJAX请求，那么你就要在componentWillUnmount里取消请求。

所有你发起的那些会长期有效的东西，在你死后你都得取消。但实际上绝大部分的前端都不会做上面的3件事情。因为浪费的只是用户的内存,这就是前端页面越来越占内存的原因，大家都只用内存不清除内存。

**总结:** componentWillUnmount钩子是用来消除你之前做的一些可能会产生后果的一些事情，比如scroll、Timer、AJAX。

**原则:** 谁污染谁治理。

**分阶段看钩子执行顺序:**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc6459945f6d4a5892bd778c97b6ef6b~tplv-k3u1fbpfcp-zoom-1.image)

能用函数组件就不用class组件，能用`React.PureComponent`就不用`React.Component`