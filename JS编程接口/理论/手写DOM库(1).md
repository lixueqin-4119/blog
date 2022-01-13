> 手把手教你封装DOM，封装出一些接口，用来操作DOM(页面的节点)。
> 
> 把一个复杂的东西打包成服务，然后只需要简单的命令就可以操作它。

**什么叫封装**

**举例**

笔记本电脑就是CPU、内存、硬盘、主板、显卡的封装

用户只需要接触显示器、键盘、鼠标、触控板等设备，即可操作复杂的计算机。

**接口**

被封装的东西需要暴露一些功能给外部

这些功能就是**接口**，如USB接口、HDMI接口。

这些接口都有规范的文档来说明。文档内容包含功能和实现的细节，别人就可以根据文档内容仿制。

设备只要支持这些接口，即可与被封装的东西通讯

比如键盘、鼠标支持USB接口，显示器支持HDMI接口

封装和接口的关系，如下图所示：


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/669c6097434e48d5ab6d433199e92ca5~tplv-k3u1fbpfcp-watermark.image?)


**术语**

**库**

我们把提供给其他人用的工具代码叫做库(很多函数放到一起给别人用就叫做库)

比如jQuery、Underscore

**API**

库暴露出来的函数或属性叫做 API应用编程接口

**框架**

当你的库变的很大，并且**需要学习才能看懂**，

那么这个库就叫框架，比如Vue/React


**其它**

1.工具parcel

  安装:yarn global add parcel  //yarn global add parcel @1.12.3 指定版本
  
    parcel --version
    
  使用:parcel src/index.html  //启动一个服务器，这个服务器会自动刷新
  
2.简写
```
window.dom.create = function () { } 可以简写成
window.dom = {
    create() { }
}

const childNodes = node.childNodes 可以简写成
const { childNodes } = node
```

3.**template标签可以放任意元素,但不会在页面显示，是专门用来容纳的**

4.trim()去掉字符串两边的空格

 ` const div =dom.create("  <td></td>");`
 
5.const { childNodes } = node //从node获取到childNodes

6.封装删除操作时需要保留节点的引用(要return)

  return node
  
  方便用户继续做其它操作
  
**7.用for循环遍历删掉的childNodes时，childNodes.length是实时改变的**

就是当我们remove后i就不是<7,而是<6了
    
不能用for循环，试试while
    
**8.js中一个函数可以接收多种参数，当操作并列时，可以根据参数长度判断用户的操作**

例如：根据参数长度判断用户是读还是写

读和写操作是一起的，可以根据参数长度判断用户的操作。

  **设计模式**
  
  **重载:根据参数个数写不同的代码就是重载**
  
  **适配:为了确保功能的实现，根据不同的生产环境，编写相应的代码。**
  
9.语法

(1)||

```
return (scope || document).querySelectorAll(selector)
```
**如果有scope就在scope里调用querySelectorAll, 没有就用document来querySelectorAll**

(2)
```
if(typeof name==='string'){
  //如果name是字符串
}else if(name instanceof object){
  //如果是对象
}
```
(3)**for (let key in object) { //遍历object }**

**变量必须放进[]** node.style.[key]

10.JS去操控CSS可能导致回流和重绘。

  document.style.background="red";
  
  document.style.fontSize="24";
  
这样的话相当于【元素的样式被改变了两次】！整个JavaScript的性能就下来了。必要的时候（对一个元素更改多个样式）我们可以“把他们合在一起”：

document.style.cssText="background:red;font-size:24;";

11.加debugger调试


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f7c30e3915934e3e959c42c2a59762c5~tplv-k3u1fbpfcp-watermark.image?)


12.**未为变量赋值时默认值为undefined**

函数，当它被调用时，人们希望它返回一些东西。但是你没有明确返回return任何内容

因此js默认会返回undefined。

13.不管你是用什么语言，实现逻辑时只需要3种表达形式：

   第1种.顺序执行 
   
   第2种.if...else 
   
   第3种.while或者for循环

**封装技术，用2种风格封装DOM操作**

