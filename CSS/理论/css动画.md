 **将div左右移动的例子**

**原理**

每过段时间(用setInterval做到)

将div移动一小段距离

直到移动到目标点

**注意性能**

绿色表示重新绘制(repaint)了

css渲染过程依次包含布局，绘制，合成

其中布局和绘制有可能被省略

![image](https://upload-images.jianshu.io/upload_images/21487050-50dae1e1d471c5de.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**第2种方式**

前端高手不用left做动画，用transform(变形)，性能会好很多

**原理**

transform:translateX(0=>300px)

直接修改会被合成，需要等一会修改

transition过渡属性可以自动脑补中间帧

**注意性能**

并没有repaint重新绘制

比改left性能好

使用transform让浏览器渲染次数变少了，性能好

### 浏览器渲染原理

查看浏览器渲染

开发者工具，选中任意一个tab,按esc键,勾选Paint flashing。

刷新页面，绿色出现的次数，就是浏览器渲染的次数

**浏览器渲染过程**

根据HTML构建HTML树(DOM)

根据CSS构建CSS树(CSSOM)

将两棵树合并成一颗渲染树

Layout布局(文档流，盒模型，计算大小和位置)

Paint绘制(把边框颜色，文字颜色，阴影等画出来)

Compose合成(根据层叠关系展示画面)
<br/>

**如何更新样式**

一般用JS来更新样式

```
比如div.style.background='red'
比如div.style.display='none'
比如div.classList.add('red') 
```

加类比加样式更好！高手都是加类更快，新手才加style。工作中也是直接加类

比如div.remove()直接删掉节点

**这些方法有什么不同吗？**

用js更新样式有3种不同的渲染更新方式

![image](https://upload-images.jianshu.io/upload_images/21487050-11372ba624143ed6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第一种，全走

div.remove()会触发当前消失，其它元素relayout

第二种，跳过layout

改变背景颜色，直接repaint+composite

第三种，跳过layout和paint

改变transform，只需composite

<br/>

**不同属性有不同的渲染流程，怎么知道每个属性触发什么流程？**

[CSS Triggers](https://link.zhihu.com/?target=https%3A//csstriggers.com/)

### [浏览器渲染性能优化](https://link.zhihu.com/?target=https%3A//developers.google.com/web/fundamentals/performance/rendering)

> js/css/样式/Layout布局/Paint绘制/Composited合成

> 5个过程每一个都可以被优化

**1.优化JavaScript执行(优化js)**

TL;DR

* 对于动画效果的实现，避免使用 setTimeout 或 setInterval，请使用 requestAnimationFrame。

* 将长时间运行的 JavaScript 从主线程移到 Web Worker。

* 使用微任务来执行对多个帧的 DOM 更改。

* 使用 Chrome DevTools 的 Timeline 和 JavaScript 分析器来评估 JavaScript 的影响。

**2.缩小样式计算的范围并降低其复杂性(优化style计算范围)**

TL;DR

* 降低选择器的复杂性；使用以类为中心的方法，例如 BEM。

* 减少必须计算其样式的元素数量。

**3.避免大型、复杂的布局和布局抖动(优化布局layout)**

TL;DR

* 布局的作用范围一般为整个文档。

* DOM 元素的数量将影响性能；应尽可能避免触发布局。

* 评估布局模型的性能；新版 Flexbox 一般比旧版 Flexbox 或基于浮动的布局模型更快。

* 避免强制同步布局和布局抖动；先读取样式值，然后进行样式更改。

**4.简化绘制的复杂度、减小绘制区域(优化绘制paint)**

TL;DR

* 除 transform 或 opacity 属性之外，更改任何属性始终都会触发绘制。

* 绘制通常是像素管道中开销最大的部分；应尽可能避免绘制。

* 通过层的提升和动画的编排来减少绘制区域。

* 使用 Chrome DevTools 绘制分析器来评估绘制的复杂性和开销；应尽可能降低复杂性并减少开销。

**5.坚持仅合成器的属性和管理层计数(优化合成composite)**

TL;DR

* 坚持使用 transform 和 opacity 属性更改来实现动画。

* 使用 will-change 或 translateZ 提升移动的元素。

* 避免过度使用提升规则；各层都需要内存和管理开销。

### transform(变形)

**4个常用功能**

```
位移 transform:translate
缩放 transform:scale 放大/缩小
旋转 transform:rotate
倾斜 transform:skew
```

**经验**

一般都需要配合transition过渡 代码:transition:all 1s;

inline元素不支持transform,需要先变成block

**一.transform:translate位移(常用)**

常用写法

```
translate(<length-percentage>,<length-percentage>?)
translateX(<length-percentage>)
translateY(<length-percentage>)
垂直于视点translateZ(<length>)且父容器 perspective
translate3d(x,y,z)
```

**经验**

1.translate(-50%,-50%)可做绝对定位元素的居中

可用于场景:loading加载中...

不支持IE 其它都支持

2.要学会看懂MDN的语法示例

![image](https://upload-images.jianshu.io/upload_images/21487050-d047d01e200c1902.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

？表示该值可省略

长度length 百分数percentage 角度angle(例:360deg)

**二.transform:scale 缩放**

常用写法

```
scaleX(<number>)需要配合transition:all 1s;过渡
scaleY(<number>)
scale(<number>,<number>?)
```

**经验**

用的较少，因为容易出现模糊

```
代码：transition:all 1s;过渡
     transform: scale(1.5);
```

**三.transform:rotate旋转**

常用写法

```
rotate([<angle>|<zero>])
rotateX([<angle>|<zero>])
rotateY([<angle>|<zero>])
rotateZ([<angle>|<zero>])
rotate3d太复杂，无法用语言表述
```

**经验**

1.一般用于360度旋转制作loading或者按钮的交互

2.用到时再搜索rotate mdn看文档

```
代码 transform: rotate(45deg);
```

**四.transform:skew倾斜**

**常用写法**

```
skewX([<angle>|<zero>])
skewY([<angle>|<zero>])
skew([<angle>|<zero>],[<angle>|<zero>]?)
```

**经验**

用的较少

用时再搜skew mdn

transform多重效果

**组合使用**

```
transform:scale(0.5)translate(-100%,-100%);
transform:none;取消所有
```

### transition过渡

> 其它
> 
> 1.#demo.end 注意不能有空格，表示demo拥有end的时候(#demo .end表示demo后代里面的end)
> 
> 意思是用css给demo增加一个class="end"
> 
> 注意要配合js
> 
> button.onclick=()=>{
> 
> demo.classList.add('start')
> 
> }

**作用**

补充中间帧

**语法**

1.transition:属性名 时长 过渡方式 延迟 //4个值

```
  transition:left 200ms linear
```

2.可以用逗号分隔两个不同属性

```
  transition:left 200ms,top 400ms
```

3.可以用all代表所有属性

```
  transition:all 200ms ease-in-out 300ms;//再延迟300ms淡入淡出
```

4.过渡方式有:

```
  linear|ease|ease-in|ease-out|ease-in-out|cubic-bezier|step-start|step-end|steps
```

**变态**

注意，并不是所有属性都能过渡

display:none;=>block没法过渡

一般改成visibility:hidden=>visible

![image](https://upload-images.jianshu.io/upload_images/21487050-371614968d4b7e44.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### animation动画


> 过渡必须要有起始
> 
> 一般只有一次动画,比如hover和非hover状态的过渡
> 
> 接下来介绍两次动画animation(更高级的过渡效果)

**语法**

```
animation:时长|过渡方式|延迟|次数|方向|填充模式|是否暂停|动画名;
```

时长:1s

过渡方式:跟transition一样，如linear

次数：3或者infinite

方向:reverse|alternate|alternate-reverse

填充模式:forwards|none|backwards|both

是否暂停:paused|running

**以上所有属性都有对应的单独属性(都可单独设置)**

![image](https://upload-images.jianshu.io/upload_images/21487050-50dbf144d566def4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**两种办法**

**1'使用两次transform也可实现两次动画**

.a===transform===>.b

.b===transform===>.c

如何知道到了中间点呢？

用setTimeout或者监听transitionend事件

![image](https://upload-images.jianshu.io/upload_images/21487050-8b35b601b64018fd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2'使用animation**

声明关键帧@keyframes

添加动画

![image](https://upload-images.jianshu.io/upload_images/21487050-6965ab1db8bb5bbe.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**@keyframes完整语法**

**标准写法**

一种写法是from to

另一种写法是百分数(更广泛)

```
 @keyframes slidein {
  from {
    transform: translateX(0%); 
  }
  to {
    transform: translateX(100%);
  }
}

@keyframes identifier {
  0% { top: 0; left: 0; }
  30% { top: 50px; }
  68%, 72% { left: 50px; }
  100% { top: 100px; left: 100%; }
}
``` 


