## Vue的模版语法
> 我们把HTMl模版叫做template。

**template的3种写法**

**1' Vue完整版,写在HTML里**
```js
html
<div id=xxx>
  {{n}}
  <button @click="add"> + 1 </button>
</div>

new Vue({ //把html变成DOM节点
  el:'#xxx',
  data:{n:0}, //data可以改成函数
  methods:{
    add(){}
  }
})
```
**2' Vue完整版,写在选项里**
```js
<div id='app'>
</div>

new Vue({
  template:`
  <div>
  {{n}}
  <button @click="add"> + 1 </button>
  </div>
  `,
  data:{n:0}, //data可以改成函数
  methods:{ add(){ this.n +=1 } }
}).$mount('#app')
```
**注意细节:**

**(1)div#app会被替代**

`new Vue({})`运行后，template里面的`<div>`会替换掉页面的`<div id='app'>`,渲染完后就没有`id='app'`的`<div>`了。

**(2) `el:'#xxx'`可以替换成`new Vue({}).$mount('#app')`**

**3' Vue非完整版,配合xxx.vue文件**
```js
第1步.写xxx.vue文件
<template>
  <div> //这里一般不需要id,id一般是用来挂载的，这里不需要
    {{n}}
    <button @click="add">
      +1
    </button>
  </div>
</template>
注意:<template>里面不是HTML而是 XML
<script>
  export default { //默认导出一个选项(构造选项)
    data(){ return {n:0} },//data必须为函数，这个函数返回的对象就是我们要用的data
    methods:{add(){ this.n += 1 }}
  }
</script>
<style>
  这里写 CSS 
</style>

第2步.在另外一个地方写如下代码
import Xxx from './xxx.vue' 
//首字母大写防冲突，Xxx是一个options对象
new Vue({
  render: h => h(Xxx) //Xxx传给Vue的render函数
  }).$mount('#app')
```
**Xxx就是导出的对象** `export default { 对象 }`

**HTML与XML的区别:**

XML书写更严格:所有空标签都可以直接闭合，比如`<div />`，必须写/。
因此XMl语法更容易写编译器。

## 模版里有哪些语法？
### 一.展示内容
**1.表达式**
```js
{{ object.a }} 表达式。data里面的object.a
{{ n+1 }} 可以写任何运算。但不支持if...else
{{ fn(n) }} 可以调用函数。默认在methods里找叫fn的函数，并传了参数n
当值是`undefined 或 null`时,它就什么都不展示,它不会显示undefined和
null，这是Vue的特殊处理。
另一种写法为<div v-text="表达式"></div> 很少有人用
```
**2.HTMl内容**
> 指令v-html=""，支持内容里有html标签。

假设`data.x`值为`<strong>hi</strong>`
`<div v-html="x"></div>`即可显示粗体的hi

**3.我就想在页面中展示{{n}}怎么办？**
```js
<div v-pre> {{ n }} </div> //v-pre不会对模版进行编译
```
### 二.绑定属性
**1.绑定字符串**

**绑定src:`<img :src="x" />`**

补充:`<img v-bind:src="x" />`其中`v-bind:`可以简写为`:`

**2.绑定对象**
```js
<div
 :style="{border:'1px solid red',height:100}">
</div>
//注意在这里可以把'100px'写成100
//补充:JS中100就是100px div.style.height=100 //'100px'
//这种写法会在div上写一个内联样式
```
### 三.绑定事件 v-on
> v-on就是在什么时候触发事件。

**v-on:事件名**

![请添加图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/524b194b85f94894ba010b059ea44f37~tplv-k3u1fbpfcp-zoom-1.image)

**缩写**` <button @click=""add> +1 </button>`
### 四.条件判断 v-if
```js
<div v-if="x>0">
  x大于0
</div> 
<div v-else-if="x===0">
  x为0
</div> 
<div v-else>
  x小于0
</div> 
```
v-if是什么时候出现在DOM树里。
### 五.循环 v-for
**`for(value,key)in 对象或数组`**

**注意:每一个v-for必须有:key="" 绑定一个变量**，不写就会有警告！

```js
数组的例子
<ul>
  <li v-for="(u,index) in users" :key="index"> 
    索引:{{index}} 值:{{u.name}}
  </li>
</ul>

对象的例子
<ul>
  <li v-for="(value, name) in obj" :key="name">
    属性名:{{name}},属性值:{{value}}
  </li>
</ul>
```

### 六.显示、隐藏 v-show
```js
<div v-show="n%2===0"> n是偶数 </div>
//表达式如果是真的就显示<div>，负责就不显示<div>。if...else的简化版
//如果是假的<div>就不会出现在页面
```

v-show是什么时候展示出来，是通过css隐藏的。

### 七.[v-once](https://cn.vuejs.org/v2/api/#v-once)
只渲染元素和组件一次。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。
```html
<!-- 单个元素 -->
<span v-once>This will never change: {{msg}}</span>
<!-- 有子元素 -->
<div v-once>
  <h1>comment</h1>
  <p>{{msg}}</p>
</div>
<!-- 组件 -->
<my-component v-once :comment="msg"></my-component>
<!-- `v-for` 指令-->
<ul>
  <li v-for="i in list" v-once>{{i}}</li>
</ul>
```
### 总结
**Vue模版主要特点有**

1.使用XML语法(不是HTML)

2.使用{{}}插入表达式

3.使用v-html、v-on、v-bind等指令操作DOM

4.使用v-if、v-for等指令实现条件判断和循环

