# 构造选项
> [V2 options文档](https://cn.vuejs.org/v2/api/#%E9%80%89%E9%A1%B9-%E6%95%B0%E6%8D%AE)
> 
>  内容:`new Vue(options)`options取值详解 
> 
> [查看示例代码](https://github.com/lixueqin-4119/vue-demo5/tree/master/src)

### Vue实例
**什么是Vue实例？**

Vue实例就如同jQuery实例:

封装了对DOM的所有操作

封装了对data的所有操作

比如，如果我们用jQuery选择一个元素，会得到一个jQuery的实例(实例就是对象)，而不是得到这个元素。这个对象封装了对那个`<div>`的所有操作。Vue也是一样的，我们要做的就是**用Vue去搞出一个Vue实例**来。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/59abfe9a7aca476cbb49aa90578039be~tplv-k3u1fbpfcp-zoom-1.image)

`new Vue()` 这就是构造一个Vue的实例。

这个实例会根据你给的选项得出一个对象(vm)，vm封装了这个DOM对象以及对应的所有操作，不管是事件绑定还是数据的读写、DOM更新，全部都由vm这个对象负责。你只需要去调用它的API就好了。

原型:`对象.__proto__===其构造函数.prototype`
推出`vm.__proto__===Vue.prototype`

函数也是对象，所以Vue函数对象的内存图如上。

`函数.__proto__===Function.prototype`**推出**`Vue.__proto__===Function.prototype`

**问题一:** 初始化时可以写些什么对象进去(options)？

**问题二:** vm自己有哪些属性？

**问题三:** Vue函数本身有哪些属性?

**问题四:** 每个函数都有个属性叫`prototype`，同时每个对象都有个属性叫`__proto__`。假设`Vue.prototype`对应的对象的地址是`#419`，那请问这个`#419`里面有哪些属性呢？

**问题五:**`Vue.prototype.__proto__= ?`

**Vue实例的作用**

![请添加图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ef9f49e3840540a39eb004882c18d1f7~tplv-k3u1fbpfcp-zoom-1.image)

vm对象封装了对视图的所有操作但不包括ajax,因为ajax是网络层的。

按照ES6的说法，构造函数也可以说是类，所以vm所属的类是Vue。

`new Vue(options)`**options是new Vue的参数，一般称之为选项或构造选项。**

## 一.options里面有什么？
[V2 options文档](https://cn.vuejs.org/v2/api/#%E9%80%89%E9%A1%B9-%E6%95%B0%E6%8D%AE)

**options的五类属性**
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7d492a31980453a8e321f0d54c265d1~tplv-k3u1fbpfcp-zoom-1.image)

**第1类属性 Data:**
```js
data 数据 
props 属性
computed 计算属性 //被计算出来的
methods 方法，用来定义方法的
watch 观察 //当data变化时做某些事情就用watch
propsData //很少用,单元测试会用
```
**方法和函数的区别？**

1.概念:方法是属于面向对象概念，函数属于数学概念。

在面向对象里叫方法,有对象才有方法，方法依附于对象即`对象.方法`，比如说`obj.sayhi()`sayhi就叫方法也是函数，一般叫方法。
如果`sayhi()`这样写就叫函数

在数学里叫函数。

2.都指同一个东西
```js
function(p1,p2){
  return 
}
```
**第2类属性 DOM:**
```js
el 挂载点 //你要用你的模版替换页面上的哪一块，你的挂载点
template //你的HTML内容。着重讲语法v-if、v-for
render  渲染 //⚠️注意template和render只能二选一！
//template是给完整版用的，render是给非完整版用的。一起用必然有一个会失效！
renderError //很少用
```
**第3类属性 生命周期钩子:**

生命周期:Vue组件在页面中插入一个`<div>`监听它的事件，然后用户点击按钮时变化。可以切入的点叫做钩子。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0970384e9c6c497d97926a0053c4f2c1~tplv-k3u1fbpfcp-zoom-1.image)

```js
beforeCreate  创建之前
created  创建之后
beforeMount
mounted 挂在之后
beforeUpdate
updated 更新之后
activated
deactivated
beforeDestroy
destroyed 失败之后
errorCaptured //很少用
```
**第4类属性 资源**
```js
directives 指令
filters 过滤器 //尽量不去用，用methods代替
components 组件 
//如果要在一个文件引用另一个Vue的文件就用组建。Demo.vue就叫组件,英文名叫components。
```

**第5类属性 组合:**
```js
parent //很少用
mixins 混入
extends 扩展
provide 提供
inject 注入
```

## 入门属性
### 1.el 挂载点
> **可以用$mount代替**

