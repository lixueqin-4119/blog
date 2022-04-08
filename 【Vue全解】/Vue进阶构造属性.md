> **Vue构造属性: directives、mixins、extends、provide、inject**

### 1.[Directives 指令](https://cn.vuejs.org/v2/api/#Vue-directive)
**2种写法**

**1' 声明一个全局指令**
```js
Vue.directive('x',directiveOptions)
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f4b484634fe4a778bfa878f2a0c2db7~tplv-k3u1fbpfcp-zoom-1.image)

**例子:** 造出v-x，点击即打印x。

声明指令x,当元素被**插入**到页面中之后，我们就监听它的click事件。
```js
1.先声明
main.js
Vue.directive('x',{ //单个directive
  inserted:function(el){
    el.addEventListener('click',()=>{console.log('x')})
  },
})

2.再使用x指令
进入App.vue组件
<template>
  <img v-x width="25%" src="./assets/logo.png">
  <HelloWorld msg="Hello Vue"/> 
//让HelloWorld也响应:进入HelloWorld.vue组件添加`<h1 v-x>`,再次点击<h1>控制台就会输出x。
</template>
```
**你可以在任何组件里用`v-x`了。**
inserted你把**el**放哪个元素上，el就是哪个元素，比如放`<img>`上，el就是`<img>`

**2' 声明一个局部指令**
**`v-x`只能在该实例中用**
```js
new Vue({
 ...,
 directives:{
   "x":directiveOptions
 }
})
```
**例子:** 只能在HelloWord.vue组件里用 x
```js
HelloWord.vue
<template>
  <div class="hello">
    <h1 v-x>{{ msg }}</h1> //使用
  </div>