# 第一种.用对象风格封装DOM操作(原生js)
[查看完整代码](https://github.com/lixueqin-4119/dom)

**对象风格，也叫命名空间风格**

window.dom是我们提供的全局对象


### 一.增
**增**

dom.create(`<div>hello</div>`)用于创建节点

dom.before(node,node2)用于新增哥哥

dom.after(node,node2)用于新增弟弟

dom.append(parent,child)用于新增儿子

dom.wrap(`<div></div>`)用于新增爸爸

**1.实现window.dom={} 和 create 创建节点**

新建dom.js进行封装，main.js是接口

代码如下：
```
window.dom={
  create(tagName){  //create
    return document.createElement(tagName);
    } 
};
测试main.js
const div = dom.create("div") //接口
console.log(div)
```
实现
```
<div>  
  <span>1</span>
</div>
思路：这段其实就是个html,把html写到标签里，就可以自动变成元素了。
```
代码
```
window.dom = {
    create(string) {  
        const container = document.createElement("div")
        container.innerHTML = string;
        //console.log(container)
        return container.children[0];
    }
}
测试main.js
const div = dom.create("<div><span>1</span></div>")  //接口
console.log(div)
```
container结构如下：


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8aa7392064154026b70fabdac4c7147d~tplv-k3u1fbpfcp-watermark.image?)

**bug:** 不能放`<td></td>`,因为container本身是div。

div里是不能放<td>的，<td>是表格里的只能放在table tr，否则不符合html语法。

那有什么标签是可以放任意元素，而不出错的呢？

**template标签**

补充：template只能用`content.firstChild`获取

**trim()把字符串两边的空格给去掉。**

const div =dom.create("`<td>hi</td>`");
```
window.dom = {
    create(string) {  
        const container = document.createElement("template")
        container.innerHTML = string.trim();
        //console.log(container)
        return container.content.firstChild
    }
}
```
**2.实现after新增弟弟**
> 把node2插入到node的下一个节点的后面
```
after(node, node2) { 
    node.parentNode.insertBefore(node2, node.nextSibling)
  }
测试main.js  代码：dom.after(test, div)
```

**当node是最后一个节点时，还能插入吗？**
```
<div>
  <div id="test">test</div> //回车在html里是文本
</div>
```
不是最后一个，最后一个节点是回车。

`<div><div id="test">test</div></div>` 

console.log(node.nextSibling)

当node是最后一个节点时，测试后可以插入。

**3.实现before新增哥哥**
```
before(node, node2) {
        node.parentNode.insertBefore(node, node2)
    }    
测试main.js 
dom.before(test, div)
```
**4.实现append新增儿子**
```
append(parent, node) {
  parent.appendChild(node)
}
接口
const div = dom.create("<div>newDiv</div>")
console.log(div)
dom.append(div, test)
```
**5.实现wrap新增爸爸**
> 思路：先把div3(parcet)插到前面，然后再将div2(node)放到div3里。 append特点：默认只会出现一次且只取最后的值


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e31936a8caf430b8b400c1436040016~tplv-k3u1fbpfcp-watermark.image?)

```
wrap(node,parent){
  dom.before(node,parent)
  dom.append(parent,node)
}
接口
const div3 = dom.create(`<div id="parent"></div>`)
dom.wrap(test, div3)
```


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/54d43c25d6de42d8b17201c291557d2d~tplv-k3u1fbpfcp-watermark.image?)

### 二.删
**删**

dom.remove(node) 删除节点

dom.empty(parent)自己不删，删后代
```
remove(node) {
   node.remove() //node.parentNode.removeChild(node)
   return node   //保留节点的引用
}
接口
dom.remove(test)
```
**remove特点：移除会返回节点**
```
<div id="empty">
  <div id="em1">
    empty1
    <div id="em2"></div>
    <div id="em3"></div>
  </div>
  <div id="e2">
     empty2
     <div class="e"></div>
     <div class="e1"></div>
  </div>
</div>
        
empty(node) {
  const { childNodes } = node //从node获取到childNodes
  const array = []
  let x = node.firstChild
  while (x) {
    array.push(dom.remove(node.firstChild))
    x = node.firstChild //循环，每次都把第1个删掉，直到删完。
        }
   return array //获取节点的引用，方便用户继续做其它操作
}
接口
const nodes = dom.empty(window.empty)
console.log(nodes)
```
回车在html中是文本节点

### 三.改

1.dom.attr(node,'title',?)用于读写属性

2.dom.text(node,?)用于读写文本内容

3.dom.html(node,?)用于读写html内容

4.dom.style(node,{color:'red'})用于修改style

5.dom.class.add(node,'blue')用于添加class

6.dom.class.remove(node,'blue')用于删除class

7.dom.class.has(node,'blue')用于验证是否包含class

8.dom.on(node,'click',fn) 用于添加事件监听

9.dom.off(node,'click',fn)用于删除事件监听

