上一次我们用对象风格封装DOM操作(原生js)，这次用jQuery风格重新封装。
  
**jQuery核心思想** 
> 接受一个selector。\
> 根据这个选择器得到一些元素。\
> return 一个对象。\
> 这个对象有些方法可以操作这个元素。

**用jQuery风格封装**
> [完整代码](https://sourcegraph.com/github.com/FrankFang/dom-2@master/-/blob/src/jquery.js?L94)\
> **链式风格也叫jQuery风格**\
> window.jQuery()是我们提供的全局函数\
> **特殊函数jQuery**\
> jQuery(选择器)用于获取对应的元素\
> 但它却不返回这些元素\
> 相反，它返回一个对象，称为jQuery构造出来的对象\
> 这个对象可以操作对应的元素

# jQuery核心思想
### 1.闭包 ＆ 链式操作
**添加class**
```js
<div class="test">测试1</div>
<div class="test">测试2</div>
<div class="test">测试3</div> 
    
window.jQuery = function (selector) { //接收1个选择器#test
    const elements = document.querySelectorAll(selector)
    //api可以操作elements
    const api = {
        addClass(className) {
            for (let i = 0; i < elements.length; i++) {
                elements[i].classList.add(className) //闭包
            }
            return null
        }
    }
    return api //操作elements的api
}

接口
const api = jQuery('.test') //返回api对象
api.addClass('red') //遍历所有获取的元素，添加.red
```
**闭包:函数访问外部变量**

### 链式操作
第1步.addClass函数 return api

第2步.api.addClass('red').addClass('blue')

```js
const api = {
  addClass(className) { //主要代码
    ...
    return api 
    }
 }
 return api
  
接口
const api = jQuery('.test')
api.addClass('red').addClass('blue')//链式操作
```
**解析**

方法,addClass函数里return api。

你用api调了一函数(addClass)，这个函数返回前面的那个东西(api)。

这样你就可以继续在后面调addClass。**这种操作就叫链式操作。**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3f08a59b88154a439b37b8da5e1d5a19~tplv-k3u1fbpfcp-zoom-1.image)


### this
**obj.fn(p1)**

**obj.fn.call(obj,p1)**

**函数里的this就是obj**
  
api.addClass('red').addClass('blue')

this就是api
```js
addClass(className){
    ...
    return this //api
 }
```
### 声明一个对象api，再return这个对象。其实可以直接return这个对象！
```js
window.jQuery = function (selector) { 
    const elements = document.querySelectorAll(selector)
    return {
        addClass(className) {
            for (let i = 0; i < elements.length; i++) {
                elements[i].classList.add(className)
            }
            return this
        }
     }
}
```
**总结**

**jquery核心思想**

**1.用闭包维持elements**

const api=jQuery('.test')

jQuery提供一个函数，这个函数接收一个选择器(css中的选择器)。

根据选择器获取到这些元素，但是它不会返回给你这些元素，只会返回一个对象，这个对象会有一些方法(函数),由函数操作你的元素。

**2.链式操作return this**

this是**调用后**才确定的!(未知的)

你在addClass前面传的什么，this就是什么。
```js
const api = jQuery('.test') 
api.addClass('red').addClass('blue')
//this就是api
```
**变量声明后只用一次时，可以省略声明**

上面的代码可以简写为
```js
jQuery('.test') 
  .addClass('red')
  .addClass('blue')
```

### jQuery对象
var obj=new Object()

Object就是构造函数

**jQuery是构造函数吗？**

是，因为jQuery函数确实构造出了一个对象

不是，因为不需要写new jQuery()就能构造一个对象，以前讲的构造函数都要结合new才行。

**结论**

**jQuery是一个不需要加new的构造函数**

jQuery不是常规意义上的构造函数

这是因为jQuery用了一些技巧

**jQuery对象**代指jQuery函数构造出来的对象(口头约定)

只是口头约定下，jquery是函数不是普通对象

**术语**

举例

Object是个函数，**Object对象**表示Object构造出的对象

Array是个函数，Array对象/数组对象表示Array构造出来的对象

Function是个函数，Function对象/函数对象表示Function构造出来的对象

# 链式风格
**查**

1.jQuery('#xxx')返回值不是元素而是一个api对象

