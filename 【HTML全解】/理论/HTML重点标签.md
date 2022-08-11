推荐2个打开超链接,预览html的工具

**1’http-server**

```js
安装 yarn global add http-server
使用 http-server . -c-1 //hs -c-1
```

然后在浏览器的地址栏中录入index的路径

补充:手机电脑同一wifi下，手机也可以访问index页面

**2’parcel**

```js
安装 yarn global add parcel
使用 parcel index.html
```
注意parcel有个bug,不引入js的的话，parcel是不会帮你自动打包的。


## 一.a标签

> 属性
> 
> href
> 
> target 指定在哪个窗口打开这个超链接
> 
> download 下载网页，很多浏览器不支持没用
> 
> rel=noopener 为了防止一个bug
> 
> 作用
> 
> 跳转外部页面/跳转内部锚点/跳转到邮箱或电话

**1.属性href取值有8种**

![image](https://upload-images.jianshu.io/upload_images/21487050-22f827bcc1479ec8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解析:

(1)[//google.com](https://link.zhihu.com/?target=//google.com) 无协议网址

最高级。会自动选择使用http/https

(2)路径

绝对路径href="/a/b/c" 以及 相对路径href="a/b/c"

index.html 以及./index.html

**注意不要通过双击打开链接**，不然地址按绝对路径方式出错。因为链接的路径是file。

vs code里的路径是基于文件的。浏览器里的路径是http服务的根目录。这2种链接地址不一样

为什么用浏览器打开地址不出错？因为它会以当前文件为根目录！

**(3)伪协议写法 就是为了执行js用的。**
```js
<a href="javascript:alert(1);">javascript伪协议</a> //基本不用了
```
现在只用下面这个

需求:什么都不做的a标签。

```js
<a href="javascript:;">空的伪协议</a> 
```

(4)跳转到指定标签。可以加id ,例子：href="#xxx"

(5)发邮件 <a [href="mailto:xxx@163.com](mailto:href=%22mailto:xxx@163.com)">发邮件给自己</>

打电话 <a href="tel:1232222222">打电话给我</a>

**2.target的取值**

**内置名字**

_blank 在空白页面打开

_self 默认值，在当前页面打开

_top 在顶级窗口打开

_parent 在当前链接所在的ifram的上一层打开

**程序员命名**

1’window的name

target="xxx",xxx就是window的name值。作用：重复利用同一个窗口展示不同页面

2’iframe的name

## 二.iframe标签

内嵌窗口

已经很少用了

## 三.table标签
相关的标签

> table
> 
> thead 头部
> 
> tbody
> 
> tfoot 尾部
> 
> tr 行row，水平方向
> 
> th 表头
> 
> td 数据
> 
> 相关的样式
> 
> table-layout
> 
> border-collapse 边框是否合并(collapse)
> 
> border-spacing 表格间隙(0)

```js
<table> 
  <thead></thead>
  <tbody></tbody>
  <tfoot></tfoot>
</table>
```
```js
    <table>
        <thead>
         <tr>
           <th></th>
           <th>小白</th>
           <th>小李</th>
           <th>小红</th>
         </tr>
        </thead>
        <tbody>
         <tr>
          <th>数学</th>
          <td>66</td>
          <td>78</td>
          <td>80</td>
         </tr>
         <tr>
          <th>语文</th>
          <td>66</td>
          <td>78</td>
          <td>80</td>
         </tr>
        <tr>
          <th>外语</th>
          <td>90</td>
          <td>78</td>
          <td>80</td>
        </tr>
        </tbody>
        <tfoot>
         <tr>
          <th>总分</th>
          <td>222</td>
          <td>234</td>
          <td>240</td>
         </tr>
        </tfoot>
      </table>  
```

## 四.img标签

![image](https://pic1.zhimg.com/80/v2-95923d5c754dcd47b61f192a6e4be2ec_1440w.jpg)

解析：

1.width height同时写会变形(**不要同时设置**)，单独写另一个会自适应。

**2.onload/onerror事件 (onload成功 onerror失败)**

**监听图片是否加载成功，加载成功则调用onload否则加载onerror.**

用户体验的优化，图片加载失败时用404图片保底。

![image](https://pic2.zhimg.com/80/v2-2525e5790c1b036dfe88b8f23b522ee9_1440w.jpg
)

**3.手机页面图片响应式**

max-width:100%;

## 五.form标签

> 作用
> 
> 发get或post请求，然后刷新页面
> 
> 属性
> 
> action/autocomplete/method/target
> 
> 事件
> 
> onsubmit

解析

1.action相当于img的source，action作用是请求哪个页面。

2.method是控制用post还是get请求。

3.autocomplete是否自动填充on/off(对用户来说非常方便的一个特性)

[4.target](https://link.zhihu.com/?target=http%3A//4.target) 刷新页面。我要提交到哪个页面，哪个页面应该刷新

5.form必须要有type="submit"的按钮，表单才能提交，如果不写则默认为submit。

button的type属性若不写则默认为submit。

```js
<form action="/xxx" method="POST" autocomplete="on" target="_blank">
   <input name="username" type="text" />
   <input type="submit" value="提交" />
   <button>提交</button>
</form>
```

**input的submit与button的submit有什么区别？**
```js
<input type="submit" value="提交"/>

<button type="submit">提交</button>
```
答：input里面不支持其它标签只能有纯文本，button里面可以有任何东西。

总结，input不支持其它标签，button支持。

## 六.input标签

![image](https://upload-images.jianshu.io/upload_images/21487050-7879d7c52f8660d6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解析：

1.input的type的常用值

text/color/password/radio/checkbox/file(单个文件)/hidden(给js自动填写id或者字符串之类的东西)

```js
<input type="file" multiple> //multiple可选择多个文件
```

2.性别用gender

```js
<input name="gender" type="radio" />男
<input name="gender" type="radio" />女
```

3.html5自带验证器(例如：required必须要填)

**其它输入标签**

**1.多行文本textarea**

默认可以拖动，如何取消？**style="resize:none;"**

**2.选择select+option**

```js
<select>
<option value="">请选择</option>
<option value="1">星期一</option>
<option value="2">星期二</option>
<option value="3">星期三</option>
</select>
```

**input事件**

> onchange 当用户输入改变时触发
> 
> onfocus 当用户把鼠标集中在它身上时触发
> 
> onblur 当鼠标从这里出来时触发

注意事项

1.一般不监听input的click事件，一般都是监听用户focus事件。

2.form里面的input要有name

3.form里要放一个type=submit才能触发submit
