> 从零开始手写redux

### 全局state的读写
**例1**.使用Context读写appState
```js
import React, { useState, useContext } from "react"
import ReactDOM from 'react-dom'
import './style.css'

const appContext = React.createContext(null)
const App = () => {
  const [appState, setAppState] = useState({
    user: { name: 'frank', age: 18 }
  })
  const contextValue = { appState, setAppState } //封装成一个对象，塞给上下文
  return (
    <appContext.Provider value={contextValue}>
      <大儿子 />
      <二儿子 />
      <小儿子 />
    </appContext.Provider >
  )
}
const 大儿子 = () => <section>大儿子<User /></section>
const 二儿子 = () => <section>二儿子<UserModifier /></section>
const 小儿子 = () => <section>小儿子</section>

const User = () => { //获取信息
  const contextValue = useContext(appContext)
  return <div> User:{contextValue.appState.user.name} </div>
}
const UserModifier = () => { //修改信息
  const { appState, setAppState } = useContext(appContext);
  const onChange = (e) => {
    appState.user.name = e.target.value
    setAppState({ ...appState })
  }
  return <div>
    <input value={appState.user.name} onChange={onChange} />
  </div>
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9986cbf3832b43f1b91a59cb986e0a83~tplv-k3u1fbpfcp-zoom-1.image)

**1.setState特点**

如果你给的对象是原来那个对象的引用，只要是引用相同，那我直接不变，setState时是不会生效的。

**解决方法:** 创建一个新的对象，新的对象包含了appState的所有属性
```js
const contextValue = { appState, setAppState } 

const UserModifier = () => { //修改
  const contextValue = useContext(appContext);
  onChange(e){
    contextValue.appState.user.name = e.target.value
      contextValue.setAppState({...contextValue.appState})
   // contextValue.setAppState(contextValue.appState) 错误
  } 
  ... 
}
```

### reducer的来历
> reducer是如何规范state的创建流程的。

目前创建的state特别不规范，它直接修改了原始的state，

我们应该提供一个函数来帮它创建新的state。
```js
const reducer = (state, actionType, actionData) => {
//const createNewState = (state, actionType, actionData) => {
  if (actionType === 'updateUser') {
    return {   //新的对象
      ...state,//首先会拷贝user之外的属性
      user: {  //创建user
        ...state.user,
        ...actionData 
      }
    }
  } else {
    return state
  }
}
const UserModifier = () => {
  const { appState, setAppState } = useContext(appContext);
  const onChange = (e) => {
    setAppState(createNewState(appState, 'updateUser', 
      {name: e.target.value}))
  //setAppState(reducer(appState, 'updateUser', {name: e.target.value}))    
  }
  return <div>
    <input value={appState.user.name} onChange={onChange} />
  </div>
}
```
return的是全新的对象，规范了创建state的过程。

**现在把函数名`createNewState`改为`reducer`，这就是reducer的由来。**

**不同的是reducer只接受2个参数**:将`actionType, actionData`统一成一个叫**action**的东西，它接受一个**type和payload**`{type,payload}`，payload比较麻烦，其实就是data的意思。
```js
const reducer = (state, { type, payload }) => {
  if (type === 'updateUser') {
    return {
      ...state,
      user: {
        ...state.user,
        ...payload
      }
    }
  } else {
    return state
  }
}
const UserModifier = () => {
  const { appState, setAppState } = useContext(appContext);
  const onChange = (e) => { 
    setAppState(reducer(appState, { type: 'updateUser',
      payload: { name: e.target.value } }))
  }
  return <div>
    <input value={appState.user.name} onChange={onChange} />
  </div>
}
```
**reducer就是用来规范state创建流程的一个函数。**

### dispatch的来历
> 如何使用dispatch来规范setState的流程。

先看下我们是如何setState的:

当我们改name、age、group时，每次都要写前面3个单词，既然每次setState都要复制这3份，不如干脆写一个函数 **`dispatch`**。

```js
setAppState(reducer(appState, { type: 'updateUser', payload: { name: e.target.value } }))
setAppState(reducer(appState, { type: 'updateUser', payload: { age: e.target.value } }))
setAppState(reducer(appState, { type: 'updateGroup', payload: { group: e.target.value } }))
```
`dispatch`接受一个action,但由于dispatch没法访问到`setAppState`和`appState`,所以**不能直接使用**dispatch。

因为`setAppState`和`appState`这2个变量在useContext里，React规定只能在组件内部使用Hooks，那把dispacth也放到组件`UserModifier`里？不行，因为dispatch我是要抽到我的组件`redux`里的。

那咋办？

其实之所以出现这种窘境是因为我们把state放到了context，那不放context行不？不行，那样改动太大了。那怎样才能**让dispatch可以访问到state和setState**

**解决思路**:用一个组件包住dispatch,然后再把dispatch给这个组件。
```js
const 二儿子 = () => <section>二儿子<Wrapper /></section>

