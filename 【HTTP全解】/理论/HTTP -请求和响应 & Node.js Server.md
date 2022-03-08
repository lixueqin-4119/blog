> 主要内容 
> 
> 用代码实现http(演示Node.js服务) 
> 
> 前置条件:安装Node.js 14

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-563a86d10452674d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

后端开发的代码在服务端运行，前端开发的代码在客户端运行。

请求指客户端里的浏览器发一个东西，发到服务器。

响应指服务器拿到请求后，在同一个端口返回一个响应。


**如何发请求？**

**1.用chrome地址栏**

发请求: 浏览器输入http://baidu.com

查看响应: 打开开发者工具->选择network->刷新->选中第一个请求，选择Response

**2.用curl命令发请求**

(后面会单独讲解)

```
 curl -v https://www.baidu.com
```

**概念**

帮你发请求的工具叫做 **「用户代理」**

用chrome发送请求那chrome就是代理代理，用curl,curl就是用户代理。

## 实现http-server

> 实现http-server，根据路径返回字符串
> 
>  Node.js有一个http模块可以做到

[源代码](https://github.com/lixueqin-4119/nodejs-test)

关键代码如下图所示：
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-6e28ed3d4b09c935?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注意事项**

这些代码就是服务器代码，一般放在服务器上

request是请求对象,response是响应对象

path是不带查询参数的路径/x

query是查询参数的对象形式{a:'1'}

quertString是查询参数的字符串形式?a=1

pathWithQuery是带查询参数的路径，一般不用



**代码逻辑**

1.每次请求,都会执行console.log

2.用if else判断路径，并返回响应

如果是已知路径，一律返回200

如果是未知路径，一律返回404

3.response.write()填写响应的内容

response.end()将响应发给客户端用户,只要你end就会发送给浏览器

4.乱码处理response.setHeader('Content-Type', 'text/html;charset=utf-8')

5.每次修改记得重启

control+c中断

6.后缀是垃圾

不需要加后缀(/x.css)

Content-Type才是决定文件类型的关键

## 体系化学习

系统学习**概念**(HTTP)

**1.必须学会什么？**

基础概念(有哪些是避必会的)

如何调试(用的是Node.js,可以用log/debugger)

在哪查资料(用的是Node.js,看Node.js文档)

标准制定者是谁(HTTP规范文档:RFC 2616等)

**2.如何学 -CRM**

copy 抄

run 放在自己的机器上运行成功

modify 改变，加入自己的想法，然后重新运行

## HTTP

基础概念

**一.请求**

1.请求(所有请求都要按照如下格式)

**(1)请求动词 路径加查询参数 协议名/版本**

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-250856336d6c5181?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

必须按照HTTP规范来做，否则服务器就理解不了你想干什么

**(2)Host:域名或IP**

**(3)Accept:text/html**

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-087842ea0d40c2d0?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Accept内容,表示我想接收什么

HTTP告诉服务器想接收的内容:首选html,没有html有xml也行，没有xml有avif也行...

**(4)Content-Type:请求体的格式**

我在请求的时候可以上传一些内容post。上传的内容放到请求体里。

**(5)回车**

**(6)请求体(也就是上传内容)**



**2.细节**

**(1)三部分:请求行，请求头，请求体**

请求行指“请求动词 路径加查询参数 协议名/版本”

请求头指 “Host Accept Content-Type” 三部分

**(2)请求动词包括：GET用来获取内容 / POST上传内容 /PUT/PATCH/DELETE等**

**(3)请求体在GET请求中一般为空**

(4)文档位于RFC 2616第五章

(5)大小写不敏感，但js大小写敏感



**二.响应**

1.响应

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-3299ebbe0e2a76bb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**(1)协议名/版本 状态码 状态字符串**

**(2)Content-Type:响应体的格式**

标注了响应体Response的格式

**(3)回车**

**(4)响应体(也就是下载内容)**

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-4f68eab1cc4e02e6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.细节**

**(1)三部分:状态行，响应头，响应体**

**状态行指“状态行，响应头，响应体”**

**(2)面试题:常见状态码**

(3)文档位于RFC 2616 第六章

## 用curl命令构造请求

> curl 就是个浏览器。
> 
> curl 可以完成chrome的基本所有功能，只不过它不能可视化，因为它没有可视化的界面。 

**1.看过程curl -v**

```
node server.js 8888  server要先开启
curl -v http://localhost:8888/
```

**2.设置请求动词-X POST**

修改请求为POST,注意大小写

```
curl -v -X POST http://localhost:8888/
```

**3.设置路径和查询参数**

直接在url后面加

```
curl -v -X POST http://localhost:8888/xxx?wd=hi
```

**补充：**

设置锚点

```
 curl -v -X POST http://localhost:8888/xxx?wd=hi#5
```

**因为锚点是不会发送到服务器的，所以根本不会展示出来**

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-bca58e54e4ab05d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**4.设置请求头**

-H 'Name:Value'或 --header 'Name:Value'

```
curl -v -X POST -H 'Accept:text/html' http://localhost:8888/xxx?wd=hi
```

意思是，我要接收的内容的文档类型是html

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-1626d18e442fa9b5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.设置请求体

```
-d'内容'或--data'内容'
```

我要上传的“内容”是plain纯文本

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-2c606fc6eb9de196?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)6.curl也可以用来下载图片。

```
cd ~/Desktop/
curl 图片地址 > 图片 命名
```

## 用Node.js读取请求

构造请求后怎么用Node.js读取请求？

**1.读取请求动词**

```
  request.method
```

**2.读取路径**

```
request.url路径，带查询参数
```

path纯路径，不带查询参数

query只有查询参数

**3.读取请求头**

```
request.headers['Accept']
```

## 用Node.js设置响应

**1.设置响应状态码**

```
response.statusCode=201
```

**2.设置响应头**

```
response.setHeader('Content-Type', 'text/html;charset=utf-8')
```

**3.设置响应体**

```
response.write(`内容`)
```

可追加内容

