> MVC是进入Vue/React的必经之路，MVC本来就是模糊的，你觉得MVC是什么就是什么，只要能自圆其说即可。这就是抽象，抽象就是不具体。

### 什么是MVC?

设计模式是个框，什么都能往里装，MVC是框里的一个东西。设计模式就是对通用代码取个名字而已。

越抽象能解决的问题就越多。具体问题只能针对具体需求，但抽象问题能针对更多需求。mvc属于设计模式的一种，但我们并不把它称为设计模式。因为它太著名了，每个程序员都知道mvc,它就叫MVC。设计模式就是对通用代码取个名字而已。

**为什么要有设计模式？**

Don't repeat yourself. DRY原则

**哪里重复了？**\
1.代码级别的重复\
你把相同的3行代码写了2遍，那么你就应该重构它。\
重构:把这3行代码取个名字放到函数里，到时再调用这个函数名。可以取消重复。\
2.页面级别\
你把类似的页面做了10遍，那么你就应该想出一个万金油写法。\
3.mvc就是一个万金油\
所有页面都可以使用MVC来优化代码结构\
**不学MVC又怎样？**\
1.意大利面条式代码\
2.你将变成外包式程序员\
不停重复自己，不懂得抽象。只会调用API，不能提升自己。\
只会写业务，不会封装，更不会造轮子，更不会加薪。

**MVC是啥？**\
每个模块都可以写成3个对象，分别是M、V、C\
M Model(数据模型)负责操作所有数据\
V View(视图)负责所有UI界面\
C Controller(控制器)负责监听用户事件，然后调用M和V更新数据和视图

**面试题**\
**能说说你对MVC的理解吗？**\
中英文全称(80分’)+剩下全靠扯(20分’)\
Model(数据模型)负责操作所有数据,View(视图)负责所有UI界面,Controller(控制器)负责其它。

### [示例代码](https://github.com/lixueqin-4119/js-demo1/tree/master/src)

**内容**\
1.主要要引入2个东西:模块、类\
2.在index页面中做4个功能:加减乘除、tab切换、左晃右晃、悬浮变色\
3.演示如何用MVC让代码更严谨。

```js
运行:parcel src/index.html
用parcel前最好新建js文件，因为没有js可能有bug。
```

**知识点**\
**1.用新方法:js import引入css**

```js
main.js
import './app1.css' //不是css import,没有@
```

**补充:@import性能比较低**\
新建单独的reset.css文件reset css，统一在main.js里引入。\
让app1占屏幕的1/4: 设置width和height各50vh\
**2.用新方法:js import引入jquery**

> 点击`+1按钮`实现+1,用jquery实现,需要引入jquery\
> 之前是用`<script src="CDN地址">`引入，现在也用新方法js import

**方法:**  先安装再使用

```js
安装: yarn init -y
      yarn add jquery //或npm i jquery     
使用:import $ from 'jquery' //引用jquery变量，使用时可直接用$代替jquery
   //import 'jquery'  注:main.js
```

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/24b1e4e2905a49699609802e08ac953a~tplv-k3u1fbpfcp-watermark.image?)


补充:会增加3个文件。其中package.json记录了版本号，防止下次安装时版本号跟这次不同而报错，版本不一样就会有bug。yarn.lock写明了jquery是从哪下载的，这样另外一个人安装时就知道要从哪下载。\
`import 'jquery'` 中jquery要小写，**跟目录保持一致。npm所有的包都是小写的**\
`console.log($)`打印出了一个函数，证明已成功引用。

**3.推荐插件:Code Spell Checker** 用于VSCode单词拼错时自动提示\

**4.变量为什么要初始化？**\
内存是在操作系统的统一管理下使用的。\
软件运行完后，操作系统将回收该内存空间(注意:操作系统并不清空该内存空间中遗留下来的数据)，以便再次分配给其他软件使用。所以，一个软件所分配到的空间中极可能存在以前其他软件使用后的残留数据，这些数据被称为垃圾数据。\
所以，我们为一个变量或者数组分配好存储空间后，都要对该内存空间初始化。\
**5.所有操作系统的滚动条width:14px-19px;**\
6.**获取点击元素**\
(1)`e.target`获取用户**点击的元素**，不含父元素。\
例子\
如果用户点击`<li><span>111</span></li>`那么console的就是`<span>111</span>`\
(2)`e.currentTarget`获取用户**点击的元素**，含父元素。\
例子\
如果用户点击`<li>`那么console的就是`<li><span>111</span></li>`\
7.**抽象思想:样式与行为分离**\
js只管功能，css只管样式。\
js可以直接操作css,API例如`.css()、.show()和.hide()`，但是永远不要用。用`.addClass()和.removeClass()`代替。\
**8.事件委托**

