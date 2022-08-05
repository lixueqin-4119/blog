## 一.HTML5概述
> [MDN](https://developer.mozilla.org/en-US/docs/Glossary/HTML5#see_also) HTML5权威指南


WWW指万维网(WWW=URL+HTTP+HTML),是1990年由李爵士发明的,17年被颁发图灵奖 

WWW的直观感受，如何使用万维网:你输入网址，看到网页，看不见的部分由HTTP搞定。 

**李爵士做了啥？**

自己写了第一个浏览器，第一个服务器，用自己写的浏览器访问了自己写的服务器。发明了WWW,同时发明了HTML HTTP和URL。

**李爵士这么做有什么意义？**

让每个人输入网址就能看到网页。没有网址就发明URL,没有网页就发明HTML，为了让系统更完善发明了HTTP。

**万维网与互联网的关系**

万维网是基于互联网实现的，输入地址就能看到网页的一个网络。

**制作网页需要的知识：** 域名知识 + HTTP服务器知识 + HTML知识 + 其它

## 二.HTML5技术集

html5分狭义Html5和广义Html5

**1.狭义指 Html5新增的+旧的，大概 110 个标签。**

> 核心标签 
> 
> 元数据：meta 
> 
> 章节：section nav artical aside header footer main 
> 
> 内容层次:ol+li ul+li dl+dt+dd 
> 
> 文字：a 
> 
> 嵌入内容：image video canvas svg 
> 
> 表格：table tbody thead tfoot tr td th 
> 
> 表单：form label input button select option textarea

**2.广义标签包括 Html5和其它技术(包括CSS 3和javascript等)**

![image](https://upload-images.jianshu.io/upload_images/21487050-47bdb51a24a8e7ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三. HTML的语法

**标签**
```
<!DOCTYPE html> //文档类型

<tag attr=value>内容</tag> //属性值加引号的规则

<tag attr>内容</tag> //没有值的属性

<tag attr=value> //直接闭合
```
**细节**

大小写有区别吗？一般小写

要加引号吗？有特殊字符就加，没有就不加。

如何注释？<!- -   - ->

如何组合？


**解析标签语法：**
```
1.<tag attr=value>内容</tag>
```
属性值

id=xxx

参数加 单/双引号和 无引号都是对的。

属性值加引号的规则与命令行一致。有特殊字符就加，没有就不加。
```
2.<tag attr>内容</tag>
```
没有等于号，没有值的属性，布尔属性(只有真和假)。

例子:
```
<input type="checkbox" checked="false"> //运行结果为被勾选状态☑️. 新版语法不支持，只要写了checked那不管是true还是false结果都是checked。
```
不过要注意不是所有的语法都这样。
```
3.<tag attr=value>
```
语法：直接闭合
```
例子：<link href="xxx.css">
```
由于html有超强纠错功能有/会自动纠错,最新版规范写法是不加/

## 四.HTML排错

**如何知道html是否正确？ 在线/npm**

1‘.W3C提供了个[在线工具](https://validator.w3.org/)

![image](https://upload-images.jianshu.io/upload_images/21487050-3e5d6fa92bb5a4fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择“Validate by Dtrect Input”,粘贴代码点击按钮“Check”

2‘.[W3C validator](https://www.npmjs.com/package/node-w3c-validator)

全局安装
```
 yarn global add node-w3c-validator
 node-w3c-validator --version 
```

**使用**

打开vscode新建终端运行 node-w3c-validator -i + 路径
```
代码：node-w3c-validator -i index.html 
```

> 其它
> 
> 体系化学习 
> 
> 1.学一门语言必须学会什么?
> 
> (1)语法(怎么写代码) 
> 
> (2)如何调试(怎么知道自己代码写错了)
> 
> (3)在哪查资料(其实就是为了抄代码) 
> 
> (4)标准制定者是谁(找正版文档) 
> 
> 2.如何学? -CRM法 
> 
> copy 抄 
> 
> run 放在自己的机器上运行成功 
> 
> modify 改变，加入自己的想法，然后重新运行