</template>
<script>
export default {
  directives: { //可声明多个directives
    'x': {
       inserted(el) {
         el.addEventListener("click", () => {console.log("x")});
      },
    },
  },
</script>
```

### directiveOptions的其它属性 
> **常用的钩子函数:bind、inserted、unbind**

文档: [钩子函数](https://cn.vuejs.org/v2/guide/custom-directive.html#%E9%92%A9%E5%AD%90%E5%87%BD%E6%95%B0)、[钩子函数参数](https://cn.vuejs.org/v2/guide/custom-directive.html#%E9%92%A9%E5%AD%90%E5%87%BD%E6%95%B0%E5%8F%82%E6%95%B0)
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8647e73a9fbd4e1f8d25d8920154ed3a~tplv-k3u1fbpfcp-zoom-1.image)

**函数接收的参数**,都是Vue传给我们的:`el、info、vnode、oldVnode`

**1.el** 就是你把v-x绑到哪个元素上去了

**2.info** 就是除了传元素外，你还传了哪些东西。info包含了很多详细的消息，基本上你要的东西都在第2个参数里。

原文是binding对象:

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5c35c8ddd15940079f85fafbe4491486~tplv-k3u1fbpfcp-zoom-1.image)

3.vnode 元素对应的虚拟节点

4.oldVnode 之前的虚拟节点，有可能这个元素是从旧到新

**例子:** 模仿v-on，自制v-on2指令
```js
new Vue({
  directives: {
    on2: {
      inserted(el, info) {
        //console.log(el); 打印出<button>点我</button>
        //console.log(info); 打印出一个对象
        console.log(info.arg);
        el.addEventListener(info.arg, info.value);
      },
      unbind(el, info) { //unbind用于清理垃圾，删除监听
        el.removeEventListener(info.arg, info.value);
      }
    }
  },
  template: `
    <button v-on2:click="hi">点我</button>
  `,
  methods: {
    hi() {
      console.log("hi");
    }
  }
}).$mount("#app");
```
`addEventListener`时无法确定是否是`click`，因为还有可能是其它事件，比如keyup。此时可以利用第2个参数拿到用户传的事件。用`info`得到用户传的事件

**`info`拿到的是个对象**
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/23f9df86b76d4b889e93e65b451ac91f~tplv-k3u1fbpfcp-zoom-1.image)

当元素被插入到页面中时，我们就监听这个元素的事件和对应的方法。

[函数简写](https://cn.vuejs.org/v2/guide/custom-directive.html#%E5%87%BD%E6%95%B0%E7%AE%80%E5%86%99)不推荐

**指令的作用**

**1.主要用于DOM操作**

Vue实例/组件用于数据绑定、事件监听、DOM更新

Vue指令主要目的就是原生DOM操作

**2.减少重复**

如果某个DOM操作你经常使用，就可以封装为指令

如果某个DOM操作比较复杂，也可以封装为指令

**解析**

**1.主要用于DOM操作**

平时在用Vue时从来不做DOM操作，只是改数据，数据自动更新UI。但是我们在实际开发中有时不得不写DOM操作，这时可以**把DOM操作封装成一个指令，然后只传个函数给这个指令**这样就可以永远不去接触DOM操作了。

`new Vue({})`一般会传data、methods、钩子函数主要用途就是数据绑定、事件监听、DOM更新。DOM更新不是通过DOM API更新，而是直接通过`监听器`去更新。如果你要进行DOM操作，就只能用Vue指令，Vue组件和Vue指令各司其职。

### mixins 混入
**mixins目的也是为了减少重复,就是把一些属性复制到你的组件上。**

**类比**

1.directives的作用是减少DOM操作的重复

2.mixins的作用是减少data、methods、钩子的重复，也就是减少构造函数的重复。

**[示例](https://codesandbox.io/s/brave-dijkstra-memmm?file=/src/components/Child1.vue) 
使用mixins减少重复**
```js
App.vue
<template>
  <div id="app">
    <Child1 v-if="child1Visible"/>
    <button @click="child1Visible = false">x</button>
    <Child2 v-if="child2Visible"/>
    <button @click="child2Visible = false">x</button>
    <Child3 v-if="child3Visible"/>
    <button @click="child3Visible = false">x</button>
    <Child4 v-if="child4Visible"/>
    <button @click="child4Visible = false">x</button>
    <Child5 v-if="child5Visible"/>
    <button @click="child5Visible = false">x</button>
  </div>
</template>
<script>
import Child1 from "./components/Child1.vue";
import Child2 from "./components/Child2.vue";
import Child3 from "./components/Child3.vue";
import Child4 from "./components/Child4.vue";
import Child5 from "./components/Child5.vue";
export default {
  name: "App",
  data() {
    return {
      child1Visible: true,
      child2Visible: true,
      child3Visible: true,
      child4Visible: true,
      child5Visible: true
    };
  },
  components: {
    Child1,
    Child2,
    Child3,
    Child4,
    Child5
  }
};
</script>

Child1.vue
<template>
  <div>Child1</div>
</template>
<script>
import log from "../mixins/log.js";
export default {
  data() {
    return {
      name: "Child1", //name自己传
    };
  },
  created() {
    console.log("Child 1 的 created");
  },
  mixins: [log],
};
</script>

Child2.vue
<template>
  <div>Child2</div>
</template>
<script>
import log from "../mixins/log.js";
export default {
  data() {
    return {
      name: "Child2" //name自己传
    };
  },
  mixins: [log]
};
</script>
Child3.vue、Child4.vue、Child5.vue代码略...

log.js
const log = { //log必须是对象
  data() { //2个data会智能合并
    return {
      name: undefined,
      time: undefined
    };
  },
  created() {
    if (!this.name) { 
      throw new Error("need name");
    }
    this.time = new Date();
    console.log(`${this.name}出生了`);
  },
  beforeDestroy() {
    const now = new Date();
    console.log(`${this.name}死亡了，共生存了 ${now - this.time} ms`);
  }
};
export default log;
```
#### mixins的实现思路
五个组件都有构造函数`data、created、beforeDestroy`,那我们就把公共的抽到一个地方。

**步骤**

**1.新建目录mixins、新建文件log.js,然后将共有的剪切到log.js里。**

log.js代码参照上面

**2.使用log**

Child1.vue代码参考上面

```js
<script>
import log from "../mixins/log.js";
export default {
  mixins: [log], //把log里的内容复制到我这里
};
</script>
```
**mixins技巧**

选项智能合并[文档](https://cn.vuejs.org/v2/guide/mixins.html#%E9%80%89%E9%A1%B9%E5%90%88%E5%B9%B6)

也可以使用[全局Vue.mixin](https://cn.vuejs.org/v2/api/#Vue-mixin) 但不推荐使用

### extends 继承、扩展
**extends也是构造选项里的一个选项，跟mixins很像，也是复制减少重复但形式不同。
**extends更抽象高级，但还是**推荐用mixins**。

**步骤**

**1.新建文件MyVue.js，这不是Vue组件。**
```js
import Vue from "vue"
const MyVue = Vue.extend({ //继承Vue,MyVue就是Vue的一个扩展
  data(){ return {name:'',time:undefined} },
  created(){
    if(!this.name){console.error('no name!')}
    this.time = new Date()
  },
  beforeDestroy(){
    const duration = (new Date()) - this.time
    console.log(`${this.name} ${duration}`)
  },
  //mixins:[log] 也可以使用mixins
})
export default MyVue
```
**2.导入+继承**
```js
Child1.vue
<template>
  <div>Child1</div>
</template>
<script>
import MyVue from "../MyVue.js";
export default{
  extends:MyVue,
  data(){
    return {
      name:"Child1"
    }
  }
}
</script>
```
extends是比mixins更抽象一点的封装。如果你嫌写5次mixins麻烦，可以考虑extends一次，不过实际工作中用的很少。

### provide(提供) 和 inject(注入)
[例子](https://codesandbox.io/s/fragrant-rain-h8lm6?file=/src/main.js)
```js
App.vue
<template>
  <div :class="`app theme-${themeName} fontSize-${fontSizeName}`">
//同时添加3个class
//注意: :class=""双引号是XML的双引号，不是JS的双引号。``这是JS的引号
    <Child1/>
    <button>x</button>
    <Child2/>
    <button>x</button>
    <Child3/>
    <button>x</button>
    <Child4/>
    <button>x</button>
    <Child5/>
    <button>x</button>
  </div>
</template>
<script>
import Child1 from "./components/Child1.vue";
import Child2 from "./components/Child2.vue";
import Child3 from "./components/Child3.vue";
import Child4 from "./components/Child4.vue";
import Child5 from "./components/Child5.vue";
export default {
  name: "App",
  provide() {
    return {
      themeName: this.themeName,
      fontSizeNmae: this.fontSizeName,
      changeTheme: this.changeTheme, 
      changeFontSize: this.changeFontSize
    };
  },
  data() {
    return {
      themeName: "blue", // 'red'
    //blue和red存在themeName里，点击换肤时会切换blue、red
      fontSizeName: "normal" // 'big' | 'small'
    };
  },
  methods: {
    changeTheme() {
      if (this.themeName === "blue") {
        this.themeName = "red";
      } else {
        this.themeName = "blue";
      }
    },
    changeFontSize(size) {
    //name在这3个中时下标才会 >= 0,不在下标是-1
      if (["normal", "big", "small"].indexOf(size) === -1) {
        throw new Error(`wront size: ${size}`);
      }
      this.fontSizeName = size;
    }
  },
  components: {
    Child1,
    Child2,
    Child3,
    Child4,
    Child5
  }
};
</script>
<style>
.app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
//换肤的实现是用CSS
//如果一个元素能同时满足app和theme-blue,就设置button为蓝色
.app.theme-blue button {
  background: blue;
  color: white;
}
.app.theme-blue {
  color: darkblue;
}
.app.theme-red button {
  background: red;
  color: white;
}
.app.theme-red {
  color: darkred;
}
.app.fontSize-normal {
  font-size: 16px;
}
.app button {
  font-size: inherit;
}
.app.fontSize-small {
  font-size: 12px;
}
.app.fontSize-big {
  font-size: 20px;
}
</style>
```
**流程**

**1.新建ChangeThemeButton组件**:新建ChangeThemeButton.vue
```js
ChangeThemeButton.vue
<template>
  <div>
    <button @click="changeTheme">换肤</button>
    <button @click="changeFontSize('big')">大字</button>
    <button @click="changeFontSize('small')">小字</button>
    <button @click="changeFontSize('normal')">正常字</button>
  </div>
</template>
<script>
export default {

};
  inject: ["themeName", "changeTheme", "changeFontSize"]
};
</script>

