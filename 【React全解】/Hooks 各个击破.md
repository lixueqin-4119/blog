---
theme: juejin
---
# Hooks 各个击破
> [React文档](https://zh-hans.reactjs.org/docs/hooks-reference.html#gatsby-focus-wrapper)
> 
> Hooks：useState、useEffect、useLayoutEffect、useContext、useReducer、useMemo、React.memo、callCallback、useRef、useImperativeHandle、自定义Hook、useDebugValue

### useState(最常用)

> 在React的函数组件里，默认只有属性，没有状态。

**1.使用状态**
```js
//数组第1项是读接口，第2项是写接口，初始值0
const [n,setN] = React.useState(0) //数字
const [user,setUser] = React.useState({name:'F'}) //对象
```
**2.注意事项(1):不可局部更新**

更新部分属性时，未更新的属性会消失。

**3.注意事项(2):地址要变**

setState(obj)如果obj对象地址不变，那么React就认为数据没有变化，因此不会帮你改变内容。

**4.useState接受函数**

**5.setState接受函数**

**例1:不可局部更新**

如果state是个对象，能否部分setState?
不行，因为setState不会帮我们合并属性。所以当只更新部分属性时，未更新的属性就会消失。

那怎么解决"未更新的属性会消失"的问题？

**用`...`拷贝之前所有的属性，然后再覆盖属性。**

```js
import React, {useState} from "react";
import ReactDOM from "react-dom";

function App() {
  const [user,setUser] = useState({name:'Frank', age: 18})
  const onClick = ()=>{
    setUser({
      ...user, //拷贝user的所有属性
      name: 'Jack' //覆盖name
    })
  }
  return (
    <div className="App">
      <h1>{user.name}</h1>
      <h2>{user.age}</h2>
      <button onClick={onClick}>Click</button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
**题外话**:`useReducer`也不会合并属性，React新版的所有东西都不会帮你合并，它认为这是你自己要做的事。

**例2.地址要变**

我想把name改下:于是直接修改user.name然后setUser(user)
你会发现改不了，因为你改的是同一个对象，地址是一样的。
React不会看你里面的内容它只看地址，**你不改地址它就不帮你改内容。**

那怎么改地址？

```js
const onClick=()=>{ 
  user.name="小李"
  setUser(user)   
}
const onClick=()=>{ //改地址
  setUser({ //新的对象
    ...user,
    name:"小李"
  })
}
```

**例3.useState接受函数**(很少用)

引用状态，可用函数,但很少会这样写，多算一遍就多算呗。

useState写成函数的**好处**是:减少多余的计算过程，因为JS引擎不会立即执行函数。

```js
function App() {
  const [user,setUser]=useState({name:'Frank', age: 9+9})//引用状态
                    //useState(()=>( {name:'Frank', age: 9+9} ))
  const onClick = ()=>{
    setUser({ ... }) //设置状态
  }
```
**例4.setState接受函数**(推荐优先使用函数)

点击button后你会发现`n=1`而不是2，因为当你setN(n+1)时，n不会变。
不管你做多少次计算，只有最后一次有用。

**解决方法:** 改成函数

```js
function App() {
  const [n, setN] = useState(0)
  const onClick = ()=>{
  //setN(n+1) 第1次计算
  //setN(n+1) 第2次计算，也是最后1次计算
    setN(n => n + 1) //形式化的操作
    setN(n => n + 1)
  }
  return (
    <div className="App">
      <h1>n: {n}</h1>
      <button onClick={onClick}>+2</button>
    </div>
  );
}
```
JS语法有问题:对象必须加()。(JS的bug)

总结:对state进行多次操作时，优先使用函数。
  
### useReducer(最常用)
**useReducer4步走:**

**1.创建初始值initicalState**

```js
const initical = { n:0 }
```
**2.创建所有操作reducer(state,action)**

reducer接受2个参数:**旧的状态state**和**操作的类型action**(一般是类型)，最后返回新的state。

怎么得到新的state？

看下动作的的类型是什么

**规则和useState一样,必须返回新的对象。**(不能直接操作n)

```js
const reducer=(state,action)=>{
  if(action.type==='add'){
    return { n:state.n+1 } //return新对象
  }else if(action.type==='mult'){
    return { n:state.n*2 }
  }else{
    console.log("unknown type")
  }
}
```
**3.传给useReducer,得到读和写API**

(1)需要导入useReducer或者直接使用全称`React.useReducer`

(2)useReducer接收2个参数:**所有操作reducer**和**初始状态initical**

(3)你将得到`读API、写API`写API一般叫dispatch，因为你必须通过reducer才能setState,所以叫dispatch。

```js
import React,{useReducer} from "react"

function App(){
  const [state,dispatch]=useReducer(reducer,initical)
}
```

**拿出属性n的2种方法:** 

1' `{state.n}` 2'`const {n}=state`然后`{n}`

**4.调用 写({type:'操作类型'})**
```js
const onClick=()=>{
  dispatch({
    type:'add' //调用reducer的add操作
  })
}
```
相当于useState,只不过把所有操作聚拢在一个函数里,这样的好处是:调用的代码简短了。

**调用传参:**`+2`时传了参数`number:2`,那么reducer里的`1`就可以变成一个参数。因为dispatch()里传的对象就是action。

```js
if (action.type === "add") {
//return { n: state.n + 1 };
  return { n: state.n + action.number };
}
...
const onClick2 = () => {
//dispatch({type:'add'})
  dispatch({type:'add',number:2}) //里面的对象就是action
}
```
这就是**useReducer**对useState的升级操作,总的来说useReducer是useState的复杂版。好处是用来践行React社区一直推崇的flux/Redux思想。随着hooks的流行这个思想会退化。

**完整代码**
```js
import React, { useState, useReducer } from "react";
import ReactDOM from "react-dom";

const initial = { n: 0};
const reducer = (state, action) => {
  if (action.type === "add") {
    return { n: state.n + action.number };
  } else if (action.type === "multi") {
    return { n: state.n * 2 };
  } else {
    throw new Error("unknown type");
  }
};

function App() {
  const [state, dispatch] = useReducer(reducer, initial);
  const { n } = state;
  const onClick = () => {
    dispatch({ type: "add", number: 1 });
  };
  const onClick2 = () => {
    dispatch({ type: "add", number: 2 });
  };
  return (
    <div className="App">
      <h1>n: {n}</h1>
      <button onClick={onClick}>+1</button>
      <button onClick={onClick2}>+2</button>
    </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
**如何选择 使用useReducer还是useState?**

事不过三原则

如果你发现有几个变量应该放一起(对象里)这时候就用useReducer对对象进行整体的操作。

[useReducer的常用例子](https://codesandbox.io/s/admiring-framework-4by2t)
```js
const initFormData = {
  name: "",
  age: 18,
  nationality: "汉族"
};

function reducer(state, action) {
  switch (action.type) {
    case "patch": //更新
//把第1个对象的所有属性和第2个对象的所有属性全部放到第3个空对象里，这就是更新
      return { ...state, ...action.formData }; 
    case "reset": //重置，返回最开始的对象
      return initFormData;
    default:
      throw new Error("你传的啥 type 呀");
  }
}

function App() {
  const [formData, dispatch] = useReducer(reducer, initFormData);
  // const patch = (key, value)=>{
  //   dispatch({ type: "patch", formData: { [key]: value } })
  // }
  const onSubmit = () => {};
  const onReset = () => {
    dispatch({ type: "reset" });
  };
  return (
    <form onSubmit={onSubmit} onReset={onReset}>
      <div>
        <label>
          姓名
          <input value={formData.name} onChange={e => dispatch(
            {type:"patch", formData:{ name: e.target.value }})
            }
          />
        </label>
      </div>
      <div>
        <label>
          年龄
          <input value={formData.age} onChange={e =>dispatch(
            {type:"patch",formData: { age: e.target.value }})
            }
          />
        </label>
      </div>
      <div>
        <label>
          民族
          <input value={formData.nationality} 
            onChange={e => dispatch({type:"patch",
              formData:{nationality: e.target.value}})
            }
          />
        </label>
      </div>
      <div>
        <button type="submit">提交</button>
        <button type="reset">重置</button>
      </div>
      <hr />
      {JSON.stringify(formData)}
    </form>
  );
}
```
用户一旦输入就会触发onChange事件。用户输入即更新，因为内容不一样了嘛。
每次更新，App都会render遍。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/68972b116ffc45b19a16feb7705dbc54~tplv-k3u1fbpfcp-zoom-1.image)


### 如何用useReducer代替Redux ？
> 前提:你得知道Redux是什么
> 用React的`reducer`+`context`即可代替Redux。

```js
import React, { useReducer, useContext, useEffect } from "react";
import ReactDOM from "react-dom";

const store = { //第1步.将数据集中在一个store对象
  user: null,
  books: null,
  movies: null
};

function reducer(state, action) { //第2步.将所有操作集中在reducer
  switch (action.type) {
    case "setUser":
      return { ...state, user: action.user };
    case "setBooks":
      return { ...state, books: action.books };
    case "setMovies":
      return { ...state, movies: action.movies };
    default:
      throw new Error();
  }
}

const Context = React.createContext(null); //第3步.创建一个Context

function App() {
  const [state, dispatch] = useReducer(reducer, store); //第4步.创建对数据的读写API

  const api = { state, dispatch };
  return (
    <Context.Provider value={api}> //第5步.将创建的"数据的读写API"放到Context
      <User /> //第6步.用Context.Provider将Context提供给所有组件,就是将组件放里面
      <hr />
      <Books />
      <Movies />
    </Context.Provider>
  );
}

function User() {
  const { state, dispatch } = useContext(Context); //第7步.各个组件用useContext获取读写API
  useEffect(() => {
    ajax("/user").then(user => {
      dispatch({ type: "setUser", user: user });
    });
  }, []);
  return (
    <div>
      <h1>个人信息</h1>
      <div>name: {state.user ? state.user.name : ""}</div>
    </div>
  );
}
function Books() {
  const { state, dispatch } = useContext(Context);//第7步.使用useContext获取读写API
  useEffect(() => {
    ajax("/books").then(books => {
      dispatch({ type: "setBooks", books: books });
    });
  }, []);
  return (
    <div>
      <h1>我的书籍</h1>
      <ol>
        {state.books ? state.books.map(book =>
          <li key={book.id}>{book.name}</li>) : "加载中"}
      </ol>
    </div>
  );
}
function Movies() {
  const { state, dispatch } = useContext(Context);//使用useContext获取读写API
  useEffect(() => {
    ajax("/movies").then(movies => {
      dispatch({ type: "setMovies", movies: movies });
    });
  }, []);
  return (
    <div>
      <h1>我的电影</h1>
      <ol>
        {state.movies ? state.movies.map(movie => 
          <li key={movie.id}>{movie.name}</li>)
          : "加载中"}
      </ol>
    </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

// 帮助函数
// 假 ajax
// 两秒钟后，根据 path 返回一个对象，必定成功不会失败
function ajax(path) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (path === "/user") {
        resolve({
          id: 1,
          name: "Frank"
        });
      } else if (path === "/books") {
        resolve([
          {
            id: 1,
            name: "JavaScript 高级程序设计"
          },
          {
            id: 2,
            name: "JavaScript 精粹"
          }
        ]);
      } else if (path === "/movies") {
        resolve([
          {
            id: 1,
            name: "爱在黎明破晓前"
          },
          {
            id: 2,
            name: "恋恋笔记本"
          }
        ]);
      }
    }, 2000);
  });
}
```
**解析**

**第1步.将数据集中在一个store对象**

```js
const store = { //加载信息
  user:null,
  books:null,
  movies:null
}
```
**第2步.将所有操作集中在reducer**

接收一个旧的状态，给我一个操作，我就可以得到一个新的状态。

**怎么得到新的状态呢？** 

看你操作的类型是什么。

比如说你要**填充user**:你得给我一个user,所以你的action里面要有一个user。我把你给我的user传到store上。

```js
const reducer = (state,action) => { 
  switch(action.type){
    case 'setUser': //填充user
      return {...state,user:action.user};
    case 'setBooks':
      return {...state,books:action.books};
    case 'setMovies':
      return {...state,movies:action.movies};
    default:
      throw new Error();
  }
}
```
**第3步.创建一个Context**

createContext需要自动引入或者直接React.createContext

```js
const Context = React.createContext(null) //初始值一般是null，不传会报错
```
**第4步.创建对数据的读写API**

`useReducer`的第2个参数是初始值。

**`useReducer`一般写在函数里面,只能在函数里面运行**。

```js
const Context = React.createContext(null) 
function App() {
  const [state,dispatch]=useReducer(reducer,store) //(reducer,初始值)
}
//也可以写在外面，不过要在函数里调用。
//function x(){ const [state,dispatch]=useReducer(reducer,store)  }
//function App() {
//  x()
//}
```
**第5步.将创建的"数据的读写API"放到Context**

方法:把`<div>`删了改为`<Context.Provider>`，value就是把读写API`[state,dispatch]`赋值给`Context.Provider`。

语法:`value={JS}`告诉React里面是JS。

`{state:state,dispatch:dispatch}`这个{}里才是对象,对象的state就是上面的state变量，对象的dispatch就是上面的dispatch变量。

```js
const Context = React.createContext(null)
function App() {
  const [state,dispatch]=useReducer(reducer,store)
  return (
    <Context.Provider value = {{state:state,dispatch:dispatch}}>
      <User />
      <hr />
      <Books />
      <Movies />
    </Context.Provider>
  )
```
`value={{state:state,dispatch:dispatch}}`ES6可以直接缩写成`value={{state,dispatch}}`

**第6步.用Context.Provider将Context提供给所有组件**

就是将组件`<User />、<Books />、<Movies />`放到`<Context.Provider>`里面
```js
return (
    <Context.Provider value = {{state:state,dispatch:dispatch}}>
      <User />
      <hr />
      <Books />
      <Movies />
    </Context.Provider>
  )
```
**第7步.各个组件用useContext获取读写API**
> 现在各个组件就可以使用读写API了

useContext接收的值就是你创建的`Context`
```js
import React, { useReducer, useContext, useEffect } from "react";

function User(){
  const {state,dispatch} = useContext(Context) //注意这里是{}
  ajax("/user").then((user)=>{ //初始化user:调用ajax()
  //dispatch触发"setUser",user的值就是得到的user,形参占位
    dispatch({type:"setUser",user:user}) 
  })
  return (
    <div>
      <h1>个人信息</h1>
        //展示
        <div>name:{state.user ? state.user.name : ""}</div>
    </div>
  )
}

由谁来设置一开始的值呢？
一开始是null,所以name是空的。
用假的ajax获取用户信息,很简单的promise。
// 帮助函数,假的ajax
// 2s后,根据 path 返回一个对象，必定成功不会失败
function ajax(path) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (path === "/user") {
        resolve({
          id: 1,
          name: "Frank"
        });
      } else if (path === "/books") {
        resolve([
          {
            id: 1,
            name: "JavaScript 高级程序设计"
          },
          {
            id: 2,
            name: "JavaScript 精粹"
          }
        ]);
      } else if (path === "/movies") {
        resolve([
          {
            id: 1,
            name: "爱在黎明破晓前"
          },
          {
            id: 2,
            name: "恋恋笔记本"
          }
        ]);
      }
    }, 2000);
  });
}
```
**知识点**

**1.useEffect设置只在第一次渲染时执行某函数**

每次User刷新时,代码setState`dispatch`就会再执行一遍并重复请求ajax。
怎样减少请求ajax,设置只在第一次进入页面时请求？

**借助useEffect**

需要自动引入或者直接React.useEffect

useEffect需要传个函数,当第2个参数是空数组时，那么前面的函数就**只会**在第一次渲染时执行，之后永远不会执行。例子:
```js
React.useEffect(()=>{},[])
```
**项目代码**

```js
import React, { useReducer, useContext, useEffect } from "react";

function User() {
  const { state , dispatch } = react.useContext(Context)
  useEffect(()=>{
    ajax("/user").then((user)=>{
      dispatch({type:"setUser",user:user})
    })
  },[])
}
```
请求user数据`ajax("/user")`，得到user数据后(这里的user是形参)，用setUser把数据`user:user`放到上下文`Context`里面。然后它自己就会刷新了，不用手动调自己刷新，因为React知道state变了就要变了。

**2.加载中怎么做的？**

 如果movies存在就展示n个`<li>`,如果不存在就展示`"加载中"`
```js
function Movies() {
  const { state, dispatch } = useContext(Context);//使用useContext获取读写API
  useEffect(() => {
    ajax("/movies").then(movies => {
      dispatch({ type: "setMovies", movies: movies });
    });
  }, []);
  return (
    <div>
      <h1>我的电影</h1>
      <ol>
        {state.movies ? state.movies.map(movie => 
          <li key={movie.id}>{movie.name}</li>)
          : "加载中"}
      </ol>
    </div>
  );
}
```
### 总结
**用useReducer代替Redux，是如何实现代替的？**

1.redux有个store，我们对象代替了`const store={}`

2.redux有个reducer，我们用函数代替了`function reducer(state,action){}`

3.redux它可以在任意地方使用，我们用Context代替了`const Context=React.createContext(null)`

非常好的代替redux的方法。

### 如何模块化？
> 模块化不属于React内容,属于基础知识。
> 
> 模块就是文件，文件就是模块，**文件名小写，组件名大写**。

**步骤**

我们有3个组件，把这3个组件分别放到不同的组件

**第1步.新建目录components**

**第2步.新建组件文件**

(1)有几个组件就建几个文件:分别新建文件`user.js、books.js、movies.js`

然后把各个部分相关的代码分别剪切进去，并导出。

**第3步.对于共用的函数，也要新建文件，单独拎出来。**

(1)Context是组件共用的，所以要新建文件`Context.js`,把相关代码剪切出来，并导出。

**同样公共的ajax也是如此**

出了组件放components里，其它都放外面(src)

新建文件`ajax.js`，把相关代码剪切出来，并导出。

**(2)使用Context、ajax**

```js
要想使用Context、ajax,那每个组件都需要import
import Context from '../Context.js' //导入Context`
import ajax from '../ajax' //导入ajax
```
**第4步.使用模块和公共的函数**

index.js

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/723abf0f8a2140f8b40f996b88ca661d~tplv-k3u1fbpfcp-zoom-1.image)


### 细化reducer

**假设我的组件有很多，那reducer的switch的case岂不是要写累死了？**

**第一部分.先重构代码**

> 变成对象之后就好弄了，因为对象很好合并,函数难合并(基础知识)。

```js
function reducer(state, action) {
  switch (action.type) {
    case "setUser":
      return { ...state, user: action.user };
    case "setBooks":
      return { ...state, books: action.books };
    case "setMovies":
      return { ...state, movies: action.movies };
    default:
      throw new Error();
  }
}
```
**重构后**

```js
const obj = {
  setUser:(state, action)=>{
    return { ...state, user: action.user };
  },
//removeUser:()=>{},
  setBooks:(state, action)=>{
      return { ...state, books: action.books };
  },
//deleteBook:()=>{},
  setMovies:(state, action)=>{
     return { ...state, movies: action.movies };
  },
//deleteMovie:()=>{}
}

//使用obj
function reducer(state, action) {
  const fn = obj[action.type] //判空
  if(fn){
    fn(state,action)
  }else{
    throw new Error('你传的什么鬼 type')
  }
}
```
分开后就好弄了，setUser是user模块的reducer、setBooks是books模块的reducer、setMovies是movies模块的reducer。

假如还有其他的，比如除了setUser可能还有removeUser,除了setBooks可能还有deleteBook,除了setMovies可能还有deleteMovie...

**那怎么对这6个函数分成3个模块呢？**

**第二部分.细化reducer**(模块化)

**1.新建目录reducers**

**2.新建子文件**

(1)新建`user_reducer.js、books_reducer.js、movies_reducer.js`

(2)然后将代码剪切放到`export default{ ... }`里

**3.使用**

```js
import userReducer from './reducers/user_reducer'
import booksReducer from './reducers/books_reducer'
import moviesReducer from './reducers/movies_reducer'

const obj = {
  ...userReducer, //把userReducer里的2个函数地址拷过来
  ...booksReducer,
  ...moviesReducer
}
```
### useContext(常用)
**概念**

上下文就是你运行一个程序所需要知道的所有其它变量(全局变量)。

**全局变量**是全局的**上下文**，所有变量都可以访问它。

**上下文**是局部的**全局变量**，context只在`<C.Provider>`内有用，出了这个范围的组件是用不到这个contextde。

**使用方法**:

一.使用`C = createContext(initical)`创建上下文

二.使用`<C.provider value={}>`初始化并圈定作用域

三.在作用域内的**组件里使用**`useContext(C)`来获取上下文

```js
import React, { createContext } from "react";
const C = createContext(null)
 
<C.Provider value={}>
  ...
</C.Provider>
```
value的初始值可以是任何值，一般我们会给一个读写接口.

`<C.Provider>`内的所有组件都可以用上下文C
```js
import React, { createContext, useState, useContext } from "react";
import ReactDOM from "react-dom";

const C = createContext(null); 
function App() {
  console.log("App 执行了");
  const [n, setN] = useState(0); 
  return (
    <C.Provider value={{ n, setN }}> 
      <div className="App">
        <Baba />
      </div>
    </C.Provider>
  );
}

function Baba() {
  const { n, setN } = useContext(C); //使用context
  return (
    <div>
      我是爸爸 n: {n} <Child />
    </div>
  );
}
function Child() {
  const { n, setN } = useContext(C); //使用context
  const onClick = () => {
    setN(i => i + 1);
  };
  return (
    <div>
      我是儿子 我得到的 n: {n}
      <button onClick={onClick}>+1</button>
    </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
`+1`操作的不是本身的state，而是从App那里得到的读、写接口。
App也可以不用state，用reducer:`  const [n, setN] = useState(0);`，context不管你用啥，它只是告诉你`n、setN`可以共享给你的子代的任何组件的，范围就是由`<C.Provider>`圈定的。

**useContext注意事项**

**不是响应式的**

你在一个模块将C里面的值改变，另一个模块不会感知到这个变化。

更新的机制并不是响应式的，而是重新渲染的过程。

比如，当我们点击`+1`时:setN去通知`useState`,useState重新渲染App,发现n变了，于是问里面的组件`<Baba />`有没有用到n?没有，就继续问`<Child />`有没有用到n?用到了，这时候儿子就知道要刷新了，是一个从上而下逐级通知的过程，并不是响应式的过程。

**Vue3**是你改n时，它就知道n变了，于是它就找谁用到了n,它就把谁直接改变了。它不会从上而下整体过一遍，没有这么复杂，因为它是一个响应式的过程。

**总结:** useContext的更新机制式是自顶向下，逐级更新数据。
而不是监听这个数据变化，直接通知对应的组件。
 
### useEffect ＆ useLayoutEffect

### useEffect副作用

对环境的改变即为副作用，如修改document.title

但我们不一定非要把副作用放在useEffect里

useEffect API名字叫的不好，建议理解成afterRender，每次render后就会调用的一个函数。

**用途:** 它可以代替之前的3种钩子:出生、更新、死亡

1.作为`componentDidMount`使用，[]作第2个参数

2.作为`componentDidUpdate`使用，可指定依赖

3.作为`componentWillUnmount`使用，通过return

以上三种用途可同时存在

**特点**

**如果同时存在多个useEffect,会按从上倒下的顺序执行。**

**如何使用**
```js
import React, { useState,useEffect } from "react";
import ReactDOM from "react-dom";

function App() {
  const [n, setN] = useState(0);
  const onclick=()=>{
    setN(i => i+1)
  }

  useEffect(()=>{
    console.log("第一次渲染后执行这句话")
  },[])
  useEffect(()=>{
    console.log("每次都会执行这句话，update")
  })
  useEffect(()=>{
    console.log("只有当n变了才会执行这句话")//监听某个值变化时执行，包含第一次
  },[n])
  useEffect(()=>{
    if(n !== 0){
      console.log("n变化时会执行这句话，剔除第一次")//默认包含第1次，要想排除第1次可以判断下
    }
  },[n])
  //第一次进来时使实现setInterval，每秒打印一个hi
  //当组件消失时，把定时器关掉，不然会一直打印hi
  //告诉React return一个函数:当组件挂掉时要执行的代码
  afterRender(()=>{
    const id=setInterval(() => {
      console.log("hi")
    }, 1000);
    return ()=>{
      window.clearInterval(id)
    }
  })

  return (
      <div>
        n:{n}
        <button onClick={onclick}>+1</button>
      </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
如果你只是改变自己的状态就不是副作用，如果改变环境或者全局变量就是副作用。

**注意:**

1.当第2个参数是`[]`时，表示只会在**第一次渲染后**执行前面的函数。

2.当不写第2个参数时，表示每次update都会执行前面的函数。

3.当第2个参数是`[n]`时，表示只会在**某个值变化**(n)时才会去执行前面的函数，**包含第一次**。

要想剔除第一次可以，可以加个判断。

4.加return死亡时执行

如果我这个组件要挂了，我这个组件正要离开页面，一般在使用router时会经常去用。

比如，一开始是第1个页面，点了按钮后会跳到第2个页面，那么第1个页面的所有组件都挂掉了。

挂掉的时候你可能需要做一些清理动作。**用return**，return一个函数:函数里面是当组件挂掉时要执行的代码。

这样就不会造成内存泄露或者是不必要的代码。

### useLayoutEffect

例子：一开始是value:0,然后迅速变成value:1000

```js
import React, { useState, useEffect } from "react";
import ReactDOM from "react-dom";

const BlinkyRender = () => {
  const [value, setValue] = useState(0);

  useEffect(() => {
    document.querySelector('#x').innerText = `value: 1000`
  }, [value]);

  return (
    <div id="x" onClick={() => setValue(0)}>value: {value}</div>
  );
};

ReactDOM.render(
  <BlinkyRender />,
  document.querySelector("#root")
);
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aaafbb1c1c2d4459bccbc61ff39e0793~tplv-k3u1fbpfcp-zoom-1.image)

**useEffect在浏览器渲染完成后执行:** 一开始是value是0,然后迅速变成1000,中间闪烁了下，有闪烁过程。

如果我们改变useEffect的执行顺序,**在浏览器渲染前执行**,会有什么效果？
**没有闪烁过程**

代码
```js
import React, {useState, useRef, useLayoutEffect, useEffect} from "react";
import ReactDOM from "react-dom";

function App() {
  const [n, setN] = useState(0)
  const time = useRef(null)
  const onClick = ()=>{
    setN(i=>i+1) 
    time.current = performance.now()
  }
  useLayoutEffect(()=>{ // 改成 useEffect 试试
    if(time.current){
      console.log(performance.now() - time.current)
    }
  })
  return (
    <div className="App">
      <h1>n: {n}</h1>
      <button onClick={onClick}>Click</button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
useLayoutEffect总是比useEffect先执行。用useEffect有闪烁，用useLayoutEffect没有闪烁。

**那是不是应该多用useLayoutEffect？**

不是，因为大部分时候不会去改变DOM，不用截胡。

因为用户想看的就是外观，本来只需要1ms的，现在加了几句话变成3ms了，影响用户体验。

所以从经验上来说，我们更希望将useEffect放到浏览器改变外观之后，所以优先使用useEffect。

**useEffect和useLayoutEffect的本质区别:**

useEffect在浏览器渲染完成后执行,useLayoutEffect在浏览器渲染完成前执行。

**总结:**

**优先使用useEffect**，除非不能满足你的需求再使用useLayoutEffect。
虽然useLayoutEffect的性能更好，优先级更高，但是会影响用户看到画面变换的时间，得不偿失。

**代码佐证时间差别**:从setN到副作用开始执行，中间有多久？

**结果:** useLayoutEffect是0.3ms,useEffect是0.8ms，相差0.5ms。
如果你改变的外观越多，时间就越多，呈线性的。

```js
import React, {useState, useRef, useLayoutEffect, useEffect} from "react";
import ReactDOM from "react-dom";

function App() {
  const [n, setN] = useState(0)
  const time = useRef(null)
  const onClick = ()=>{
    setN(i=>i+1) //打点一:setN后马上打点
    time.current = performance.now() //beforeRender
  }
  useLayoutEffect(()=>{ // 改成 useEffect 试试
  //afterRender
    if(time.current){
      console.log(performance.now() - time.current)
    }
  })
  return (
    <div className="App">
      <h1>n: {n}</h1>
      <button onClick={onClick}>Click</button>
    </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
**知识点:**`performance.now()`是全局对象，用来打印当前的时间

**特点**

**1.useLayoutEffect总是比useEffect先执行。**

下面的代码打印2和3，再打印1。
```js
 useEffect(()=>{ 
   if(time.current){ console.log("1") },[])
 }
 useLayoutEffect(()=>{ 
   if(time.current){ console.log("2") },[])
 }
 useLayoutEffect(()=>{ 
   if(time.current){ console.log("3") },[])
 }  
```
**2.useLayoutEffect里的任务最好影响了Layout**

如果没有改变屏幕外观Layout，就没必要放浏览器渲染前，占时间。

**经验:** 为了用户体验，优先使用useEffect(优先渲染)

### useMemo & useCallback
### useMemo(最常用)
> 要理解`React.useMemo`需要先了解`React.memo`。
> 
> useCallback是useMemo的语法糖。


**React.memo**
```js
import React from "react";
import ReactDOM from "react-dom";

function App() {
  const [n, setN] = React.useState(0);
  const [m, setM] = React.useState(0);
  const onClick = () => {
    setN(n + 1);
  };

  return (
    <div className="App">
      <div>
        <button onClick={onClick}>update n {n}</button>
      </div>
      <Child data={m}/>
   // <Child2 data={m}/> 优化版
    </div>
  );
* [ ] }

function Child(props) {
  console.log("child 执行了");
  console.log('假设这里有大量代码')
  return <div>child: {props.data}</div>;
}
const Child2 = React.memo(Child);//接收Child组件

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/61c1588742294f1cb759f7751fb2d9f6~tplv-k3u1fbpfcp-zoom-1.image)

点击button时`n`会变，那child会再次执行吗？
child会再次执行。child只依赖m,初始值为0，既然参数不变为什么还会再执行呢，不应该执行的。

使用`React.memo`把child封装下,Child2是Child的优化版,它会只在它的props变化时渲染，代码`<Child2 data={m}/>`

现在点击button后，2个log就再也不会执行了。除了第一次渲染时会执行console，之后再也不会执行。除非当m第一次渲染时才会执行，因为m的数据变了，这就是React.memo的好处。

**React.memo使得一个组件只有在它的props变化时，它才会再执行一遍并且再次渲染**

**Child组件还可以优化：**

```js
const Child = React.memo(props=>{
  console.log("child 执行了");
  console.log('假设这里有大量代码')
  return <div> child:{props.data} </dic>
})
```

**但是有个bug**

例子:假设onClick支持onClick事件，它希望别人给它传个onClick监听，在点击div时，就会调用`props.onClick`。给Child2传个onClick。

```js
function App() {
  console.log("App 执行了")
  const [n, setN] = React.useState(0);
  const [m, setM] = React.useState(0);
  const onClick = () => { setN(n + 1); };
  
  const onClickChild=()=>{} //这句话重新执行
  return (
    <div className="App">
      <div>
        <button onClick={onClick}>update n {n}</button>
      </div>
       <Child2 data={m} onClick={onClickChild}/>
    </div>
  );
}

function Child(props) {
  console.log("child 执行了");
  console.log('假设这里有大量代码')
  return <div onClick = {props.onClick}>child: {props.data}</div>;
}
const Child2 = React.memo(Child);
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
Child2是优化过后的函数，理论上来说，只要m和onClickChild不变，它就不需要重新执行。比如我更新n，它应该不需要重新执行。

测试下:**Child2竟然执行了，为什么呢？**

因为当我点击`n+1`时，App会重新执行，`const onClickChild=()=>{}`这句话也会重新执行。之前是一个空函数，现在又是另一个空函数，2个不同的空函数就代表onClick变了。

**那为什么n可以呢？**

因为当你写m=0时，第一次的0和第二次的0都是数值，数值是相等的。但是函数是个对象，第一、二次的空函数的地址是不相等的，**这就是值与引用的区别**。

**那怎么解决这个问题呢？**

我不希望用户在更新n时，由于函数的更新而去渲染自己。

**用useMemo，useMemo可以实现函数的重用。**

方法:useMemo接受一个函数，这个函数的返回值就是你要缓存的东西。
```js
function App() {
  console.log("App 执行了")
  const [n, setN] = React.useState(0);
  const [m, setM] = React.useState(0);
  const onClick = () => { setN(n + 1); };
  
  const onClickChild = useMemo(()=>{ 
    return ()=>{} //复用
  },[m])
  //const onClickChild=()=>{} 
  return (
    <div className="App">
      <div>
        <button onClick={onClick}>update n {n}</button>
      </div>
       <Child2 data={m} onClick={onClickChild}/>
    </div>
  );
}

function Child(props) {
  console.log("child 执行了");
  console.log('假设这里有大量代码')
  return <div onClick = {props.onClick}>child: {props.data}</div>;
}
const Child2 = React.memo(Child);
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/07f4e46a705d49fb8679ea2e3213cd4b~tplv-k3u1fbpfcp-zoom-1.image)

App执行了，child没执行。因为函数已经被我们复用，只有在m变化时，你再重新给我生成一个，因为有可能这个函数用到了m。useMemo用来缓存一些，你希望在2次新旧组件迭代的时候，希望用上次的值，这个值就是一个函数。

**总结**

我们在使用React时经常发现有多余的render,比如说n变了，但是依赖m的组件却自动刷新了，为了解决这个问题可以使用`React.memo`,这个memo可以做到如果props不变就没有必要再执行了。但它有个bug，就算我2次用到的是空函数/函数，由于我的App重新渲染了，所以这个函数的地址就变了,是一个新的空函数。这就导致可props本质上还是变了，变了就会一秒破功。新旧函数虽然功能一样，都是地址不一样。我们可以使用`React.useMemo`

**useMemo特点**

1.**第一个参数**一定是函数`（）= value`,不接受参数。

2.**第二个参数**是数组

3.只有当依赖变化时，才会计算出新的value。如果依赖不变，那么就重用之前的value

这不就是Vue2的computed吗？

我这个值是根据计算得出来的，而且我会缓存使用之前的值。

**注意**

如果你的value是个函数，那么你就要写成useMemo( ()=> (x)=> console.log(x))

这是一个返回函数的函数，很难用，于是就有了`useCallback`。

#### useCallback(最常用)
**用法**

直接写你return的函数就行了。

```js
useCallback(x=>log(x),[m])等价于
useMemo(()=> x=> log(x),[m])
```
优化技巧2
```js
const onClickChild = useMemo(()=>{ 
    return ()=>{
      console.log(m)
    } 
},[m])

//useCallback语法糖
const onClickChild =useCallback(()=>{ console.log(m) },[m])
```
**优化技巧1**

用useMemo使得一些函数被重用，这样就不至于去更新你已经用React.memo优化过的组件,一般这2个是一起用的，先memo再useMemo。

**优化技巧2**

如果你觉得useMemo太难用，可以用useCallback代替。

### useRef & forwardRef & useImperativeHandle
### useRef(常用)
 > forwardRef、useImperativeHandle跟useRef有非常大的关系

```js
import React,{useRef} from "react"
import ReactDOM from "react-dom"
 
//window.count = 0;
function App() {
  console.log("App 执行了");
 const count=useRef(0) //current是随着App render不会变的量
  useEffect(()=>{
    count.current +=1
    console.log(count)
  })
//window.count +=1
  const [n, setN] = useState(0);
  const onClick = () => {
    setN(n + 1);
  };
  return (
    <div className="App">
      <button onClick={onClick}>update n {n}</button>
    </div>
  );
}
```
**`useRef`+`useEffect`实现`count +=1`操作:**

全局变量`window.count`可记录render的次数。但是全局变量有个坏处，变量名容易冲突。

这时我们可以用useRef。

每次更新完后用useEffect对`conut.current`进行操作。

**conut规定:** 如果你要对count进行操作的话，必须要用`conut.current`，因为current才是它真正的值。

在我们不停的渲染中，count始终不会变化，每一次得到的都是同一个count，count的值被记录在useRef对应的一个对象上，这个对象跟App一一对应。

**为什么需要current？**

App每次渲染都会得到一个count。

**为了保证2次useRef是同一样的值(只有引用能做到)**

新旧组件引用的对象必须是同一个对象，否则就会出问题。对象地址是同一个，只是值改变了。

如果没有current你改的就是对象本身。

```js
const count=useRef({current:0}) //一开始不是对象，这里假设它就是一个对象
count.current +=1
```
**总结:**

目前为止，我们已经学了3个关于"**是否要变化**"的hook。

**1.useState/useReducer**

它们两个每次的n都会变化，n每次变

**2.useMemo/useCallback**

只在依赖m，[m]变的时候fn才会变，有条件的改变

**3.useRef**

永远不变

**延伸**

[Vue3的ref](https://vuejs.org/guide/extras/composition-api-faq.html#what-is-composition-api)就是抄袭React的ref,但是有一点不一样:

如果你对Vue的ref进行改变,UI会自动变化，不需要手动刷新。但是React不会自动变化。

**例子**:点击button后，虽然useRef改变了，但是UI不会自动变化。
```js
function App() {
//console.log("App 执行了");
  const [n, setN] = useState(0);
//const [_, set_] = useState(null);
  const count = useRef(0);
  const onClick2 = () => {
    count.current +=1
  //set_(Math.random);
    console.log(count.current);
  };
  useEffect(() => {
    console.log(count.current);
  });
  return (
    <div className="App">
      <div>
        <button onClick={onClick2}> update count{count.current} </button>
      </div>
    </div>
  );
}
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b99ab42abed44c58a198c9fe528a16dc~tplv-k3u1fbpfcp-zoom-1.image)

**要想刷新UI只需要调用setState下并手动set:**
```js
const [_,set_]=React.useState(null) //调用useState
//手动set,只要这次值跟上次不一样UI就会更新
const onClick2 = ()=>{
  count.current +=1
  set_(Math.random())
  coneolr.log(count.current)
}
```
Vue3的思路就是，你不需要写`set_(Math.random())`，我发现你对current变更就会自动更新UI。

**对比**

React的理念是UI=f(data),你要想**变化时自动render**就自己加，监听ref,当ref.current变化时，调用setX即可。

**1.useRef**

初始化:const count=useRef(0)

读取:count.current

**2.Vue3**

初始化:const count=ref(0)

读取:count.value

不同点:当count.value变化时，Vue3会自动render

### forwardRef
 > forwardRef跟useRef有非常大的关系

**例1.为什么要用forwardRef**

原因:props无法传递ref属性

```js
import React, { useRef } from "react";
import ReactDOM from "react-dom";
function App() {
  const buttonRef = useRef(null);
  return (
    <div className="App">
      <Button2 ref={buttonRef}>按钮</Button2>
      {/* 看浏览器控制台的报错 */}
    </div>
  );
}

const Button2 = props => {
  console.log(props)
  return <button className="red" {...props} />;
};
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
**知识点**

1.用`buttonRef`引用到`Button2`对应的DOM对象，这样我就不需要用jQuery去找了。

```js
相当于:
const button =document.querySelector("#x")
<Button2 id="x">
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/58d29e4bd399473ca676d5d66a18ee6b~tplv-k3u1fbpfcp-zoom-1.image)

**error**:函数组件不能接受refs,只有类组件才能接受refs,你应该用`forwardRef`

**log下props**:只把按钮传过去了，ref没有传，这就是报错的原因。

 ![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/864aa6c9efa74118a3ea29836afd7111~tplv-k3u1fbpfcp-zoom-1.image)

你给我的ref我根本读不到引用，那我怎么把`<button>`给你啊？应该用`forwardRef`。

**如何使用`React.forwardRef`**

1.`Button3`先用forwardRef包装Button2,把外边给你的ref转发给你的第二个参数，这样你就可以使用refl了。

2.`Button2`添加第二个参数ref

3.使用ref

**例2.实现ref的传递**

```js
import React, { useRef } from "react";
import ReactDOM from "react-dom";
function App() {
  const buttonRef = useRef(null);
  return (
    <div className="App">
      <Button3 ref={buttonRef}>按钮</Button2>
    </div>
  );
}

const Button2 = (props, ref) => { //2.添加ref
  console.log(props);
  console.log(ref)
  return <button className="red" ref={ref} {...props} />;//3.使用ref
};
const Button3 = React.forwardRef(Button2); //1.用forwardRef包装Button2

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
这样改就没有任何问题了，同样props里还是没有ref,但是ref是可以包含到外面给我传进来的button ref的。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/77e4400776ee4156939d17655ffe6a76~tplv-k3u1fbpfcp-zoom-1.image)

**总结:** 如果你的函数组件(Button2)，想要接收别人`App`传来的ref参数，你必须把自己用`React.forwardRef`包起来。想用ref就必须要用`React.forwardRef`，仅限函数组件，class组件是默认可以用的。

优化代码
```js
const Button3 = React.forwardRef((props, ref) => {
  console.log(props);
  console.log(ref)
  return <button className="red" ref={ref} {...props} />;
})
```
**例3.2次ref传递得到button的引用**

通过ref引用到里面的button需要做两次传递:

buttonRef第一次通过forwardRef传给了Button2,Button2得到ref后传递给了button。

```js
function App() {
//MovableButton就是对Button2的一个包装
  const MovableButton = movable(Button2);
  const buttonRef = useRef(null);
  useEffect(() => {
    console.log(buttonRef.curent);
  });
  return (
    <div className="App">
      <MovableButton name="email" ref={buttonRef}>//通过ref引用到里面的button
        按钮
      </MovableButton>
    </div>
  );
}
const Button2 = React.forwardRef((props, ref) => {
  return <button ref={ref} {...props} />;
});

// 仅用于实验目的，不要在公司代码中使用
function movable(Component) { //可以移动的组件
  function Component2(props, ref) { //接收组件1Component，返回组件2Component2
    console.log(props, ref);
    const [position, setPosition] = useState([0, 0]);
    const lastPosition = useRef(null);
    const onMouseDown = e => {
      lastPosition.current = [e.clientX, e.clientY];
    };
    const onMouseMove = e => {
      if (lastPosition.current) {
        const x = e.clientX - lastPosition.current[0];
        const y = e.clientY - lastPosition.current[1];
        setPosition([position[0] + x, position[1] + y]);
        lastPosition.current = [e.clientX, e.clientY];
      }
    };
    const onMouseUp = () => {
      lastPosition.current = null;
    };
    return (
      <div
        className="movable"
        onMouseDown={onMouseDown}
        onMouseMove={onMouseMove}
        onMouseUp={onMouseUp}
        style={{ left: position && position[0], top: position && position[1] }}
      >
        <Component {...props} ref={ref} />
      </div>
    );
  }
  return React.forwardRef(Component2);
}
```
**总结:** 由于props不包含ref,所以需要forwardRef。

为什么props不包含ref呢？因为大部分时候不需要

如果你希望一个组件支持ref属性，那么你就需要用forwardRef把这个函数组件包起来，然后给他增加第二个属性ref。

### useImperativeHandle(用不着)
> useImperativeHandle跟useRef相关的钩子
> 
> 使用一个重要的handle，名字起的稀烂，应该叫setRef

**分析:用于自定义ref的属性**

**例1.不用useImperativeHandle的代码:**

```js
import React, {useRef,useState,useEffect,useImperativeHandle,createRef} from "react";
import ReactDOM from "react-dom";

function App() {
  const buttonRef = useRef(null); //buttonRef就是buttonDOM对象的引用
  useEffect(() => { //渲染之前不存在，只能在渲染之后打
    console.log(buttonRef.current);
  });
  return (
    <div className="App">
      <Button2 ref={buttonRef}>按钮</Button2>
      <button className="close" onClick={() => {
          console.log(buttonRef);
          buttonRef.current.remove();
        }}
      >
        x
      </button>
    </div>
  );
}

const Button2 = React.forwardRef((props, ref) => {
  return <button ref={ref} {...props} />;
});
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/85a92567bf7b49b3b9325fa1e731d5c1~tplv-k3u1fbpfcp-zoom-1.image)

buttonRef就是button DOM对象的引用，打印出来就是个`<button>`
**如果你希望得到的不是`<button>`而是一个你对`<button>`的封装呢？**
这个需求很奇怪，所以大部分时候用不到。

**例2.用了useImperativeHandle的代码:**
```js
function App() {
  const buttonRef = useRef(null);
  useEffect(() => { 
    console.log(buttonRef.current);
  });
  return (
    <div className="App">
      <Button2 ref={buttonRef}>按钮</Button2> //Button2想自定义ref
      <button className="close" onClick={() => {
          console.log(buttonRef);
          buttonRef.current.x();
        }}>
        x
      </button>
    </div>
  );
}

const Button2 = React.forwardRef((props, ref) => {
  const realButton = createRef(null);
//如何自定义ref
  const setRef = useImperativeHandle;
  setRef(ref, () => { //假的ref
    return {
      x: () => {
        realButton.current.remove();
      },
      realButton: realButton //真的ref(也可以给它真正的ref用)
    };
  });
  return <button ref={realButton} {...props} />;
});
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8627be04dba34e37a6209c0dd2c1e063~tplv-k3u1fbpfcp-zoom-1.image)

**ref可以支持自定义**

比如说Button2想自定义ref,不想把button给别人，**那怎么自定义ref呢？**
把ref赋值成一个对象。

ref就是个对象，ref的x就是一个函数，这个函数会去对button进行一些操作。
setRef是个假的ref,把它暴露在外面

我自己使用真的ref`useImperativeHandle`

这样别人引用我时，只能引用到假的setRef

所以这个hook真正意图是对ref进行设置，以达到某种不可告人的目的，这个`useImperativeHandle`几乎不用。

**总结:** 如果一个函数组件暴露了ref在外面，那么你可以自定义这个ref。

### 自定义 Hook 

**例1**.封装数据操作

**步骤**

**1.新建目录hooks,新建文件useList.js** 

```js
useList.js
import { useState, useEffect } from "react";

const useList = () => { 
  const [list, setList] = useState(null); //设置state
  useEffect(() => { 
    ajax("/list").then(list => { 
      setList(list);
    });
  }, []); 
  return {
    list: list, //是同一个对象的引用，把地址传给外面
    setList: setList
  };
};
export default useList;

function ajax() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve([
        { id: 1, name: "Frank" },
        { id: 2, name: "Jack" },
        { id: 3, name: "Alice" },
        { id: 4, name: "Bob" }
      ]);
    }, 2000);
  });
}
```
**useList.js解析**

一开始就请求"/list"数据:得到list之后就setList，setList之后list就会变，引用的人也就知道了。[] 确保只在第一次运行， 把读写接口return出去，引用/调用useList函数时就可以得到读写接口，`list`是同一个对象的引用，把地址传给外面list(index.js的list引用)。

在我调用setList时，我set的虽然是我这个state(useState),但是由于useList是在`App组件`里调用的。所以在使用`useList`时,相当于把代码(useList函数里的代码)拷到App组件里了。所以虽然我的useState不是在App里写的，但是依然不报错，因为我是在这里运行的。

**2.引用useList**

```js
index.js
import React from "react";
import ReactDOM from "react-dom";
import useList from "./hooks/useList";

