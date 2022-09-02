# JS 对象
读对象的属性时：

(1)如果使用[ ]语法，那么JS 先求[ ]中表达式的值，注意区分表达式是"变量"还是常量。

(2)如果使用点语法，那么点后面一定是 string 常量。

# 对象object

前6种是简单类型，不能包含其它任何东西。

对象是复杂类型，可以包含其它的6种东西甚至包括自己。

**语法**

1.定义

无序的数据集合

键值对的集合

2.写法
```js
1’ let obj={'name':'frank','age':18}  //推荐写法
2’ let obj=new Object({'name':'frank'})
3’ console.log('name':'frank','age':18)
```
3.细节

**键名必须是字符串。不像标识符，键名可以以数字开头。键名可以包含任意字符。**

引号可省略，省略之后就只能按照标识符的规则来写。

**就算省略引号，键名也还是字符串(重要)**

属性名：每个key都是对象的属性名(property)

属性值：每个value都是对象的属性值


# 奇怪的属性名

1.所有属性名会自动变成字符串

```js
let obj={
  1:'a',
  3.2:'b',
  1e2:true,
  1e-2:true,
  0xFF:true
}
Object.keys(obj)

输出结果：['1', '100', '255', '3.2', '0.01']
```

2.**变量作属性名**

**如何用变量做属性名**

之前都是用常量做属性名
```js
let p1='name'
let obj={p1:'frank'}   属性名为'p1'
let obj={[p1]:'frank'} 属性名为'name'
```

**对比**

不加[]的属性名会自动变成字符串

加了[]的则会当作变量求值

值如果不是字符串，则会自动变成字符串

例子
```js
let a='xxx'
var obj={
  a:1111
}
控制台输入：obj
输出结果：{a:1111}

var obj={
  [a]:1111
}
控制台输入：obj
输出结果：{xxx:1111}
```
**重要：如果你想用变量的值做key必须加[]!**

# 对象的隐藏属性

**隐藏属性**

JS中每一个对象都有一个隐藏属性

这个隐藏属性储存着其**共有属性组成的对象**的地址

这个**公有属性组成的对象**叫做原型

也就是说，隐藏属性储存着原型的地址

**代码示例**

```js
var obj={}
obj.toString() //居然不报错
```

因为obj的隐藏属性**对应的对象**上有toString()

