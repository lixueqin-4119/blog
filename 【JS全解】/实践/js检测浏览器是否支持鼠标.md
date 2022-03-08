> 怎么检测浏览器是否支持悬停事件，如 onmouseover/onmouseout 或者CSS中的 :hover 伪标签？
> 有时候我们需要代码兼容手机，手机上是触屏，没有鼠标事件。有没有方法检测浏览器是否支持鼠标？

方法一
```
let isTouchDevice='ontouchstart' in document.documentElement;
console.log(isTouchDevice)
```
方法二
```
     try {
            document.createEvent("TouchEvent");
            alert(true);  
        } catch (e) {
            alert(false);
        }
```


兼容手机
```
let isTouchDevice = 'ontouchstart' in document.documentElement;
if (isTouchDevice) {
   //触屏手机
} else {
   //PC
}
```