```js
const $tabBar = $('#app2 .tab-bar') 
$tabBar.on('click', 'li', (e) => { ... }
```

9.[toggleClass()方法](https://www.w3school.com.cn/jquery/attributes_toggleclass.asp)\
10.[localStorage本地存储](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage)

```js
localStorage.getItem(变量 或'字符串') //读
localStorage.setItem('n', n) //写,增加一个数据。第1个参数'n'自定义名字随便取
localStorage.removeItem('myCat'); //移除localStorage项
localStorage.clear();//移除所有的localStorage项
```

**11.默认事件**\
.eq()等于下标、.trigger()触发事件

```js
$tabBar.children().eq(0).trigger('click')
```

12.[JS模块](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules)\
可以使用`export default x`将一个变量默认导出给外部使用\
可以使用`import x from './xxx.js'`引用另一个模块导出的默认变量\
可以使用`import {x} from './xxx.js'`引用另一个模块导出的名为x的变量

### 添加数据保存功能

> 本地存储localStorage,刷新页面后还是当前的值。

```js
app2
const localKey = 'app2.index'
const index=localStorage.getItem(localKey) ||0 

$tabBar.on('click', 'li', (e) => { 
  ...
  const index = $li.index() //获取下标
  localStorage.setItem(localKey, index) //保存数据   
}
$tabBar.children().eq(index).trigger('click') //保底值0
```


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/adf5dbcd0ea745baa1f1045013493a4f~tplv-k3u1fbpfcp-watermark.image?)

# MVC的抽象思维

> 内容:把代码全部改写成MVC

**抽象思维1:最小知识原则**\
阶段1：引入一个模块需要引入html、css、js\
阶段2：引入一个模块需要引入html、js\
阶段3：引入一个模块需要引入js\
你需要知道的知识越少越少，模块化为这一点奠定了基础。\
**代价**\
这样做会使页面一开始是空白的，没内容没样式\
解决方法:加菊花、加骨架、加占位内容、SSR技术等

## 功能1:加减乘除

**项目中是怎么体现最小知识原则的？**\
最开始引入模块时需要在html里引入很多js,现在只需在main.js里引入每个模块的js，也不需要关心这个js里有没有css。


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa3c2cb9604e40578aa8e781e9008b94~tplv-k3u1fbpfcp-watermark.image?)

**阶段3.引入一个模块需要引入js**

> 封装html，简化到js里。

步骤:将首页关于app1的html剪切到它自己的app1.js里

```js
const html = `
  //app1的html
`
//html得到的是字符串，需要变成节点标签，然后再prependTo添到page的最前面。
const $element = $(html).prependTo($('body>.page'))
```

这样的好处是，使用我这个模块的人根本不用关心html是怎样的。\
剩下的模块也一样操作并用`appendTo`的插到元素的后面。\
补充：插到元素的前面用`prependTo`，插到元素的后面用`appendTo`。\
别人用我这个模版时只需要写个空的`<div>`,引入main.js,main.js里依次引入4个模块即可。

\
**页面空白的解决方法**

> 加菊花、加骨架、加占位内容、SSR技术等

**1.加菊花**

