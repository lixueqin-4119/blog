**主要内容:使用类优化代码**\
1.引入类(class)\
2.引入继承(class继承class)\
3.初识Vue

### 抽象思维四:事不过三

同样的代码写三遍，就应该抽成一个函数\
同样的属性写三遍，就应该做成共用属性(原型或类)\
同样的原型写三遍，就应该用继承\
**代价**\
有的时候会造成继承层级太深，无法一下看懂代码\
可以通过写文档、画类图解决

**步骤**\
对比MVC的app1.js 和 app2.js，属性几乎一模一样。\
何不把公共的属性抽成公共属性/原型，在js里公共属性就是原型。\
它们两个v的属性一模一样，可以抽成原型。\
它们两个c的公共属性抽到公共属性里。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b3ab2f12f7eb4391aeb1a4b8dc5b942c~tplv-k3u1fbpfcp-watermark.image?)
### 第一部分:先抽m

**步骤**\
**(1)新建目录base存放基础代码，新建文件Model.js**(js中的类都大写)。

```js
class Model {
}
```

**(2)抽离共用的**

> 分析里面的属性或接口是共用的还是独享的。\
> app1的data 与app2的data完全没有关系，是独享的不能抽。4个函数可以抽。\
> 可以用原型的思想或类的思想，这里**用类的思想**。

```js
//(2)所有的model都有增删改查4个属性，不需要实现。
class Model {
  constructor(options) { //data初始化时给我
    this.data = options.data
  }
  create() {
    console?.error?.("你还没实现create")//兼容IE
  }
  delete() {
    console?.error?.("你还没实现delete")
  }
  update() {
    console?.error?.("你还没实现update")
  }
  get() {
    console?.error?.("你还没实现get")
  }
}
export default Model
```

(3)使用

```js
app1.js
import Model from './base/Model.js' //.js可加可不加

const m = new Model({ //使用
  data: {
    n: parseInt(localStorage.getItem('n'))
  }
})
m.update = (data) => {
  Object.assign(m.data, data)
  eventBus.trigger("m:updated")
  localStorage.setItem('n', m.data.n)
}
//console.dir(m)  构造函数或类
//m.create() 还没实现所有报错
```

函数都在原型里，只有data在当前对象本身上。注:无需逗号


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8849ae252dec4fc8844816f8f76f85d0~tplv-k3u1fbpfcp-watermark.image?)

#### 最终版本

> 将调用也移到构造函数里,就是将`m.update`移到data的后面。

```js
Model.js
constructor(options) {
/*this.data = options.data
  this.create = options.create
  this.delete = options.delete
  this.update = options.update
  this.get = options.get
*///简化为
  ['data','create','delete','update','get'].forEach((key) =>{ //1'遍历的写法
    if (key in options) {
      this[key] = options[key]
    }
  })  
}
  create() {
    console?.error?.("你还没实现create")
  }
  delete() {
    console?.error?.("你还没实现delete")
  }
  update() {
    console?.error?.("你还没实现update")
  }
  get() {
    console?.error?.("你还没实现get")
  }
}
export default Model

app1.js
import Model from './base/Model.js'
const m = new Model({
    data: {
        n: parseInt(localStorage.getItem('n'))
    },
    update(data) { //不推荐用箭头函数
        Object.assign(m.data, data)
        eventBus.trigger("m:updated")
        localStorage.setItem('n', m.data.n)
    }
})
//console.dir(m)

app2.js
import Model from './base/Model.js'
const m = new Model({
  data: {
    index: parseInt(localStorage.getItem(localKey) || 0)
  },
  update(data) {
    Object.assign(m.data, data)
    eventBus.trigger("m:updated")
    localStorage.setItem('index', m.data.index)
  }
})
```

在面向对象里箭头函数非常容易出错，所以不推荐用。\
函数都在当前对象本身上。


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2a2d48110b2544e395b6a09f8884de22~tplv-k3u1fbpfcp-watermark.image?)

