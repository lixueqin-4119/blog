# JS数据类型

推荐阅读[《我用了两个月的时间才理解 let》](https://zhuanlan.zhihu.com/p/28140450)

[JS秘密花园](https://bonsaiden.github.io/JavaScript-Garden/zh/)收录了JS的各种bug

## **数据为什么需要类型？**

数字与字符串

(1)功能不同

数字是数字，字符串是字符串，要严谨

数字能加减乘除，字符串不行

字符串能表示电话号码(有时候电话号码还可以有字母)，数字不同

(2)存储形式不同

JS中，数字是用64位浮点数的形式存储的

JS中，字符串是用类似UTF8形式存储的(UCS-2)

## JS是如何存数字的？

就是把所有数字变成二进制存下来，再用十六进制简化。

把所有的十进制都转为二进制

补充：内存里只能存0和1，2都存不了。2就要把它变成1或0。

**二进制**

1.十进制转二进制

例子：31变成二进制 31 = ?x2^5 + ?x2^4 + ?x2^3 + ?x2^2 + ?x2^1 + ?x2^0

经过一番尝试 31=0x2^5 + 1x2^4 + 1x2^3 + 1x2^2 + 1x2^1 + 1x2^0

所以31(十进制)=01 1111(二进制)

2.二进制转十进制

例子：100011变成十进制
![](https://upload-images.jianshu.io/upload_images/21487050-ef8e34ec0e93cd14?imageMogr2/auto-orient/strip|imageView2/2/w/680/format/webp)

每位数乘以2的N次方，然后加起来即可

100011=2^5 + 2^1 + 2^0=35

**用十六进制表示二进制**

十六进制是二进制的简写(把每4位改写成1位)

十六进制：0 1 2 3 4 5 6 7 8 9 A B C D E F

十六进制没有大于9的数字，后面是字母替代

![](https://upload-images.jianshu.io/upload_images/21487050-356dc7cd60a5ab91?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

![](https://upload-images.jianshu.io/upload_images/21487050-b6318a60c9cb5218?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)



## JS如何存字符？

**1.英文编号**

转成数字，但是注意‘1’不能用1来表示

编号表示，用0～127表示所有符号

最常用的：0是48，A是65，a是97

上面是外国的编号只能存128个字符，于是中文也开始编号了。

![](https://upload-images.jianshu.io/upload_images/21487050-2216f37d3e1f4ef4.png?imageMogr2/auto-orient/strip|imageView2/2/w/280/format/webp)


**2.中文编号「国标2312」**

**中文编号由中国国家标准局来编，名称「国标2312」**

国标是16位，也就是2个字节。

![](https://upload-images.jianshu.io/upload_images/21487050-2cdb60ab71d4a3f7?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

**3.国标扩简称GBK**

怎么表示生僻字，繁体字，韩文？之前忘了编进取。微软推出了一个国标扩展，简称GBK。

![](https://upload-images.jianshu.io/upload_images/21487050-4a48ebaa0aebe0ff.png?imageMogr2/auto-orient/strip|imageView2/2/w/280/format/webp)

国标局推出GB18030想取代微软的GBK但由于不兼容GB2312，所以没人愿意使用。

**4.藏文泰语还是没法支持，于是推出了万国码 Unicode**

Unicode优点：已收录13万字符(大于16位)，全世界通用。以后还会继续扩充不会停止。

缺点：两个字节不够用，每个字符要用3个及以上字节。这样所有文件都扩大50%不划算。
那怎么办？

虽然用Unicode但存的时候偷懒，UTF-8就被发明出来了。

![](https://upload-images.jianshu.io/upload_images/21487050-c291293d349fdbc9.png?imageMogr2/auto-orient/strip|imageView2/2/w/396/format/webp)


UTF-8就是通过"让前面那个人告诉计算机我有多少位"来把这个变长得以实现，从而得到节省空间的目的。

字符也是存数字，数字也是存数字。计算机怎么知道存的是字符还是数字呢？

很简单，计算机根本不知道，它看你的文件格式。文件后缀是txt那存的就是字符串。

## js中的数据类型

**8种**

数字number

字符串string

布尔bool, 只有两个值true和false(注意大小写)

符号symbol

空undefined

空null

对象object

[BigInt](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt)


总结：4基2空1对象,前6种类型都是简单类型，只有object叫做复杂类型。


**以下不是数据类型**

数组,函数,日期

它们都属于object


## **一.数字number 64位浮点数**

**1.写法**

(1)整数写法     1

(2)小数写法     0.1

(3)科学计数法   1.23e4(1.23x10^4 )

(4)八进制写法   0123或00123或0o123

(5)十六进制写法 0x3F或0X3F

(6)二进制写法   0b11或0B11

**2.特殊值**

(1)正0和负0

(2)无穷大 

infinity、+infinity、-infinity

(3)无法表示的数字

NaN(not a number) 

无法表示的数字是数字(认知有限时的替代)

NaN==NaN 

输出结果是false

**为什么是64位浮点数？**

![](https://upload-images.jianshu.io/upload_images/21487050-885b117e7f156893?imageMogr2/auto-orient/strip|imageView2/2/w/586/format/webp)

要先转为二进制，然后再改写。

```
例子：0.5的存储形式是？ 
首先转为二进制0.1，所以是 0 -1 0
```

### **64位浮点数最大/小存多少？**

范围和精度

**范围(忽略符号位)**


指数拉满2^1024、有效数字拉满全是1，得到最大二进制数字。

转化为十进制就是(**1后面接300个0**)

Number.MAX_VALUE:1.7976931348623157e+308

指数负方向拉满2^-1023  、 有效数字最小1，得到最小值(**0后面加300个小数点0**)

Number.MIN_VALUE:5e-324


**精度(有效数字)**

最多只能到52+1个二进制位表示有效数字

2^52 对应的十进制是9后面15个0，所以**15位有效数字**都能精确表示

16位有效数字如果小于90开头也能精确表示

9110 0000 0000 0001就存不下来


## **二.字符串string**


每个字符两个字节(阉割版UTF8)

**写法**

```
单引号'你好' 双引号"你好" 反引号`你好`
```
**注意**

引号不属于字符串的一部分，就像书名不属于书名的一部分一样

如果要在单引号里面包含单引号怎么办？

```
例子:'it's ok'
正确写法：'it\'s ok'  //这就是转义
或者"it's ok"
或者`it's ok`
```
### **什么是转义？**

**用另一种写法表示你想要的东西(背下来)**

```js
\' 表示'
\" 表示"
\n 表示换行
\r 表示回车
\t 表示tab制表符
\\ 表示\
\uFFFF 表示对应的Unicode字符 
\xFF 	表示前256个Unicode字符
```
例子:   🔍你 Unicode

```
控制台: "\u4F60"
输出:   '你'
 
控制台: "\x33"
输出:   '3'
```
### **多行字符串**

用反引号` `可以做到

```
let s=`大家好
我是小红
你是谁
 `
控制行输入: s
输出结果:
"大家好
我是小红
你是谁"
```

### **字符串的属性**

等等，对象才有属性，为什么字符串也有属性？

只有对象才有属性，字符串 数字 布尔没有属性这么一说的。

为什么字符串也属性？等学完对象才能解答

**字符串的长度**

string.length

```
'123'.length    //3
'\n\r\t'.length //3
'\\\\\\'.length //3
''.length       //0
' '.length      //1
```

### **通过下标读取字符**

**1.string[index]**

let s='hello'

s[0]//"h"

**2.注意index从0开始**

s[0]是第一个字符

**3.注意index到length**

let s="hello"

s[5] //undefined,居然不报错

### **base64转码**

1.window.btoa

 正常字符串转为 Base64 编码的字符串

**编码**

 ```
 例子：window.btoa('123')
 输出结果：'MTIz'
 ```

2.window.atob

 Base64编码的字符串转为原来的字符串

**反编码**

``` 
例子：window.atob('MTIz')
 输出结果：'123'
```

**3.一般用来隐藏招聘启事里的简历**

 邮箱：ZmFuZ3lpbmdoYW5nQGZveG1haWwuY29t

 有时候也用来自欺欺人

 所谓的「加密」也就能骗过一部分外行

``` 
例子：window.atob('ZmFuZ3lpbmdoYW5nQGZveG1haWwuY29t')
输出结果：'fangyinghang@foxmail.com'
```

## **三.布尔bool两个值true和false**

1.下列运算符会得到bool值

(1)否定运算

例如：!value

(2)相等运算

例如：1==2、1！=2、3===4、3!==4

(3)比较运算

例如：1>2、1>=2、3<4、3<=4

**2.if配bool**

**(1)if语句常常需要判断真假**

if(value){...}else{...}

**(2)问题来了**

如果value是bool值还好说

如果value不是bool值咋办，谁真谁假?

1是真还是假，0是真还是假?

命令行：成功返回0，失败返回12345

主要还是由程序员决定的

'1'是真还是假，'0'是真还是假?

那怎么判断呢？

## **5个falsy值**

falsy就是相当于false但是又不是false的值

**分别是undefined null 0 NaN ''(注意没空格)**

2个空undefined null，2个数字0 NaN，1个字符串''。

判断1：1是真还是假，0是真还是假?

1不属于2个数字所以是真，0属于2个数字所以是假

1是真，0是假

判断2：'1'是真还是假，'0'是真还是假?

'1'和'0'都是真

**总结：**

**7个数据类型**

数字number 字符串string 布尔bool 符号symbol 空undefined 空null  对象object

4基2空1对象

**5个falsy值**:2空2数字1字符串 undefined null 0 NaN ''

### **undefined和null两种空类型**

为什么有两个空，这就是js原(la)创(ji)之处

**区别**

没有本质区别

细节一

如果一个变量声明了但没有赋值，那么默认值就是undefined,而不是null

```
var a  //a的值就是undefined
```

细节二

如果一个函数没有写return，那么默认return undefined,而不是null

细节三

**前端程序员习惯把非对象的空值写为undefined,把对象的空值写为null**

四.符号symbol(很少用，没用)

## 变量声明

### **三种声明方式**

```
var a=1
let a=1
const a=1
a=1
```

**区别**

var是过时的、不好用的方式

let是新的，更合理的方式

const是声明时必须赋值，且不能再改的方式

最后这种方式时错误的不准这样声明

**总结：**

**以后就用let或const,不要用var!**

**let声明变量 const声明常量(不能变的量)**

 var变量提升押题时再讲

### **let声明**

**规则**

 1.遵循块作用域，即使用范围不能超出{}

 2.不能重复申明

```
let a=1
let a=2 //错误
```

 3.可以赋值也可以不赋值

 4.必须先声明再使用，否则报错

 5.全局声明的let变量，不会变成window的属性(var可以)

6.for循环配合let有奇效

**面试题**

```js
 for(var i=0;i<5;i++){
   setTimeout(()=>console.log(i),0)
 }
 输出结果：5
         5
         5
         5
         5
```
```js
 for(let i=0;i<5;i++){
   setTimeout(()=>console.log(i),0)
 }
 输出结果：0
         1
         2
         3
         4
```
 
### **const声明**

const变量是只读变量，又叫做常量，不会变

**规则**

跟let几乎一样

**只有一条不一样：声明时就要赋值，赋值后不能改**

```js
const i=1
i=2   //错误，不能修改
```
变量声明时，既指定了值又指定了**类型！(重要)**

**name和'name'的区别？**

**name是变量**

值可变，可能是`'name'`,也可能是`'hello'`

**'name'是字符串常量**

常量就是不变量

`'name'`只能是`'name'`，不能是其他值

## **类型转换**

**1.number=>string**

1' String(n)

2' n+''(前端流行做法)

例子

```js
1' var n=1
   String(n)
   输出结果："1"

2' var n=1
   n+''
   输出结果："1"
```

补充

js有个bug:String(100000000)

当括号内位数达到22位时突然变成科学计数法就无法正常转换字符串

![](https://upload-images.jianshu.io/upload_images/21487050-51366f98a560ba07.png?imageMogr2/auto-orient/strip|imageView2/2/w/444/format/webp)

[解决js数字过大表现为科学计数法的问题](https://segmentfault.com/q/1010000010460890)

**2.string=>number**

1' Number(s)

2' s-0(前端流行做法)

3' parseInt(s)/parseFloat(s)

例子
```js
1' var s='123'
   Number(s)
   输出结果：123

2' var s='123'
   s-0
   输出结果：123
   
3' parseInt(s)  
```

**3.x=>bool**

1' Boolean(x)

2' !!x(前端流行做法)

**4.x=>string**

1' String(x)

2' x.toString()

**toString有个bug: 1.toString()**

```
改为(1).toString()或者1..toString()
```
