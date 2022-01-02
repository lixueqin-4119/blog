# DOM 编程
**网页其实是颗树,JS如何操作这棵树?**

JS操作不了。让浏览器往window上加一个document。

JS用document操作网页

"用document对象操作整个网页"这种思想就叫做文档对象模型(Document Object Model)

**记住一个事实：DOM很难用**

下节课我们会想办法解决这个难题(封装)。

如果你觉得DOM很傻，不要怀疑自己，你觉得是对的。

DOM的接口设计的非常反人类，导致前端程序员不得不使用jQuery去操作DOM。后来jQuery又被Vue代替了，于是我们又用Vue操作DOM。后来又有了React,我们又用React操作DOM。

我们从来不会用DOM自带的功能操作DOM，因为实在是反人类。

但是我们还是要学DOM,不然看不懂 虚拟DOM 和 jQuery。

其它

1.箭头函数不支持this，有箭头函数就不能有this！

2.在html里，不管有多少空格都会缩成一个空格。

3.JS里面的属性统称为properties,HTML里面的属性统称为attributes。
 
# DOM提供的所有函数(API)
### 一.获取任意元素
> 标签又叫元素，获取元素的API

**有很多API**

1.window.id或者直接id  //最快

2.document.getElementById('id') 

3.document.getElementsByTagName('div')[0] //找到所有标签名为div的'第1个'元素

4.document.getElementsByClassName('red')[0] //找到所有class名为red的'第1个'元素

**5.document.querySelector('#id')**

**6.document.querySelectorAll('#id')[0]**

**用哪一个**

1.工作中用querySelector和querySelectorAll

2.做demo直接用id

3.要兼容IE的才用getElement(s)ByXXX
```
id="kw"
window.kw 或者 kw
document.querySelector('#id') //(css选择器的写法)
例如：document.querySelector('div>span:nth-child(2)')
```

### 二.获取特定元素
1.获取html元素:document.documentElement

  获取标签名 document.documentElement.tagName
  
2.获取head元素:ducument.head

3.获取body元素:ducument.body

4.获取窗口(窗口不是元素):window

  代码:window.onclick=()=>{console.log('hi')}  //获取window,添加事件监听
  
5.获取所有元素

  document.all //获取当前页面所有标签
  
 **变态**
 
  document.all是IE发明的奇葩，第6个falsy值。
  
  document.all以前用来区分是否是IE(现在document.all默认为假)
```
if(document.all){console.log('ie浏览器'); 只能在ie运行}
else{console.log('其它浏览器'); 只能在非ie运行}
结果:其它浏览器

不做判断时可以直接用
document.all[2]
```
**获取到的元素是个啥？**

都是对象。

只需要搞清对象自身有哪些属性，以及它的原型有哪些即可。

# div 原型链
> 元素的 自身属性 和 6层原型链

js的所有对象都有个隐藏属性，这个隐藏属性指向了对象的原型。

div.__proto__===HTMLDivElement.prototype
```
百度浏览器
let div=document.getElementsByTagName("div")[0]
console.dir(div)
输出:div#wrapper.wrapper_new
    ... //div自身属性
    [[Prototype]]: HTMLDivElement //所有div的共同属性
    [[Prototype]]: HTMLElement //所有标签的共同属性
    [[Prototype]]: Element //所有元素的属性
    [[Prototype]]: Node //节点
    [[Prototype]]: EventTarget
       1.addEventListener 添加
       2.dispatchEvent 触发
       3.removeEventListener 删除
    [[Prototype]]: Object //根对象
```
**节点Node?元素Element?怎么区分？**

**节点分为标签和文字**

节点包括下面几种不同的节点:

x.nodeType得到一个[数字](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/nodeType)

**1表示元素Element，也叫标签Tag**

**3表示文本Text**

8表示注释Comment

9表示文档Document