Child1.vue
<template>
  <div>Child 1
    <change-theme-button/> //这样也能找到组件，Vue会自己找
  </div>
</template>
<script>
import ChangeThemeButton from "./ChangeThemeButton.vue";
export default {
  components: { ChangeThemeButton }
};
</script>
```
**2.App.vue完善需求实现切肤**

代码参考上面App.vue

**3.provide + inject**

App组件(App.vue)只要切换自己的“data的themeName值”就能换肤，那如何在按钮里(ChangeThemeButton组件)改变App的data？自己只能改变自己的data,怎么才能改变别人的data？用provide实现改变别人的data。

**(1)provide:在提供数据的地方写provide，提供给别人改**
```js
App.vue
<script>
export default {
  name: "App",
  provide() {
    return {
      themeName: this.themeName, //提供给别人用或改
      fontSizeNmae: this.fontSizeName, //提供给别人用或改
      changeTheme: this.changeTheme,  //提供给别人用或改
      changeFontSize: this.changeFontSize //提供给别人用或改
    };
  },
```
**(2)inject:**把别人提供的东西注入到我的组件来，使用**provide**
```js
ChangeThemeButton.vue
<template>
  <div>
    <button>当前主题色：{{themeName}},换肤</button>
</template>    
<script>
export default {
  inject: ["themeName"]//注入
};
</script>
```
**注意:**provide提供的是字符串的复制品，拿到的是string,不能改，应该把改的函数传过来，拿到**函数的引用**调用它就行(changeTheme)。
```js
App.vue
provide() {
    return {
      themeName: this.themeName,//提供给别人用或改
      changeTheme: this.changeTheme,//提供给别人用或改
    };
  },
methods: {
    changeTheme() {
      if (this.themeName === "blue") {
        this.themeName = "red";
      } else {
        this.themeName = "blue";
      }
    },
    
ChangeThemeButton.vue
<template>
  <div>
    <button @click="z">当前主题色：{{themeName}},换肤</button>
</template>  
<script>
export default {
  inject: ["themeName", "changeTheme"],
  methods:{
    z(){
      this.changeTheme()
    }
  }
};
</script>    
```
字符串拿来时已经复制了，改复制品没用，也可以用**对象的引用**,但不推荐。因为当这样做的人多了之后就没法控制量了，变量被到处改，无法知道变量处于什么状态，容易失控。

**知识点**

1.`:class=""`双引号是XML的双引号，不是JS的双引号。

  ``这是JS的引号
  
```js
<div :class="`app theme-${themeName} fontSize-${fontSizeName}`">
解析:第1个class是app。第2个class是theme-${themeName}，blue和red存在themeName里。
    第3个class是fontSize-${fontSizeName}
```
**2.CSS选择器**
```js
<div :class="`app theme-${themeName}`">同时设置多个class
.app.theme-blue{ background：blue }
//如果一个元素同时满足这2个选择器，一个class为app,一个class为theme-blue，那么就设置为blue

.app.theme-blue button{ background：blue } 
//如果一个元素能同时满足app和theme-blue,那么它里面的button就是蓝色

.app .theme-blue 
//如果app里面有theme-blue
```