[图标库](https://www.iconfont.cn/search/index?searchType=icon&q=loading&page=1&fromCollection=-1&fills=&tag=) 🔍loading



![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2476635bd53944ce868db4bc90713fcf~tplv-k3u1fbpfcp-watermark.image?)

```js
<div class="page">
  <img id="img" src="./images/loading.png" alt="" />
</div>
main.js
img.remove() 
```

**2.加骨架**



![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e4bde0b52e7e4fef93c940b6c9ffdee2~tplv-k3u1fbpfcp-watermark.image?)

**3.SSR服务器端渲染技术(大公司会用)**\
逻辑:把页面中的标记替换成字符串\
比如，把app1的内容替换成html



![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d5e230ce5d8847869e5ed6e058e726a4~tplv-k3u1fbpfcp-watermark.image?)



![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b9136d98d7749ec8984a5e732646693~tplv-k3u1fbpfcp-watermark.image?)

### 抽象思维2

**以不变应万变**\
既然每个模块都可以用m + v +c搞定\
那么每个模块我就都这样写就好啦，不用再思考类似的需求该怎么做了\
**代价**\
有时候会有一些多余的用不到代码\
有时候遇到特殊情况不知道怎么变通，比如没有html的模块怎么做mvc\
**mvc的思维：写一个m对象、一个v对象和一个c对象。**\
M 内容:数据相关的\
V 内容:视图层，用户能看得见的\
C 内容:控制层，非MV的，
```js
const v={ //对象里只能放属性
  html:` ` //属性:值
}
```

### bug1:按钮无效，点击按钮没反应

**原因：**  因为我们的button获取太早了。\
浏览器看到了函数调用会立即调用它不会等，42行已经执行了这句话，而render是在第19行执行的。\
**解决：**  写一个初始化方法，不让它立即调用。\
**修改前**
```js
//数据相关 M
const m = {
    data: {  n: localStorage.getItem('n') //初始化数据 }
}
//视图相关 V
const v = {
    html: `  ...  `,
    update() { c.ui.number.text(m.data.n || 100) },
    render() {
        const $element = $(v.html).prependTo($('body>.page'))
    }
}
//其它 C
const c = {
    ui: {//找元素目的是为了绑定事件,绑定事件放哪，这个就放哪
        button1: $('#add1'),
        button2: $('#minus1'),
        button3: $('#mul2'),
        button4: $('#divide2'),
        number: $('#number')
    },
    bindEvents() {
        console.log("bindEvents执行了")
        console.log(c.ui.button1)
        c.ui.button1.on('click', () => {
            //debugger
            let n = parseInt(c.ui.number.text())
            n += 1
            localStorage.setItem('n', n)
            c.ui.number.text(n)
        })
        c.ui.button2.on('click', () => {
            let n = parseInt(c.ui.number.text())
            n -= 1
            localStorage.setItem('n', n)
            c.ui.number.text(n)
        })
        c.ui.button3.on('click', () => {
            let n = parseInt(c.ui.number.text())
            n *= 2
            localStorage.setItem('n', n)
            c.ui.number.text(n)
        })
        c.ui.button4.on('click', () => {
            let n = parseInt(c.ui.number.text())
            n /= 2
            localStorage.setItem('n', n)
            c.ui.number.text(n)
        })
    }
}
//第一次渲染html
v.render() //别忘了调用！转移前调用，转移后仍需要调用。
c.bindEvents()
```

**初始化方法:添加init**

```js
const c = {
  init() {
    c.ui = {
      //找元素目的是为了绑定事件,绑定事件放哪，这个就放哪
      button1: $('#add1'),
      button2: $('#minus1'),
      button3: $('#mul2'),
      button4: $('#divide2'),
      number: $('#number')
    }
  },
```

在调用我初始化时，我再去取ui的这个属性。\
这样就能保证我这时候取的就是现在取的。

```js
const c = {
  init() {
    c.ui = {
      //找元素目的是为了绑定事件,绑定事件放哪，这个就放哪
      button1: $('#add1'),
      button2: $('#minus1'),
      button3: $('#mul2'),
      button4: $('#divide2'),
      number: $('#number')
    }
      c.bindEvents()
  },
  ...
  v.render()
  c.init()
```

### bug2:本地缓存失效

什么时候获取到n的？`n: localStorage.getItem('n')`时\
获取后什么时候用它的?\
在第1次渲染时把死的html渲染进去了，根本没用到n`const $element = $(v.html).prependTo($('body>.page'))`\
**1.做个替换操作:**  html里加个占位符,然后在渲染时replace下

```js
<span id="number">{{n}}</span>
render() {
  const $element = $(v.html.replace('{{n}}', m.data.n))
                  .prependTo($('body>.page'))
    }
```

**2.既然render可以更新n,那update就不需要那么复杂,直接再次render就好了。**

```js
update(){
  v.render()
}
```

注意再次render时还没有把m.data.n改变,那是什么时候改变的呢？\
代码有问题，我们应该改m的n而不是UI的text。\
**3.现在根本就不需要update,每次重新渲染就行了。把update(){}删掉。**

```js
bindEvents() {
  c.ui.button1.on('click', () => { //功能1
    //let n = m.data.n
    //n += 1
    //m.data.n = n  
    //简化为
     m.data.n += 1
     v.render()
  }
}
```

**4.测试:我点击+1操作失败，为什么显示的是1001？**

```js
console.log("here")
m.data.n += 1
console.log(m.data.n) //1001
v.render()
```

因为n是从localStorage里获取的，这玩意只能保存字符串，所以要parseInt下。

```js
const m = {
  data: {
    n: parseInt(localStorage.getItem('n'))
    }
}
```

**5.再测试:还是不行啊。点击后没有改原来的，而是再次创建个新的UI。**\
思路:替换，不要原来的只要现在的。怎么替换我怎么知道原来有，难道要到页面里检测？\
不，只需要做个标记。\
将视图渲染的结果放到v上面，`el: null,//默认为空`。如果你render了，我就先看下有没有这个el,有的话就直接生成新的替换它
```js
const v = {
  el: null, //默认为空
  ...
  render() {
    if (v.el === null) { //没渲染过
      v.el = $(v.html.replace('{{n}}', m.data.n)).prependTo($('body>.page'))
    } else { //更新el值
      const newEl = $(v.html.replace('{{n}}', m.data.n))
      v.el.replaceWith(newEl)
      v.el = newEl
    }
  }
}    
```

### 最小知识原则

> 模块不应该知道外面的选择器或page

你怎么知道页面有个page呢？应该把它传进来`$('body>.page')`\
**如何接收它呢？**\
将`v.render()`移到init的第1行，先渲染再初始化顺序还是没变，而且还能减少函数调用。最小知识原则再次体现作用。

**init初始化时接收一个参数，把它渲染到页面的哪一块？**\
告诉我个container，渲染时将container传给视图就能拿到container。

```js
const v = {
  render(container) {
    if (v.el === null) { 
      v.el = $(v.html.replace('{{n}}', m.data.n)).prependTo($(container))
    } else { ... }     
    
const c = {
  init(container) {
    v.render(container)
    ...
  },
}
```

这样我们在初始化c`c.init()`时要得到一个东西，app1怎么知道外面有什么div呢？\
**方法:**  先导出c,这样app1需要别人传一个参才能把自己给弄起来。\
然后外面要导入并初始化它，给它个容器。这个容器是写死的，因为我不希望它自己创建个容器。

```js
app1.js
export default c

main.js
import x from './app1.js'  //x就是c的地址，x就是我导出的c。
x.init('#app1')//给它个容器

index.html
<div class="page">
  <section id="app1"></section> //这个容器是我自己写死的
</div>
```

既然这里写了app1，里面就不需要写了，去掉`id="app1"`

```js
 html: `
    把<section id="app1"> 改成 <section>`
```

### [bug3完整代码](https://github.com/lixueqin-4119/js-demo44/blob/master/src/app1.js)

第1次点击可以，第2次点击就不行了啊！这就要用到事件委托\
为什么刷新后事件监听没了？\
原因:页面刷新后,新的button不是之前那个button,**那怎么绑定新的呢？**\
绑定个不会更新的地方。`<section>`加个属性测试下，frank没消失证明这个元素没有被替换，可以绑定事件。


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a57ff29f426f478baa93c268032407b5~tplv-k3u1fbpfcp-watermark.image?)


