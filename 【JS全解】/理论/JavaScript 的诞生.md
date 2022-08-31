> JavaScript（通常缩写为JS）是一种高级的、解释型的编程语言。
> JavaScript是一门基于原型、头等函数的语言，是一门多范式的语言，它支持面向对象程序设计，指令式编程，以及函数式编程。
> 它提供语法来操控文本、数组、日期以及正则表达式等，不支持I/O，比如网络、存储和图形等，但这些都可以由它的宿主环境提供支持。 
> 它已经由ECMA（欧洲电脑制造商协会）通过ECMAScript实现语言的标准化。它被世界上的绝大多数网站所使用，也被世界主流浏览器（Chrome、IE、Firefox、Safari、Opera）支持。

> JavaScript与Java在名字或语法上都有很多相似性，但这两门编程语言从设计之初就有很大的不同，JavaScript的语言设计主要受到了Self（一种基于原型的编程语言）和Scheme（一门函数式编程语言）的影响。
> 在语法结构上它又与C语言有很多相似（例如if条件语句、switch语句、while循环、do-while循环等）。

> 在客户端，JavaScript在传统意义上被实现为一种解释语言，但在最近，它已经可以被即时编译（JIT）执行。
> 随着最新的HTML5和CSS3语言标准的推行它还可用于游戏、桌面和移动应用程序的开发和在服务器端网络环境运行，如**Node.js**。 

## Javascript的历史
>   &nbsp;  &nbsp;1995年，Netscape（网景）公司的布兰登·艾奇（Brendan Eich）在网景导航者浏览器上首次设计出了JavaScript。
>   Netscape最初将这个脚本语言命名为LiveScript，后来Netscape公司与Sun公司（2009年被Oracle公司收购）合作之后将其改名为JavaScript，这是由于当时Sun公司推出的Java语言备受关注，Netscape公司为了营销借用了Java这个名称，但实际上JavaScript与Java的关系就像“雷锋”与“雷峰塔”，它们本质上是两种不同的编程语言。
>   在设计之初，JavaScript是一种可以嵌入到网页中的编程语言，用来控制浏览器的行为。随着JavaScript的发展，JavaScript的用途已经不仅局限于浏览器了。
>   **Node.js** 的出现使得开发人员能够在服务器端编写JavaScript代码，使得JavaScript的应用更加广泛。

## 概论
<hr>

**完整的JavaScript包括以下几个部分：**

 - **ECMAScript**，描述了该语言的语法和基本对象
 - **文档对象模型（DOM）**，描述处理网页内容的方法和接口
- **浏览器对象模型（BOM**），描述与浏览器进行交互的方法和接口

**JavaScript的基本特点：**
- 是一种解释性脚本语言。
- 主要用来向HTML页面添加交互行为。
- 可以直接嵌入HTML页面，但写成单独的js文件有利于结构和行为的分离。

**JavaScript常用来完成以下任务：**
- 嵌入动态文本于HTML页面
- 对浏览器事件作出响应
- 读写HTML元素
- 在数据被提交到服务器之前验证数据
- 检测访客的浏览器信息
- 控制cookie，包括创建和修改等

## 特性
<hr>

> 不同于服务器端脚本语言，例如PHP与ASP，JavaScript主要被作为客户端脚本语言在用户的浏览器上运行，不需要服务器的支持。所以在早期程序员比较青睐于JavaScript以减少对服务器的负担，而与此同时也带来另一个问题：安全性。
> 随着服务器变得强大，现在的程序员更喜欢运行于服务端的脚本以保证安全，但JavaScript仍然以其跨平台、容易上手等优势大行其道。
> 同时，有些特殊功能（如AJAX）必须依赖JavaScript在客户端进行支持。随着引擎V8和框架Node.js的发展，及其事件驱动及异步IO等特性，JavaScript逐渐被用来编写服务器端程序。
> 在近几年中，Node.js的出世，让JavaScript也具有了一定的服务器功能。 

 **以下是 ECMAScript通常实现所共有的特性。**

**指令式与结构化**

> JavaScript从支持许多C语言的结构化编程语法（例如if条件语句、while循环、switch语句、do-while循环等）。
> 但作用域是一个例外：JavaScript在过去只支持使用v**ar关键字来定义变量的函数作用域**。
> ECMAScript  2015加入了**let关键字来支持块级作用域**。意味着JavaScript现在既支持函数作用域又支持块级作用域。
> 和C语言一样，JavaScript中的表达式和语句是不同的。有一点格式上的不同，JavaScript支持自动在语句末添加分号，因此允许忽略语句末尾的分号。