const Wrapper = () => {
  const { appState, setAppState } = useContext(appContext);
  const dispatch = (action) => {
    setAppState(reducer(appState, action))
  }
  return <UserModifier dispatch={dispatch} state={appState} />
}
const UserModifier = ({ dispatch, state }) => {
  const onChange = (e) => {
    dispatch({ type: 'updateUser', payload: {name: e.target.value}})
  }
  return <div>
    <input value={state.user.name} onChange={onChange} />
  </div>
}
```
**解析**

由于坏继母从中作梗，遗嘱`dispatch`不能直接交给亲女儿`context`，于是富商用画`Wrapper`将`dispatch`包了起来，接着将自己的遗嘱`dispatch`交给了聪明的金毛`<UserModifier />`，光有指纹不够还需要印章`state`，最后金毛`UserModifier`通过`props`成功拿到了物证`dispatch`和`state`

**总结**

目前我们完成了`UserModifier`一个组件的封装，它可以通过props来读写全局数据。但实际上这个功能不是由**redux**实现的，而是由redux的第2个库`React-redux`，其实大家用都是一起用的，所以这里就不做区分了。
直接调dispatch不用再去调那三个了。

**dispatch就是用来规范setState流程的一个函数。**

### connect
> 内容:如何写出一个connect高阶组件

我们将`UserModifier`包装成了`Wrapper`,用的时候一定要使用`<Wrapper />`,因为如果你直接使用`UserModifier`是得不到`dispatch`和`state`的。因此任何一个组件要想读写state都需要封装`<Wrapper />`，那岂不写累死啦

**我们需要声明一个函数来做这件事。**
```js
const 二儿子 = () => <section>二儿子<UserModifier /></section>

const createWrapper = (Component) => { //自动创建Wrapper
  const Wrapper = () => {
    const { appState, setAppState } = useContext(appContext);
    const dispatch = (action) => {
      setAppState(reducer(appState, action))
    }
    return <Component dispatch={dispatch} state={appState} />
  }
  return Wrapper
}

const UserModifier = createWrapper(({ dispatch, state }) => {
  const onChange = (e) => {
   dispatch({ type: 'updateUser', payload: { name: e.target.value } })
  }
  return <div>
    <input value={state.user.name} onChange={onChange} />
  </div>
})
```
现在我们创建组件的过程就是在组件的外面写上一个**createWrapper**
它的作用就是将这个组件与全局状态连接起来，所以redux叫做`connect`。`connect`是由redux的第二个库React-redux提供的。将`createWrapper`改为`connect`

Wrapper声明也只用了一次，不用特意声明，直接return这个函数就好了。
```js
const connect = (Component) => {
  return () => {
    const { appState, setAppState } = useContext(appContext);
    const dispatch = (action) => {
      setAppState(reducer(appState, action))
    }
    return <Component dispatch={dispatch} state={appState} />
  }
}

const UserModifier = connect(({ dispatch, state }) => {
  const onChange = (e) => {
    dispatch({ type:'updateUser', payload:{ name:e.target.value }})
  }
  return <div>
    <input value={state.user.name} onChange={onChange} />
  </div>
})
```

### 如何传组件的属性
**如果我在`<UserModifier />`上加一个属性`x={'x'}`**,
那就得把这个x传给哪个真正的组件，让它从这里可以拿到x。

**怎么传？**
需要让**返回组件的props全部都放到`<Component />`上面**
```js
const 二儿子 = () => <section>二儿子<UserModifier x={'x'} /></section>