将container存下来，这样就能通过`v.container`获取了。\
干脆把名改下: 调用的时改为`v.init(container)`

```js
const v = {
  init(container) {
    v.container = $(container)
    v.render()//刷新整个页面
  },
  render() {
    if (v.el === null) { 
      v.el = $(v.html.replace('{{n}}', m.data.n)).prependTo(v.container)//
    } else {
      const newEl = $(v.html.replace('{{n}}', m.data.n))
      v.el.replaceWith(newEl)
      v.el = newEl
    } } }
const c = {
  init(container) {
    v.init(container) //v.render(container)
  }
}
```

**将container存下来:**  替换掉原来的。绑定事件是在视图的容器上，容器不会被替换，容器里面的东西才会被替换。所以只要容器不会替换，监听就不会失效。

```js
bindEvents() {
  v.container.on('click', '#add1', () => {
    m.data.n += 1
    v.render()
  })
/*c.ui.button1.on('click', () => {
    m.data.n += 1
    v.render()
  }*/
)
```

同理，将其它按钮也替换下，代码如下:

```js
const c = {
  init(container) {
    v.init(container)
  /*c.ui = { 直接删掉
      找元素目的是为了绑定事件,绑定事件放哪，这个就放哪
      button1: $('#add1'),
      button2: $('#minus1'),
      button3: $('#mul2'),
      button4: $('#divide2'),
      number: $('#number')
    }*/
    c.bindEvents()
  },
  bindEvents() {
    v.container.on('click', '#add1', () => { //直接使用选择器，UI就不需要了删掉。
      m.data.n += 1
      v.render()
    })
    v.container.on('click', '#minus1', () => {
      m.data.n -= 1
      v.render()
        })
    v.container.on('click', '#mul2', () => {
      m.data.n *= 2
      v.render()
        })
    v.container.on('click', '#divide2', () => {
      m.data.n /= 2
      v.render()
      //let n = parseInt(c.ui.number.text())
      //n /= 2
      //localStorage.setItem('n', n)
      //c.ui.number.text(n)
    })
  }
}
```

