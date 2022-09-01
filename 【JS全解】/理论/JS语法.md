## JS语法

**前言**

1.对[ES 6](https://www.w3school.com.cn/js/js_versions.asp)的评价

为啥说ES 6一半垃圾一半好？

JS的原创之处并不优秀，JS的优秀之处并非原创。

因为 ES 不能删除以前的特性，要兼容网站。

也就是说以前能运行的网站，以后都要能运行。

优点兼容，稳定。升级的时候以前的代码不需要重写。

2.推荐书籍

入门[《网道 JavaScript 教程》](https://wangdoc.com/javascript/)

进阶[《你不知道的 JavaScript（上卷）》](https://book.douban.com/subject/26351021/)


## 表达式、语句、标识符

### 一.表达式与语句

1.表达式

  (1)1+2表达式的值是3

  (2)函数调用add(1,2)，这个函数调用也是表达式。它的值为函数的返回值。

    注意：值与返回值是不一样的。只有函数才有返回值。

  (3)console.log表达式(属于函数表达式)的值为函数本身

    console.log是函数,注意没()。函数表达式的值为函数本身。

    console.log表达式的值就是console.log

  (4)**面试题**

    console.log(3)表达式的值为？undefined
    
![](https://pic1.zhimg.com/v2-de10a1685cfea935e284534961265cd4_b.jpg)
    
 解析:跟add(1,2)一样都是“函数+()”，函数调用的值为**函数的返回值**。

 所以log(3)函数的返回值是undefined。console.log(3)打印出来的东西是3。

2.语句

  var a=1是一个语句

  语句没有值，表达式才有值。语句主要是用来改变环境的。
```js
var a=1 //undefined
```
二者的区别

(1)表达式一般都有值，语句可能有也可能没有

(2)语句一般会改变环境(声明，赋值)

(3)上面两句话并不是绝对的


### 二.大小写敏感

var a 和 var A是不同的

object 和 Object是不同的

function 和 Function是不同的

window 和 Window是不同的。平时只用window，大Window从来不用

### 三.空格

  大部分空格没有实际意义

  1.var a=1和var a = 1没有区别

  2.加回车大部分时候也不影响

  **面试题**
  
**只有一个地方不能加回车，那就是return后面**

return后面不能加回车，否则它会自动补充一个undefined! 这就是js的原创之处。

![](https://pic1.zhimg.com/80/v2-6c9cb5ee573f2f28a653a59ea13967d2_1440w.jpg?source=3af55fa1)

### 四.标识符 

取名字时用到的符号叫标识符

1.规则

(1)第1个字符可以是Unicode字母(包含英语/希腊/法语字母等等) 或 $ 或 _ 或 中文

(2)后面的字符，除了上面所说，还可以有数字

2.变量名是标识符
```js
var_=1
var $=2
var 你好='hi'
```
3.其它标识符用到再说

### 五.注释

1.//单行注视    /* 多行注视 */

2.注释分类

1’不好的注释

  把代码翻译成中文

  过时的注释

  发泄不满的注释

2’好的注释

(1)踩坑注解

(2)为什么代码会写的这么奇怪，遇到什么bug

对于某些特殊代码，例如非会员故意延迟5s

注释时注意职业素养，不要瞎写

不好的注释:描述你做了什么，好的注释:为什么这样做以及后面蕴藏的坑

## 区块block

把代码包在一起就叫做区块，常常与if/for/while合用
```js
{
  let a=1
  let b=2
}
```
## if...else...语句

一.语法

1.if(表达式){语句1}else{语句2}

  ()括号里必须有值

2.{}在语句只有一句的时候可以省略，不建议这样做


二.变态情况

**面试题**

1.表达式里可以非常变态，如a=1

  变态之处:1个等于号是赋值语句。要想输出“a不是1”，应当是=== ( ==已过时,不要用)

例子
```
a=2
if(a=1){
  console.log('a是1')
}else{
 console.log('a不是1')
 }

输出结果：a是1
```
2.语句1里可以非常变态，如嵌套的if...else

例子
```js
let a=2
if( a < 100)
  if( a < 10)
    console.log('a小于10')

输出结果:a小于10
```
3.语句2里可以非常变态，如嵌套的if else
```js
if(a<100){
}else if(a>1000){
console.log('a大于1000')
}else{
}
```
4.缩进也可以很变态，如**面试题**常常下套
```
a=1
if(a===2)
  console.log('a')
  console.log('a等于2')

输出结果:a等于2
```
解析：如果省略{}那么if只会管到第1句

js没有行的概念，只会执行第1个语句.就算2个写在一行，也只会执行第一个

5.正确写法
```
if(表达式){
  语句
}else if(表达式){
  语句
}else{
  语句
}
```

6.推荐写法，只在函数里面用。
```
function fn(){
  if(表达式){
    return 表达式
  }
  if(表达式){
    return 表达式
  }
return 表达式
}
```

## switch语句(不推荐用，但要了解)

> if... else...升级版

1.语法
```
switch(fruit){
  case "banana":
    //...
    break;
  case "apple":
    //...
    break;
  default:
    //...
}
```
2.break

大部分时候，省略break你就完了

少部分时候，可以利用break

例子:方便进行2个case的判断
```
a=2
switch(a){
  case 1:
  case 3:
    console.log("单数")
    break;
  case 2:
  case 4:
    console.log("双数")
    break;
}

输出结果:双数
```
这也是js语言设计不好之处。

## 问号冒号表达式(又叫三元表达式)
> 最简单的if...else写法

表达式1 ？表达式2 : 表达式3
 
例子
```
 function max(a,b){
   if(a>b)return a;
   else retuen b;
 }
 
 function max(a,b){
   return a>b ? a : b
 }
 
 function abs(n){//求一个数的绝对值
   return n>0 ? n : -n
 }
```

## &&短路逻辑

> 代替if...else...

前端有个特点，能不用if...else就不用

A && B && C && D取第1个假值或D(读法andand)
并不会取true/false

例子
调用函数，但不确定是否存在
如果xx存在就调用，不存在就不调用
```
if(window.f1){
  console.log("f1存在")
}
等价于
window.f1 && console.log("f1存在")
```

如果前面为真后面就执行,如果前面为假，后面就不执行.

常见写法
```
console && console.log && console.log('hi')
```

## ||短路逻辑
A || B || C || D取第1个真值或D(读法或或)
并不会取true/false

```
if(!a){ //如果不是a就是b
  b
}

等价于

a || b
```

常用写法
```
a = a || 100

等于

if(a){
  a=a
}else{
  a = 100   //保底值
}
```

**总结**

条件语句

1.if...else...

 常用，但是很多时候都不用它，尤其是代码简短的时候

2.switch

  不要少break

3.A ? B : C

  常用

4.A && B

  常用 

  fn && fn()如果fn存在就调用,不存在就不调用

5.A || B

  常用

  A = A || B  //A存在就取A，不存在就取B。B是保底值
  
## while循环 当...时 
1.语法

while(表达式){语句}

判断表达式的真假

当表达式为真，执行语句，执行完再判断表达式的真假

当表达式为假，执行后面的语句

真的话就执行并且再次判断，假的话就跳出。

2.其它

do...while用的不多


例1
```
var a=0
while(a<10){
  console.log(a)
  a=a+1
}
console.log('done')

输出结果: 0
         1
         2
         3
         4
         5
         6
         7
         8
         9
         done
```
注意：不加done,chrome控制台会多打印一个10，实际上等于9时就跳出来了。  

补充：chrome控制台换行不执行命令快捷键:shift+enter

例2      

浮点数不精确造成的问题
```
a=0.1
while(a!==1){
  console.log(a)
  a=a+0.1
}
```
这段代码会死循环，a不会等于1，因为浮点数不精确造成的。

## for循环

1.语法糖

for循环是while循环的方便写法

方便在哪？

2.语法
```
for(语句1;表达式2;语句3){
  循环体
}
```
先执行语句1

然后判断表达式2

如果为真，执行循环体，然后执行语句3

如果为假，直接退出循环，执行后面的语句


例子
```
for(var i = 0; i < 5; i++){
  setTimeout(()=>{
    console.log(i)
  },0)
}

输出结果:  5    
          5
          5
          5
          5
```
为什么是5个5？ -执行时机

因为for循环里面执行了5遍，setTimeout就执行了5遍

那怎么解决这个问题呢？

var改为let
```
for(let i = 0; i < 5; i++){
  setTimeout(()=>{
    console.log(i)
  },0)
}

输出结果: 0
         1
         2
         3
         4
```

例2
```
for(var i = 0; i < 5; i++){ }
setTimeout(()=>{console.log(i)},0)

执行结果:5
```

## break 和 continue

退出所有循环 VS 退出当前一次循环

例1
```
for(var i=0;i<10;i++){
  if(i%2===1){//i等于单数(i除以2的余数=1就是单数)
    break
  }
}

输入:     i

输出结果:  1
```
解析:break直接跳，不走i++

例2
```
for(var i=0;i<10;i++){
  if(i%2===1){
    continue
  }else{
    console.log(i)
  }
}

输出结果:  0
          2
          4
          6
          8
```
解析:continue跳出这次循环
```
for(var i=0;i<10;i++){
  for(var j=101;j<110;j++){
    if(i===5){
      break
    }
  }
  console.log(i)
}

输出结果: 0
         1
         2
         3
         4
         5
         6
         7
         8
         9      
```      

## label语句(面试5%)
1.语法
```
foo: {
  console.log(1);
  break foo;
  console.log('本行不会输出')
}
console.log(2);
```

2.面试
例1
```
{
  foo: 1
}
```
上面的东西是什么？(注意1后面没有分号;)

在Firefox里面，foo就是个标签,值为1


例2
```
var a={
  foo: 1
}
```
现在a才是对象