const connect = (Component) => {
 return (props) => {
  ...
  return <Component {...props} dispatch={dispatch} state={appState} />
 }
}
const UserModifier = connect(({ dispatch, state, x }) => { ... })
```

### 如何传组件的子内容
**方法:** 一般是通过children拿的，就是因为我们把这个`props`全部透传给了`{...props}`,所以props children也传进去了。
```js
const 二儿子 = () => <section>二儿子<UserModifier> 内容 </UserModifier></section>

const connect = (Component) => {
  return (props) => {
   ...
   return <Component {...props} dispatch={dispatch} state={appState} />
  }
}
const UserModifier = connect(({ dispatch, state, children }) => {
    const onChange = (e) => {
        dispatch({ type: 'updateUser', payload: { name: e.target.value } })
    }
    return <div>
        {children}
        <input value={state.user.name} onChange={onChange} />
    </div>
})
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a163f8d61b1c494e8195039fea8b956d~tplv-k3u1fbpfcp-zoom-1.image)

**connect的作用:让组件与全局状态连接起来。**

**什么是高阶组件?**

**一个函数它接受一个组件，返回一个新的组件，那么这就叫做高阶组件。**

实际上就是类比于高阶函数的这么一个说法。

如果你看redux提供的connect，你会发现它接受的参数比我还多，这个我们后面再讲。

### 利用connect减少render
**目前的问题:太多render了**

给每个组件打印出一句话，看下效果

```js
const 大儿子 = () => {
    console.log('大儿子执行了', +Math.random())
    return <section>大儿子<User /></section>
}
const 二儿子 = () => {
    console.log('二儿子执行了', +Math.random())
    return <section>二儿子<UserModifier /></section>
}
const 小儿子 = () => {
    console.log('小儿子执行了', +Math.random())
    return <section>小儿子</section>
}
...
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/92d6d27700124c9590a98471fb7724e5~tplv-k3u1fbpfcp-zoom-1.image)

**产生的原因:**
当我们改变input值时会调用`setAppState`,setAppState是通过`dispatch`调用到的，dispatch最终会setAppState，它是从Context来的，Context最初是在这里拿到的。
```js
const App = () => {
  const [appState, setAppState] = useState({ user: { name: 'frank', age: 18 }})
}
...
const connect = (Component) => {
  return () => {
    const { appState, setAppState } = useContext(appContext);
    const dispatch = (action) => {
      setAppState(reducer(appState, action))
    }
    return <Component dispatch={dispatch} state={appState} />
  }
}
```
根据React组件规定，只要你调用了这个组件的setState，这个组件一定会再次执行。只要你给setState传的是新对象，App就一定会重新执行，只要App重新执行，里面的子组件都会重新执行。

**如何阻止多余的render？**

使用useMemo，但不推荐这种写法，太麻烦。

```js
//阻止小儿子重复render
const App = () => {
    ...
  const x = useMemo(() => {
    return <小儿子 />
  }, [])

  const contextValue = { appState, setAppState }
    return (
      <appContext.Provider value={contextValue}>
        <大儿子 />
        <二儿子 />
        {x}
      </appContext.Provider >
  )
}
```
多个render其实对性能的影响并不大，每一个render影响都很小，它不会去修改页面上的任何东西。只是会执行下函数，但如果这种小的性能影响积累太多也不好，所以尽量不要让它积累的太多，一两个可以放过，但是几千个是不能放过的，不推荐。

**redux能不能给我设计出一种机制，只有用到User的地方，在User变化时才重新执行?**

1.首先需要把setAppState干掉，因为它必然会导致后面的这些东西重新执行。

把它放到store对象上，怎么用呢？

state默认存储初始信息，这里先写死，到时候再去弄。

setState接受一个新的state，去修改store的state。

```js
const store = {
  state: { user: { name: 'frank', age: 18 } },
  setState(newState) { store.state = newState }
}
```
那appState就用state代替了，setAppState就用setState代替了。

直接传store，可以用上下文拿到state、setState。

```js
const appContext = React.createContext(null)
const store = {
  state: {
    user: { name: 'frank', age: 18 }
  },
  setState(newState) {
    console.log(newState)
    store.state = newState
  }
}
const App = () => {
  return (
    <appContext.Provider value={store}>
      <大儿子 />
      <二儿子 />
      <小儿子 />
    </appContext.Provider >
  )
}
const 大儿子 = () => {
  console.log('大儿子执行了', +Math.random())
  return <section>大儿子<User /></section>
}
const 二儿子 = () => {
  console.log('二儿子执行了', +Math.random())
  return <section>二儿子<UserModifier /></section>
}
const 小儿子 = () => {
  console.log('小儿子执行了', +Math.random())
  return <section>小儿子</section>
}

