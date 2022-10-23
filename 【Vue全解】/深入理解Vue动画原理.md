# 深入讲解 Vue 动画原理
> 文档 [过渡 & 动画](https://vuejs.bootcss.com/guide/transitions.html)

**轮播组件slides**

轮播难点在于最末位到首位的切换方式，在讲轮播之前需要讲下动画。\
Vue动画支持很多种不同的方式。

### Vue动画方式1 - CSS transition
> Vue提供了transition组件


```js
HTML
//先引入Vue(bootCDN)
<script src="https://cdn.bootcdn.net/ajax/libs/vue/2.5.17/vue.min.js"></script>

<div id="demo">
  <button v-on:click="show = !show">
    Toggle
  </button>
//1.写`<transition>`
  <transition name="fade"> 
    <p v-if="show">hello</p>
  </transition>
</div>

CSS
//2.写类
.fade-enter-active, .fade-leave-active {
  transition: all 2s;
  }
.fade-enter, .fade-leave-to {
  opacity: 0;
  width:100px
  }
//3.设置初始值
p{ 
  border:1px solid red;
  width:300px
  }
  
JS
new Vue({
  el: '#demo',
  data: { show: true }
})
```
**步骤**

第1步.在html里写`<transition>`

第2步.在css里写`.fade`开头的一系列类

最后给需要的属性添加**初始值**

对于这些在过渡中切换的类名来说，如果你使用一个没有名字的 <transition>，则`v-`是这些类名的默认前缀。如果你使用了 `<transition name="fade">`，那么`v-enter`会替换为`fade-enter`。

[文档 过渡的类名](https://vuejs.bootcss.com/guide/transitions.html#%E8%BF%87%E6%B8%A1%E7%9A%84%E7%B1%BB%E5%90%8D)

在进入/离开的过渡中，会有6个class切换:

`v-enter-active、v-leave-active`表示过渡时间有多长，一般会合并写这2个类，因为动的都是一样的。
`v-enter、v-leave-to`表示进入和退出状态，这2个类也会合写。剩下2个一般不用。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa0441c3b94e4ca99c41d28349e87d33~tplv-k3u1fbpfcp-zoom-1.image)

**p经历的过程：**

一开始`p`就是这个类，但是由于目前是隐藏的`show: false`，所以需要enter进入DOM，进入DOM的过程可以详细的去控制。

`v-enter`控制进入时的开始的状态，`v-enter-to`控制进入时的结束的状态，`fade-enter-active`控制它如何去添加补间动画，一般不需要加`v-enter-to`因为结束状态应该就是它原本状态`p`,没必要加。
等动画结束，就会把这3个类`v-enter、fade-enter-active、v-enter-to`都删掉，恢复到原始状态`p`

`p`由于目前是隐藏的所以需要enter进入DOM，进入DOM的过程可以详细的进行控制。开始是`红色`，然后变成`黑色`，过程持续`3s`。动画结束后enter被删掉，恢复到原始的`白色`。

#### CSS 过渡
```js
html
<div id="example-1">
  <button @click="show = !show"> Toggle render </button>
  <transition name="slide-fade"> //滑出
    <p v-if="show">hello</p>
  </transition>
</div>

CSS
/* 可以设置不同的进入和离开动画,设置持续时间和动画函数 */
.slide-fade-enter-active { 
  transition: all 3s ease;//滑出淡入不是线性3s
}
.slide-fade-leave-active { 
  transition: all 1s cubic-bezier(1, 0.5, 0.8, 1);
}
.slide-fade-enter, .slide-fade-leave-to {
//fade-leave-to"淡出的结束状态"就是"淡入开始的状态"，fade-enter对应fade-leave-to
  transform: translateX(10px);//淡入那一瞬间的位置
  opacity: 0;
}

JS
new Vue({
    el: '#example-1',
    data: { show: true }
})
```

### Vue动画方式2 - CSS animation
```js
html
<div id="example-2">
  <button @click="show = !show">Toggle show</button>
  <transition name="bounce"> //bounce类的前缀
    <p v-if="show">Lorem ipsum dolor sit amet, consectetur adipiscing elit.</p>
  </transition>
</div>

CSS
.bounce-enter-active { 
  animation: bounce-in .5s;
}
.bounce-leave-active { 
  animation: bounce-in .5s reverse; //bounce-in
}
@keyframes bounce-in { //bounce-in
  0% {transform: scale(0);}
  50% {transform: scale(1.5);}
  100% {transform: scale(1);} //结束时恢复正常状态
}

JS
new Vue({
  el: '#example-2',
  data: { show: true }
})
```
**keyframes语法**
```js
@keyframes spin { 
  0% {opacity: 0;}
  100% {opacity: 1;}
}
使用
.fade-enter-active{
  animation:spin 1s reverse;
}
```
**类名**
```js
可以通过以下 attribute 来自定义过渡类名：
enter-class、enter-active-class、enter-to-class、
leave-class、leave-active-class、leave-to-class
```
也可以结合第三方CSS动画库 [Animate.css](https://animate.style/) 提供了很多动画效果。

#### [Animate.css](https://animate.style/)
[bootCDN](https://www.bootcdn.cn/animate.css/3.5.2/)选择`animate.min.css`
**示例**
```js
html
<link href="https://cdn.bootcdn.net/ajax/libs/animate.css/3.5.2/animate.min.css" rel="stylesheet">

<div id="example-3">
  <button @click="show = !show">
    Toggle render
  </button>
  <transition
    enter-active-class="animated tada" 
  //animated后接的就是动画效果，格式animated xxx
    leave-active-class="animated bounce" 
  >
    <p v-if="show">hello</p>
  </transition>
</div>

JS
new Vue({
  el: '#example-3',
  data: {show: true}
})

CSS 不需要写CSS
```
[同时使用过渡和动画](https://vuejs.bootcss.com/guide/transitions.html#%E5%90%8C%E6%97%B6%E4%BD%BF%E7%94%A8%E8%BF%87%E6%B8%A1%E5%92%8C%E5%8A%A8%E7%94%BB)

在一些场景中，你需要给同一个元素同时设置两种过渡动效，比如 animation很快的被触发并完成了，而transition效果还没结束。在这种情况中，你就需要使用**type**属性并设置**animation**或**transition**来明确声明你需要Vue监听的类型。

[显性的过渡持续时间](https://vuejs.bootcss.com/guide/transitions.html#%E6%98%BE%E6%80%A7%E7%9A%84%E8%BF%87%E6%B8%A1%E6%8C%81%E7%BB%AD%E6%97%B6%E9%97%B4)

有的时候需要手动设置淡入淡出时间
```js
<transition :duration="1000">...</transition> //:duration="1000"
```
**JavaScript 钩子**

**文档 [JS钩子](https://vuejs.bootcss.com/guide/transitions.html#JavaScript-%E9%92%A9%E5%AD%90)**

可以在`attribute`中声明JS钩子,你可以用这些钩子知道当前动画处在哪个阶段。

### Vue动画方式3 - JS 操作动画
**[velocity](http://velocityjs.org/#dependencies)是一个非常著名的用JS操作动画的库**,推荐用这个做动画。

bootCDN选择velocity.min.js,最好用版本1.2.3的。

```js
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>

<div id="example-4">
  <button @click="show = !show">
    Toggle
  </button>
  <transition
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
    v-bind:css="false"
  >
    <p v-if="show">
      Demo
    </p>
  </transition>
</div>

JS
new Vue({
  el: '#example-4',
  data: {
    show: false
  },
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
      el.style.transformOrigin = 'left'
    },
    enter: function (el, done) {
      Velocity(el, { opacity: 1, fontSize: '1.4em' }, { duration: 300 })
      Velocity(el, { fontSize: '1em' }, { complete: done })
    },
    leave: function (el, done) {
      Velocity(el, { translateX: '15px', rotateZ: '50deg' }, { duration: 600 })
      Velocity(el, { rotateZ: '100deg' }, { loop: 2 })
      Velocity(el, {
        rotateZ: '45deg',
        translateY: '30px',
        translateX: '30px',
        opacity: 0
      }, { complete: done })
    }
  }
})
```

### Vue动画方式4 - 多元素动画
**一.多个元素的过渡** [文档](https://vuejs.bootcss.com/guide/transitions.html#%E5%A4%9A%E4%B8%AA%E5%85%83%E7%B4%A0%E7%9A%84%E8%BF%87%E6%B8%A1)
```js
示例1:淡出淡入
<div id="example-4">
  <transition name="fade" mode="out-in">
    <button key="on" v-if="status==='off'" @click="status='on'">on</button>
    <button key="off" v-else @click="status='off'">off</button>
  </transition>
</div>

JS
new Vue({
    el: '#example-4',
    data: { status: 'on'},
})    

CSS
.fade-enter-active,.fade-leave-active {
  transition: all 1s;
}
.fade-enter {
  opacity: 0;
}
.fade-leave-to {
  opacity: 0;
}

示例2:先进再出
<transition name="fade" mode="in-out">
CSS
.fade-enter-active,.fade-leave-active {
  transition: all 1s;
}
.fade-enter {
  opacity: 0;
  transform:translateX(100px)
}
.fade-leave-to {
  opacity: 0;
  transform:translateX(-100px)
}
#example-4{
  position:relative;
}
button{
 position:absolute;
}
JS同上略

示例3:实现轮播效果
<transition name="fade">
CSS
#example-4{
  position:relative;
  padding:100px;
}
JS同上略
```
**必须要加key才有动画**,不然它不知道你要变什么，它会以为两个button是一个。**当两个标签一样的时候一定要加key**。

**同时生效的进入和离开的过渡不能满足要求，所以Vue提供了过渡模式:**

out-in：当前元素先进行过渡，完成之后新元素过渡进入。

in-out(用的少):淡出的时候往左移

**`mode="out-in/in-out"`是为了解决2个元素占用位置冲突的问题。**

**二.多个组件的过渡**

**就是用`<component>`绑定is属性**

只需要使用**动态组件**
```js
//实际上就是tab切换
<div id="transition-components-demo">
  <transition name="component-fade" mode="out-in">
    <component v-bind:is="view"></component>
  </transition>
</div>

JS
new Vue({
  el: '#transition-components-demo',
  data: {
    view: 'v-a' //view是啥，就是哪个组件的名字
  },
  components: {
    'v-a': {
      template: '<div>Component A</div>'
    },
    'v-b': {
      template: '<div>Component B</div>'
    }
  }
})

CSS
.component-fade-enter-active, .component-fade-leave-active {
  transition: opacity .3s ease;
}
.component-fade-enter, .component-fade-leave-to {
  opacity: 0;
}
```
适用场景：如果你有10个组件在一个地方显示，想在切的时候有动画。就用`<component :is="view">`组件,如果你想让第一个组件出现就让`view: 'v-a'`，view等于第几个组件，就默认显示它。不需要加key。

### Vue动画5 - 列表动画(常用)
文档[列表过渡](https://vuejs.bootcss.com/guide/transitions.html#%E5%88%97%E8%A1%A8%E8%BF%87%E6%B8%A1)
```js
<div id="list-demo" class="demo">
  <button v-on:click="add">Add</button>
  <button v-on:click="remove">Remove</button>
  <transition-group name="list" tag="p"> 
    <span v-for="item in items" v-bind:key="item" class="list-item">
      {{ item }}
    </span>
  </transition-group>
</div>

CSS
.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to
/* .list-leave-active for below version 2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}

JS
new Vue({
  el: '#list-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9],
    nextNum: 10
  },
  methods: {
    randomIndex: function () {
      return Math.floor(Math.random() * this.items.length)
    },
    add: function () {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove: function () {
      this.items.splice(this.randomIndex(), 1)
    },
  }
})
```
**知识点**

**1.`name="list"`list就是css的前缀**

**2.`<transition-group> 组件`**

使用场景:要想用`v-for`实现同时渲染整个列表，这时候就可以使用`<transition-group>`组件。

**该组件特点:**

(1)默认为一个`<span>`,也可以通过 tag attribute更换为其他元素。

```js
<transition-group name="list" tag="p">
  <span v-for="item in items" v-bind:key="item" class="list-item">
    {{ item }}
  </span>
</transition-group>
```
`tag='p'`tag取值是p，`<transition-group>`就会将自身替换为p标签。tag取值是什么，`<span>`就会被什么标签包围。不写tag就默认是`<span>`。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8048af4bd76645f794810ca1953e60f8~tplv-k3u1fbpfcp-zoom-1.image)

(2)**过渡模式**不可用，因为我们不再相互切换特有的元素。
mode="out-in"不能用，，`<transition-group>`不支持mode

(3)内部元素**总是需要**提供唯一的 key attribute 值。
内部元素`<span>`必须写key`v-bind:key="item"`

(4)CSS 过渡的类将会应用在内部的元素中，而不是这个组/容器本身。

**for循环如何做动画？**

`<transition-group>` + `tag="div"` + v-for循环的模版(span/div)

注意:不能加其它的标签,for循环模版外只能是`<transition-group>`

### 总结 
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/88d8b36979284213a58ee3dafe3341f3~tplv-k3u1fbpfcp-zoom-1.image)