2.jQuery('#xxx').find('.red')查找#xxx里的.red元素

3.实现end函数

4.jQuery('.red').each(fn)遍历并对每个元素执行fn

5.jQuery('#xxx').parent()获取爸爸

6.jQuery('#xxx').children()获取儿子

1.略

**2.jQuery('#xxx').find('.red')查找#xxx里的.red元素**

this是api,闭包变量elements。

```js
<div class="test"> //elements为3，3个.test,一个一个遍历
  测试1
   <div class="child">child1</div>
   <div class="child">child2</div>
   <div class="child">child3</div>
</div>
<div class="test">
  测试2
   <div class="child">child1</div>
   <div class="child">child2</div>
</div>
<div class="test">
   测试3
   <div class="child">child1</div>
</div>

find(selector) {
  let arr = []
  for (let i = 0; i < elements.length; i++) {
    const elements2 = Array.from(elements[i].querySelectorAll(selector))
    arr = arr.concat(elements2)
  }
   return arr
}

接口
const x1=jQuery('.test').find('.child')
console.log(x1)
```
解析：**假设有多个selector选择器元素**

elements类似于数组，数组不能querySelectorAll。

遍历elements数组(当前有3个test数组)，在数组里分别find子元素。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4957bda6ce044beda006380854270733~tplv-k3u1fbpfcp-zoom-1.image)


**遍历到child后，我们应该操作child。如何操作才能确定操作到的是child而不是其它?**

当前是纯数组arr,返回的也是数组return arr

**数组不是函数，不能直接操作。**
```js
Uncaught TypeError: x1.addClass is not a function
```
**那return this可以吗？不行**

