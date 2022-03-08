### 点击事件
**第一部分.概念**
```
<div class=爷爷>
  <div class=爸爸>
    <div class=儿子>文字</div>
  </div>
</div>
```
给3个div分别添加事件监听fnYe/fnBa/fnEr

提问一：点击了谁？

点击文字，算不算点击儿子？

点击文字，算不算点击爸爸？

点击文字，算不算点击爷爷？

**都算**

提问二：调用顺序

点击文字，最先调用fnYe/fnBa/fnEr中的哪个函数？

**都行**

IE5认为先调fnEr，网景认为先调fnYe,然后掐上了，最后闹到了W3C

和事佬W3C
2002年，W3C发布标准，文档名为"DOM Level 2 Events Specification"

规定浏览器应该同时支持两种调用顺序

首先按**爷爷=>爸爸=>儿子**顺序看有没有函数监听

然后按**儿子=>爸爸=>爷爷**顺序看有没有函数监听

**捕获总是在冒泡前面**

这个过程是固定的，只是看中间有没有函数。

有监听函数就调用，并提供**事件信息**，没有就掉过。

**术语**
**从外向内**找监听函数，叫**事件捕获**
**从内向外**找监听函数，叫**事件冒泡**

冒泡是IE创建的事件模型(一般要冒泡)

疑问：那岂不是fnYe/fnBa/fnEr都调用两次?非也！

开发者**自己选择**把fnYe放在**捕获阶段**还是**冒泡阶段**

DOM事件机制图
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/815f1903a11e46158aa8f80fc0f0f4d8~tplv-k3u1fbpfcp-zoom-1.image)


### 第二部分.代码实践
**addEventListener函数**

**事件绑定API**

IE5*:baba.attachEvent('onclick',fn)  //冒泡

网景:baba.addEventListener('click',fn) //捕获

W3C:baba.addEventListener('click',fn,bool) 

**如果bool不传或为falsy(类似于false的值)**

就让fn走冒泡，即当浏览器在冒泡阶段发现baba有fn监听函数，就会调用fn,并提供事件信息

**如果bool为true**

就让fn走捕获，即当浏览器在捕获阶段发现baba有fn监听函数，就会调用fn,并提供事件信息

注意:走冒泡阶段不是说只走这个阶段。而是说捕获、冒泡这个阶段是一定要走的，只不过是你在哪边执行函数的问题。**传false就是把fn放冒泡阶段执行，传true就是把fn放捕获阶段执行。**


例子 [代码](http://js.jirengu.com/guterijaja/2/edit)

1.css
```
div[class^=level] {
  border: 1px solid;
  border-radius: 50%;
  display: inline-flex; 
}
```
2.e会在事件结束后自动消亡，e只存在于事件点击的那一瞬间。

1秒钟之后就没有这个点击事件了。

e是空的。

保存e 代码：const t=e.currentTarget
```
let n=1
level1.addEventListener('click',(e)=>{
  //console.log(e) 空的
  const t=e.currentTarget
  setTimeout(()=>{
     t.classList.remove('x')
  },n*1000)
  n +=1
})
```
3.**冒泡过程：**

颜色变换顺序，从里到外。**默认是冒泡(不加参数)**

**捕获过程：**

颜色变换顺序，从外到里。加true。
```
level7.addEventListener('click',(e)=>{
const t=e.currentTarget
setTimeout(()=>{
     t.classList.remove('x')
  },n*1000)
  n +=1
},true)
```
**4.怎么同时绑定两个事件？(冒泡和捕获)**
```
level1.addEventListener('click',(e)=>{
  const t=e.currentTarget
  setTimeout(()=>{
     t.classList.remove('x')
  },n*1000)
  n +=1
},true)
level1.addEventListener('click',(e)=>{
  const t=e.currentTarget
  setTimeout(()=>{
     t.classList.add('x')  //add
  },n*1000)
  n +=1
})
```

**代码优化**

把重复代码取个名字
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9fac4b7730b64e9f8aaa5f55ee5008ef~tplv-k3u1fbpfcp-zoom-1.image)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f2bd236d17c444ebb5addba380cbe114~tplv-k3u1fbpfcp-zoom-1.image)


**总结**

儿子被点击了，算不算点击老子？**算**

那么先调用老子的函数还是先调用儿子的函数？

**不确定。**

IE里先调用儿子的函数,Firefox里先调用老子。w3c两个都支持，先从老子到儿子再从儿子到老子。

**捕获与冒泡**

捕获说先调用爸爸的监听函数

冒泡说先调用儿子的监听函数

**W3C事件模型**

先捕获(先爸爸=>儿子)**再冒泡?**(再儿子=>爸爸)

