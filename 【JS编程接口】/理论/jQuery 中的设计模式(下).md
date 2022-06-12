[阮一峰](http://www.ruanyifeng.com/blog/2011/07/jquery_fundamentals.html) 、[中文文档](https://www.jquery123.com/)、[完整代码](https://github.com/FrankFang/dom-2-prototype/blob/master/src/jquery.js)

### 1.简写jQuery
**window.$=window.jQuery**

方法:页面最后添加`window.$=window.jQuery`,任意地方就可以直接使用`$`

`代码：$('.test').children().print()`

类似于bash alias,添加一个别名即可。
```js
window.jQuery=function(selectorOrArrayOrtemplate){...}
window.$=window.jQuery
这两句可以合成下面一句
window.$=window.jQuery=function(selectorOrArrayOrtemplate){...}
```
**执行顺序：当两个=在同一行时,那么是从右边开始执行的。**

也就是说，先执行window.jQuery=function(selectorOrArrayOrtemplate){...}，

得到window.jQuery的值后，再将值赋值给window.$。

### 2.命名风格
**下面的代码令人误解**
```js
const div1=$('.test')
const div2=document.querySelector('.test')
```
我们会误以为div1是一个DOM,实际上div1是jQuery构造的api对象。

**DOM对象只能用DOM API** 如querySelector、appendChild

**jQuery对象只能用jQuery的API** 如find、each

错误用法：div1.document.querySelector('.test')

**怎么避免这种误解，如何区分？**

**jQuery API加$ ,DOM API 加el或不加 。**
```js
const $div1=$('.test')
const elDiv2=document.querySelector('.test') 
或者 const div2=document.querySelector('.test') 
```
**$开头的变量都是jQuery对象**

# 3.链式风格
jQuery.prototyppe

jQuery实际上就是调用dom api,除此之外就是加一些if/for循环

一.增

`$('<div><span>1</span></div>')`

`.appendTo(document.body)`插入到body中


思路:

`const $div=$('<div></div>')`

`$div.appendTo(document.body)`


**二.删**

`$div.remove()`

`$div.empty()`

**三.改**

1.`$div.text(?)`读写文本内容

2.`$div.html?)`读写html内容

3.`$div.attr('title',?)`读写属性

4.`$div.css({color:'red'})`读写style

5.`$div.addClass('blue')`添加class

6.`$div.on('click',fn)` 添加事件监听

7.`$div.off('click',fn)`删除事件监听

**注意**

`$div`大部分时候对应了多个div元素

思路

传参就是写文本，没传参就是读文本。

1.`$div.css`是用来操作"内联的style"

2.`$div`有可能是一个也有可能是多个div,要默认div是数组然后遍历它，每一个操作都要遍历。

# 4.使用原型(节约内存)
**使用原型**

把共用属性(函数)全部放到`$.prototype`

`$.fn=$.prototype` //名字太长

然后让api.__proto__指向`$.fn`

我们声明了一个函数，这个函数会获取到这个elements，然后会返回一个对象，这个对象里面的api会去操作这个elements。

```js
window.$ = window.jQuery = function (selectorOrArray) { //声明函数
  let elements  //获取elements
  if (typeof selectorOrArray === 'string') { 
    elements = document.querySelectorAll(selectorOrArray)
  } else if (selectorOrArray instanceof Array) {
    elements = selectorOrArray
  }
  return { //返回一个对象
    addClass(className) { //对象里面的api会去操作这个elements
      ...
    },
    ...
 } }
```
如果我有2次调用呢？这个操作实际上重复了。
```js
const api1=$('.red')
const api2=$('.blue')
```
api1和api2的内存实际上是一摸一样的，都需要find和each。

**那既然是共用属性，我们就可以把共用函数写到一个对象上去。**

然后将原型指向共用对象__proto__:#619

**那#619这个对象叫什么名字？放哪？**

把这个对象放到jQuery上。#619=jQuery.prototype

即api1.__proto__===jQuery.prototype

### 如何实现？
**第1步**
**先把函数都移走，只留下自己的属性。然后加上下面的代码：**
```js
jQuery.prototype={
  constructor:jQuery
}
```
**为什么加这句代码？**

因为一开始jQuery的prototype就有constructor属性

**第2步**

(1)把共用的都移到jQuery.prototype={ }

把共用的都拷进来，共用的不一定都是函数。

注意elements不能拷它不是共用的。

(2)再把jQuery.prototype={} 与 return{} 关联起来。
```js
const  api=Object.create(jQuery.prototype)
```
创建一个对象，这个对象的__proto__为括号里面的东西，也就是jQuery.prototype。

相当于`const api={__proto:jQuery.prototype}`

**然后再将剩下的"非共有属性"关联起来。(改写return)**
```js
api.elements=elements
api.oldApi=selectorOrArrayOrTemplate.oldApi
return api
```
**把"非共有属性"放到我自己身上。**

上面代码可以优化为

```js
window.$ = window.jQuery = function (selectorOrArray) {
  ...
  const  api=Object.create(jQuery.prototype)
  Object.assign(api,{
    elements:elements,
    oldApi:selectorOrArrayOrTemplate.oldApi
  })
  return api
}
```
**Object.assign意思是把后面对象的属性一个一个的复制到前面(api)。**

也就是把elements复制到api.elements,

把selectorOrArrayOrTemplate.oldApi复制到api的
selectorOrArrayOrTemplate.oldApi

**这样就实现了内存的节约，原型的本意就是节约内存。**

### 变量访问不到用api作为桥梁
**注意**

把共有属性放到jQuery.prototype{}后，elements就不能用了，因为elements变量之前都在同一个函数里面。分开后不处于同一作用域，自然就不能访问变量(elements)了。


上面我们将elements放到了api上，函数要想访问elements就需要得到

api.elements,this就是api。所以只需要在elements前加上this即可！

将所有共用属性里对elements的读操作改成`this.elements`。 elements前面加上this，用this访问api.
```js
Object.assign(api,{
    elements:elements,
    oldApi:selectorOrArrayOrTemplate.oldApi
  })
```

**jQuery觉得它的prototype太长了，便取了个别名fn**

fn表示prototype
```js
jQuery.fn=jQuery.prototype={ ... }
```

### 把代码公开github
添加文档告诉别人怎么用

**这就叫造轮子。**

每个人都会实现一个全局函数jQuery给别人用,如果你的函数好用就会获得star。

jQuery就是有个程序员贡献给前端社区用的。

jQuery有多牛X,是目前前端最长寿的库(06年发布)，是世界上使用范围最广泛的库,95%的网站都在用[jQuery](https://trends.builtwith.com/javascript/jQuery)。
其它的像vue、react基本不会超过12年。

### jQuery用到了哪些设计模式？
**1.不用new的构造函数，这个模式没有专门的名字**
```
const api=new jQuery('.red') //不需要new
const api=$('.red')
```
**2.$(支持多种参数)，这个模式叫重载**

可以传选择器、数组、div

**重载：一个函数支持多种形式的参数**

支持字符串、对象、数组

**3.用闭包隐藏细节，这个模式没有专门的名字**

**闭包：如果一个函数用到了外部的变量，那么该函数和这个变量就叫做闭包**

闭包的好处是，用户永远不能直接操作elements,必须通过函数来操作elements。

闭包隐藏细节就是，生成一个变量，一个函数去读这个变量。

**4.getter/setter**

$div.text()即可读也可写。

getter/setter就是，有一个函数它既可以get也可以set。
```
以前要想读写必须写两个函数：
getText()
setText('newValue')
现在一个函数就够了：
$div.text('newValue') //传了参数就是写，否则就是读。
```
**5.别名**

`$.fn是$.prototype`的别名，让一个名字=另外一个名字，这叫别名

**6.适配器**

jQuery针对不同的浏览器使用不同代码，这叫适配器。

if...else

**设计模式是啥？**

这个代码写的太漂亮了，别人肯定也用得到

那就给这种写法取个名字吧，比如适配器模式

设计模式就是对通用代码取个名字而已

### 应该学习设计模式吗？

**设计模式不是用来学的**

你看了这些代码，但你并不知道这代码解决什么问题

看了白看

**设计模式是用来总结的**

你只管去写代码

把你的代码尽量写好，不断重写

总结你的代码，把写得好的地方抽象出来

看看符合哪个设计模式

你就可以告诉别人你用到了这几个设计模式

jQuery简单又经典，通过jQuery可以学会很多封装技巧。
