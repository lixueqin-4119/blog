> 布局就是把页面分成一块一块,按左中右，上中下等排列

**布局一般分为3类**

1’固定宽度布局，一般宽度为960/1000/1024px

例如淘宝

2’不固定宽度布局，主要靠文档流的原理来布局

一般在手机上用

补充：文档流本来就是自适应的，不需要加额外的样式

3’响应式布局

PC上固定宽度，手机上不固定宽度

也就是一种混合布局

<br/>

**布局的两种思路**

1’从大到小(老手) 先定下大局，然后完善每个部分的小布局

2‘从小到大(新手) 先完成小布局，然后组合成大布局

## css布局

> css布局(div+css布局) 
> 用章节标签代替div(header main section aside footer)

**3种css布局技术**

**1.float布局**

需要兼容IE9及以下(需要PC网页)，必要时采用负margin(常用技巧)

用在IE布局,老浏览器用float

**2.flex布局**

不需要兼容IE(不需要PC网页)，只做手机H5页面,要兼容旧手机浏览器。必要时采用负margin(常用技巧)

只要不需要兼容IE,就可以在所有地方用,新浏览器用flex。

**3.grid布局**

不需要兼容IE(不需要PC网页)，只做手机H5页面,只兼容最新浏览器。

Grid布局功能最强大，尤其适合“**不规则布局**”。

<br/>


## float布局

**float布局两步走**

第1步 在子元素上加float:left 和 width

第2步 在父元素上加.clearfix。clearfix内容如下

```
 .clearfix:after{
  content:'';
  display:block;
  clear:both;
}
```

**想用float布局做个导航栏但hearder高度为0怎么办？**
<br/>


答：用.clearfix。一个块级元素的高度是由它内部文档流元素的总和决定的，header高度是由内部的文档流元素div nav决定的。float可以让元素脱离文档流。header里没有文档流元素，那么header高度就为0
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-bd2f5ee6b26fbb79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<br/>


老手的经验：

1.最后一个不设width(会自适应)，可以加个max-width:100px;

2.如果用了float布局，就不需要再想响应式了。

因为手机上不用IE，float布局是专门为IE准备的，所以不需要做响应式。
<br/>


**3.IE6/7存在双倍margin bug**

在float元素的左边加上margin-left:10px;放在IE6/7上就会变成20px
怎么解决？

1’根据bug修改margin,专门再为IE写一个_margin-left:5px;

正常浏览器识别第1个，IE6/7识别第2个。

2’加一个display:inline-block;

如下图所示：
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-cbb033544319db6b?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
IE6/7已经过时了，现在都IE11了，这些知道就好，工作中很少会遇到。
<br/>


## float 实践

> 顶部条 两栏布局 
> 
> 内容区 三栏布局 
> 
> 导航 四栏布局 
> 
> 产品展示区 平均布局

一.导航 四栏布局

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-e190e7e985be31c0?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
1.ul,ol{list-style:none;}

2.padding:4px 0.5em;

上下高度要确定，左右是当前字体的0.3

3.display:inline-block; //尽量把自己压窄


二.顶部条 两栏布局
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-2b3fa02852e1a435?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
1.max-width:100%; 图片最大不能超过一行

2.消除img下多余的背景色vertical-align:top/middle;

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-0691dcf0b778329e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.两边对齐float:left


4.两个居中对其

量一下，加个margin-top

1'img和nav高度不一致怎么对齐？(26,32)

(26+32)/2=29

26-29=-3

margin-top:3px;

2'如果img nav两个中有一个是奇数(26,31)，那就调成偶数好算

把nav调成偶数,调整文字高度line-height:32px;

这时候要再看下高度是多少(26,42)

(26+42)/2=34

26-34=-8

margin-top:8px;

三.内容区 三栏布局
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-ce78fb9a76d310ce?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

四.产品展示区 平均布局

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-146ef9932e7be72e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

例一.假设要放4张图片,公式4x+3y=800,算出xy值

x=191 y=12

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-7f7e8526438a95c2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**由于最后一个还有一个margin-right:12 结果导致空间不够自动折行，怎么办？**

**思路：借助div增加负margin(常用技巧)**

做法：在image外再加个div把image包起来，然后设置该div的margin-right:-12px; 值就是每个y的值,这里是12

> 老司机经验
> 
> 1.加上头尾，即可满足所有PC页面需求
> 
> 2.手机页面傻子才用float
> 
> 3.float要程序员自己计算宽度，不灵活
> 
> 4.float用来应付IE足矣

## Flex布局
容器container & 子元素items

**一.flex container的属性和样式**
**1.让一个元素变成flex容器**

```
.container{
  display:flex|inline-flex;
}
```

2.flex中的流动方向是由container自己控制的(不同于float)

改变items流动方向(主轴)

```
.container{
 flex-direction:row横,默认|row-reverse|column|column-reverse
}
```

3.flex弹性盒，控制是否换行flex-wrap
空间不够时会自动压缩自己，不会折行(不同于文档流空间不够会自动折行)

```
.container{
flex-wrap:wrap|nowrap默认,不折行
}
```

4.主轴和次轴的对齐方式

(1)主轴(左右)的对齐方式 (重要)

默认主轴是横轴 左右,除非你改变了flex-direction方向

```
.container{
  justify-content:center居中|space-between;2栏布局
  }
```

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-9e1a27add2a12b9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(2)次轴上下的对齐方式

默认次轴是纵轴 上下,

```
.container{
 align-items:stretch|center|flex-start|flex-end;
}
```

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-23b3d24f95d1b524?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.多行内容的分布(很少用)


