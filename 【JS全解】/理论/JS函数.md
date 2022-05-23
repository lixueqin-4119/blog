# JS 函数
> 本节课主要讲解函数对象，JS的第二座大山this


# 定义函数

> 4种方式:具名函数、匿名函数、箭头函数、用构造函数

**1.具名函数**

全局作用域

function 函数名(形式参数1，形式参数2){

  语句

  return 返回值

}

**2.匿名函数**

上面的具名函数，去掉函数名就是匿名函数

let a=function(x,y){return x+y}

等于号右边又叫函数表达式

**面试题**
```
let a=function fn(x,y){return x+y}
fn(1,2) //作用域
```
**请问fn能成功调用吗？**

不能。函数在等于号右边，那么fn作用域只能在等于号右边，要用的话也只能用a。

![](https://pic3.zhimg.com/v2-8fdfa08abfed480ff378b808e169132e_b.jpg)

**3.箭头函数**

let f1 = x => x*x 

箭头左边是输入参数，右边是输出参数

let f2 = (x,y) => x+y

当有2个参数时需要扩起来
```js
let f3 = (x,y) => { 
  console.log('hello')
  return x+y 
}
```
2个语句需要用{}括起来,并且要return

let f4 = (x,y) => ({name:x,age:y}) //括号内表示是一个整体

**直接返回对象会出错，必须加个圆括号**

**4.用构造函数(没人用)**

let f=new Function('x','y','return x+y')

基本没人用，但是能让你知道函数是谁构造的

所有函数都是Function构造出来的，包括Object、Array、Function也是

**fn和fn()的区别是什么？**

fn指函数自身,fn()调用(执行)这个函数

例子
```
let fn=()=>{console.log('hi')}
let fn2=fn
fn2()
```
fn保存了匿名函数的地址,这个地址被复制给了fn2。

fn2()调用了匿名函数,fn和fn2都是匿名函数的引用而已

真正的函数既不是fn也不是fn2

# 函数的要素

每个函数都有这些东西

> **调用时机**
> 
> 作用域
> 
> 闭包
> 
> 形式参数
> 
> 返回值
> 
> 调用栈
> 
> 函数提升
> 
> arguments(除了箭头函数)
> 
> this(除了箭头函数)

### 一.调用时机   

> 时机不同结果不同

**fn不加()永远不会调用**

**例1**
```
let a=1
function fn(){
  console.log(a)
}
a=2 //a更新到2了
fn() 
问打印出多少？ 
2
```
**例2**
```
let a=1
function fn(){
  console.log(a)
}
fn() //先调用
a = 2
问打印出多少？
1
```
**例3**
```
let a=1
function fn(){
  setTimeout(()=>{ //setTimeout 0一段时间(尽快)之后执行
    console.log(a)
    },0)
}
fn()
a=2
问打印出多少？
2
```
js会先执行当前代码，然后再做等会再做的事。

先执行a=2，然后执行setTimeout。

**例4**
```
let i=0
for(i=0;i<6;i++){
  setTimeout(()=>{
    console.log(i)  
  },0)
}
问打印出多少？
6个6

let i=0
for(i=0;i<6;i++){
    console.log(i)  
}
问打印出多少？
0、1、2、3、4、5
```
setTimeout忙完手头的事后，尽快做另件事。

每次for循环后,立马打印i

for执行了6次

补充

为什么不是0～5？

一个i只有一个值，这里只有一个i，返回的是最新值。

为什么不是6个5？

i=5时满足条件会进入循环，此时i=6

**例5**
```
for(let i=0;i<6;i++){
  setTimeout(()=>{
    console.log(i) 
  },0)
}
问打印出多少？
0、1、2、3、4、5
```
因为js在for和let一起用的时候会加东西，每次循环会多创建一个i(服了js)

js为满足新人想法设计的

这种写法setTimeout根本没有意义

js函数由于变量值会变，所以每次求值时都要想想代码的顺序。

如果不确定，那你求的值可能是以前的或者是错的。

同样，下面2种方法也能打印出0～5。
```
for (var i=0; i<6; ++i) {  //利用 setTimeout 的第三个参数
	setTimeout(function(i){
		console.log(i)
	}, 0, i)
}

for(var i=0; i<6; ++i) {   //利用闭包
	!(function(j) {
		setTimeout(function(){
			console.log(j)
		}, 0)
	})(i)
}
```
### 二.作用域 & 三.闭包

就近原则 & 闭包

**作用域**

每个函数都会默认创建一个作用域

例1
```
function fn(){
  let a=1
}
console.log(a) //a不存在
```
就算fn执行了，也访问不到作用域里面的a，a的作用域就在{}里面。

**全局变量 & 局部变量**

2种全局变量：

1' 在顶级作用域声明的变量是全局变量

2' window的属性是全局变量

其它都是局部变量

![](https://pic3.zhimg.com/v2-6bd507ba59916c995095a9ad244fbd9e_b.jpg)

**函数可嵌套，作用域也可嵌套**

局部作用域可以套局部作用域
```
function f1(){
  let a=1
  function f2(){
    let a=2
    console.log(a)
  }
  console.log(a) //1
  a=3
  f2() //2
}
f1()
输出结果：1
        2
```
函数里面还可以有函数。对于js来说，函数和其它对象没有什么区别。

**作用域规则**

就近原则

1.如果多个作用域有同名变量a

  那么查找a的声明时，就向上取最近的作用域，简称「就近原则」

2.查找a的过程与函数执行无关，作用域的确定跟函数执行没有半点关系。

  像这种跟函数执行没有关系的作用域叫「静态作用域」也叫「词法作用域」

但a的值与函数执行有关

**闭包**

js函数会就近寻找最近的变量，这就是闭包。

例子
```js
function f1(){
  let a=1
  function f2(){
    let a=2
    function f3(){
      console.log(a)
    }
    a=22
    f3() 
  }
  console.log(a)
  a=100
  f2()
}
f1()
```
**定义**

如果一个函数用到了外部的变量

那么这个函数加这个变量

就叫做闭包

f3用到了外面的a,那么a和f3就是闭包

闭包的用途以后见

### 四.形式参数 & 五.返回值

**形式参数**

**1.形式参数的意思就是非实际参数**

```
function add(x,y){//不代表实际值，只代表参数的顺序
  return x+y
}
```
调用add时，1和2是实际参数，会被赋值给x y

根据内存图把地址直接复制一遍

**2.行参可认为是变量声明**

上面代码近似等价于下面
```
function add(){
  var x=arguments[0]
  var y=arguments[1]
  return x+y
}
```
形参就是语法糖，形参本质就是变量声明。

**3.形参可多可少，形参只是给参数取名字**
```
function add(x){
 return x+arguments[1] //arguments[1]第2位
}
add(1,2)

function add(x,y,z){
 return x+y 
}
add(1,2)
```
js没有typeScript那么严谨

### 返回值

**每个函数都有返回值**

function hi(){console.log('hi')}

hi()

没写return，所以返回值是undefined

function hi(){ return console.log('hi')}

hi()

返回值为 console.log('hi')的值，即undefined

打印值是打印值，返回值是返回值！

**函数执行完了后才会返回**

**只有函数才有返回值**

### 六.调用栈(重要)

递归、调用栈与爆栈

**调用栈**

> 我们在调用一个函数时,需要进入到另一个环境去执行它，执行完后再返回回来。
> 那么如果我们进入到另一个函数时还需要进入下一个函数，多次过后，怎么知道回去的路？
> 调用栈实际上就是记录，你进入一个函数后回去回到哪里。

**什么是调用栈**

js引擎在调用一个函数前，需要把函数所在的环境push到一个数组里。

这个数组叫做调用栈

等函数执行完了，就会把环境弹pop出来

然后return到之前的环境，继续执行后续代码

保存函数所在环境的这个数组就叫调用栈

举例

console.log(1)

console.log('1+2的结果为'+add(1,2))

console.log(2)

![](https://pic4.zhimg.com/v2-124b3825ee580bb4f641ea98ae42635f_b.jpg)

### 栈会不会满呢？

如果使用递归函数很有可能把栈压满，因为需要一直压栈才能得到结果。

**递归函数**

1.阶乘

function f(n){

  return n !=== 1 ? n*f(n-1) : 1

}

2.理解递归

f(4)

=4 * f(3)

=4 * (3 * f(2))

=4 * (3 * (2) * f(1)))

=4 * (3 * (2) * (1)))

=4 * (3 * (2))

=4 * (6)

24

先递进，再回归

**递归函数的调用栈**

递归函数的调用栈很长
```
function sum(n){
  return n !=== 1 ? n*sum(n-1) : 1
}
sum(10000)//sum(10000)就要压1万次栈
50005000

sum(11434) //爆栈
Uncaught RangeError: Maximum call stack size exceeded
```
爆栈:如果调用栈中压入的帧过多，程序就会崩溃

保存函数所在环境的这个数组就叫调用栈，长度大概是1万～2万，超过程序就崩溃.

**测试各浏览器的调用栈有多长**

代码
```
function computeMaxCallStackSize(){
  try{
    return 1+computeMaxCallStackSize();
  }catch(e){
  //报错说明stack overflowl
    return 1
  }
}
computeMaxCallStackSize()

Chrome 11375
Firefox 26773
Node 12536
```
总结

我们在进入一个函数时要把环境先存下来才能进去，要不然等会就不知道怎么回来了。

存的东西越多就需要一个数组来保存，保存函数所在环境的这个数组就叫做调用栈。

长度大概在1～2万，超过这个值程序就崩溃。

**七.函数提升**

function fn(){}

不管你把具名函数声明在哪里，它都会跑到第一行。

什么不是函数提升

let fn=function(){}

这是赋值，右边的匿名函数声明不会提升

# 八.arguments & this(最重要)

arguments和this 每个函数都有，除了箭头函数

![](https://pic2.zhimg.com/v2-61da158de55e639f00fa6f952ef3ab6d_b.jpg)

### arguments

arguments就是包含了所有参数的**伪数组**(伪数组没有共用属性)

![](https://pic2.zhimg.com/v2-672e40195aa1e6037c0d6c595f04b089_b.jpg)

**如何传arguments？**

调用fn即可传arguments

fn(1,2,3)那么arguments就是[1,2,3]伪数组

### this

如果不给任何的条件，this默认指向window,一般不需要window。

如何传this？

fn.call(xxx,1,2,3)传this和arguments

**第1个参数是this,除了第1个参数外剩下的就是arguments！**

xxx是this,后面就是arguments

注意：xxx会被**自动转化成对象**。

     如果传的this不是对象，那么js会自动封装成对象(js的糟粕)

**怎么取消？**

声明函数时加上一句'use strict' 告诉js不要瞎加东西

```
function fn(){
  'use strict'
  console.log(this)
}
fn.call(1)
输出结果：1
```

this是隐藏参数，arguments是普通参数。this是参数。(个人观点)

# 验证"this是参数"这个观点(非常重要)

我们在写函数时怎么得到，未来要创建的那个对象的引用。因为你没创建怎么引用它呢？

假设没有this

```
let person={
  name:'brucelee',
  sayHi(){
    console.log(`你好,我叫`+person.name)
  }
}
```
补充：用直接保存了对象地址的**变量**获取'name'，这种办法叫**引用**。

问题一
```
let sayHi=function(){
  console.log(`你好，我叫`+person.name)
}
let person={
  name:'brucelee',
  'say':sayHi
}
```
解析

person如果改名，sayHi函数就挂了

sayHi函数甚至有可能在另一个文件里面

所以我们不希望sayHi函数里出现person引用

问题二
```
class Person{  //用类
  constructor(name){
    this.name=name //这里的this是new强制指定的，先不讨论
  }
  sayHi(){
    console.log(???)//代码还没生成更不可能引用对象，没有this就没法写代码
  }
}
```
解析

这里只有类，还没创建对象，故不可能获取对象的引用

那么如何拿到对象的name？根本拿不到。

代码Person还没生成更不可能引用对象，没有this就没法写代码

需要一种方法拿到对象，这样才能获取对象的name属性

研究的问题：

我们在写函数的时候，需要得到一个对象。但我并不知道那个对象叫什么名字，因为那个对象可能还没出生。怎么在不知道一个对象的名字的情况下，拿到对象的引用呢？

**一种土方法，用形参**
```
let person={ //对象
  name:'bruselee',
  sayHi(p){
    console.log(`你好，我叫`+p.name)
  }
}
person.sayHi(person)//你好，我叫bruselee


class Person{ //类
  constructor(name){ this.name=name}
  sayHi(p){
    console.log(`你好，我叫`+ p.name)
  }
}
person.sayHi(person)
```
谁会用这种方法？Python就用了...

![](https://pic1.zhimg.com/v2-1b2499e1e03c25b9933409e7fb1c45a4_b.jpg)

js没有模仿Python的思路，走了另一条路

**加关键字this**

**js在每个函数里加了this,用this获取那个对象**
```
let person={
  name:'brucelee',
  sayHi(){  //(this)隐藏的this
    console.log(`你好，我叫`+this.name)
  }
}
```
sayHi(this){}这个this是隐藏的，当person.sayHi(person)时，看不见的this用来接收person。当this.name时，实际就是person.name.

person.sayHi()相当于person.sayHi(person)

然后person被传给this了(person是个地址)

这样，每个函数都能用this获取一个未知对象的引用了

person.sayHi()会隐式地把person作为this传给sayHi，方便sayHi获取person对应的对象。

总结

我们想让函数获取对象的引用，但是并不想通过变量名做到。

Python通过额外的self参数做到，js通过额外的this做到：

person.sayHi()会把person自动传给sayHi,sayHi可以通过this引用person。

p=new Person()

p.sayHi() //调用的时候隐式写了句this=p,这是js引擎擅自做的。

**this就是最终调sayHi的对象！**

### this就是不成文的隐性的约定,调用的时候下面两种哪个是对的？

1.person.sayHi()  //对的

2.person.sayHi(person)

第1种省略形式反而对了，完整形式反而是错的！

js提供两种调用形式，解决这种不和谐。

1'小白调用法: person.sayHi() 会自动把person传到函数里，作为this

2'大师调用法: person.sayHi.call(person) //推荐

  需要自己手动把person传到函数里，作为this
  
哪种更好？

call在调用时就可以明确值。另一种写法无法明确值，所以用call更好

```
let person={
  name:'brucelee',
  sayHi(){  
    console.log(`你好，我叫`+this.name)
  }
}
person.sayHi.call({name:'666'})
输出结果：你好，我叫666
```  
sayHi放到person上干嘛呢？把函数放到对象上，这个函数和对象没有任何关系。

一般会这样写person.sayHi.call(person) //this就是person 

### call指定this

**1.不用this的写法**
```
function (x,y){ 
rethen x+y
 }
 没有用到this
 add.call(undefined,1,2) //占位
```
为什么要多写一个undefined?

因为第一个参数要作为this,其实代码里没有用this,所以只能用undefined占位，其实用null也可以，叫什么无所谓。不用this就要这样写。

**2.用this的写法**
![](https://pic2.zhimg.com/v2-7505c53e7b27e8eb476acdeac2f3ffe1_b.jpg)

完善forEach2,模拟forEach功能
```
Array.prototype.forEach2=function(fn){
  for(let i=0;i<this.length;i++){
    fn(this[i],i)
  }
}
let array=[1,2,3]
array.forEach2.call(array,(item)=>{console.log(item)}) //fn用箭头函数
```
使用

大师调用array.forEach2.call(array,(item)=>{console.log(item)}) //fn用箭头函数

小白调用array.forEach2((item)=>{console.log(item)})

this是什么

由于大家使用forEach2的时候总是会用arr.forEach2

所以arr就被自动传给forEach2了

**this可以不是数组**

比如: 
```
Array.prototype.forEach.call({0:'a',length:1},(item)=>{console.log(item)}) 
输出结果：a
```

**this的两种使用方法**

隐式传递

fn(1,2) //	等价于fn.call(undefined,1,2)

obj.child.fn(1) //等价于obj.child.fn.call(obj.child,1)

显示传递

fn.call(undefined,1,2)

fn.apply(undefined,[1,2])

this就是call的第一个参数

### 绑定this

**使用.bind可以让this不被改变**

function f1(p1,p2){

  console.log(this,p1,p2)

}

let f2=f1.bind({name:'brucelee'})

f2()  //f2是f1绑定的版本

**.bind还可以绑定其它参数(所有)**

let f3=f1.bind({name:'brucelee'},hi)

f3() //等价于f1.call({name:'brucelee'},hi)

this和p1已经被绑死了

vue、react经常用bind

# 箭头函数

没有arguments和this
```
console.log(this) //window
let fn=()=>console,log(this)
fn() //window
fn.call({name:'frank'}) //window
```
箭头函数的this就是普通变量。箭头函数里的this就是外面的this(默认的window),箭头函数没有this。就算你加call都没有。

```
let fn2=()=>console,log(arguments)
fn2(1,2,3) //没有arguments
Uncaught ReferenceError: arguments is not defined
```

### 立即执行函数(很少用)

只有js有的变态玩意

作用：我只想要一个局部变量，却不得不造一个函数并执行它

```
! function (){
  var a=2
  console.log(a)
}()
输出结果：2
        true
```

ES 5时代为了得到局部变量，必须引入一个函数。但这个函数如果有名字就得不偿失，于是这个函数必须是匿名函数。声明匿名函数然后立即加个()执行它。
但js认为这种语法不合法，所以程序员发现只要在前面加个运算符(推荐!)即可。

如果你的同事用了非!的运算符,记得在代码前一行加个分号;不然容易bug!

console.log(a);  //加分号;

! function (){...}


**新版js加个{}即可**

```js
{
  var a=2
  console.log(a)
}
```
