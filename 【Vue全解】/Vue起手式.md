# 起手式
> [Vue中文文档](https://cn.vuejs.org/index.html) 、[工具@vue/cli文档](https://cli.vuejs.org/zh/guide/installation.html)


### Vue的历史
Vue、Angular.js、React.js前端三大主流框架。
MVC中的V是Vue的重点，M和V则被简化。
Vue和vue.js没有区别，vue就是vue.js的简称。
Vue你可以认为是框架也可以说是库，Vue中文英文文档都是尤雨溪写的。
 
15年的1.0版本Vue自称是MVVM框架，不是MVC是MVVM，不过这已经成历史了。现在Vue是**MV** * 框架,意思就是你说MVC、MVVM都行。但是Vue升级到2.0后，它虽然看起来是MVVM框架，但实际不是MVVM框架了。

**Vue作者**尤雨溪，**主要作品:** Vue、Vue Router、Vuex、@vue/cli(主要维护者是蒋豪群)

Vue2版本`vue.runtime.min.js` Vue3版本`vue.runtime.global.prod.js`

## 目标一: 项目搭建
**方法一:使用@Vue/cli**
> **使用@vue/cli搭建Vue项目**
> **@Vue/cli** 是Vue的创建初始化工具，webpack的升级版，帮你把所有的webpack loader、webpack plugin配好了。利用@Vue/cli，不用学webpack也能用Vue。

**步骤** 🔍[@vue/cli文档](https://cli.vuejs.org/zh/guide/installation.html)

**1.打开终端**
```
cd ~/Desktop/
yarn global add @vue/cli@4.1.0-beta.0
vue --version
vue create vue-demo //创建目录vue-demo
```
**2.设置配置**(该配置只适合学习，如果是真实项目请自行斟酌选项)

**选错用control+c中断重来**

没有截图的使用默认选项。

多选:用空格键选中或取消 **注意:** 不要勾选`Choose Vue version`!

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a2b667a82cf04bdd936184b5fc88157b~tplv-k3u1fbpfcp-zoom-1.image)

**3.运行**
```js
cd vue-demo
yarn serve
中断control+c，然后把生成的目录vue-demo拖进来
open .
yarn serve //打开本地预览服务器
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1e3e903ab52849e18cdbc30f95fdebf6~tplv-k3u1fbpfcp-zoom-1.image)

现在就可以使用Vue了，还可以用codesandbox.io。

第1种方式:用命令行创建目录，它会自动帮你安装依赖。

第2种方式:用[codesandbox.io](https://codesandbox.io/)。

不要登录，点击“create sandbox”，选择创建vue沙盒。

下载至本地:点击File-> Export to ZIP 并解压缩

**方法二:自己从零搭建Vue项目**

## 目标二.vue.js和vue.runtime.js的使用
做一个简单的"+1"项目

**如何使用Vue实例？**
```html
App.vue
<template>
  <div id="app">
    {{n}}
    <button @click="add">+1</button>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  data(){ //data是函数
    return {
      n:0
    }
  },
  methods:{//methods是对象
    add(){
      this.n +=1
    }
  }
}
</script>

<style lang="scss">
</style>
```
**分析**

index.html`<div id="app"> {{n}} </div>`**不支持{{n}}**

main.js `new Vue`封装了对实例的所有操作
```js
new Vue({//初始化#app
  el : '#app' //我要对这个div进行MVC的封装
})
```
Vue不支持{{n}}，接下来介绍Vue的2个版本，就知道为什么Vue有的支持有的不支持了。
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c5946598aa224fe386721d0c22c8fbfa~tplv-k3u1fbpfcp-zoom-1.image)


### 方法一:从html得到视图「完整版Vue」
1.[文档:](https://cn.vuejs.org/v2/guide/installation.html#CDN) 使用[BootCDN](https://www.bootcdn.cn/vue/2.6.14/)安装

[对不同构建版本的解释](https://cn.vuejs.org/v2/guide/installation.html#%E5%AF%B9%E4%B8%8D%E5%90%8C%E6%9E%84%E5%BB%BA%E7%89%88%E6%9C%AC%E7%9A%84%E8%A7%A3%E9%87%8A)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/341502e07a2349cbab707a239fdea294~tplv-k3u1fbpfcp-zoom-1.image)

CDN后缀为vue.min.js是vue.js的简洁版，**推荐用vue.min.js**。

**⚠️注意CDN引入的Vue版本要与本地Vue版本一致，都是2。**

查看版本号:打开package.json:`"vue": "^2.6.11";`。

CDN引入的版本:vue/2.6.14 



CDN选择后缀为vue.min.js，选择复制`<script>`标签，插到index.html。
```html
index.html
<div id="app"> {{n}}</div>
<script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.14/vue.min.js"></script>
```
引入版本后就会得到一个**全局变量**
```js
main.js
console.log(window.Vue)

