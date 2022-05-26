# js对象分类

推荐文章\
  [你可以不会class，但是一定要学会prototype](https://zhuanlan.zhihu.com/p/35279244)\
  [JS的new到底是干什么的？](https://zhuanlan.zhihu.com/p/23987456)\
  [JS中proto和prototype存在的意义是什么？](https://www.zhihu.com/question/56770432/answer/315342130)

# 对象需要分类吗？

> 需求：输出各种形状的面积和周长

```
正方形
分析 正方形有三个属性:边长、面积、周长
let square={
  width:5,
  getArea(){
    return this.width * this.width
  },
  getLength(){
    return this.width * 4
  }
}
```

来一打正方形

```
let squareList=[]
for(let i=0;i<12;i++){
  squareList[1]={
   width:5, 
  getArea(){
    return this.width * this.width
  },
  getLength(){
    return this.width * 4
  }
 }
}
```
width不全是5怎么办？

```
let squareList=[]
let widthList=[5,6,7,8,5,6,7,5,5,4,5,6]
for(let i=0;i<12;i++){
  squareList[i]={
  width:widthList[i],
  getArea(){
    return this.width * this.width
  },
  getLength(){
    return this.width * 4
  }
  }
}
```

垃圾代码，浪费太多内存。

![](https://pic4.zhimg.com/v2-30330aa89afdd80f23ec2fd808845c8b_b.jpg)
借助原型，将12个对象的共用属性放到原型里

```
let squareList=[]
let widthList=[5,6,7,8,9,5,6,7,8,9,5,6]
let squarePrototype={
 getArea(){
    return this.width * this.width
  },
  getLength(){
    return this.width * 4
  }
}
for(let i=0;i<12;i++){
  squareList[i]=Object.create(squarePrototype)
  squareList[i].width= widthList[i]
}
控制台输入：squareList
```

![](https://pic1.zhimg.com/v2-2f38c6c1ab18f2f86a096915b2468aa0_b.jpg)

还是垃圾代码，创建的square的代码太分散了。

那就把代码抽离到一个函数里，然后调用函数。

抽离到函数

```
let squareList=[]
let widthList=[5,6,7,8,9,5,6,7,8,9,5,6]
function createSquare(width){//此函数叫构造函数,构造函数就是可以构造出对象的函数
  let obj=Object.create(squarePrototype)//以squarePrototype为原型创建空对象
  obj.width=width
  return obj
}
let squarePrototype={
 getArea(){
    return this.width * this.width
  },
  getLength(){
    return this.width * 4
  }
}
for(let i=0;i<12;i++){
  squareList[i]=createSquare(widthList[i])
  //封装，把细节写到一个函数里，别人只需要调用函数并且传个宽度。
}
```
squarePrototype原型和createSquare函数还是分散的,能不能组合在一起?

函数也是一个对象，如果把原型放到函数上是不是更紧凑？

```
let squareList = []
let widthList = [5,6,5,6,5,6,5,6,5,6,5,6]

//声明createSquare函数，它可以创建square对象
function createSquare(width){
  let obj = Object.create(createSquare.squarePrototype) // 先使用后定义？NO
  这段代码在调用createSquare才会执行
  obj.width = width
  return obj
}
createSquare.squarePrototype = { //把原型放到函数上，结合够紧密了吗？
  getArea(){ 
    return this.width * this.width 
  },
  getLength(){
    return this.width * 4
  },
  constructor: createSquare //方便通过原型找到构造函数(又把构造函数放到原型上)
}
for(let i = 0; i<12; i++){
  squareList[i] = createSquare(widthList[i])//这里才调用createSquare
  console.log(squareList[i].constructor) 
  // constructor 可以知道谁构造了这个对象：你妈是谁？
}
```
除了把原型放到构造函数上，还把构造函数放到原型上。

拿到函数后可以方便的找到它的原型，也可以通过拿到原型方便的找到它的函数。

这段代码几乎完美，为什么不固定下来，让每个js开发者直接用呢

new操作符，让我们感受js之父的爱

函数和原型结合(重写)

```
let squareList = []
let widthList = [5,6,5,6,5,6,5,6,5,6,5,6]
function Square(width){ 
  this.width = width
}
Square.prototype.getArea = function(){ //设置共用属性(getArea)
  return this.width * this.width 
}
Square.prototype.getLength = function(){
  return this.width * 4
}
for(let i = 0; i<12; i++){
  squareList[i] = new Square(widthList[i])
  console.log(squareList[i].constructor)
}
// 多美，几乎没有一句多余的废话
// 每个函数都有 prototype 属性，这是 JS 之父故意的
// 每个 prototype 都有 constructor 属性，也是故意的
```

> js之父为了让new可以运行提前规定:
> 
> 所有js里的函数自带prototype属性，prototype属性自带constructor。constructor的值等于函数本身。
> 
> 例子
> 
> function f1(){}
> 
> console.dir(f1)
> 
> 控制台输入：f1.prototype.constructor === f1
> 
> 输出结果：true

![](https://pic1.zhimg.com/v2-7052ca45ac1582a85a9c261547fd7770_b.jpg)

1.之前要自己创建对象并把对象的原型指向那个拥有getArea,getLength的原型。

  现在new会帮我们做

2.用this代表新的对象

3.return也不用写，new会帮我们做

4.之前会创建个对象，里面有共有属性。

  现在需要一个一个加。因为prototype已经有一个constructor属性了，Square.prototype=
  
  新对象就会导致constructor被搞丢。

所以最好方式是在prototype上加属性。也可以用Object.assign加。

# 总结

1.new X()自动做了四件事情

自动创建空对象

自动为空对象关联原型，原型地址为X.prototype

自动将空对象作为this关键字运行构造函数

自动return this

--这就是JS之父的爱

2.构造函数X

X函数本身负责给对象本身添加属性

X.prototype对象负责保存对象的共用属性

例子
```
function Dog(name){
  this.name=name
  this.color='白'
  this.king='萨摩耶'
}
Dog.prototype.wangwang=function(){console.log('汪汪')}
Dog.prototype.run=function(){console.log('狗在跑')}
let dog1=new Dog('小白')

控制台输入：dog1
输出结果：Dog {name: '小白', color: '白', king: '萨摩耶'}

控制台输入：dog1.wangwang()
输出结果：汪汪

控制台输入：dog1.run()
输出结果：狗在跑
```
this指还未创建出来的新对象dog1

# 代码规范

**1.大小写**

所有构造函数首字母大写

```
function add(x,y){return x+y}  //不是构造函数不用大写
function Dog(name){this.name=name}
```
所有被构造出来的对象，首字母小写

```
let dog1=new Dog()
```

**2.词性**

(1)new 后面的函数，使用名词形式

   如new Person()、new Object()

(2)其它函数，一般使用动词开头

   普通函数一般都用动词

如createSquare(5)、createElement('div') 

function createSquare(width){...}

其它规则以后再说

问题

1.怎么确定要传几个参数？

function Square(width){...}

自己写的话清楚。但如果是别人提供的构造函数，我怎么知道要接受几个参数？

```
let arr1=new Array(3) 
```

数组是3还是长度为3的数组？

只能看文档🔍 mdn array

new Array(element0,element1[,...[,elementN]])//多个参数就是里面的元素

new Array(arrayLength) //一个参数就是数组的长度

# js唯一的一个公式
如何确定一个对象的原型？

1.为什么

let obj=new Object()的原型是Object.prototype

let arr=new Array()的原型是Array.prototype

let square=new Square()的原型是Square.prototype

let fn=new Function()的原型是Function.prototype

2.因为new操作故意这么做的

![](https://pic2.zhimg.com/v2-331b8c7ace0df66851e8a52bedaa1185_b.jpg)

**结论**

new后面是什么，原型就是 对应的.prototype

你是谁构造的，你的原型就是谁的prototype属性对应的对象

**原型公式**

对象.__proto__ === 其构造函数.prototype

两个原型地址是一样的！

**练习1**

let x={}

1.x的原型是什么？x.__proto__的值是什么？上面两个问题是等价的吗？

  let x=new Object{}

**x.__proto__=Object.prototype**

  所以x的原型就是Object.prototype这个对象

  x.__proto__存的地址才是其原型

2.用内存图画出x的所有属性

![](https://pic1.zhimg.com/v2-b01f58c6ed32bf494365d65db8aa3700_b.jpg)

**练习2**

let square=new Square(5)

1.square的原型是什么？square.__proto__的值是什么？

square.__proto__=Square.prototype

2.用内存图画出square的所有属性,略

**练习3**

1.Object.prototype是哪个函数构造出来的？

  不知道，它没有爸爸也没有妈妈

2.Object.prototype的原型是？

  没有原型

3.Object.prototype.__proto__?

  null

4.用内存图画出上述内容,略

所有「函数对象」的「构造函数」都是 Function

window.Function是一个函数对象，那么这个「函数对象」的构造函数是Function;

window.Object是一个函数对象，那么这个「函数对象」的构造函数是Function;

window.Function 是一个函数对象，那么这个「函数对象」的__proto__是(对象.
__proto__===其构造函数.prototype）

Square最终版(存疑),还可以进一步优化，以后再说。

# 对象需要分类

用构造函数再写个圆

```
function Circle(radius){
  this.radius = radius
}
Circle.prototype.getArea=function(){
  return Math.pow(this.radius,2) * Math.PI
}
Circle.prototype.getLength=function(){
  return this.radius * 2 * Math.PI
}
let c1=new Circle(10)//声明一个新的圆
控制台输入：c1.radius
输出结果：10
控制台输入：c1.getArea()
输出结果：314.1592653589793
控制台输入：c1.getLength()
输出结果：62.83185307179586
```

那再来个长方形
```
function Rect(width,height){
  this.width = width
  this.height=height
}
Rect.prototype.getArea=function(){
  return this.width * this.height
}
Rect.prototype.getLength=function(){
  return (this.width+this.height)*2
}

let r1=new Rect(4,5) //声明一个新的长方形
控制台输入：r1
输出结果：Rect {width: 4, height: 5}
控制台输入：r1.getArea()
输出结果：20
控制台输入：r1.getLength()
输出结果：18
```
对象需要分类吗？

需要分类

**理由一**

有很多对象拥有一样的属性和行为，需要把它们分为同一类

如square1和square2，这样创建类似对象的时候就很方便  

**理由二**

但是还有很多对象拥有其他的属性和行为，所以就需要不同的分类

比如Square/circle/Rect就是不同的分类

Array/Function也是不同的分类

而Object创建出来的对象，是最没有特点的对象。

# 类型 VS 类

类型

类型是JS数据的分类，有7种(4基2空1对象)

类

类是针对于对象的分类，有无数种

常见的有Array、Function、Date、RegExp正则等

# object是最没有特色的类，现在来看下有特色的类

### 第一种.数组对象


**1.定义一个数组**

let arr=[1,2,3] //缩写

let arr=new Array(1,2,3) //元素为1，2，3

let arr=new Array(3)  //长度为3

关注一个对象时主要看自身属性和共用属性

**2.数组对象的自身属性**

  自身属性有：'0'/'1'/'2'/'length'
  
  注意:属性名没有数字，只有字符串。数组里的都是字符串不是数字！

![](https://pic1.zhimg.com/v2-9626bd9be481a9bc1a8bd8604ce3e290_b.jpg)

**3.数组对象的共用属性**

  共用属性有：'push'/'pop'/'shift'/'unshift'/'join'

  用法都在MDN,后面会单独教这些API

  数组对象比普通对象多一层共有属性

###  第二种.函数对象

**1.定义一个函数**

function fn(x,y){return x+y}

let fn2=function fn(x,y){return x+y}

let fn=(x,y)=>x+y

let fn=new Function('x','y','return x+y') //重要

**2.函数对象自身属性**

'name'/'length'

例子
```
let fn = new Function('x', 'y', 'return x+y')
console.dir(fn)
```
![](https://pic3.zhimg.com/v2-739b7a222b78057b9e90e82d1085ba46_b.jpg)

3.函数对象共用属性

'call'/'bind'/'apply'

 后面会单独介绍函数

# JS终极一问

1.window是谁构造的？

  Window(大写)

![](https://pic4.zhimg.com/v2-fdd792a650236073e2b9019d43311a4b_b.jpg)

所以 window.__proto__ === Window.prototype

2.window.Object是谁构造的？

  window.Function

  因为所有的函数都是window.Function构造的

![](https://pic2.zhimg.com/v2-9f641a0749919c1baed8a977ee74c101_b.jpg)

3.window.Function是谁构造的？

  window.Function

  因为所有的函数都是window.Function构造的

  自己构造自己？并不是这样，这是「上帝」的安排

  浏览器构造了Function，然后指定它的构造者是自己

  window.Function.constructor === window.Function 

# es6新语法class

某些前端认为prototype是过时的，我的想法相反，但是不管怎样还是要学class。

原型好还是类好？都是用来给对象分类的

**prototype语法**
```
function Square(width){ 
  this.width = width
}
Square.prototype.getArea = function(){ //设置共用属性(getArea)
  return this.width * this.width 
}
Square.prototype.getLength = function(){
  return this.width * 4
}
```
**class语法**

待接受参数(width)写到constructor里,表示它是构造函数

```
class Square{
  constructor(width){
    this.width=width
  }
}
getArea(){
  return this.width * this.width 
}
getLength(){
  return this.width * 4
}
```

**class语法引入了更多概念**
```
class Square{
static x = 1 //第1个概念.意思是：属性(x)是Square的，Square.x即可使用x
   width = 0 //第2个概念.初始化this.width(宽度默认为0)
  constructor(width){
    this.width=width
  }
}
getArea(){
  return this.width * this.width 
}
getLength(){
  return this.width * 4
}
get area2(){ //第3个概念.只读属性，可以将getArea()写成get area2()。区别是：调用area2时不需要加括号()
  return this.width * this.width 
}
```

用 class 重写 Circle
```
class Circle{
  constructor(radius){
    this.radius = radius
  } 
  getArea(){ 
    return Math.pow(this.radius,2) * Math.PI  
  }
  getLength(){
    return this.radius * 2 * Math.PI
  }
}
let circle = new Circle(5)
circle.radius
circle.getArea()
circle.getLength()

circle.getLength()
```

**新手建议**

1.这么多新语法怎么学

1' MDN class文档全部看完，并记下来

2' 用到再学,实践中学，记得更牢

[ES 6的所有新语法](https://fangyinghang.com/es-6-tutorials/)
关于类和对象的新语法有[类](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes)，[对象初始化](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Object_initializer#ECMAScript_6%E6%96%B0%E6%A0%87%E8%AE%B0)和[解构赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

### class 中2种函数写法的区别
**语法1**
```
class Person{
    sayHi(name){}
    //等价于
    sayHi: function(name){} 
    //注意，一般我们不在这个语法里使用箭头函数
}

//等价于

function Person(){}
Person.prototype.sayHi = function(name){}
```
**语法2：注意冒号变成了等于号**
```
class Person{
  sayHi = (name)=>{} // 注意，一般我们不在这个语法里使用普通函数，多用箭头函数
}

//等价于

function Person(){
    this.sayHi = (name)=>{}
}
```
备注:不要强求完全转换成 ES5

大部分class语法都可以转为ES5 语法，但并不是100%能转，有些class语法你意思理解就行，不需要强行转换为 ES5。


class就是声明一个类，类就是用来创建对象的，只需要按照这个语法写就可以了。

如果想要这个对象拥有一些属性呢，就写到constructor里面。

如果想要这个对象拥有一些函数呢，就写在外面。