const User = () => {
  console.log('User执行了', +Math.random())
  const { state } = useContext(appContext)
  return <div> User:{state.user.name} </div>
}
const reducer = (state, { type, payload }) => {
  if (type === 'updateUser') {
    return {
      ...state,
      user: {
        ...state.user,
        ...payload
      }
    }
  } else {
    return state
  }
}

const connect = (Component) => {
  return () => {
    const { state, setState } = useContext(appContext);
    const dispatch = (action) => {
      setState(reducer(state, action))
    }
    return <Component dispatch={dispatch} state={state} />
  }
}

const UserModifier = connect(({ dispatch, state }) => {
  const onChange = (e) => {
    dispatch({ type: 'updateUser', payload: { name: e.target.value } })
  }
  return <div>
    <input value={state.user.name} onChange={onChange} />
  </div>
})
```
展示没问题，但是修改有问题，改不了，其实已经修改了。
数据没有变是因为**没有调用App的setState。**

**那怎么办？你得让它去刷新这些组件啊**

connect里面有个Wrapper，虽然没有名字，但是它就是Wrapper，给Wrapper声明一个刷新的东西

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a92ee945ebf146fabd3a30303ab950c6~tplv-k3u1fbpfcp-zoom-1.image)

```js
//这个update只能实现一个组件的渲染
const connect = (Component) => {
  return () => {
    const { state, setState } = useContext(appContext);
    const [, update] = useState({}) 
    const dispatch = (action) => {
      setState(reducer(state, action))
      update({}) //调用新的update对象
    }
    return <Component dispatch={dispatch} state={state} />
  }
}
```

**什么时候想update呢？**

调用update，传一个对象

每次我调用了`setState`之后，我去调用update**新的对象**，因为新的对象跟旧的对象永远`不相等`，所以它就会强制刷新Wrapper组件，Wrapper组件一刷新，里面的组件也会自动刷新，这样就能保证被connect的组件才会刷新。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aeb5b037c0424d6a9a863bf3bc054f4e~tplv-k3u1fbpfcp-zoom-1.image)

**为什么输入时会显示?**

因为当我输入"阿斯顿"时它会把我最新的值setState到store上去，然后store的值就变了。

这个时候再调用update`update({})`就会导致当前Wrapper组件重新渲染，重新渲染它就从上下文里又拿到新的state，然后新的state再传给我们的`<Component />`组件,这个组件的state就变了。

**为什么上面的没变？**

因为上面的组件没有被调用它的dispatch,每一个组件封装了一个它自己的dispatch，所以一个组件只能更新自己，那怎么办？我们需要让每一个组件的dispatch都知道state变化了，只能去**订阅变化**。

**步骤**

**1.添加subscribe事件监听**

每一个组件订阅state的变化。订阅时得告诉我订阅之后你要做什么，所以提供一个函数。

这个函数之后会调用，但我现在不调用，现在需要把这个函数放到我的一个队列`listeners`里。
把所有监听者放进来,`listeners`默认是数组。
```js
const store = {
  state: {user: { name: 'frank', age: 18 }},
  setState(newState) {store.state = newState},
  listeners: [], //订阅者
  subscribe(fn) {
    store.listeners.push(fn) //订阅
      return () => { //取消订阅
        const index = store.listeners.indexOf(fn)
          store.listeners.splice(index)
      }
  }
}
```
订阅之后你可能会去取消订阅，所以要返回一个取消订阅的函数，这个函数可以把fn从`listeners`里面去掉，分两步走。先找到fn的index,然后把它删掉。

**2.执行订阅**

什么时候去执行订阅的内容？

在我变动的时候，**一旦我被setState了**，我就得告诉我的订阅者说我已经变了。
遍历订阅者，对于每个订阅者，它给我提供了fn,我把fn调用一下。
为了方便，我把最新的state给它，这样就不要去取了，不给也行。

**只要我setState了**，就会通知所有订阅者说，我变了，然后执行它给我的那个回调。然后把我最新的state给它
```js
const store = {
  state: {user: { name: 'frank', age: 18 }},
  setState(newState) {
    store.state = newState
    store.listeners.map(fn=>fn(store.state))//把最新的state给它
  },
  ...
 }
