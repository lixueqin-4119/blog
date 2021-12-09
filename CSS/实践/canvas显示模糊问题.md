canvas显示模糊问题

在使用canvas过程中，发现用css设置canvas的宽高，会使绘制的图像模糊/扭曲。

如下图所示：

![](../../images/1.png)

查看 [文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Basic_usage)后明白:

* canvas默认宽度为300px和高度为150px，也可自定义。
* 该元素可以使用CSS来定义大小，但在绘制时图像会伸缩以适应它的框架尺寸：如果CSS的尺寸与初始画布的 "比例" 不一致，它会出现扭曲。
* 如果你绘制出来的图像是扭曲的, 尝试用width和height属性为canvas明确规定宽高，而不是使用CSS。

解决方法

思路1:
一开始就写死宽高，如果没办法确定的话，可以用js获取屏幕宽高。

```
var canvas=document.getElementById("canvas");

canvas.width=document.documentElement.clientWidth
canvas.height=document.documentElement.clientHeight
```


思路2:
通过检测设备像素比，绘制对应倍数比例的 canvas 元素。

代码如下：

```
function createHDCanvas (w=300,h=150) {
  var ratio = window.devicePixelRatio || 1;
  var canvas = document.createElement('canvas');
  canvas.width = w * ratio;      // 实际渲染像素
  canvas.height = h * ratio;     // 实际渲染像素
  canvas.style.width = `${w}px`; // 控制显示大小
  canvas.style.height = `${h}px`;// 控制显示大小
  canvas.getContext('2d').setTransform(ratio, 0, 0, ratio, 0, 0);
  return canvas;
}
```