**总结：事件委托思路**\
思路:首先要做到**容器不可变:**  这个容器放最外边。每次点击事件时只会更新里面的`<div>`。\
绑的是最外面的元素`<section>`上，就算里面的变了，但id是死的不会变。

```js
v.container.on('click', '#add1', () => { ... })
```

这就叫事件委托，只绑定在最外面的元素上，不绑定在子元素上。

**目前为止MVC到底给了我们什么好处呢？**\
目前只有痛苦。以前很简单监听事件改变DOM元素就完了，现在它从一个非常高的角度说你应该用mvc,但你并不能发现它的好处，因为现在还缺少一些过程。现在还处于初级阶段，但等你实现好了就没有那么多弊端了。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cf5da71f7927448aa11ae67ce13de6f7~tplv-k3u1fbpfcp-watermark.image?)


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a777bc47a40d4b92b699e7c533854348~tplv-k3u1fbpfcp-watermark.image?)

MVC模版，经典的属性，这些属性在所有的MVC中都可以用。
**简化**\
1.el和container都是元素说不定可以合并，因为container的第1个元素就是el。一开始没必要看el是不是null,直接看container里是不是null的。

```js
const v = {
  render() {
  /*if (v.container.children.length === 0) { //没渲染过
      $(v.html.replace('{{n}}', m.data.n)).prependTo(v.container)
    } else {//更新el值
      v.container.empty() //直接清空不替换了
      $(v.html.replace('{{n}}', m.data.n)).prependTo(v.container)
    }
  *///可以简化为
    if (v.container.children.length !== 0) v.container.empty()
    $(v.html.replace('{{n}}', m.data.n)).prependTo(v.container)
  }
}
```

2.container太长了改为el。(**Vue就是这样的思路**)

### 抽象思维3:所有的视图就是渲染下数据

**view=render(data)**\
比起操作DOM对象，直接render简单多了\
只要改变data，就可以得到对应的view

```js
//JS的思维
  n=span.text
  n +=1
  span.text=n
//MVC或者React的思维
  n=100
  render(n)
  n+1=101
  render(n)
```