**1.dom.attr(node,'title',?)用于读写属性**
```
attr(node,name,value){
    node.setAttribute(name,value)
}
接口
dom.attr(test,'title','hi,I am li.') //设置title
const title=dom.attr(test,'title')
```
**读title**
> **js中一个函数可以接收多种参数，可根据参数长度判断用户的操作**
> 
> 读写操作是一起的，可以根据参数长度判断用户是读还是写。

读写操作是一起的，可以根据参数长度判断用户是读还是写。
```
attr(node, name, value) { //重载
        if (arguments.length === 3) { //如果参数为3个就set
            node.setAttribute(name, value)
        } else if (arguments.length === 2) {//如果参数为2个就get
            return node.getAttribute(name)
        }
    }   
接口
dom.attr(test, 'title', 'hi,I am li.')

const title = dom.attr(test, 'title') //接收2个参数
console.log(`title:${title}`)
```
**重载:根据参数个数写不同的代码就是重载**

**2.dom.text(node,?)用于读写文本内容**
```
text(node,string){  
    if(arguments.length===2){  //重载
      //console.log('innerText' in node)
        if('innerText' in node){ //适配
            node.innerText=string //只支持IE
        }else{
            node.textContent=string //只支持firefox、chrome
        }
    }else if(arguments.length===1){
        if('innerText' in node){
           return node.innerText
        }else{
           return node.textContent
        }
    } 
}

接口
dom.text(test, '你好，这是新文本')
dom.text(test)
```
**适配：为了确保功能的实现，根据不同的生产环境，编写相应的代码。**
 
**3.dom.html(node,?)用于读写html内容**
```
html(node,string){
    if(arguments.length===2){
    node.innerHTML=string
    }else  if(arguments.length===1){
        return node.innerHTML
    }
}
接口
dom.html(test, '<span>newHtml</span>')
dom.html(empty)
console.log(empty)
```

**4.dom.style(node,{color:'red'})用于修改style**
```
style(node, object) {
  for (let key in object) { //遍历object的key
  //key:border/color
  //node.style.border=...
  //node.style.color=...
  //变量必须放[]
  node.style[key] = object[key]
    }
}   
接口
//对象的key是它的属性名，value是属性值
dom.style(test,{ border:'1px solid red',color:'blue' }) 
dom.style(test, 'border')
dom.style(test, 'border', '1px solid red')
//如果参数是3个就是设置，如果是2个可能是读也可能是写
```
**变量必须放[]**
```
style(node, name, value) {
  if (arguments.length === 3) {
    //dom.style(div,'color','red')
    node.style[name] = value
  } else if (arguments.length === 2) {
     if (typeof name === 'string') { //如果name是字符串
     //dom.style(div,'color')
       return node.style[name]
     } else if (name instanceof Object) {
        //dom.style(div,{color:'red'})
        const object = name
        for (let key in object) { //遍历object
          node.style[key] = object[key]
         }
 }}}
接口
dom.style(test, { border: '1px solid red', color: 'blue' })
console.log(dom.style(test, 'border'))
dom.style(test, 'border', '1px solid black')
```
如果是对象就设置它的值,如果是字符串就获取它的值.

5.**dom.class.add(node,'blue')   用于添加class**
  **dom.class.remove(node,'blue')用于删除class**
  **dom.class.has(node,'blue')用于验证是否包含class**
```
<style>
  .red { background: red; } 
  .blue {color: blue;}
</style>

class: {
  add(node, className) {
     node.classList.add(className)
  },
  remove(node, className) {
     node.classList.remove(className)
  },
  has(node, className) {
     return node.classList.contains(className) //记得return
  }
}   
接口
dom.class.add(test, 'red')
dom.class.add(test, 'blue')
dom.class.remove(test, 'red')
console.log(dom.class.has(test, 'red'))
```
6.**dom.on(node,'click',fn)  用于添加事件监听**

  **dom.off(node,'click',fn) 用于删除事件监听**
```
on(node, eventName, fn) {
  node.addEventListener(eventName, fn)
    },
off(node, eventName, fn) {
  node.removeEventListener(eventName, fn)
    }
接口
const fn = () => { console.log('点击了') } //给函数取名字
dom.on(test, 'click', fn)
dom.off(test, 'click', fn)
```

### 四.查
1.dom.find('选择器')用于获取标签或标签们

2.dom.parent(node)用于获取父元素

3.dom.children(node)用于获取子元素

4.dom.siblings(node)用于获取兄弟姐妹元素

5.dom.next(node)用于获取弟弟

6.dom.previous(node)用于获取哥哥