终端:yarn serve //打开本地预览服务器
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f7e63116c54043b0b9628f2513b09454~tplv-k3u1fbpfcp-zoom-1.image)

**2.使用**
```js
main.js
new Vue({
  el: '#app',
  data: {
    n: 0
  }
})
```
html中的{{n}}加载出来了，页面显示0

**关注点:** MVC视图没有写在js里，直接写在页面index.html里。

这就是完整版的功能，可以直接在页面里把n变成0

### 方法二:用JS构建试图「非完整版vue」
CDN选择后缀为`vue.runtime.min.js`，选择复制`<script>`标签，插到`index.html`。

页面并未显示0

**原因:非完整版vue不支持从html里面获取视图。** 只要是从html里产生视图就都不支持，所以在js里用template也没用。

**解决方法:修改render**

[文档](https://cn.vuejs.org/v2/guide/installation.html#%E8%BF%90%E8%A1%8C%E6%97%B6-%E7%BC%96%E8%AF%91%E5%99%A8-vs-%E5%8F%AA%E5%8C%85%E5%90%AB%E8%BF%90%E8%A1%8C%E6%97%B6)

```js
index.html
<script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.14/vue.runtime.min.js">
</script>

main.js
console.log(window.Vue)
new Vue({
  el: '#app',
  render(createElement) {
    const h = createElement
    return h('div', [this.n, h('button', { on: { click: this.add } }, '+1')])
  },
  data: {
    n: 0
  },
  methods: {
    add() {
      this.n += 1
    }
  }
})
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6a31e275d93e44538c0ecf9dbec76b1c~tplv-k3u1fbpfcp-zoom-1.image)

**不完整版vue**必须要用createElement方式把所有元素构造出来。

#### 完整版vue VS 不完整版vue
**完整版vue思路:**

Vue要把'html{{n}}'变成'html 0'。

直接替换不太好，因为如果有`v-if、v-for`这些复杂语句你直接用正则替换是做不到的。于是Vue需要写一个compiler(编译器),这个compiler可以把含有占位符或条件循环语句的东西变成真实的DOM节点。这样下次你在写`this.n +=1`时那么它就知道你要改的是n，于是它就直接改DOM节点，而不是再去编译遍。它根据分析出DOM节点得到了'html 1'。像复杂的语法`@click='add'`不能直接替换。

compiler(编译器)特点复杂，占用一定代码体积，比「不完整版Vue」大30%的体积，所以需要不完整版Vue。

**不完整版vue思路:**

不完整版Vue没有compiler,所以没办法把html变成节点，没有这个功能，是利用webpack在写的时候就进行转换。webpack通过`vue-loader`将`<div id="app"> {{n}}... </div>`转化为`h('div',this.n)`。我们写还是写html,但是用户下载的是"只支持h函数的「不完整版Vue」",中间通过webpack loader翻译。用户对Vue的依赖可以使用体积少30%的「不完整版vue」，vue为了减少30%体积把核心功能删掉了，在打包的时候自己翻译，翻译完后就不需要html版本了。

我们写代码`<div id="app"> {{n}}... </div>`，然后通过`yarn build`得到用户可以用的`h('div',this.n)`,用户可以用的`h('div',this.n)`+不完整版vue。这里没有html，html早就编译好了，就是把编译器放到build里面，vue-loader会调用compiler，这就是vue作者的逻辑。不完整版意义在于更加的独立，这种方法很不方便，但实际上是对的。

**总结:**「完整版vue」体积大30%，「不完整版vue」体积小30%但需要用webpack配合。

### 方法三:使用vue-loader
**Vue单文件组件:「不完整版vue」如何用vue-loader翻译？**

可以把`.vue`文件翻译成h构建方法

但这样做html就只有一个div#app,`SEO不友好`

**步骤**

**1.使用它提供的方法，src目录下新建文件Demo.vue**,代码如下：

```js
Demo.vue
//语法
<template> //视图
    <div class="red">
        {{n}}
        <button @click="add">+1</button>
    </div>
</template>

<script> //视图外的其它选项
export default {
    data(){//如果你是用vue-loader写的这个文件，那data必须是函数！
      return { n:0 }
    },
    methods:{
      add(){
        this.n +=1
      }
    }
}
</script>

<style scoped> //CSS
.red{ color: red; }
</style>
```
**vue-loader就可以把这些东西变成一个对象**

**2.使用**
```js
main.js
import Demo from './Demo.vue' //先导入
console.log(Demo)
new Vue({
  el: '#app',
  render(h) {
    return h(Demo)
  },
})

终端:yarn serve //打开本地预览服务器
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0037e5035bdc47bfa5f5bc14598e7800~tplv-k3u1fbpfcp-zoom-1.image)