```
align-content:flex-start|flex-end|center|stretch|..
```

**二.flex item的属性和样式**

1.item上加order改变显示顺序

```
.item:first-child {
  order: 5;//改为几就会在第几个位置上出现
}
```

**order默认0，取值范围包括负数/0/正整数**

2.item上加flex-grow,用来分配多余的空间，默认值0

代码1 平均分配

```
.item {
flex-grow: 1;
}
```

代码2 按比例分配

```
.item:first-child {
   flex-grow: 1;
}
.item:nth-child(2) {
  flex-grow: 2;
}
.item:nth-child(3) {
  flex-grow: 1;
```

**导航分配技巧**

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-8c8901c874c091b4?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3.item上加flex-shrink:0不压缩，默认1

原始宽度不够时压缩(空间不够变小时)，如何分配压缩,让谁缩小![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-8b4b6fc5541b7cde?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4.flex-basis控制基准宽度 默认auto，可用width代替(不重要)

缩写flex:flex-grow flex-shrink flex-basis

如果写shrink必须加宽度。(要么写1个要么写3个)

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-3e4b42357a4e1ce7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.让某一个item特例独行,align-self:flex-end(很少用)

**重点总结**

1.display:flex;

2.flex-direction:row/column;

3.flex-wrap:wrap;

4.主轴(左右)的对齐方式justify-content:center/space-between2栏布局

5.align-items:center 纵轴(上下)居中
## flex实践

> 用flex做两栏布局 
> 用flex做三栏布局 
> 用flex做四栏布局 
> 用flex做平均布局 
> 用flex组合使用，做更复杂的布局

老手的经验
1.css最忌讳把宽高写死，永远不要把width和height写死，除非特殊说明

尽量用min-width/max-width/min-height/max-height

2.什么叫写死
```
写死  width:100px;
不写死 width:50%;
      max-width:100px;
      width;30vw; //vw意思是屏幕宽度的1%，这里指屏幕宽度的30%
      min-width:80%;
```
**特点：不使用px或者 加min max前缀**

电脑上width一般都写死的，一般800px，手机不写死

3.flex可以基本满足所有需求,不管是手机还是PC

**4.flex 和 margin-xxx:auto 配合有意外的效果**

例子：实现左右布局

父元素加justify-content:space-between;

或者在ul里加margin-left:auto;(更推荐这种)

## flex实践
**一.用flex做 顶部条 两栏布局**

1.横向布局display:flex;

只要在父元素上加display:flex;就能让子元素变成一行

2.左右布局

父元素里加justify-content:space-between;

或者在后面的元素ul里加margin-left:auto;(推荐这种方法!)

3.line-height与height的区别

用于设置多行元素的空间量，如多行文本的间距,ul>li。



**二.用flex做 内容区 三栏布局**

1.自己填充flex-grow:1;

用于设置main的width，让它自己填充，不写死

```
.content{min-width:800px}
.content>aside{flex-grow:1;}
```

2.margin-left:auto;

margin-right:auto;

代替margin:0 auto;

**三.用flex做产品展示区 平均布局**

```
.x {
  display: flex;
  flex-wrap: wrap; /*换行*/
  margin-right: -12px;
}
```

## Grid布局

> Grid布局功能最强大，尤其适合“不规则布局”。

一维布局用Flex(必须选择横着/竖着排列),二维布局用Grid(不需要选择两个方向都支持)

**一.Grid跟flex差不多，也分container和items**

1.让一个元素变成Grid容器

```
.container{
  display:grid|inline-grid;
}
```


2.设置行和列(虚拟的，线不存在，只是设置了范围)

column主轴左右 row次轴上下

```
.container{   
  grid-template-columns:40px 50px auto 50px 40px; 3行5列
  grid-template-rows:25% 100px auto;
}
```

(2)给每条线取名字(太复杂一般不取，用默认的)

Grid优点给每根线都取了个名字,默认是从1到4(grid-template-columns)，也可自定义

```
.container{
  grid-template-columns:[first]40px [line2]50px [line3]auto 
     [col4-start]50px [five]40px [end];
  grid-template-rows:[row1-start]25% [row1-end]100px [third-line]auto [last-line];
}
```

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-37d395e7bd1063b1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
(3)item设置范围

```
  grid-row-start: 1;
  grid-row-end: 2;
  grid-column-start: 1;
  grid-column-end: 6;
```

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-c56ce1a4f396c97a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(4)还可以使用fr(fr主要用于平均布局)

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-7d85a19d7f18fcc2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
.container{   
  grid-template-columns:1fr 1fr 1fr 1fr; 2行4列
  grid-template-rows:1fr 1fr;
}
```

(5)空隙gap

```
1.设置总空隙 gap:12px;
2.左右 column-gap:10px;
3.上下 row-gap:10px;
```

**3.grid-template-areas**

第1步，加grid-template-areas:" ";

第2步，把html放到表格里grid-area: ;

4.预留一个空位置
一般用.表示这里为空(很少用)

## Grid实践

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-630851b14e0bb140?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1.grid repeat**缩写方式**
```
 grid-template-rows:240px 120px 120px 120px 120px; 
  缩写grid-template-rows:240 repeat(4,120px);
```

**2.尽力往里缩**

```
 display:inline-grid;
```
因为内联元素会尽力往里缩,去掉额外的空间

3.rows行 次轴 垂直方向,columns 列 主轴 水平方向


## 常用Grid布局
**grid-template-areas**

第1步加grid-template-areas:" ";

第2步把html放到表格里grid-area: ;

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-01ce34b27636824e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