例子
```百度浏览器
div.nodeType
输出:1
div.childNodes
输出:NodeList(6) [略...]
div.firstChild
输出:#text
div.firstChild.textContent
结果:'\n'
div.firstChild.nodeType
3
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0b35757a06e24feb966a331477602902~tplv-k3u1fbpfcp-zoom-1.image)

由于div是由HTMLDivElement这个函数构造出来的，所以这个构造函数往this(div)上添加了hidden属性、...

也继承了Element,Element给this加了id、...

也是Node构造的,Node给this加了childNodes/firstChild...

**每一层构造函数都会给this上加属性**

# 节点的增删改查
### 一.增
**创建一个标签节点**

let div1=document.createElement('div')

document.createElement('style')

document.createElement('script')

document.createElement('li')

**创建一个文本节点**

let text1=document.createTextNode('你好')

**标签里面插入文本**

1' div1.appendChild(text1) 

2' 推荐

let div1=document.createElement('div')

div1.innerText='你好' 或者 div1.textContent='你好'

但是不能用div1.appendChild('你好')

不同的原型提供了不同的函数，但是不能混着用！

**插入页面中**

**这时只会创建到内存不会显示到页面**

你创建的标签默认处于JS线程中，必须把它插到head(`<style>或<link>`)或者body里面才会生效。

**document.body.appendChild(div1)或者 已在页面中的元素.appendChild(div1)**
```
document.body.appendChild(div1)
div1.style.top = 0
div1.style.left = 0
div1.style.color='red'
div2.style.width=300
```

### appendChild
代码

页面中有 div#test1 和 div#test2

let div=document.createElement('div')

test1.appendChild(div)

test2.appendChild(div)

请问最终div出现在哪里？

test2里面。因为一个元素不能出现在两个地方，除非复制一份。
```
let div1=document.createElement('div')
let text1=document.createTextNode('你好')
div1.appendChild(text1)
div1
document.head.appendChild(div1)
document.body.appendChild(div1)
复制，让head body两个地方都有
let div2=div1.cloneNode(true) //复制
document.head.appendChild(div1)
document.body.appendChild(div2)
```

### 二.删
1' parentNode.removeChild(childNode)

2' childNode.remove() 注意:不支持ie

例子
```
div1.parentNode  //id="div1" 
div1.parentNode.removeChild(div1)
```
怎样彻底删除节点？

div2.remove()

div2=null
                 
### 三.改
**1.改属性**

**(1)写标准属性**

1' 改class:div1.className='red' 会覆盖之前的,id="div1"

2' 改class:div1.classList.add('green') 

改style:div1.style='width:200px;color:blue;' 会覆盖之前的

改style的一部分:div1.style.width='200px'

大小写：div1.style.backgroundColor='white' //所有用-隔开的，用大写代替

改data-* 属性:div1.dataset.x='fuck' 添加自定义属性

获取自定义属性值:div1.getAttribute('data-x')或者div1.dataset.x

例子
```
自定义属性
s_is.dataset.x='fuck' //id='s_is'
输出：dataset.x='fuck'
获取
s_is.getAttribute('data-x')或者 s_is.dataset.x
输出：fuck
```

**(2)读标准属性**

查看样式、id、class：div1.style/id/className

1' div2.classList/a.href

2' div2.getAttribute('class')/a.getAttribute('href')

2种方法都行，读样式、id、class时都一样，只是在**读链接**时值有点不同：
**用href时，有可能浏览器会自动加一些东西。**

例子
```
<a id='test' href="/xxx">/xxx</a>

1' console.log(test.href) //浏览器会自动加些东西
   输出："http://js.jirengu.com/xxx"

2' console.log(test.getAttribute('href')) //可获取链接的原本值
  输出："/xxx"
```

**(3)改on开头的属性(改事件处理函数)**

**div1.onclick默认为null**

默认点击div不会有任何事发生

但是如果你把div1.onclick改成一个函数fn,那么点击div时浏览器就会调用这个函数。

**而且是这样调用的fn.call(this,event)** event是第1个参数

div1会被当作this,event则包含了点击事件的所有信息，如坐标

如果需要this，就不能用箭头函数，用function才能得到this。因为箭头函数不支持this。

**div1.addEventListener是升级版的div1.onclick,后面会说**
```
<div id='div1'>test</div>

console.log(div1.onclick);
输出：null
div1.onclick=()=>{ console.log('hello') }

div1.onclick=function(x){ //event是第1个参数 x
  console.log(this) //this就是div1,x就是event
  console.log(x) 
}
//原理：div1.onclick.call(div1,event)
```
点击后，this 和 x 是浏览器用call传进来的

**(4)改内容** //id="div1"

**改文本内容** 

div1.innerText='xxx'或者 div1.textContent='xxx'

**改html内容**

div1.innerHTML='`<p> <strong>重要</strong> </p>`' 

字符限制在2万以内超出浏览器会卡死

**改标签**

div1.innerHTML='' 

div1.appendChild(div2)

先清空再加内容

**(5)改爸爸**
newParent.appendChild(div1)

### 查
**查爸爸**

node.parentNode或者node.parentElement

**查爷爷**

node.parentNode.parentNode

例子
```
<div id='div1'></div>
div1.parentNode
div1.parentNode.parentNode
div1.children
```
**查子代**

1' node.childNodes //不要用，包括文本节点，会获取到不想要的

2' node.children 

当子代变化时，两者也会实时变化吗？

都会实时变化。querySelectorAll不会实时更新

let c=document.querySelectorAll('li')
```
<ul id='test'>
  <li>1</li>
  <li>2</li>
