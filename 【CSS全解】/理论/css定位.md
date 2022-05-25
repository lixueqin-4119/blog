> 布局与定位的区别：布局是屏幕平面上的，定位是垂直于屏幕的。

一个div的分层:

自底向上依次为background, border ,块级子元素(div), 浮动元素(float), 内联子元素(文字)

后出来的内联子元素(文字)会盖住先出来的内联子元素(文字)

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-01d98970cdedf61f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 新属性 position


**总结**

position属性值:

static默认值，待在文档流里

relative相对定位，升起来，但不脱离文档流

absolute绝对定位，定位基准是祖先里的非static

fixed固定定位，定位基准是viewport(有诈)

sticky粘滞定位，不好描述直接举例

**经验**

如果你写了absolute，一般都得补一个relative

如果你写了absolute或fixed，一定要补top和left

sticky兼容性很差

## position:relative;

1.使用场景

(1)用于做位移(很少用)

(2)用于给absolute元素做爸爸

用法1:占的位置不变，只是你显示的地方跟之前有所偏移(很少用)


![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-6de2a06070a04985.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

用法2:跟absolute结合使用,给absolute元素做爸爸

给爸爸加上position:relative;儿子上加position:absolute;

匹配后close就会以container为依据去定位。

2.relative配合z-index

z-index:auto默认值，不创建新重叠上下文

z-index:0/1/2

z-index:-1/-2


z-index与relative配合可以让后出来的div被覆盖

只要比其他的元素多一个数，就会压制的元素。这就是相对定位互相压制的办法

默认每个元素的z-index是auto，auto计算出来值是0。但是不能写0，auto是auto 0是0。

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-ba8e210458a3f913?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## position:absolute;

**1.使用场景**

(1)脱离原来位置另起一层,用绝对定位做关闭按钮(很常用!)

relative跟absolute结合使用

(2)鼠标悬浮才显示"提示内容"的功能

1.文字内容不许换行white-space: nowrap;

2.transform: translateX(-50%);

3.calc() bottom: calc(100% + 10px);计算下100%+10px

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-dcd0f7953438d207?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.配合Z-index**

**3.经验**

1.很多人都以为absolute是相对于relative定位的

错！absolute是相对于祖先元素中最近的一个定位元素 定位的，只要不是static就是定位元素。

2.某些浏览器上如果不写top/left会位置错落(一定要记住)

top/left/buttom/right四个位置至少写两个，不然有得浏览器会乱！

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-499ac748aac9e0bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.善用left:100%;

擅用100%

4.善用left:50%;加负margin

设置居中:

1’left:50%; margin-left:-27px; 这种方法有点弱要自己算

2’left:50%;transform:translateX(-50%);


## position:fixed;

**1.使用场景**

(1)烦人的广告

(2)回到顶部按钮

**2.配合Z-index**

**3.经验**

手机上尽量不要用这个属性，坑很多

不信你搜一下「移动端fixed」

## position:sticky
1.粘滞定位

特别适合做导航标题，下滑滚动页面依然固定在上面不动的标题

2.兼容性不好，要加前缀，代码如下
```
position: -webkit-sticky;
position: sticky;
top: 20px;
```
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-adba4d4c18a396e1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 层叠上下文
1.层叠上下文也叫堆叠上下文。每个层叠上下文就是一个新的小世界(作用域)

2.这个小世界里的z-index与上下文无关

3.处在同一个小世界的z-index才能比较

4.负Z-index有可能逃不出元素的background

那么就是因为这个元素变成了层叠上下文

负Z-index不一定能逃出一个div,除非这个div不是层叠上下文

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-48e70e7b7c1fe170?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

position的四个取值(static除外)可使元素成为定位元素，高于内联子元素，覆盖文字。

只要你的position不是static，你就自动跑到所有元素的最上面

定位元素(Z-index=0)会以文字的上一层为基准，第一层就是0，一直往上。

定位元素(Z-index=-1)会以background的下一层为基准，第一层就是-1，一直往下。

**哪些不正交属性可以创建它？创建层叠上下文**

1.z-index:0;

2.position:fixed/sticky的元素

3.opacity属性值小于1

4.transform属性值不为“none”的元素transform:translateX(-1px);

5.position:absolute/relative且 z-index值不为auto的元素