**上面两种思维哪种更好？**\
思维实验:如果我把下面这个思维做到极致了，我的代码能不能变的更好？[代码](https://github.com/lixueqin-4119/js-demo45/blob/master/src/app1.js)\
**先优化下代码，重复的太多**

```js
/*bindEvents() {
  v.el.on('click', '#add1', () => {
    m.data.n += 1
    v.render(m.data.n)
   })
  v.el.on('click', '#minus1', () => {
    m.data.n -= 1
    v.render(m.data.n)
   })
  v.el.on('click', '#mul2', () => {
    m.data.n *= 2
    v.render(m.data.n)
   })
  v.el.on('click', '#divide2', () => {
    m.data.n /= 2
    v.render(m.data.n)
  })    
}*/
//等价于
const c = {
  init(container) {
    v.init(container)
    v.render(m.data.n) //第1个view=render(data)
    c.autoBindEvents()
  },
  events: {
    'click #add1': 'add1',
    'click #minus1 ': 'minus1',
    'click #mul2 ': 'mul2',
    'click #divide2 ': 'divide2'
  },
  add1() {
      m.data.n += 1
    //v.render(m.data.n)
  },
  minus1() {
      m.data.n -= 1
    //v.render(m.data.n)
  },
  mul2() {
      m.data.n *= 2
    //v.render(m.data.n)
  },
  divide2() {
      m.data.n /= 2
    //v.render(m.data.n)      
  },
autoBindEvents() {
  for (let key in c.events) {
    const value = c[c.events[key]] //c.events[key]取的是字符串，我要取的是方法
    const spaceIndex = key.indexOf(' ')
    const part1 = key.slice(0, spaceIndex + 1)//去掉多余的空格
    const part2 = key.slice(spaceIndex)
    console.log(part1, part2, value)
    v.el.on(part1, part2, value) //拼凑操作
  }
}}
```

总结:这就是表驱动编程-自动绑定事件

### eventBus 对象间通信

> 上面只是+1操作并没有把结果渲染到页面，难道还是要把`v.render(m.data.n)`写4遍？\
> 思考:我们的需求是`m.data.n +=1`时`m.data.n`值一变就自动render。\
> 思路:监听n的变化,2种方法:[Vue2的Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 或者 eventBus对象间通信

**步骤**\
**第1步.声明eventBus空对象**

> eventBus就是一个空对象。传一个空对象，因为我只需要获取它的2个API:**trigger和on方法。**  一个地方触发事件另一个地方监听事件，实现对象间通信。

**第2步.对m创建增、删、改、查方法并触发事件**\
**第3步.监听事件**

```js
const eventBus = $({}) //第1步

const m = { 
  data: { n: parseInt(localStorage.getItem('n')) },
  create() { }, //第2步.创建增删改查
  delete() { },
  update(data) {
    Object.assign(m.data, data) //把data的所有属性一个一个的赋给m.data
    eventBus.trigger("m:updated")//第2步.触发事件，注意事件不能有空格
     localStorage.setItem('n', m.data.n)//保存数据,更新时把它存一下
  },
  get() { }
}
const c = { 
  init(container) {
    v.init(container)
    v.render(m.data.n)
    c.autoBindEvents()
    eventBus.on('m:updated', () => { 第3步.注意事件必须与trigger保持一致
      v.render(m.data.n)
    })
  },
  add1() {
    //m.data.n += 1
    //v.render(m.data.n)
    m.update({ n: m.data.n + 1 })//只有更新时才会触发update件
    },
  minus1() {
    m.update({ n: m.data.n - 1 })
  },
  mul2() {
    m.update({ n: m.data.n * 2 })
  },
  divide2() {
    m.update({ n: m.data.n / 2 })
  },    
```

[功能1的MVC代码](https://github.com/lixueqin-4119/js-demo46/blame/master/src/app1.js)\
**知识点**\
1.`Object.assign()//所有属性`\
Object.assign(m.data, date)//把data的所有属性一个一个的赋给m.data `2.注意,事件不能有空格`eventBus.trigger("m:updated")`

### 功能2: tab切换

> 参照app1.js的模版快速修改[功能2](https://github.com/lixueqin-4119/js-demo47/blob/master/src/app2.js)

**获取当前点击的位置**\
除了"遍历爸爸的儿子"外，还有个更简单的方法**用DOM做标记**\
方法:用data-index绑定一个属性,然后用`e.currentTarget.dataset.index`即可获取当前下标。

**MVC的第1个好处:**  可以让你的操作恒定复杂度。操作很小时看不出区别，当操作很多时一下就能对比出区别了。