**弱类型**

> Javascript是弱类型的，这意味着变量可以被隐式地转换为另一个类型。
> 二元运算符+会把两个操作数转换为字符串，除非两个操作数都为数字类型。这是因为+也表示字符串连接操作。
> 二元操作符-会把两个操作数转换为数字类型。 一元操作符，包括+和-，都会把操作数转换为数字。 
> **下列为变量转换为字符串的例子：**
> - 字符串类型不变。
> - 数字会转换为其字符串表示。
> - 数组的元素会转换为字符串，然后连接成通过逗号,分隔的长字符串。
> - 其它对象会转换为[object Object]，其中Object中该对象的构造函数名<br>
> 类型的隐藏转换，是JavaScript受到批评的原因之一，因为其增加了规则的复杂度与发生错误的可能性。
> **JavaScript中诡异的隐式转换**
> ![请添加图片描述](https://upload-images.jianshu.io/upload_images/21487050-cff2d06afd10a89f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 
> **基于原型的面向对象** 
> 
> 在JavaScript中，对象是关联数组，通过原型（prototype，见下）进行扩充。每一个字符串键值提供对象的一个属性的名称。可以通过两种效果相同的方式进行访问：使用点号（obj.x）或使用方括号（obj['x']）。属性可以在运行时添加、重定义或删除。一个对象大多数属性（包括来自原型继承链的属性）都可以通过
> for...in循环访问。
> 
> **原型** 
> 
> JavaScript使用原型，而许多其它面向对象语言使用类用于实现继承。有了原型，使得在JavaScript中模拟基于类的面向对象特征变成可能。
> 
> **函数作为对象构造器** 
> 
> 函数在JavaScript中兼作为对象构造函数。在函数调用前加上new会创建一个原型的实例，并继承来自构造函数的属性和方法（包括来自Object原型）。ECMAScript
> 5提供Object.create方法，可以显式地创建实例还不是自动从Object继承。构造函数的prototype属性决定了用于新对象的内部原型。可以通过修改构造函数的原型的方法来为对象添加新的方法。也可以修改JavaScript的内部对象的原型，如Array或Object。尽管可以这么做，但对Object原型进行修改并不是一个好的做法。因为大多数JavaScript对象都会从Object继承，且不会希望其原型做了修改。
> 
> **函数作为方法** 
> 
> 与大多数而向对象的语言不同，在JavaScript中函数定义与方法定义没有明显的区别。唯一的区别在于调用时：当函数被作为方法调用时，函数的this会指向调用此函数的对象。
> 
> **传统的类定义与使用格式** 
> 
> ECMAScript ES6加入了对class、extends关键字的支持，使得类的定义与继承行为更加类似于其它面向对象语言，同时也更加容易使用。
> 
> **函数式** 
> 
> 在JavaScript中，函数是一等的，函数也被认为是对象。因此，函数也可以有属性与方法，例如call()和bind等。嵌套函数指定义于其它函数内部的函数，在外部函数被调用时，嵌套函数会被创建。另外，嵌套函数是一个闭包，在外部函数的作用域（包括常量，局部变量和参数）都成为内部函数状态的一部分，甚至在外部函数执行完毕后，内部函数的状态依然保留。JavaScript同时也支持匿名函数。
> 
> **其它**
> 
> **运行时环境** 
> 
> JavaScript通常依赖于运行时环境（例如浏览器）来提供对象和方法，脚本可以通过这些对象和方法与环境（例如网页DOM）进行交互。它还依赖于运行时环境来提供包含/导入脚本（例如HTML的
> script元素）的功能。这本身不是语言功能，但是在大多数JavaScript实现中很常见。
> 
> **异步** 
> 
> JavaScript一般来说是单线程的。为了并发地处理事件，JavaScript程序输入/输出是使用事件和回调函数执行的。例如，这意味着JavaScript可以在等待数据库查询返回信息时处理鼠标单击。ECMAScript
> 
> ES6引入了Promise用于优雅地处理异步事件，其可以使得传统的基于回调的异步代码更加清晰与简单。
> 
> **变长参数**
> 
>  JavaScript中函数的参数长度是可变的，在函数内部可以通过arguments对象访问这些参数。

## 编程
<hr>
JavaScript是一种脚本语言，其源代码在发往客户端运行之前不需经过编译，而是将文本格式的字符代码发**送给浏览器由浏览器解释运**行。
解释型语言的弱点是安全性较差，而且在JavaScript中，如果一条运行不了，那么下面的语言也无法运行。而其解决办法就是使用异常处理

`try{}catch(){}︰`

```handlebars
console.log("a");    //這是正確的
console.log("b");    //這是正確的
console.logg("c");   //这是错误的，并且到这里会停下来
console.log("d");    //這是正確的

/*解決辦法*/
try{console.log("a");}catch(e){}    //這是正確的
try{console.log("b");}catch(e){}    //這是正確的
try{console.logg("c");}catch(e){}    //这是错误的，但是到这里不会停下来，而是跳过
try{console.log("d");}catch(e){}    //這是正確的
try{console.log("e");}catch(e){}    //這是正確的
```

> JavaScript被归类为解释型语言，因为目前主流的引擎都是每次运行时加载代码并解译。
> 
> V8是将所有代码解译后再开始运行，其他引擎则是逐行解译（SpiderMonkey会将解译过的指令暂存，以提高性能，称为即时编译），但由于V8的核心部分多数用JavaScript撰写（而SpiderMonkey是用C++），因此在不同的测试上，两者性能互有优劣。

与其相对应的是编译语言，例如C语言，以编译语言编写的程序在运行之前，必须经过编译，将代码编译为机器代码，再加以运行。

示例
以下是一个简单的JavaScript Hello World

```handlebars
<!DOCTYPE HTML>
<html>
    <head>
    <title>简单的JavaScript Hello World</title>
        <script type="text/javascript">
            document.write("Hello, world!");   // 直接插入页面中
            alert("Hello, world!");            // 弹窗显示
            console.log("Hello, world!");      // 在控制台里显示，需要先开启开发工具控制台
        </script>
    </head>
    <body>
　　　　……
    </body>
</html>
```
或是在浏览器的地址栏中使用javascript:，以交互方式表示：

```handlebars
javascript:alert("Hello world!");
```
> ## 版本
> 
> JavaScript最初开发于1996年，被使用于Netscape Navigator网页浏览器。同年微软在Internet
> 
> Explorer发布了一个实现。由于商标问题，这项实现被命名为JScript。1997年，JavaScript已经被网景公司提交给ECMA制定为标准，称之为ECMAScript，标准编号ECMA-262。使用显式版本号声明对语言的引用，作为一项Mozilla的特性，已在较新版本中被移除（至少为Firefox
> 59）。Firefox 4是最后一个需要显式地在引用时声明明确版本号的版本（1.8.5）。

# Javascript的缺陷

**一、为什么Javascript有设计缺陷？**

这里有三个客观原因，导致Javascript的设计不够完善.

**1.设计阶段过于仓促**
Javascript的设计，其实只用了十天。而且，设计师是为了向公司交差，本人并不愿意这样设计。

另一方面，这种语言的设计初衷，是为了解决一些简单的网页互动，并没有考虑复杂应用的需要。设计者做梦也想不到，Javascript将来可以写出像Gmail这种极其庞大复杂的网页。

**2.没有先例**

**3. 过早的标准化**

Javascript的发展非常快，根本没有时间调整设计。
更糟的是，Javascript的规格还没来及调整，就固化了。

1996年8月，微软公司强势介入，宣布推出自己的脚本语言Jscript；11月，为了压制微软，网景公司决定申请Javascript的国际标准；1997年6月，第一个国际标准ECMA-262正式颁布。

也就是说，Javascript推出一年半之后，国际标准就问世了。设计缺陷还没有充分暴露就成了标准。

 ## **二、Javascript的10个设计缺陷**


**1. 不适合开发大型程序**
Javascript没有名称空间，很难模块化；没有如何将代码分布在多个文件的规范；允许同名函数的重复定义，后面的定义可以覆盖前面的定义，很不利于模块化加载。

**2. 非常小的标准库**
Javascript提供的标准函数库非常小，只能完成一些基本操作，很多功能都不具备。

**3. null和undefined**

null属于对象（object）的一种，意思是该对象为空；

undefined则是一种数据类型，表示未定义。

```handlebars
  typeof null;       // object
　typeof undefined;  // undefined
```

两者非常容易混淆，但是含义完全不同。
　

```handlebars
   var foo;
　　alert(foo == null); // true
　　alert(foo == undefined); // true
　　alert(foo === null); // false
　　alert(foo === undefined); // true
```
**在编程实践中，null几乎没用，根本不应该设计它。**

**4. 全局变量难以控制**

Javascript的全局变量，在所有模块中都是可见的；任何一个函数内部都可以生成全局变量，这大大加剧了程序的复杂性。

```handlebars
a = 1;
　　(function(){
　　　　b=2;
　　　　alert(a);
　　})(); // 1
　　alert(b); //2
```
**5. 自动插入行尾分号**

Javascript的所有语句，都必须以分号结尾。但是，如果你忘记加分号，解释器并不报错，而是为你自动加上分号。有时候，这会导致一些难以发现的错误。

比如，下面这个函数根本无法达到预期的结果，返回值不是一个对象，而是undefined。
　


原因是解释器自动在return语句后面加上了分号。

```handlebars
function(){
　　　　return;
　　　　　　{
　　　　　　　　i=1
　　　　　　};
　　}
```
**6. 加号运算符**

+号作为运算符，有两个含义，可以表示数字与数字的和，也可以表示字符与字符的连接。

```handlebars
   alert(1+10); // 11
　　alert("1"+"10"); // 110
```
如果一个操作项是字符，另一个操作项是数字，则数字自动转化为字符。

```handlebars
   alert(1+"10"); // 110
　　alert("10"+1); // 101
```

这样的设计，不必要地加剧了运算的复杂性，完全可以另行设置一个字符连接的运算符。

**7. NaN**

NaN是一种数字，表示超出了解释器的极限。它有一些很奇怪的特性：
　

```handlebars
   NaN === NaN; //false
　　NaN !== NaN; //true
　　alert( 1 + NaN ); // NaN
```
与其设计NaN，不如解释器直接报错，反而有利于简化程序。

**8. 数组和对象的区分**

由于Javascript的数组也属于对象（object），所以要区分一个对象到底是不是数组，相当麻烦。Douglas Crockford的代码是这样的：

```handlebars
if ( arr &&
　　　　typeof arr === 'object' &&
　　　　typeof arr.length === 'number' &&
　　　　!arr.propertyIsEnumerable('length')){

　　　　alert("arr is an array");

　　}
```
**9. == 和 ===**

==用来判断两个值是否相等。当两个值类型不同时，会发生自动转换，得到的结果非常不符合直觉。

```handlebars

"" == "0" // false

　　0 == "" // true

　　0 == "0" // true

　　false == "false" // false

　　false == "0" // true

　　false == undefined // false

　　false == null // false

　　null == undefined // true

　　" \t\r\n" == 0 // true
```
**因此，推荐任何时候都使用"==="（精确判断）比较符。**

**10. 基本类型的包装对象**

**Javascript有三种基本数据类型：字符串、数字和布尔值。**

它们都有相应的建构函数，可以生成字符串对象、数字对象和布尔值对象。

```handlebars
   new Boolean(false);

　　new Number(1234);

　　new String("Hello World");
```

与基本数据类型对应的对象类型，作用很小，造成的混淆却很大。

```handlebars
   alert( typeof 1234); // number

　　alert( typeof new Number(1234)); // object
```


## **三、如何看待Javascript的设计缺陷？**

**既然Javascript有缺陷，数量还不少，那么它是不是一种很糟糕的语言？**


回答是Javascript并不算糟糕，相反它的编程能力很强大，前途很光明。


首先，如果遵守 **良好的编程规范** ，加上 **第三方函数库** 的帮助，Javascript的这些缺陷大部分可以回避。

其次，Javascript目前是网页编程的唯一语言，只要互联网继续发展，它就必然一起发展。目前，许多新项目大大扩展了它的用途，node.js使得Javascript可以用于后端的服务器编程，coffeeScript使你可以用python和ruby的语法，撰写Javascript。

最后，只要发布新版本的语言标准（比如 ECMAscript 5），就可以弥补这些设计缺陷。当然，标准的发布和标准的实现是两回事，上述的很多缺陷也许会一直伴随到Javascript存在的最后一天。

<br/>
<br/>
本文主要来源于维基百科及 [阮一峰的Javascript诞生记](http://www.ruanyifeng.com/blog/2011/06/birth_of_javascript.html)