你要用你的模版替换页面上的哪一块,可以用$mount代替。组件或者实例的挂载点。
```js
index.html
<div id="app"> {{n}} </div>

main.js
new Vue({ 
  el: '#app',
  render(h) {
    return h(Demo)
  }
})
可以用$mount替代:
new Vue({ 
  render: h => h(Demo)
}).$mount('#app')

//const vm = new Vue({
//  render: h => h(Demo)
//})
//vm.$mount('#app')
```
**总结**

特点1.名字要保持一致

特点2.如果在`<div>`内加内容，那么多半用户是看不见的。js加载完后会把hello干掉。

特点3.可以用$mount替代。挂载基本等同于replace或append

### 2.data 内部数据
> **组件的定义只接受函数。**

用vue完整版来示例
```js
main.js
console.log(window.Vue)
// import Vue from 'vue' 删掉,这次不从npm引入,直接使用全局的Vue
const Vue = window.Vue
Vue.config.productionTip = false

new Vue({ //实例
  data: { //内部数据，只支持函数
    n: 0
  },
  template: `
  <div class="red">
    {{n}}
    <button @click="add">+1</button>
  </div>
  `,
  methods: {
    add() { //add要先定义好，不然会报错
      this.n += 1
    }
  }
}).$mount('#app')
```
**Bug:** Vue的data有bug，后面讲"数据响应式"时会说。

**为什么data必须是函数？**

如果你是个组件，比如Demo组件，引入Demo组件`import Demo from './Demo.vue'`

[Vue文档](https://cn.vuejs.org/v2/api/#data)说如果你的data是写在`.vue`文件中的(也可以认为是Vue组件)
```js
Demo.vue
export default {
    data(){ //vue-loader写文件时，data必须是函数
      return {
        n:0
      }
    },
}
```
Demo实际上是对象，Vue会自动把Demo传给`new Vue(Demo)`

假设如果有两个组件共用内部数据data，当其中一个改变时另一个也会变，因为它们引用的是同一个data。**函数会阻止两个组件共用data的问题。**
```js
main.js
render:h=>h(x,[h(Demo),h(Demo)])
```

### 3.methods 方法
> **事件处理函数或者是普通函数**

add必须写在methods里面，如果写到外面会报错。
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6e3aa17b14a344e78b35305ec8ecb8ba~tplv-k3u1fbpfcp-zoom-1.image)

```js
main.js
//new Vue({  错误
//  add() { this.n += 1 },
//  methods: {
//  }
//})
```
**事件处理函数:** 写到一个@click或keypress或者任何事件作为它的处理函数

**普通函数**method代替filter。
```js
main.js
new Vue({ //实例
  data: {
    n: 0,
    array:[1,2,3,4,5,6,7,8,9]
  },
  template: `
    <div class="red">
      {{n}}
      <button @click="add">+1</button>
      <hr>
      {{filter(array)}} //2'filter()
    </div>
   `,
   methods: {
    add() {
      this.n += 1
    },
  filter(array) {
    return array.filter(i => i % 2 === 0)
    }
//filter() {
  //return this.array.filter(i => i % 2 === 0)
  //}
}).$mount('#app')
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/88dd18790e6540e1a967c39e97282924~tplv-k3u1fbpfcp-zoom-1.image)

**bug：** methods第2种用法，用来主动在模版里调用。这种调用特点是每次渲染都会重新调用。就算毫无意义跟之前是相同的结果它也会执行。

### 4.components 组件
> **使用Vue组件，注意大小写**

如果要在一个文件引用另一个Vue的文件就用组件。Demo.vue就叫组件(components)。

`const vm=new Vue({...})`vm是Vue实例或Vue对象

这个不能叫做组件，它使用"其它的Vue实例"的时候，"其它的Vue实例"才是组件。

#### 如何使用组件？
**首先要创建组件，组件的3种引入形式**

**1' 创建一个.vue文件**(推荐)。

这个文件就是Vue组件，比如`Demo.vue`，然后**引入该文件**。

**使用组件**

说明要用的组件是frank，然后就可以在template里面写frank。
```js
main.js
import Demo from './Demo.vue' //引入Demo文件

new Vue({ //实例
  components: { //说明要用的组件是frank
    frank: Demo //名字:值,Demo组件
  //Demo:  Demo //es6语法可以简写为Demo
  },
  template: `
  <div class="red">
    <frank/>
  </div>
  `,
}).$mount('#app')  

Demo.vue
<template>
    <div class="red">
       fuck
    </div>
</template>
...
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/07059d5e4cda4474a4caa612fb3cad58~tplv-k3u1fbpfcp-zoom-1.image)

