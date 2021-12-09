```
BODY对象宽和高度 
document.body.clientWidth    
document.body.clientHeight 

可见区域宽和高度 
document.documentElement.clientWidth 
document.documentElement.clientHeight 
  
网页可见区域宽和高(包含边线的宽和高)
document.body.offsetWidth   
document.body.offsetHeight 
  
网页正文全文宽和高 
document.body.scrollWidth  
document.body.scrollHeight  

网页被卷去的高
document.body.scrollTop  
网页被卷去的左
document.body.scrollLeft  

网页正文部分上
window.screenTop  
网页正文部分左
window.screenLeft  

屏幕分辨率的宽和高   
window.screen.width  
window.screen.height

屏幕可用工作区宽和高 
window.screen.availWidth  
window.screen.availHeight  

  
- - -  - jQuery函数  - - - -

浏览器时下窗口可视区域宽和高
$(window).width() 　       
$(window).height() 　      

浏览器时下窗口文档的宽和高
$(document).width()         
$(document).height()　　  

浏览器时下窗口文档body的宽和高
$(document.body).width()　
$(document.body).height()

浏览器时下窗口文档body的总宽高度(包含margin padding border)
$(document.body).outerWidth(true)
$(document.body).outerHeight(true)   
  
HTML精确定位
scrollWidth 获取对象的滚动宽度   
scrollHeight获取对象的滚动高度
scrollLeft:设置或获取位于对象左边界和窗口中目前可见内容的最左端之间的距离   
scrollTop:设置或获取位于对象最顶端和窗口中可见内容的最顶端之间的距离   

offsetHeight:获取对象相对于版面或由父坐标 offsetParent 属性指定的父坐标的高度   
offsetLeft:获取对象相对于版面或由 offsetParent 属性指定的父坐标的计算左侧位置   
offsetTop:获取对象相对于版面或由 offsetTop 属性指定的父坐标的计算顶端位置   

相对文档的水平和垂直座标
event.clientX    
event.clientY 

相对容器的水平和垂直坐标    
event.offsetX  
event.offsetY   

document.documentElement.scrollTop 垂直方向滚动的值   
event.clientX+document.documentElement.scrollTop 相对文档的水平座标+垂直方向滚动的量
```