冒泡可以阻止

注意e对象被传给所有监听函数

2个过程同时存在，永远不会少任何一个过程。但是没有那么绝对，冒泡是可以阻止的。

### target vs currentTarget
**区别**

e.target 用户操作的元素

e.currentTarget 程序员监听的元素

this是e.currentTarget(不推荐使用它)

**举例**

div>span{文字}，用户点击文字

e.target就是span

e.currentTarget就是div
```
<div><span>文字</span></div>
```

### 冒泡和默认事件

**1.取消冒泡e.stopPropagation()(面试题)**
[示例代码](http://js.jirengu.com/wumefamimu/2/edit)

捕获不可取消，但冒泡可以

**e.stopPropagation()可中断/取消冒泡，浏览器不再向上走**

propagation传播、蔓延

**一般用于封装某些 独立 的组件**

示例
```
level4.addEventListener('click',fn,true)
level4.addEventListener('click',(e)=>{
  e.stopPropagation()
  fm(e) //调用方法
})
```

**2.查看Bubbles和Cancelable**

**所有冒泡都可取消，默认动作有的可以取消有的不能取消**

🔍scroll event mdn

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6d74cbf64c724cd3922a69b40a3357f9~tplv-k3u1fbpfcp-zoom-1.image)

Bubbles的意思是,该事件是否冒泡

Cancelable的意思是,开发者是否可以阻止默认事件

**如何查看一个事件是否可以阻止默认动作？**

搜索该事件，查看Bubbles和Cancelable的值

比如，查看"click事件"就🔍 click event mdn

**3.取消默认动作e.preventDefault()**