**接收参数的2种处理方法：**\
1' 当参数多时用遍历的写法\
2'不遍历的写法:结构化

### 第二部分:简化v、c

> 由于v和c有重要联系，所以前端决定把v和c共用成一个对象,把v拷到c。vue.js认为，这是前端库主要处理视图，所以应该叫view。\
> 注意:要合并v、c一开始就要把它合并

**步骤**\
**(1)新建View.js (js中的类都大写)。**

```js
class View {
}
```

**(2)抽离共用的**

> 分析里面的属性或方法是共用的还是独享的。\
> el是独享的每个人都不一样，所以只能写到构造函数里不能写原型里。html可能是函数也可能是string，2种类型，所以不共用。init是共用的，constructor就是初始化。render无法抽离因为目前没使用虚拟DOM\
> 继续用类的思想。

**(3)使用**

> 先合并v、c,改名view。然后再抽离。\
> [抽离前代码](https://github.com/lixueqin-4119/js-demo48/blob/master/src/app2.js)\
> [抽离后代码](https://github.com/lixueqin-4119/js-demo49/blob/master/src/app1.js)

```js
//(2)抽离共用的
import $ from 'jquery'
class View {//初始化写到constructor上
  constructor({ el, html, render }) {//2'不遍历的写法:结构化
    this.el = $(el)
    this.html = html
    this.render = render
  }
}
export default View

//(3)合并v、c,抽离前
app1.js
import View from './base/View.js'
const view = {
  el: null,
  html: `...`,
  init(container) {
    view.el = $(container) //接收的el会被作为容器
    view.render(m.data.n)
    view.autoBindEvents()
    eventBus.on('m:updated', () => {
      view.render(m.data.n)
    })
  },
  render(n) {
    if (view.el.children.length !== 0) view.el.empty()
    $(view.html.replace('{{n}}', n)).prependTo(view.el)
  },
  ...
export default view
app2.js
import Model from './base/Model.js'
import View from './base/View.js'
//方法:把v拷到c,2个init合并成1个
...
export default init //导出初始化方法
//(3)抽离后

app1.js
app2.js
```

**优化eventBus**\
既然所有的View、M都要用eventBus，那我们可以把eventBus收拢放一个地方。\
**步骤**\
新建文件EventBus.js\
之前我们用的是jquery的eventBus，实际上应该自己写。

```js
import $ from "jquery"

class EventBus {
  constructor() {
    this._eventBus = $(window)
  }
  trigger(eventName, data) {
    return this._eventBus.trigger(eventName, data)
  }
  on(eventName, fn) {
    return this._eventBus.on(eventName, fn)
  }
  off(eventName, fn) {
    return this._eventBus.off(eventName, fn)
  }
}
export default EventBus
```

使用

```js
app1.js
import EventBus from './base/EventBus'

const eventBus = new EventBus()
eventBus.trigger()
app2.js  ...
```

**代码明显变多了，这样写到底有什么好处？**\
优点：这样写eventBus可以随时改变eventBus的实现。现在是用jquery实现的，如果哪天突然不用jquery了，直接在这个模块(EventBus.js)里把jquery删掉即可。

### [继承EventBus](https://github.com/lixueqin-4119/js-demo49/blob/master/src/base/EventBus.js)

> 把m和v都继承EventBus，这样就能直接做到**少一层概念**`m.trigger、v.on`不需要再`m.eventBus.trigger、v.eventBus.on`\
> 这也是目前所有主流库的选择,比如DOM就是这样做的。把eventBus放到所有东西的最上面，因为事件这个东西太常用了。\
> `console.dir(document.body)`
>![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a206b717b95244d789539bc8f8171d3f~tplv-k3u1fbpfcp-watermark.image?)
> eventTarget实际上就是eventBus，换了个名字而已。这就是所有的DOM元素都能触发事件、监听事件的原因，所有DOM的类的类的...最后倒数第2层都是eventBus。


**步骤**\
1.先继承\
**如果你继承了一个类就必须在初始化constructor里调用类的父类的初始化。**

```js
Model.js
import EventBus from "./EventBus"
class Model extends EventBus {
  constructor(options) {
    super() //调用EventBus#constructor()
  }
}
View.js ...
```

**2.后使用**

```js
app1.js
 m.trigger("m:updated")
 //继承后就不需要再给eventBus赋值了
View.js
this.on() 
app2.js ...
```

**js代码不能以[]开头(重要)**\
JS要么每句话加";" ,要么不能以[]开头,二选一。



![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/28d1555937b1485ebaaa7f226d46312b~tplv-k3u1fbpfcp-watermark.image?)

### 初识Vue

由于目前我们的MVC不够完善，所以选择用vue来演示。\
**parcel快速搭建Vue项目**\
**1.导入Vue**

```js
先安装: yarn add vue@2.6.10 //指定版本
重启: parcel src/index.html
导入:import Vue from 'vue' //app1.js

//console.log(Vue) //函数，导入成功
```

**2.新建Vue**

```js
const init = (el) => {
    new Vue({
        el: el
    })
}
```

**去除警告：**

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f30fbffdff02495a9c7915b79adcacf4~tplv-k3u1fbpfcp-watermark.image?)

**新建Vue时报错，提示要切换为Vue完整版,默认是不完整版。**\
**方法:**  在package.json添加

```js
"alias": {
  "vue$" : "./node_modules/vue/dist/vue.common.js"
}
```

最后重启parcel



**1.Vue认为m也没必要写,那怎么替换呢？**  {{n}} //占位\
**2.Vue如何绑定事件？** @click=""

```js
<button @click="add"> +1 </button>
<button @click="minus"> -1 </button>
<button @click="mul"> *2 </button>
<button @click="divide"> /2 </button>
methods:{  //事件
  add() { this.n +=1 },
  minus() { this.n -= 1 },
  mul() { this.n *= 2 },
  divide() { this.n /= 2 }
}
```

**3.Vue如何保存值？**\
监听事件变化

```js
watch: {
  n() { //当n变化时执行函数 n:function(){}
    localStorage.setItem('n', this.n)
  }
}
```

render不用写。只用绑定事件就好。\
**4.Vue认为m、c都不重要,Vue基本上就到了MVC简化的极限了，这也是Vue这么流行的原因。**

> Vue的属性:el、data、methods、watch、template

```js
const init = (el) => {
  new Vue({
    el: el,
    data: {
      n: parseFloat(localStorage.getItem('n'))//Vue认为m也没必要写
    },
    methods: {
      add() {
        this.n += 1
      },
      ...
  },
    watch: {
      n() { //当n变化时执行函数 n:function(){}
        localStorage.setItem('n', this.n)
      }
    },
    template: `
      <section> //<section>会被替换
        ...
      </section> `
  })
}
```

5.`:class=""` //加前缀:表示class里的是js代码\
render不用写。只用绑定事件就好。

6.表驱动编程是做什么的？

> 假如我们要写一个查询年龄的函数，如果用if else代码量是线性增长的。但用哈希表来记录这些映射关系会简单很多.\
> 数据部分被抽离了，分工明确。函数主体只是去表中查找然后输出结果，这就是表编程的基本思想。

```js
const list={
    "小明":10,
    "小白":14,
}
function age2(name){
    if(name in list){
        console.log(name+"的年龄是"+list[name])
    }else{
        console.log("查无此人")
    }
}
```

**7.如何理解模块化?**\
模块化（modular）编程，是强调将计算机程序的功能分离成独立的、可相互改变的“模块”（module）的软件设计技术，它使得每个模块都包含着执行预期功能的一个唯一方面（aspect）所必需的所有东西。

在一个完整的页面应用中，不同的节点功能，不同的结构可以规划为多个模块，每个模块的实现的方式以及用到的技术大不相同，使用模块化编程可以减小各个模块之间的影响和联系，可以更方便的优化代码和重构代码，提高我们代码的重用性，便于后期维护。

> **前端流行的模块化规范有CommonJS，AMD，CMD，ES6的import／export。**\
> 1.CommonJS的主要实践者就是nodejs,一般对模块输出用module.exports去输出，用require去引入模块，CommonJS一般采用同步加载【require\
> / module.exports / exports】\
> 2.AMD遵从RequireJs规范，推崇依赖前置（提前执行）【require / defined】\
> 3.CMD遵从SeaJs规范，推崇依赖就近（延迟执行）【require / defined】\
> 4.ES6 可静态分析，提前编译，不是在运行时确认【import / export】

## 总结

**Vue思想在MVC思想中的体现**\
**抽象思维1:最小知识原则**

> 模块化使得两者间解耦。每个模块不需要知道对方模块在做什么，甚至两个实现不一样，一个用Vue、一个用React、一个用jquery，模块化把所有的知识降到最小。Vue其实也是一样的，你用Vue后你不需要知道MVC,因为它已经把所有的都简化了。但是如果你知道MVC,在看这些属性时就知道，它是MVC最开始雏形里的哪个东西。\
> el就是View里的容器，data就是Model里的数据,template就是View里的html，如果你没学MVC你就完全不知道为什么会这样。

**抽象思维2:MVC思想**\
虽然MVC思想在Vue里浓缩的只有v了，但思想一直在。只不过当你需求太简单时不会再想着把它分开，但当你复杂的时候你还是得分开。Vue只能满足最基础的需求，当你的应用复杂时MVC就还是MVC,Vue就还是MVC。

```js
const m = {
  get(){ return parseFloat(localStorage.getItem('n')),
  set(n){ localStorage.getItem('n',this.n),
}
new Vue({
  data:{ n:m.get() },
  watch:{
    n:function(){
      m.set(this.n)
    }
  }
})
```

**抽象思维3:表驱动编程**\
Vue里methods、watch就是表，你只要告诉我什么东西变化了做什么，我就能帮你做。因为我把表之外的逻辑都写好了。

**抽象思维4:事不过三原则**

```js
const v=new Vue({ ... })
console.log(v) //打印Vue的实例
```

第1、2层是Vue给它的一些属性，第3层`$on 、$emit 、$off 、$once`emit就是trigger，Vue所有内置的方法都用$开头。

**Vue它也是个eventBus,所以我们可以直接用Vue做eventBus:**

```js
const init = (el) => {

    const eventBus = new Vue()
    //eventBus.$emit() //trigger
    //eventBus.$on()
    //eventBus.$off()
    console.log(eventBus.$emit)
    console.log(eventBus.$on)
    console.log(eventBus.$off)
}    
```

**抽象思维5:俯瞰全局(实现eventBus)**\
把所有的对象看成点\
一个点和一个点怎么通信\
一个点和多个点怎么通信\
多个点和多个点怎么通信\
最终我们找出一个专用的点负责通信，这就是eventBus(事件总线)

**抽象思维6:view=render(data)**\
vue没有体现，但react体现了，react会显示的让你调用render。Vue之所以没有体现是觉得太复杂了它自己给简化了。\
Vue只要发现n变化了就自动帮你`局部`渲染。Vue的更新机制比这个更加精致，只变该变的。\
**测试:**\
在+1按钮上添加`id="xxx"`点击+1后检查id是否存在。如果不见了说明被重新渲染了。\
结果:只会局部渲染`<span>`,id还在。\
说明button从来没有被移除过页面。

**重点**\
1.MVC的概念\
2.eventBus的概念\
3.view=render(data)概念\
4.表驱动编程概念\
5.类什么时候用\
6.继承什么时候用