优先使用第1种，其它2种不够模块化。

**2' 用js的方式**

不要components，直接声明全局的Demo2。
```js
main.js
Vue.component('Demo2', {
  template: `
    <div> demo2 </div>
  `
})
new Vue({ 
template: `
  <div class="red">
    <Demo2/>
  </div>
  `,
}).$mount('#app')
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9527d0f0f61d4cdc995fe5cef8f3ef0f~tplv-k3u1fbpfcp-zoom-1.image)

你是入口就是实例，被别人用的就是组件。

**3' 前2种的结合**

保留js对象，又保留components
```js
main.js
Vue.component('Demo2', {
  template: `
    <div> demo2 </div>
  `
})
new Vue({ 
  components: {
    fuck: {
      template: `
      <div> demo3 </div>
    `
    }
  },
 template: `
  <div class="red">
     <fuck/>
  </div>
  `,
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d1f24af5608427f9e8a1185dc950457~tplv-k3u1fbpfcp-zoom-1.image)

**fuck也可以有data**
```js
  fuck: {
      data() { //组件data必须用函数
        return { n: 0 }
      },
      template: `
      <div> fuck's n:{{n}} </div>
    `
    }
```
fuck对象里面的写法,跟外面的options是完全一样的。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d32cb72b702c44948109d5454b763e4f~tplv-k3u1fbpfcp-zoom-1.image)

**什么是组件？**

组件:可以组合的物件就叫组件。比如手臂、腿就是人的组件

**组件可以认为是实例中的实例。**

**注意大小写**

1.文件名最好全小写，因为有些古老的操作系统，比如window10可能不能识别大小写，防止2个大小写文件重名。

2.组件首字母最好大写。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fb2a2f5234f34fe5b84fbc4a689b3766~tplv-k3u1fbpfcp-zoom-1.image)


### 5.四个钩子
> 1.created 实例出现在内存中
> 
> 2.mounted 实例出现在页面中
> 
> 3.updated 实例更新了
> 
> 4.destroyed 实例消亡了

**1.2.3.created、mounted、updated**
```js
new Vue({ //实例
  created() {
    //debugger
    console.log("这玩意出现在内存中")
  },
  mounted() {
    //debugger
    console.log("这玩意出现在页面中")
  },
   updated() {
    console.log("更新了") //点击+1按钮后显示更新了
    console.log(this.n) //每次拿到的n都是最新的
  },
}).$mount('#app')
```
可以通过debugger验证实例是否出现在页面:n和button没加载出来说明出现在内存，加载出来证明出现在页面。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c0115025b42f46c394c86a10e62bc319~tplv-k3u1fbpfcp-zoom-1.image)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d2f52de18f524d55bce81977f1710a5b~tplv-k3u1fbpfcp-zoom-1.image)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9d85f3889e174b70a3edc3cc2daed3b5~tplv-k3u1fbpfcp-zoom-1.image)

**4.destroyed 实例消亡了**

**步骤**

逻辑:让一个组件出现又消失

**1.src新建文件`demo2.vue`**。

**把目前的实例变组件:** 把`main.js`中`new Vue({ //实例 })`的实例剪切到`demo2.vue`的`<script>里`。别忘了把template内容也移到`<template>`里。

**2.创建实例**
```js
main.js
import Demo from './demo2.vue'

new Vue({ //实例
  components: { Demo },
  data: { //自己new Vue就不是组件，所以data可以是对象
    visible: true
  },
  template: `
    <div>
      <button @click="toggle">toggle</button>
      <hr>
      <Demo v-if="visible===true"/>
    </div>
  `,
  methods: {
    toggle() {
      this.visible = !this.visible //把visible变为反值，实现按钮的切换
    }
  }
}).$mount('#app')
```
3.监听destroyed
```js
  destroyed(){
    console.log("已经消亡了")
  }
```
每次toggle后n将重新初始化为0。

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-6kq9dGAP-1648651132320)(media/16259421256477/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202022-03-30%20%E4%B8%8B%E5%8D%887.21.06.png)\]](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5d9a45b967a24232aaff93664d27f2f0~tplv-k3u1fbpfcp-zoom-1.image)


**知识点**

**1.渲染页面**
```js
render: h => h(Demo) //更简单
//等价于
components: { Demo },
  template: `
    <Demo/>
  `,
```
**2.`v-if`什么时候出现**
```js
new Vue({ 
  components: { Demo },
  data: { //自己new Vue就不是组件，所有data可以是对象
    visible: true
  },
  template: `
      <Demo v-if="visible===true"/>
  `
}).$mount('#app')
```
**3.实例 VS 组件**

