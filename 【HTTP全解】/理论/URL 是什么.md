主要内容：什么是URL

## IP(协议)
IP协议用来表示每个设备的地址。

IP是Internet Protocol(网络之间互连的协议)的缩写，是TCP/IP体系中的网络层协议。

**IP主要约定了两件事：**

1.如何定位一台设备

  有IP就可以定位一台设备

2.如何封装数据报文，以跟其它设备交流

I**P分内网IP和外网IP**

外网就是路由器之外，内网就是路由器包括的有线和无线

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-5da394e260ac4b9c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**如何获取外网IP**

1.从电信租用宽带，一年一千多

2.买个猫(调制解调器)，光猫就是把光信号变成电信号。把光信号变成路由器能理解的网络信号

3.买个路由器，手机和电脑分别连接路由器广播出来的有线WIFI

只要路由器通过光猫连上电信的服务器，那么路由器就会有个「外网IP」,比如「14.17.32.211」，

这就是你在互联网中的地址。

4.但是如果你重启路由器，那么你很有可能被重新分配一个「外网IP」。也就是说你的路由器没有「固定的外网IP」

**查看外网IP**

1’终端命令

```
ipconfig
```

2’网站 ip138.com 
不能告诉别人，别人会通过地址找到你

你的路由器的外网IP如果是14.17.32.211那么你的手机和电脑的IP又是什么呢？

是「内网」

**内网IP**

1.路由器会在你家创建个内网，内网中的设备使用内网IP，一般这个IP格式是192.168.xxx.xxx

2.一般路由器会给自己分配个好记的内网IP，如192.168.1.1

3.然后路由会再给每一个内网中的设备分配一个不同的内网IP，比如电脑是192.168.1.2，手机是192.169.1.3，以此类推

**路由器的功能**

路由器有两个IP，一个外网IP和一个内网IP

内网中的设备可以互相访问，但是不能直接访问外网

内网设备想要访问外网必须经过路由器中转

外网中的设备可以互相访问但无法访问你的内网

外网设备想要把内容送到内网也必须通过路由器

也就是说内网和外网就像两个隔绝的空间，无法互通，唯一的联通点就是路由器

所以路由器有时候也被叫做「网关」

**几个特殊的IP**

1.127.0.0.1 表示自己

2.可以通过hosts将localhost指定为自己

  127.0.0.1:8080 = localhost:8080

**hosts可使任意字符串变成一个对应的ip**

hosts文件在哪？

**MAC修改hosts文件**

hosts文件位于 /private/etc/hosts，可以通过下面的方法修改。

1.终端命令

```
sudo vi /etc/hosts 
```

  回车后再输入密码，再回车就可以打开我们的hosts文件了。

2.输入 i 就可以编辑了，此时状态为insert

加上要配置的 ip+空格+域名 

3.编辑好后，按 esc键退出,然后输入:wq 

## 端口port


> 一台机器可以提供很多服务
> 
>  每个服务一个号码，这个号码就叫端口号port

**一.一台机器可以提供不同服务**

1.要提供HTTP服务最好使用80端口

  超文本传输协议- 用于传输网页

2.要提供HTTPS服务最好使用443端口

  超文本传输安全协议或QUIC

3.要提供FTP服务最好使用21端口 

  文件传输协议 - 控制端口

一共有65535个端口(基本够用)

**怎么知道应该使用什么端口？**

[维基百科](https://zh.wikipedia.org/wiki/TCP/UDP%E7%AB%AF%E5%8F%A3%E5%88%97%E8%A1%A8#0%E5%88%B01023%E5%8F%B7%E7%AB%AF%E5%8F%A3)

**二.端口使用还有什么规则？**

规则
**0到1023号端口是留给系统使用的**

你只有拥有了管理员权限后，才能使用这1024个端口

不推荐你使用0到1023号端口

一般用8080做开发，等你真正的部署你的服务器之后，才能用80端口

其它端口可以给普通用户使用，随便用，超过1024就可以了

**在本机的1025上起个服务**

终端命令
  

```
  cd ~/Desktop/
  hs -c-1 -p 1025 
```

比如http-server默认使用8080端口
一个端口如果被占用，你就只能换个端口

**总结**

IP是用来定位一个设备的；

端口是用来定位一个设备的服务的；

IP端口缺一不可

有了IP和端口就可以定位一个设备提供的服务(腾讯的设备提供的聊天服务)

## 域名

域名就是对IP的别称

baidu.com对应什么IP?

终端命令


```
 ping baidu.com
```

mac会得到不停的回复，中断control+c

**知识点**

**1.一个域名可以对应不同IP**

一般ping同一个域名会得到不同的IP

这个叫负载均衡，防止一台机器扛不住

可以把这个域名绑定到多个不同的IP

引流,将压力平均分给多个IP分摊

这样，假设即使同时有100万人访问百度，但有了多个IP的同时分担，也能轻松应对。

**2.一个IP可以对应不同域名**

这个叫做共享主机，穷开发者会这么做

## DNS 域名系统

> 那么域名和IP是怎么对应起来的?    
>  
> 通过 DNS(全称域名系统)

**DNS怎么运作的？**

当你输入baidu.com

过程

你的Chrome浏览器会向电信/联通提供的DNS服务器 询问baidu.com对应什么IP

终端命令

```
nslookup baidu.com
```

电信/联通会回答一个IP

然后Chrome才会向对应IP的80/443端口发送请求

请求内容是查看baidu.com的首页

**为什么是80/443端口**

服务器默认用80提供http服务

服务器默认用443提供https服务

可以在开发者工具里看到具体的端口

> **题外话**
> 
> 1.www www.baidu.com和baidu.com不是同一个域名
> 
> 2.他们是什么关系 
> 
> com是顶级域名
> 
> baidu.com是二级域名(俗称一级域名)
> 
> www.baisu.com是三级域名(俗称二级域名) 
> 
> 他们之间是父子关系，baidu.com是www.baisu.com的父级 > github.io把子域名xxx.github.io免费给你使用
> 
> 所以你应该知道www.baidu.com和baidu.com可以不是同一家公司，也可以是
> 
> www是多余的，只有外行才去用，程序员网站一般都不提供www前缀

## URL 统一资源定位服务

**URL = 协议+域名或IP+端口号+路径+查询字符串+锚点**

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-d49f70df7fcd59e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


1.对前端来说只有两种协议http或者https
https默认端口号443

2.查询参数?wd=,同一页面我们想看不同的查询结果 

3.锚点#,查询页面的某一块内容


注意:锚点看起来有中文，实际不支持中文

锚点是无法在Network面板看到的

**因为锚点不会传给服务器!**

##  HTTP(协议) 

HTTP(协议)用来表示如何传输网页的。

是基于TCP和IP两个协议发明的

**一.curl命令**
**1.用curl可以发HTTP请求**

```
curl -v http://baidu.com
curl -s -v --https://www.baidu.com
```

2.理解以下概念

(1)url 会被curl工具重写，先请求DNS获得IP
  先进行TCP连接，成功后开始发送HTTP请求

  请求内容看一眼

  响应内容看一眼

  效应结束后，关闭TCP连接(看不出来)

  真正结束
  
3.HTTP 

规定请求的格式是什么，响应的格式是什么

终端命令

```
 curl -v http://www.baidu.com
```

http到底是做什么的呢？

http作用，规定如何写这5行请求

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-552a2a6f668e808e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以及如何返回这几十行响应的文件

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-4a079f07bd146bdc?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

http就是个文件，上面写了请求怎么写，响应怎么写

