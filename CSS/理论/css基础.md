> 体系化学习
> 
> 1.学一门语言必须学会什么
> 
> 语法
> 
> 如何调试
> 
> 在哪查资料
> 
> 标准制定者是谁
> 
> 2.如何学 -CRM
> 
> copy
> 
> run
> 
> modify
> 

**前言**

![image](https://upload-images.jianshu.io/upload_images/21487050-c07f2eba8fec4bfc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

css 全称叫**层叠样式表**，是由李爵士的挪威同事赖先生提出的。

**层叠** 表现在 **"样式层叠"**  **"选择器层叠"** 和 **“文件层叠”**。

2004～2011年的css2.1版本是使用 **最广泛的版本** (IE支持)。
从css3开始 **分模块升级**，**不存在css4了。**

**如何知道哪些浏览器支持哪些特性？** 
使用[caniuse.com](https://link.zhihu.com/?target=http%3A//caniuse.com)

## 一.语法

语法一:样式语法

```
选择器{
属性名:属性值;
/* 注释 */
}
```

语法二：at语法

```
@charset "UTF-8";    必须放在第一行且分号;不能丢
@import url(2.css);    分号不能丢
@media (min-width:100px)and(max-width:200px;){
   语法一
}
```

**@charset "UTF-8"指定当前文件的字符编码（或字符集)**

## 二.如何调试css

css不会报错，怎么知道自己有没有写错？

**(1)学会使用开发者工具**

找到你脑中的标签

看它是否有选择器

看它的样式是否被划掉

看它的样式是否有警告

![image](https://upload-images.jianshu.io/upload_images/21487050-f77d63b220a6d1a8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**(2)Border调试法**

怀疑某个元素有问题

就给这个元素加border：1px solid red;

(3)使用vscode/webstorm看颜色

(3)工具搜索 🔍w3c css validator在线调试

## 三.在哪查资料

**1.MDN**

🔍flex mdn

**2.css tricks**

🔍flex css tricks

**3.张鑫旭的博客**

🔍flex 张鑫旭

## 四.标准制定者
李爵士创办的W3C是权威文档 [CSS spec](https://link.zhihu.com/?target=https%3A//www.w3.org/Style/CSS/specs.en.html) 

推荐阅读 [中文版](https://link.zhihu.com/?target=http%3A//www.ayqy.net/doc/css2-1/syndata.html)

## 五.练习

在哪搜练习素材

PSD

1.国外 freepik

🔍 PSD web [免费版](https://link.zhihu.com/?target=https%3A//www.freepik.com/search%3Fdates%3Dany%26format%3Dsearch%26page%3D1%26query%3Dweb%26selection%3D1%26sort%3Dpopular%26type%3Dpsd)

补充：下载免费版时记得在最后附上作者的名字

![image](https://upload-images.jianshu.io/upload_images/21487050-6818c11a5343b89f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.国内

免费psd 🔗[https://cn.365psd.com/free-psd/ui-kits](https://link.zhihu.com/?target=https%3A//cn.365psd.com/free-psd/ui-kits)

3.效果图(不提供下载)

[顶级设计师社区](https://link.zhihu.com/?target=http%3A//dribbble.com)

只能用肉眼模仿它

4.商业网站(直接模仿)

## 基本概念

![image](https://upload-images.jianshu.io/upload_images/21487050-9c044081abd8af72.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其它

1.inline内联元素，block块级元素

2.常见的display为inline的内联元素有span，display为block的块级元素有div。

3.忠告：不要在display为inline的元素里加上display为block的元素！可以在div里加span。

**4.忠告：永远不要写 width:100%; 容易有bug**

5.H5纠正：

**元素不分内联和块级。所有元素都可以通过css变成内联元素或者块级元素。**

如何确定呢？

**取决于你自己给的样式。**

**display:inline/block/inline-block;**

```
.ib{
  border:1px solid red;
  display:inline;
}
```

6.脱离文档流 position:absolute;

7.文档流 3 种 inline , block 和 inline-block


## **文档流Normal Flow**

1.文档中元素流动的方向

inline从左到右，空间不够时会把自己拆分两半。

block从上到下，每一个都另起一行(不会合并)。

inline-block从左到右,空间不够时不会拆分。

2.宽度

inline宽度为内部所有inline元素的总和，尽量窄，不能用width指定，设了也没用

block默认自动计算宽度，尽量宽，可用width指定。

补充：block宽度默认为 auto 能有多宽有多宽，但不是100%!不要设置宽度为100%容易有bug！可用width指定像素或em。

inline-block宽度尽量窄，可指定width。

3.高度

(1)inline高度由line-height “间接” 确定，跟height/padding无关。

补充：inline可通过 line-height 设置高度。 内联元素不接受width height来设置宽高

间接意思是，除了line-height外，字体也会影响高度。

![image](https://upload-images.jianshu.io/upload_images/21487050-1b0b3efe2423c6c1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(2)block高度是由内部文档流中的所有元素的总和决定的(脱离文档流的不算进去),可以设置height

(3)inline-block高度跟block一样。

**overflow溢出**

当内容的宽/高大于容器时，会溢出

可用overflow来设置是否显示滚动条 overflow:auto自动

注意：如果有滚动条那么我的内联元素默认只在第一频显示。

**脱离文档流**

哪些元素脱离文档流?

float:left;

position:absolute/fixed;

## **盒模型**

1.盒模型有4个概念:margin外边距 border边框 padding内边距 content内容区；

2.两种盒模型：content box内容盒模型(width只包含content)

border box边框盒模型(border padding 和content)

3.box-sizing:border-box; //选择盒模型(边框盒模型/内容盒模型)

利用开发者工具仔细观察2种的不同。

![image](https://upload-images.jianshu.io/upload_images/21487050-e04969cf62faafe0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
代码

.content-box {
  margin: 25px; /*外边距*/
  border: 5px solid rgb(17, 0, 255);
  padding: 15px; /*内边距*/
  box-sizing: content-box;
  width: 100px;
}
.border-box {
  margin: 25px;
  border: 5px solid rgb(17, 0, 255);
  padding: 15px;
  box-sizing: border-box;
  width: 100px;
}
```

> **总结**
> 
> border box
> 
> width = contect + padding + border
> 
> content box
> 
> width = content

## **margin合并**

哪些情况会合并

**1.兄弟margin合并(符合预期的)**

2个孩子的上下margin会合并！

**2.父子margin合并**

**例一：兄弟的上下margin会合并**

![image](https://upload-images.jianshu.io/upload_images/21487050-2598783481154641.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

强制取消margin合并可以吗？可以，但不建议用

```
width:100%;
display:inline-block;
```

**例二:父子margin合并(上下会，左右不会重叠)**

第1个子元素的上边距会与父母的上边距重叠，最后一个子元素的下边距会与父母的下边距重叠。

![image](https://upload-images.jianshu.io/upload_images/21487050-ab42bc57951215bb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## **如何取消父子margin合并？**

1.父元素里加border

```
  border-top:10px solid green;
```

2.父元素里加padding

```
  padding-top:1px;
```

3.父元素里加overflow

```
  overflow:hidden;
```

## **基本单位**

1.长度单位

1’px像素

2’em 任意浏览器默认字体高都是16px，所有未经调整的浏览器都符合1em=16px

相当于自身font-size的倍数.

例子：若指定font-size:20px; width:3em; 那么它实际宽度=20*3=60px

3’百分数

4’整数

5’rem

6’vm和vh

hsl(180,50%,50%) 色相，饱和度,亮度