**实例就是main.js,代码特征`new Vue({ })`,data可以是对象、函数。** 实例需要导入组件`demo.vue`实例包含组件，如果实例是爸爸，那组件就是流落在外的儿子。
```js
main.js
import Demo from './demo.vue' //导入组件`demo.vue`

new Vue({ 
  data: { //data可以是对象
    visible: true
  },
}).$mount('#app')
```
**组件就是新建的`demo.vue`,代码特征`3个标签<template>、<script>、<style scoped>`,data必须是函数。** 可以认为是实例中的实例。
```js
demo.vue
//组件
<template> //html
</template>

<script> //js
export default {
 data(){ //vue-loader写文件时，data必须是函数 }
    }
</script>

<style scoped> //css
</style>
```
**4.函数和方法的区别？**

**函数（function）** 是可以执行的javascript代码块，由javascript程序定义或javascript实现预定义。函数可以带有实际参数或者形式参数，用于指定这个函数执行计算要使用的一个或多个值，而且还可以返回值，以表示计算的结果。

**方法（method）** 是通过对象调用的javascript函数。也就是说，方法也是函数，只是比较特殊的函数。假设有一个函数是fn，一个对象是obj，那么就可以定义一个method。方法和对象相关，函数和对象无关。 

**方法和函数大致上是相同的，但有两个主要的不同之处：**

(1)方法中的数据是隐式传递的。

(2)方法可以操作类内部的数据（请记住，对象是类的实例化–类定义了一个数据类型，而对象是该数据类型的一个实例化）   
  
   
### 6.props **外部**数据
> **外部数据是由外部来传值的(值是字符串)，也叫外部属性**
> 
> 1' 传字符串`message="n"` 
> 
> 2' 传变量` :message="n"` 传入this.n数据
>
> 3' 传函数`:fn="add"` 传入this.add函数

#### 1' 传字符串` message="n"`
**步骤**

(1)新建文件demo3.vue

props从外部接收message,这个message会自动绑到this上。Vue允许省掉this。
```js
demo3.vue
<template>
    <div class="red">
        这里是demo3的内部
        {{message}} //{{this.message}}this可省
    </div>
</template>

<script>
  export default {
   //声明:props:[属性名]
     props:['message'] //从外部接收message,这个message会自动绑到this上
  }
</script>
<style scoped>
  .red{ color: red; }
</style>
```
**(2)使用props外部数据**
```js
main.js
import Demo from './demo3.vue'

new Vue({ //实例
components: { Demo },
 template: `
    <div>
      <Demo message="你好 props"/> //传值:在组件后加key value
    </div>
  `,
}).$mount('#app')
```
`message="字符串"`

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/43d3c0999e524686bc469313e7c15aff~tplv-k3u1fbpfcp-zoom-1.image)

#### 2' 传变量` :message="n"` 传入this.n数据

加空格和冒号`" :"`, **注意Demo后有空格！**
```js
main.js
import Demo from './demo3.vue'

new Vue({ //实例
 components: { Demo },
  data: {
    n:0
  },
   template: `
    <div>
      <Demo :message="n"/> <!--传变量(数据) -->
    </div>
  `,
}).$mount('#app')
```
`空格:message="JS(变量)"`

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ce8fae6dec24b3093646547bb829060~tplv-k3u1fbpfcp-zoom-1.image)

#### 3' 传方法(函数)`:fn="add"` 传入this.add函数
**1.添加第2个参数fn**
```js
demo3.vue
<template>
    <div class="red">
        这里是demo3的内部
        {{message}}  
        <button @click="fn">call fn</button>
    </div>
</template>
<script>
export default {
    props:['message','fn']
  //从外部接收message、fn,会自动绑到this上
}
</script>

<style scoped>
.red{ color: red; }
</style>
```
**2.接收方法**
```js
main.js
import Demo from './demo3.vue'

new Vue({ //实例
  components: { Demo },
  data: { //实例的data可以是对象
    visible: true,
    n: 0
  },
   template: `
    <div>
      {{n}}
      <Demo :fn="add"/> <!--传JS变量(数据) --> 
    </div>
  `, 
  methods: {
    add() {
      this.n += 1
       }, 
  }
}).$mount('#app') 
```
`空格:message="JS(方法)"`

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9dd89d9926604d2d96b16560242dcb08~tplv-k3u1fbpfcp-zoom-1.image)

**把n回传给里面的儿子，得到的是最新的n。**
```js
main.js
template: `
  <div>
    {{n}}
    <Demo :message="n" :fn="add"/>
  </div> 
`,
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/549c2750f93647c3bacbc284d8c99c60~tplv-k3u1fbpfcp-zoom-1.image)