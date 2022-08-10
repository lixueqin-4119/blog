## **推荐书籍和工具**

**1.书籍《[网道HTML教程](http://link.zhihu.com/?target=https%3A//wangdoc.com/html/intro.html)》**

**2.VSCode格式化工具 Prettier - Code formatter**

安装后开启自动格式化:

文件/首选项/设置-> 1’搜索“auto save”选择onFocusChange-> 搜索并勾选"format on save"

**3.在线编码工具** [沙盒](http://link.zhihu.com/?target=https%3A//codesandbox.io/)

## 一.标签详解

**1.html起手式 Emment感叹号**

![image](https://upload-images.jianshu.io/upload_images/21487050-e863d31d64e7eb2d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

UTF-8支持所有人类语言，GBK只支持亚洲一些国家。

content="IE=edge"告诉浏览器使用最新版本(内核)

**2.章节标签 文章/书的层级**

```js
标题h1~h6
章节section
文章article
段落p
头部header
脚步footer
主要内容main
旁支内容aside
划分div
```
![image](https://upload-images.jianshu.io/upload_images/21487050-7440f5a39999e1a3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 二.全局属性

所有标签都有的属性叫做全局属性。

```js
class  
contenteditable 可编辑的
hidden
id
style
tabindex
title
```

**解析**

**1.class可写多个**
```js
class="middle bordered"
.middle{ }
.bordered{}
```

**2.contenteditable 可以使任意一个元素被编辑**

用户可直接编辑页面上的文字，大部分时候没用。但是contenteditable可以用来做个自己的编辑器。

**3.style标签默认不显示，有办法可以让它显示吗？**

可以，但是要把style挪到body下。

**4.结合contenteditabl 与 display:block; 可以实现让用户自己修改样式！**

这也是一种好用的调试技巧，可以用来**调试代码**！

```
<body>
<style contenteditable>
  style{display:block; border:1px solid red;}
  .middle{background: red;}
  .bordered{
    border:1px solid red;
  }
 </style>
 </body>
```

**5.hidden 可以让任何东西看不见**

hidden可以用css挽回 代码：display:blok;

**[6.id](http://link.zhihu.com/?target=http%3A//6.id/)**

id = xxx 不是老司机不要直接通过 xxx 直接获取到元素，**id不报错！**

因为你不知道 xxx 哪些能用，例如parent就不能用。这些忌讳要背的。

**如何查忌讳？**

在浏览器控制台打window. 这是window已经有的全局属性不能用，如下图所示：

![image](https://upload-images.jianshu.io/upload_images/21487050-835ea5d07a3a2765.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**总结，css和js里尽量永远不要用，全都用class就好。**

**[7.style](http://link.zhihu.com/?target=http%3A//7.style/)**

每个元素可以写一个style属性

css html js属性同时存在的话，**js优先级会覆盖css。**
```js
id=xxx
xxx.style.border='10px solid black'
```

**8.tabindex**

**功能：代替鼠标用的**

tabindex是用来控制tab的顺序的。

tabindex=1，它会按照1 2 3顺序走，**特殊值0**“最后访问”，**特殊值-1**“别访问我”。

**9.title="显示完整的一段话"**

**单行文字溢出该怎么写？** 

```js
//css不换行，溢出省略，溢出用省略号显示
white-space:nowrap;
text-overflow:ellipsis;
overflow:hidden;
```

**10.小技巧**

**css不知道怎么写？**

**可以用开发者工具**

![image](https://upload-images.jianshu.io/upload_images/21487050-adb214a2ba038581.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三.默认样式

html标签自带的样式称为默认样式。为什么有默认样式，因为html被发明时css还没出生。

1.怎么看默认样式？如图所示

![image.png](https://upload-images.jianshu.io/upload_images/21487050-d9556764f2d6b607.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开Chrome开发者工具 -> Elements ->Styles ->user agent stylesheet

[可以用element.style](http://link.zhihu.com/?target=http%3A//xn--element-hc5kp88abx7f.style/){}覆盖默认样式

User Agent就是浏览器

**2.CSS reset**

**默认样式已经不符合我们的需求，把默认样式干掉叫做css reset,重新把样式写一下。**

我经常用的代码 [cssreset.css](http://link.zhihu.com/?target=https%3A//github.com/lixueqin-4119/cssreset.css)

也可以抄大厂的代码。

## 四.内容标签

常用的内容标签

1.ol+li 有序列表

2.ul+li 无序列表

3.dl+dt+dd 描述列表，dt词

4.pre 用于保留连续的空格或回车，一般用于写代码

5.code 用于写代码，代码内容等宽；常与pre连用

```js
<pre>
 <code>
  var a=1;
  console.log(a)
 </code>
</pre>
```

6\. 分割线hr

7\. 换行br

8.a 超链接

9.em 语气强调

10.strong 内容重要

11.quote 引用，行内

12.blockquote 引用，块级可换行