```
这边每一个dispatch的上面就可以订阅下。
订阅也是在store上，我们也可以通过上下文来。
我们只订阅一次，不希望每次都订阅，所以我们要把它包裹在useEffect里。

**User需要connect下,connect后就可以拿到state和dispatch**,当然它不写也会拿到，只要connect就会拿到了，那它就不需要拿到上下文了，直接state，一定要connect。
```js
const connect = (Component) => {
    return () => {
        const { state, setState } = useContext(appContext);
        const [, update] = useState({})
        useEffect(() => {
            store.subscribe(() => {
                update({})
            })
        }, [])
        const dispatch = (action) => {
            setState(reducer(state, action))
        }
        return <Component dispatch={dispatch} state={state} />
    }
}

const App = () => {    
  const User = connect(({ state, dispatch }) => {
    return <div> User:{state.user.name} </div>
  })
}
```

### Redux乍现
**新建文件redux.js**,把redux有关的移到文件里。

只把组件留下，剩下的都可以移走。
```js
index.js
import ReactDOM from 'react-dom'
import './style.css'
import { appContext, store, connect } from "./redux"

const App = () => {
  return (
    <appContext.Provider value={store}>
      <大儿子 />
      <二儿子 />
      <小儿子 />
    </appContext.Provider >
  )
}
const 大儿子 = () => {
  console.log('大儿子执行了', +Math.random())
  return <section>大儿子<User /></section>
}
const 二儿子 = () => {
  console.log('二儿子执行了', +Math.random())
  return <section>二儿子<UserModifier /></section>
}
const 小儿子 = () => {
  console.log('小儿子执行了', +Math.random())
  return <section>小儿子</section>
}

const User = connect(({ state, dispatch }) => {
  console.log("user 执行了")
  return <div> User:{state.user.name} </div>
})

const UserModifier = connect(({ dispatch, state }) => {
  const onChange = (e) => {
    dispatch({ type: 'updateUser', 
      payload: { name: e.target.value } })
  }
  return <div>
    <input value={state.user.name} onChange={onChange} />
  </div>
})
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
```js
redux.js
import React, { useState, useContext, useEffect } from "react"

export const store = {
  state: {
    user: { name: 'frank', age: 18 }
  },
  setState(newState) {
    console.log(newState)
    store.state = newState
    store.listeners.map(fn => fn(store.state))
  },
  listeners: [],
  subscribe(fn) {
    store.listeners.push(fn) //订阅
    return () => { //取消订阅
      const index = store.listeners.indexOf(fn)
      store.listeners.splice(index)
    }
  }
}

const reducer = (state, { type, payload }) => {
  if (type === 'updateUser') {
    return {
      ...state,
      user: {
        ...state.user,
        ...payload
      }
    }
  } else {
    return state
  }
}

export const connect = (Component) => {
  return () => {
    const { state, setState } = useContext(appContext);
    const [, update] = useState({})
    useEffect(()=> { store.subscribe(()=> { update({})}) }, [])
      const dispatch = (action) => {
        setState(reducer(state, action))
      }
      return <Component dispatch={dispatch} state={state} />
  }
}
export const appContext = React.createContext(null)
```