## 指令与修饰符
**一.指令Directive**

**1.什么是指令**

以`v-`开头的东西就是指令。

**2.语法**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/471bfb1a6e2f4b528eed3ef7a52aab21~tplv-k3u1fbpfcp-zoom-1.image)

`<a @click.prevent href="">百度</a>`

### [二.修饰符](https://cn.vuejs.org/v2/guide/events.html?#%E4%BA%8B%E4%BB%B6%E4%BF%AE%E9%A5%B0%E7%AC%A6)
用来修饰一个指令的就叫修饰符，比如`.prevent`。

有些指令支持修饰符，但有些不支持，看文档。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1e320fe60e344a40a0ee4ef2b1e4b8be~tplv-k3u1fbpfcp-zoom-1.image)

**1.添加修饰符**

v-on支持的修饰符最多，比如`.keycode`

需求:如果用户打**回车**就单独给它个逻辑。
```js
new Vue({
  template: `
  <div>
    <input @keypress.13="y"/> 
<!--<input @keypress.enter="y"/>-->  
  </div>
  `,
  methods: {
    y(e) {
      console.log(e);
      console.log("用户打了回车键"); 
    }
  }
}).$mount("#app");
```

```js
<input @keypress.13="y"/>或者<input @keypress.13=y /> 
```

**2.缩写`alias`**

键盘编码难记可以用缩写`alias`，查看支持的[按键码](https://cn.vuejs.org/v2/guide/events.html#%E6%8C%89%E9%94%AE%E7%A0%81)

```js
 <input @keypress.enter="y"/> //给13取个别名enter
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/687bd219a0ad48acac5f9de42defa079~tplv-k3u1fbpfcp-zoom-1.image)

上面这几个都不用记编码，可以直接用别名。

**[事件修饰符.self](https://cn.vuejs.org/v2/guide/events.html?#%E4%BA%8B%E4%BB%B6%E4%BF%AE%E9%A5%B0%E7%AC%A6)**(很少用)

用户点的元素正好是你监听的那个元素才会触发，如果是元素里面的元素不会触发。

**知识点**

**1.事件**

**input输入事件**

只要输入东西到input里都会触发事件`<input @input="y"/>`

**keypress键盘按下事件**

只要按下键盘就会触发事件y `<input @keypress="y"/>`

**2.`.prevent`阻止默认动作**

```js
<a @click.prevent='x' href="https://www.baidu.com/">百度</a>
```
**3.`.stop`阻止事件传播/冒泡**
```js
@click.stop='add'
```
### .sync 修饰符(重要)
```js
main.js
// 此处是非完整版vue
import Vue from "vue";
import App from "./App.vue";
Vue.config.productionTip = false;

new Vue({
  render: h => h(App)
}).$mount("#app");

App.vue
<template>
  <div class="app">
    App.vue 我现在有 {{total}}
    <hr>
    <Child :money="total" v-on:update:money="total = $event"/>
  //<Child :money.sync="total" />
  </div>
</template>

<script>
import Child from "./Child.vue";
export default {
  data() {
    return { total: 10000 };
  },
  components: { Child: Child }
};
</script>

<style>
.app {
  border: 3px solid red;
  padding: 10px;
}
</style>

Child.vue 
//子组件
<template>
  <div class="child">
    {{money}}
    <button @click="$emit('update:money', money-100)">
      <span>花钱</span>
    </button>
  </div>
</template>

<script>
export default {
  props: ["money"]
};
</script>

<style>
.child { border: 3px solid green; }
</style>
```

解析:

**1.main.js渲染了App,App是个组件。**

**2.App.vue**

红色边框是App组件。

`<Child />`是子组件从文件Child.vue来的，`import Child from "./Child.vue";` 使用时要声明下`components: { Child: Child }`告诉它总金额就1万`total: 10000`再多没有。

**3.Child.vue**

`<Child />`需要接收这笔钱，`props: ["money"]`外部数据money，所以App.vue`<Child :money="total" />`把总金额告诉Child.vue。

当点击button时就是要花钱，花的是他爸的钱(操作外部数据)所以不能`<button @click="money -=100">`自己操作数据。
MVC章节讲过订阅发布系统eventBus，Vue内置了eventBus,所以我们可以使用当前实例this,this就是当前实例，当前实例继承了eventBus，所以它可以触发一个事件。所以`@click="this.$emit"`，因为在Vue template里this可以删掉所以`<button @click="$emit('useMoney',money-100)">`

他爸就要监听他儿子要不要钱，如果发现儿子触发了我要钱，那就把他花的钱赋值给我的data的total，那我怎么知道儿子给我的钱是多少呢？尤雨溪把这个值存到变量上，你只要使用这个就可以了，这个变量就是`$event`。原理:把它的那个值先存到我的组件 **`$event`** 上，用时自己取。所以App.vue`<Child :money='total' v-on:useMoney = "total = $event"/>`
**尤雨溪规定要把事件名改为`'update:money'`**
有人在<Child/>上调用了on,有人在<Child/>上调用了$emit,这就是事件的发布和订阅。

**这种方法很常见，`v-on:update:money = "total = $event"`监听事件(update:money)获取$event，所以被封装成了修饰符`.sync`**。

**写法**
```js
<Child :money.sync='total'/>
//等于<Child :money='total' v-on:useMoney = "total = $event"/>
```
`.sync`这种写法就是一个语法糖或者API糖。

 **总结**
```js
@click.stop="xxx"

@click.prevent="xxx"

@keypress.enter="xxx"

:money.sync="total"
```