function App() {
  const { list, setList } = useList();
  return ( //DOM
    <div className="App">
      <h1>List</h1>
      {list ? (
        <ol>
         {list.map(item=> (<li key={item.id}>{item.name}</li>))}
        </ol>
      ) : ("加载中...")}
    </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed9a8a99e1e44bdda84ee4dcd8b6b633~tplv-k3u1fbpfcp-zoom-1.image)

**如何封装？**

1.你(useList.js)不管用到什么hook,你全部都把它写在一个函数(useList)里面:把相关的逻辑都写到一起，最后把你的读接口、写接口暴露出去就行了。

2.然后别人(index.js)就只需要知道你的读接口、写接口，其它的一概不管。

**比如说你有很多数据**
```js
const { list } = useList()
const { user } = useUser()
```
useUser会自己去初始化user,自己去请求user,请求完了自己去setUser。

我这边只需要读user就行了，这就是自定义hook的牛掰之处。

但是你既然可以封装，不妨封装的更厉害一点，不要只有一个读和写，增删改查全部都可以做出来。

比如说，我们对useList做了升级。
```js
import { useState, useEffect } from "react";

const useList = () => {
  const [list, setList] = useState(null);
  useEffect(() => {
    ajax("/list").then(list => {
      setList(list);
    });
  }, []); 
  return {
    list: list, //读接口
    addItem: name => { //增接口
      setList([...list, { id: Math.random(), name: name }]);
    },
    deleteIndex: index => { //删接口
      setList(list.slice(0, index).concat(list.slice(index + 1)));
    }
  };
};
export default useList;

function ajax() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve([
        { id: "1", name: "Frank" },
        { id: "2", name: "Jack" },
        { id: "3", name: "Alice" },
        { id: "4", name: "Bob" }
      ]);
    }, 2000);
  });
}
```
给了一个读接口，用来读list。给了一个增接口，用来添加item。给了一个删接口，用来删除index。

**点按钮就删除:** 当你onClick时，我就直接调用deleteIndex,然后把index传给你`deleteIndex(index)`就删掉了。根本不需要知道list是从哪里请求数据、是怎么删除的、我一概不关心。我只需要得到一个读或者几个写。

```js
index.js
import React, { useRef, useState, useEffect } from "react";
import ReactDOM from "react-dom";
import useList from "./hooks/useList";

function App() {
  const { list, deleteIndex } = useList(); 
//const { list, deleteIndex, addItem} = useList();  //得到一个读或者几个写
  return (
    <div className="App">
      <h1>List</h1>
      {list ? (
        <ol>
          {list.map((item, index) => (
            <li key={item.id}>
              {item.name}
              <button onClick={() => { deleteIndex(index);}} >
                x
              </button>
            </li>
          ))}
        </ol>
      ) : ( "加载中...")}
    </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
**分析**

**1.你甚至还可以在自定义Hook里使用Context**

这样你可以把自定义Hook和useReducer以及useContext结合起来,完全代替了redux。

所以在新版的React里面没有必要再使用redux了。

**2.useState只说了不能在if else里使用，但没说不能在函数里运行**

只要这个函数在函数组件里运行即可

希望大家在React项目中尽量使用自定义Hook,不要再去搞一些useState、useEfect放到这个组件上部，不要出现这种代码。

### [Stale Closure](https://dmitripavlutin.com/react-hooks-stale-closures/)(过时闭包)

用来描述你的函数引用的变量是**之前产生的那个变量**。

**怎么避免呢？**

基本上是通过加个依赖，让它自动刷新，要记得清除旧的计时器。

所以一般来说不用计时器,比较麻烦。

**JS中的Stale Closure**
```js
function createIncrement(i) { 

//每调用一次这个函数，就会对value+i的操作，闭包。
  function increment() { 
    let value = 0;
    value += i;
    console.log(value);
  }
  const message = `Current value is ${value}`;
  function log() {
    console.log(message);
  }
  return [increment, log]; 
}
const [increment, log] = createIncrement(1);//析构函数
increment(); // 1
increment(); // 2
increment(); // 3
// Does not work!
log();       // "Current value is 0"
```
useState里多次讲过，由于每次你在执行函数时都生成了一个message，所以第一次执行message得到1，第二次执行message得到2，第三次执行message得到3。

那你要是初始就把message记住了，那这个message里面的value就是0啊，log就永远只会打0，不会打后面的。因为后面的是由自己的log,那么这个log就叫做过时的log,因为i已经创建了3次，log也创建了3次，但是你却保留的是初始值log，这就导致它过时了。

**怎么解决？**

**每次log前重新去取这个log**
```js
  function log() {
    const message = `Current value is ${value}`;
    console.log(message);
  }
```
不要一开始就记下value,而是在调用log时，用log去取最新的值。
这就是JS中过时闭包的解决方法。

**React中的Stale Closure**

**1' useEffect()**

```js
function WatchCount() {
  const [count, setCount] = useState(0);
  useEffect(function() {
    setInterval(function log() {
      console.log(`Count is: ${count}`);
    }, 2000);
  }, []);//只在第一次设置计时器，所以count是过时的。
  return (
    <div>
      {count}
      <button onClick={() => setCount(count + 1) }>
        Increase
      </button>
    </div>
  );
}
```
**解决方法**:把count放在依赖里，同时把之前的id清掉。

生成了id又把id给clearInterval了，这不就相当于什么都没做嘛？
不是，生成的是最新的id，删掉的是上一次组件消失时的id，调用时机不同。
```js
function WatchCount() {
  const [count, setCount] = useState(0);
  useEffect(function() {
    const id = setInterval(function log() {
      console.log(`Count is: ${count}`);
    }, 2000);
    return function() {
      clearInterval(id);
    }
  }, [count]);
  return (
    <div>
      {count}
      <button onClick={() => setCount(count + 1) }>
        Increase
      </button>
    </div>
  );
}
```
**2' useState()**
```js
function DelayedCount() {
  const [count, setCount] = useState(0);
  function handleClickAsync() {
    setTimeout(function delay() {
      setCount(count + 1);
    }, 1000);
  }
  return (
    <div>
      {count}
      <button onClick={handleClickAsync}>
        Increase async
      </button>
    </div>
  );
}
```
1s后打印count，在这1s之间`count +=1`根本不知道它变了，你用的永远都是旧的count。

**解决方法**:坚持使用函数作为setState的参数。

这样你就不会受制于旧的还是新的，因为你传的是一个动作，这个动作是不关心这个数据当前的值是什么的，不关心你现在是什么值，只关心`+1`。
```js
function DelayedCount() {
  const [count, setCount] = useState(0);
  function handleClickAsync() {
    setTimeout(function delay() {
      setCount(count => count + 1);
    }, 1000);
  }
  function handleClickSync() {
    setCount(count + 1);
  }
  return (
    <div>
      {count}
      <button onClick={handleClickAsync}>Increase async</button>
      <button onClick={handleClickSync}>Increase sync</button>
    </div>
  );
}
```

### 总结

1.useState状态

2.`useEffect`(副作用)就是afterRender

3.`useLayoutEffect`就是比`useEffect`提前一点点。

但是很少用，因为会影响渲染的效率,除非特殊情况才会用。

4.`useContext`上下文，用来把一个`读、写接口`给整个页面用。

5.`useReducer`专门给Redux的用户设计的(能代替Redux的使用)，我们甚至可以不用`useReducer`。

6.`useMemo`(记忆)需要与`React.Memo`配合使用，`useMemo`不好用我们可以升级为更好用的`useCallback`(回调)

7.`useRef`(引用)就是保持一个量不变，关于引用还有个`forwardRef`,`forwardRef`并不是一个Hook,还有个`useImperativeHandle`就是setRef。

就是我支持ref时，可以自定义ref长什么样子，那就使用`useImperativeHandle`。

8.自定义Hook

示例中的`useList`就是自定义Hook,非常好用。

有个默认的自定义Hook`useDebugValue`就是你在debugger时,可以给你的组件加上名字，很少用。