this 是当前对象'api'，api是操作elements的，它只能操作一个。因此不能操作arr!
```js
接口
jQuery('.test')
    .find('.child')
    .addClass('fuck')
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bbd84daffacd47e1845d320b42820e53~tplv-k3u1fbpfcp-zoom-1.image)


return this

return的是find前面的`.test`而不是我想操作的`.child`

只能重新封装一个jQuery函数，得到一个新的api来操作child

jQuery不能只接收选择器selector还要能接收数组Array

**封装一个新的api，操作child。**

之前接收的是selector,现在接收个数组。把数组给你，然后封装个新api

结构一样，但保存的elements不同

**步骤：** 

第1步，return由jQuery重新构造出来的newApi。(不能直接return之前的api)

第2步，jQuery接收选择器和数组(selectorOrArray)
      
第3步，如果是数组就等于"新的elements"
      
**const elements在{}内作用域有限，可以把它放到外面，作用域提升。由于const必须赋值改用let**
```js
window.jQuery = function (selectorOrArray) { //第2步
  let elements  //作用域提升
  if (typeof selectorOrArray === 'string') { 
    elements = document.querySelectorAll(selectorOrArray)
  } else if (selectorOrArray instanceof Array) {//第3步
    elements = selectorOrArray
  }
  return {
    addClass(className) {...
     },
      find(selector) {
        let arr = []
        for (let i = 0; i < elements.length; i++) {
        const elements2 = Array.from(elements[i].querySelectorAll(selector))
          arr = arr.concat(elements2)
        }
      // const newApi = jQuery(arr)  
      // return newApi   可以直接简写为
      return jQuery(arr)  //第1步，jQuery构造出来的newApi
      }
接口
jQuery('.test')
    .find('.child')
    .addClass('fuck')      
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1679cda034c42d38829c796622ee1a1~tplv-k3u1fbpfcp-zoom-1.image)


jQuery构造出来的newApi。

const newApi = jQuery(arr),参数arr传入下面jQuery重新生成一个新arr数组。

window.jQuery = function (selectorOrArray) {}

### 3.实现end函数
```js
jQuery('.test')
  .find('.child')
  .addClass('red')
  .end() //回到上一次api
  .addClass('fuck') //操作对象是.test而不是.child
```
用户突然想回到上一次api操作test,如何实现？
```js
oldApi: selectorOrArray.oldApi, //把oldApi复制到当前api(之前在数组上)
find(selector) {
    ...
    arr.oldApi = this //this是旧api
    return jQuery(arr)
 },
end() {
  return this.oldApi //this是新的api2
}
接口
jQuery('.test')
    .find('.child')
    .addClass('red')
    .end()
    .addClass('fuck')
```
补充：数组是对象，对象可以加属性。

**this为什么会变？**
```js
帮助理解
const api1 = jQuery('.test')
const api2 = api1.find('.child').addClass('red')
const oldApi = api2.end().addClass('blue')   //当前为oldApi
```
oldApi放到数组上了并没有放到api上，api是操作数组，this是api而不是arr。

应该把oldApi复制过来
`oldApi: selectorOrArray.oldApi,//把oldApi复制过来,`

**4.jQuery('.red').each(fn)遍历并对每个元素执行fn**
```js
each(fn) {
  for (let i = 0; i < elements.length; i++) {//elements是闭包,会一直在上面
    fn.call(null, elements[i], i) //不用this
   }
  return this //api对象
}
接口
const x = jQuery('.test').find('.child')
x.each((div) => console.log(div))
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2031c09b967040bebbf01d0c089147cd~tplv-k3u1fbpfcp-zoom-1.image)


解析：

each(fn){}接收一个参数fn,x调用each时传了个fn。

(div)=>console.log(div)就是传进去的参数fn。

each遍历时会调用fn,**fn在调用时传了两个参数。**
`fn.call(null,elements[i],i)`

elements[i]是第1个参数，i是第2个参数。

div就是第1个参数，名字无所谓不会有任何影响，只是用来占位的、形式参数。

**要习惯在一个函数(each)里再传一个fn。在这个fn里拿到这个参数(div)，这个参数实际上是在调用fn时传给你的,并不是实际的div。**

**5.jQuery('#xxx').parent()获取爸爸**
> 用each实现更多的函数

```js
parent() {
  const array = []
  this.each((node) => {
    if (array.indexOf(node.parentNode) === -1) {//如果存在就不需要push
      array.push(node.parentNode)
     }
   })
 return jQuery(array) //返回可以操作数组的对象
},
print() {
  console.log(elements)
}
接口
const x = jQuery('.test')
x.parent().print()
```
return array没有可操作性，封装个操作数组的对象jQuery(array)，jQuery会返回个对象，对象会操作这些元素

**6.jQuery('#xxx').children()获取儿子**
```js
children() {
  const array = []
  this.each((node) => {
    array.push(...node.children)
   })
  return jQuery(array)
}
接口
const x = jQuery('.test')
x.children().print()
```
**...展开操作符,可以把一个数组展开**
```
...node.children
等同于
node.children[0],node.children[1],node.children[2]
```

# 实现createElement、get、appendTo、append、

```js
window.jQuery = function (selectorOrArrayOrTemplate) {
  let elements
  if (typeof selectorOrArrayOrTemplate === 'string') {
    if (selectorOrArrayOrTemplate[0] === '<') {
      // 创建 div
      elements = [createElement(selectorOrArrayOrTemplate)]
    } else {
      // 查找 div
      elements = document.querySelectorAll(selectorOrArrayOrTemplate)
    }
  } else if (selectorOrArrayOrTemplate instanceof Array) {
    elements = selectorOrArrayOrTemplate
  }

  function createElement(string) {
    const container = document.createElement("template");
    container.innerHTML = string.trim();
    return container.content.firstChild;
  }
  // api 可以操作elements
  return {
    jquery: true,
    elements: elements,
    get(index) {
      return elements[index]
    },
    appendTo(node) {
      if (node instanceof Element) {
        this.each(el => node.appendChild(el)) 
        //遍历elements，对每个el进行node.appendChild操作
      } else if (node.jquery === true) {
        this.each(el => node.get(0).appendChild(el)) 
         //遍历elements，对每个el进行node.get(0).appendChild(el))操作
      }
    },
    append(children) {
      if (children instanceof Element) {
        this.get(0).appendChild(children)
      } else if (children instanceof HTMLCollection) {
        for (let i = 0; i < children.length; i++) {
          this.get(0).appendChild(children[i])
        }
      } else if (children.jquery === true) {
        children.each(node => this.get(0).appendChild(node))
        }
      },
      ...
  }
}

window.$ = window.jQuery

接口
const $div = $('<div><span>1</span></div>')
const $childList = $('.child')
$('body').append($childList)
```