render里面就是把html` <div class="red"> {{n}}...  </div>`翻译成`render(h) { return h(Demo) }`,所以Demo.vue不需要自己写render。

**总结:** 这就是webpack给前端带来的无限可能，你可以任意的方式组织一个对象，只要你最后能够写一个loader把它还原成这个对象就可以了，这就叫做`Vue单文件组件`。

### SEO(搜索引擎优化)友好
**百度如何知道某个的相关内容呢？**
```js
curl https://www.taobao.com/
```
用`curl命令`(或者右键显示网页源代码)可以得到淘宝输出给百度的curl结果，相关内容比如 `标题<title>淘宝网 - 淘！我喜欢</title>` `文章<h1>淘宝网</h1>`。这时去百度搜索淘宝网就有可能搜到该网站，因为curl后它得到的就是这些关键信息。

反关我们的index.html curl后除了个title外一无所知,因为内容`<div id="app"></div>`是后来的js渲染进去的，这就叫做**SEO不友好**或者说用curl命令无法得到相关内容。

**SEO友好处理:** 补充内容，把主要内容写到页面里。就是把title、description、keyword、h1、a写好即可。
```html
//打开淘宝查看源代码，有例子
<meta name="description" content="淘宝网-亚洲较大的网上交易平台，提供各类服饰..." />
<meta name="keyword" content="淘宝,掏宝,网上购物,C2C,在线交易..." />
```
虽然最后会不见，但是curl命令会看见它。

**⚠️最佳实践:总是使用非完整版，然后配合vue-loader和vue文件。**

思路:

1.保证用户体验，用户下载的JS文件体积更小，但**只支持h函数**。

2.保证开发体验，开发者可直接在vue文件里写HTML标签，而**不写h函数**。

3.脏活让loader做，vue-loader把vue文件里的HTML**转为h函数**。
这样你就可以不写h函数也能得到h函数。

### 深入理解完整版与运行版的区别
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3de4dc9e0ac9459aa264620029896eff~tplv-k3u1fbpfcp-zoom-1.image)

1.非完整版**视图:** 写在render函数里，用h来创建标签。h可重命名但最好别改。
```js
Demo.vue 代码略...
main.js
import Demo from './Demo.vue'

new Vue({
  el: '#app',
  render(h) {
    return h(Demo)//用h创建标签
  }
})
```
2.可以用**CDN**引入这两个版本:vue.js是Vue完整版，vue.runtime.js是Vue非完整版。**生产环境**(如果要部署给用户)要都改成.min.js，比如vue.runtime.min.js，.min.js是去掉注释压缩后的版本。

3.引用错了会怎样？

**非完整版**支持render但不支持template，强行用template就会报错警告。

**完整版**vue.runtime.js错用成vue.js`体积会变大`，因为vue.js有编译HTML的功能。