![](https://pic3.zhimg.com/v2-f0553ccfdef14ce1c22f4052088345a2_b.jpg)

```
var obj={'10':'十'}
输入：obj
输出：{'10':'十'}
```

超纲知识

除了字符串，symbol也能做属性名
```js
let a=Symbol()

let obj={[a]:'hello'}
```
这有什么用呢？

目前没用，在学习「迭代」时会用到

# 对象属性的增删改查

### 一.删属性

**1.delete obj.xxx或delete obj['xxx']**

即可删除obj的xxx属性。只能删属性不能用来删对象。

请区分「属性值为undefined」和「不含属性名」

2.不含属性名
```js
 'xxx' in obj===false
```
 含有属性名
```js
 'xxx' in obj && obj.xxx===undefined
```
 注意:obj.xxx === undefined 不能断定'xxx'是否为obj的属性

```js
var obj={}
var obj2={x:undefined} 

控制台输入：obj.x === undefined
输出结果：true

控制台输入:obj2.x === undefined
输出结果：true

控制台输入:'x' in obj
输出结果：false
控制台输入:'x' in obj2
输出结果：true
```
 只能判断属性值，不能判断属性名，要判断属性名只能用in

例子
```js
1' var obj={name:'frank',age:18}
  obj.name=undefined //这种也可以删除但不推荐！
  控制台输入:obj
  输出结果:{name:undefined,age:18}
  
2' var obj2={name:'frank',age:18}
  delete obj2.name
  控制台输入:obj2
  输出结果:{age:18}
```

### 二.读属性

**1.查看所有属性(读属性)**

**(1)查看自身所有属性**

Object.keys(obj)

```js
var obj={name:'frank',age:18}
Object.keys(obj)   //查看key
输出结果：['name','age']

Object.values(obj) //查看value
输出结果：['frank','18']
 
Object.entries(obj) //查看key和value
输出结果:[Array(2), Array(2)]
         0: (2) ['name', 'frank']
         1: (2) ['age', 18]
```
**(2)查看自身+共有属性**

console.dir(obj)

或者自己依次用Object.keys打印出obj.__ proto__. //不推荐

**(3)判断一个属性是自身还是共有的**

```js
obj.hasOwnProperty('toString')

输出结果：false
```
 
 **2.查看单独属性**

 1' 中括号语法:obj['key']    //推荐使用

 2' 点语法:obj.key

 3' 坑新人语法:obj[key] //变量key值一般不为'key'
 
点语法会误导你，让你以为key不是字符串

等你确定不会弄混两种语法再改用点语法

**总结**

**1.obj.name等价于obj['name']**

  obj.name不等价于obj[name]

  这里的name是字符串，而不是**变量**

```js
let name='frank'
obj[name]等价于obj['frank']
```

**考题**

分清变量name和常量字符串'name'

```js
let list=['name','age','gender']
letperson={
 name:'frank',age:18,gender:'man'
}
for(let i=0;i<list.length;i++){
 let name=list[i]
 console.log(person_?_)
}
打印person的所有属性: console.log(person[name])
```

### 原型

**1.每个对象都有原型**

原型里存着对象的共有属性

比如obj的原型就是一个对象

obj.__proto__存着这个对象的地址

这个对象里有toString/constructor/valueOf等属性

**2.对象的原型也是对象**

所以对象的原型也有原型

obj={}的原型即为所有对象的原型

这个原型包含所有对象的共有属性，是对象的根

这个原型也有原型，是null
```js
控制台输入：console.log(obj.__proto__.__proto__)
  输出结果：null
```
如果一个属性不存在它的值为undefined。

null就说明被人为指定为null的，是根

### 增加或修改属性(写属性)

**直接赋值**
```js
let obj={name:'frank'} //name是字符串
obj.name='frank'       //name是字符串
obj['name']='frank'
~~obj[name]='frank'~~  ❌ 因为name值不确定
obj['na'+'me']='frank' //运算的形式赋值
let key='name';obj[key]='frank'
let key='name';~~obj.key='frank'~~ ❌obj.key等价于obj['key']
```
**批量赋值**

Object.assign(obj,{age:18,gender:'name',...})

**修改或增加共有属性**

**1.无法通过自身修改或增加共有属性**
```js
let obj={},obj2={}.  //共有toString
obj.toString='xxx' 只会改obj自身属性
obj2.toString 还是在原型上
```
2.我偏要修改或增加原型上的属性

一般来说不要修改原型，会引起很多问题
```
obj.__proto__.toString='xxx'  //不推荐

window.Object.prototype.toString='yyy'
```
3.修改隐藏属性(修改原型)

不推荐使用__proto__ 代码:obj.__proto__=common

**推荐使用Object.create**

```js
let obj=Object.create(common)
obj.name='frank'
let obj2=Object.create(common)
obj.name='jack'
```
规范大概的意思是，要改就一开始改，别后来再改，影响性能。
```js
var common={'国籍':'中国'，hairColor:'black'}
var person=Object.create(common,{name:{value:'frank'}})
cosole.log(person)
```

### 总结

**增**

基本同上：有则改，没有属性则增

**删**
```js
delete obj['name']
'name' in obj //false
obj.hasOwnProperty('name') //false
```
**改**
```js
改自身obj['name']='jack'
批量改自身Object.assign(obj,{age:18,...})
改共有属性Object.prototype['toString']='xxx'
改原型let obj=Object.create(common)
```
**查**
```js
Object.keys(obj)
console.dir(obj)
obj['name']
obj.name //记住这里的name是字符串
obj[name]//记住这里的name是变量
```