</ul>
console.log(test.childNodes.length)
console.log(test.childNodes)
5 //在html里，不管有多少空格都会缩成一个空格。

<ul id='test'><li>1</li><li>2</li></ul>
console.log(test.childNodes.length) //2
```
console.log(test.children.length) //2

**查兄弟姐妹**

1' node.parentNode.childNodes //需要排除自己以及所有的文本节点，更麻烦

2' node.parentNode.children //需要排除自己
```
id='div2'
div2.parentNode.childNodes //包含自己
let siblings=[] //遍历,排除自己
let c = div2.parentNode.children
for(let i=0;i<c.length;i++){
  if(c[i]!=div2){
     siblings.push(c[i])
  }
}
```
**查看老大**

node.firstChild 

**查看老幺**

node.lastChild

**查看上一个哥哥/姐姐**

node.previousSibling

**查看下一个弟弟/妹妹**

node.nextSibling //包含文本节点

可以避开文本节点node.previousElementSibling

例子
```
id='div2'
document.body.firstChild //或者document.body.children[0]
document.body.lastChild
div2.previousSibling
div2.previousElementSibling
```
**遍历一个div里面的所有元素**
```
travel=(node,fn)=>{ //接收节点和函数
  fn(node) //传参，fn在后面定义
  if(node.children){ //如果当前节点有子元素，遍历子元素
    for(let i=0;i<node.children.length;i++){
      travel(node.children[i],fn)//每一个节点再travel下，递归的travel
    }
  }
}
travel(div1,(node)=>console.log(node))//箭头函数是fn
```

# DOM 操作跨线程(高级知识)
浏览器功能划分：浏览器分为渲染引擎和JS引擎

**跨线程操作**

**各线程各司其职**

js引擎不能操作页面,只能操作js

渲染引擎不能操作js,只能操作页面

document.body.appendChild(div1)

这句js是如何改变页面的？

**跨线程通信**

当浏览器发现js在body里面加了个"div1"对象

浏览器就会通知渲染引擎在页面里也新增一个div元素

新增的div1元素所有属性都照抄div1对象

**时间浪费在浏览器。浏览器发现后去通知线程，这个是要时间的。所以操作会比其它操作慢。**

为什么要分两个线程？

让每一块都变的简单，好优化。

变得慢可以优化其它的点，比如可以单独给渲染做优化。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f8793e53b7aa4843b3b6ef95bd5994d8~tplv-k3u1fbpfcp-zoom-1.image)

**插入新标签的完整过程**

在div1放入页面之前，你对div1所有的操作都属于js线程内的操作。

把div1放入页面之时，浏览器会发现JS的意图，就会通知渲染线程在页面中渲染div1对应的元素。

把div1放入页面之后，你对div1的操作都**有可能**会触发重新渲染

div1.id='newId'可能会重新渲染，也可能不会

div1.title='new'可能会重新渲染，也可能不会

例子
```
<div title="titlehi"></div>
div::after{ 
  content:attr(title);
}
输出：titlehi
```
如果连续对div1多次操作，浏览器可能会合并成一次操作，也可能不会

### 属性同步
**标准属性**

对div1的标准属性的修改，会被浏览器同步到页面中

比如id、className、title

**`data-*属性`**

会同步

**非标准属性**

对非标准属性的修改，则只会停留在js线程中，不会同步

**如果你有自定义属性想被同步，可以使用data-作为前缀**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5709f67225c144ffa81d9f047da5ebb0~tplv-k3u1fbpfcp-zoom-1.image)


**property vs attribute**
> JS属性统称为properties,HTML属性统称为attributes。

**property属性**

js线程中div1的所有属性，叫做div1的property

**attribute也是属性**

渲染引擎中div1对应标签的属性，叫做attribute

区别

大部份时候，同名的property和attribute值相等

但如果不是标准属性，那么它俩只会在一开始时相等

**但注意attribute只支持字符串**

property支持字符串、布尔等类型

**面试题**

**为什么DOM操作比较慢？**

时间浪费在浏览器。浏览器发现后去通知线程，这个是要时间的。所以操作会比其它操作都慢。