7.dom.each(nodes,fn)用于遍历所有节点

8.dom.index(node)用于获取排行老几

**1.dom.find('选择器')用于获取标签或标签们**

上面一直用的选择器是固定死的(全局id=test)

实际应该是可选的

**(1)实现find，返回用户可选的对应元素**
```
find(selector) {
  return document.querySelectorAll(selector) //返回数组
    }
接口
const testDiv = dom.find('#test')[0] //获取数组的第1个
console.log(testDiv)
```
用户提供选择器,不管给的选择器是一个还是多个元素，全给你返回数组的第1个。
  
**(2)在指定范围找特定标签**
```
<div>
  <div id="test"><span>test1</span>
    <p class="red">find指定范围找特定标签1</p>
  </div>
  <div id="test2">test2
    <p class="red">find指定范围找特定标签2</p>
  </div>
</div>
        
find(selector, scope) { //选择器，范围
  //debugger调试
  return (scope || document).querySelectorAll(selector) //返回数组
  //如果有scope就在scope里调用querySelectorAll, 没有就用document来querySelectorAll
}
接口
const testDiv = dom.find('#test')[0]
console.log(testDiv)
console.log(dom.find('.red', testDiv)[0])
```
**如果有scope就在scope里调用querySelectorAll, 没有就用document来querySelectorAll**


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a2ea0a289bbf42189c040e37902104ad~tplv-k3u1fbpfcp-watermark.image?)


**2.dom.parent(node)用于获取父元素**
```
parent(node) {
  return node.parentNode
}  
接口
console.log(dom.parent(em1))
```
**3.dom.children(node)用于获取子元素**
```
<div id="em1">
  <div id="em2"></div>
  <div id="em3"></div>
</div>

children(node) {
  return node.children
}
接口
console.log(dom.children(em1))
```

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ff8f39923afc4cd1ac969a4bf456e9f2~tplv-k3u1fbpfcp-watermark.image?)

children已经找到了，但显示的是类似数组的HTMLCollection对象，不是我要的结果？[要获取每个子元素自然要遍历它](https://segmentfault.com/a/1190000004499453)            [为什么会有undefined？](https://www.coder.work/article/1040746)

js函数被调用时，如果没有明确return任何内容，就会默认返回undefined。
如果函数里存在判断导致没有返回值，返回undefined，如何看待或处理这个undefined?    [如何处理函数参数的意外值？](https://www.zhihu.com/question/510046198)

**4.dom.siblings(node)用于获取兄弟姐妹元素**
```
<div id="siblings">
  <div id="s1"></div>
  <div id="s2"></div>
  <div id="s3"></div>
</div>
    
siblings(node) { //待遍历
  return Array.from(node.parentNode.children).filter(n => n !== node)
 }      
接口
console.log(dom.siblings(dom.find("#s2")[0]))
```
children是伪数组，要先变成数组Array.from()，变成数组后就可以filter
过滤，只要元素不等于当前节点就把它放到数组里

**5.dom.next(node)用于获取弟弟**

直接return node.nextSibling会返回文本,需要筛选[nodeType](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/nodeType)

```
next(node) {
  let x = node.nextSibling
  while (x && x.nodeType === 3) {
     x = x.nextSibling
   }
   return x
}
接口 
const s2 = dom.find("#s2")[0]
console.log(dom.next(s2))
```
**6.dom.previous(node)用于获取哥哥**
```
previous(node) {
  let x = node.previousSibling
  while (x && x.nodeType === 3) {
    x = x.previousSibling
   }
  return x
 }
接口
const s2 = dom.find("#s2")[0]
console.log(dom.previous(s2))
```
**7.dom.each(nodes,fn)用于遍历所有节点**

find返回的是数组，要找第1个元素。dom.find('#travel')[0]
```
<div id="travel">
  <div id="t1">t1</div>
  <div id="t2">t2</div>
  <div id="t3">t3</div>
</div>

each(nodeList, fn) {
  for (let i = 0; i < nodeList.length; i++) {
  fn.call(null, nodeList[i])//没有this,this不传
        }
}    
接口
const t = dom.find('#siblings')[0]
dom.each(dom.children(t), (n) => dom.style(n, 'color', 'red'))
```

**8.dom.index(node)用于获取排行老几**
```
index(node) {
        const list = dom.children(node.parentNode)
        let i
        for (i = 0; i < list.length; i++) {
            if (list[i] === node) {
                break
            }
        }
        return i
    }    
接口  
console.log(dom.index(t1))
```