**如何阻止滚动？**[示例代码](http://js.jirengu.com/sivakuleze/1/edit?html,js,console,output)

解析:scroll的default不是滚动，scroll之后再去触发某个动作才叫默认动作(事件)，
所以阻止scroll默认动作没用，因先有滚动才有滚动事件。

**要阻止滚动事件，应当阻止滚轮事件wheel的默认动作。**

但是滚动条还能用，🔍css hide scrollbar，用CSS让滚动条display:none; 

**别忘了考虑手机**
```
css
::-webkit-scrollbar { //设置滚轮宽度为0
  width:0 ! important
}

js
x.addEventListener('wheel',(e)=>{ 
  e.preventDefault()
})
x.addEventListener('touchstart',(e)=>{ //取消touchstart事件
  e.preventDefault()
})
```
**总结**

**1.target和currentTarget**

一个是用户点击的，一个是开发者监听的

**2.取消冒泡**

e.stopPropagation()

**3.事件的特性**

Bubbles表示是否冒泡

Cancelable表示是否支持开发者取消默认事件

**4.如何禁用滚动**

取消特定元素的wheel和touchstart的默认动作

### [自定义事件](https://developer.mozilla.org/zh-CN/docs/Web/Events/Creating_and_triggering_events#%E6%B7%BB%E5%8A%A0%E8%87%AA%E5%AE%9A%E4%B9%89%E6%95%B0%E6%8D%AE_%E2%80%93_customevent)
**浏览器自带事件**

除了click和scroll事件还有其它 [事件类型(eventType)](https://developer.mozilla.org/zh-CN/docs/Web/Events)

除了自带事件，还可以**自定义一个事件**[示例代码](http://js.jirengu.com/tuhonowiga/1/edit?js,output)

**触发事件**.dispatchEvent()
```
button1.addEventListener('click', () => {//添加自定义事件
  //第1步.声明自定义事件(事件名，信息)
  //第2步.触发事件
  const event = new CustomEvent('frank', { 'detail': { name: 'frank', age: 18 } })
  button1.dispatchEvent(event)
})
button1.addEventListener('frank', (e) => {//触发自定义事件
    console.log(e.detail)
})
```
**这个事件会冒泡吗？** 不监听button1，监听button1外的div1

可以自己设置bubbles和cancelable的值
```
button1.addEventListener('click', () => { //添加自定义事件
    const event = new CustomEvent('frank', {
        'detail': { name: 'frank', age: 18 },
        bubbles: true,
        cancelable: false
    })
    button1.dispatchEvent(event)
})
...
```

### 事件委托
委托一个元素帮我监听我本该监听的东西。

**场景一**  [示例代码](http://js.jirengu.com/satodiyofu/4/edit?html,js,output)

**你要给100个按钮添加点击事件，咋办？**

第1种.用遍历。可以但不推荐，遍历100遍就添加了100个监听器，每添加一个监听都会有个监听器出现在内存。

第2种.监听这100个按钮的祖先，等冒泡的时候判断target是不是这100个按钮中的一个。
     绑定到祖先div上就行
     
**知识点**

1.快捷录入:`div>button{click $}*100`

2.将标签名小写 代码:x.tagName.toLowerCase()

3.标签文本 代码:x.textContent

4.获取id名  代码:x.dataset.id

**dataset可以获取以data开头的属性的值**

 `<button data-id="1">click 1</button> ` 
 
示例 
```
<div id=div1>
    <span>span 1</span>
    <button data-id="1">click 1</button>
    <button data-id="2">click 2</button>
    <button>click 3</button>
    ...
    <button>click 100</button>
</div>
    
div1.addEventListener('click',(e)=>{
  const t=e.target
  if(t.tagName.toLowerCase()==='button'){
  console.log('button被点击了')
  console.log("button内容是"+t.textContent)
  console.log("button data-id是"+t.dataset.id)
  }
})

输出结果：button被点击了
        button内容是click 1
        button data-id是1
```
**场景二**  [示例代码](http://js.jirengu.com/dubimiqoyo/3/edit?html,js,output)

**你要监听目前不存在的元素的点击事件怎么办？**

监听祖先，等点击的时候看看是不是我想要监听的元素即可。
```
<div id=div1>
</div>

setTimeout(() => {
    const button = document.createElement('button')
    button.textContent = 'click 1'
    div1.appendChild(button)
}, 1000)

div1.addEventListener('click', (e) => {
    const t = e.target
    if (t.tagName.toLowerCase() === 'button') {
        console.log("button 被 click")
    }
})
```
**优点:**

1.省监听数(内存)

2.可以监听动态元素

### 封装事件委托
**要求**  [代码](http://js.jirengu.com/casuzodeji/4/edit)

写出这样一个函数on('click','#testDiv','li',fn)

当用户点击#testDiv里的li元素时，调用fn函数。要求用到事件委托。

**思路**

判断target是否匹配'li'

解析
```
on('click','#div1','button',()=>{
  console.log('button被点击了')
})
function on(){
  要求补充on...
}
```
只要用户用on事件就能在#div1上做事件委托来看button有没有被点击。

**知识点**

1.Element.matches()

**语法**

let result = element.matches(selectorString);

**result 的值为 true 或 false.**

selectorString 是个css选择器字符串.

用来判断一个元素是否适合一个选择器，返回结果为true/false

比如说，判断一个元素是不是button

答案一
```
<div id=div1></div>

setTimeout(() => {
  const button = document.createElement('button')
  button.textContent = 'click 1'
  div1.appendChild(button)
}, 1000)
on('click', '#div1', 'button', () => {
  console.log('button被点击了')
})
function on(eventType, element, selector, fn) { 
  if (!(element instanceof Element)) {
    element = document.querySelector(element)
  }
  element.addEventListener(eventType, (e) => { 
    const t = e.target
    if (t.matches(selector)) {
      fn(e)
    }
  })
}
```
**只要用on就能实现事件委托**

实际上这个答案是错的，但在面试中却能得到满分。


假设在button里套个span，点击按钮测试会有什么后果？[代码](http://js.jirengu.com/jubehalefu/2/edit)
```
setTimeout(() => {
    const button = document.createElement('button')
    const span = document.createElement('span')
    span.textContent = 'click 1'
    button.appendChild(span)
    div1.appendChild(button)
}, 1000)
```
button点击后没有响应。因为当我们在执行 **if(t.matches(selector))** 当前的t元素是span，selector是button，span元素不匹配button就不会执行fn(e)

**答案二** [代码](http://js.jirengu.com/jeturirobi/4/edit)

递归判断target/target的爸爸/target的爷爷。
```
function on(eventType,element, selector, fn) {
  if(!(element instanceof Element)){ //如果element是假的,instanceof返回bool值
    element=document.querySelector(element)
  }
    element.addEventListener(eventType, e => {
      let el = e.target
      while (!el.matches(selector)) {//如果被操做的元素el不符合button,就让这个元素等于它爸
        if (element === el) {//当元素已经到顶，找不到了时，就让元素为null
          el = null
          break
        }
        el = el.parentNode
      }
      el && fn.call(el, e, el)//如果爸爸匹配就调用fn
    })
    return element
  }
```


### JS支持事件吗？
支持,也不支持。

本节课讲的DOM事件不属于JS的功能，术语浏览器提供的DOM的功能。

JS只是调用了DOM提供的addEventListener而已，js本身并不支持事件。

**总结：今天讲的内容是DOM 事件不是JS事件。**

**如何当JS支持事件？请手写一个事件系统。**

**面试题**[用队列](https://zhuanlan.zhihu.com/p/60324936)


**高频面试题（可写博客回答）** 

**1.请简述DOM事件模型/机制**

**2.请简述事件委托**