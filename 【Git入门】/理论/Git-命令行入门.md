## **一.命令缩写**

程序员把动词缩写，就变成了命令

![image](https://upload-images.jianshu.io/upload_images/21487050-8980442a6672bb54.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**知识点：**

(1)file不是命令没有缩写,recursive和force表示选项，无缩写；

(2)cd就是改变目录的意思;

**备注：**
Git Bash 和 git 的关系是（mac系统里的终端就相当于 Git Bash）完全不一样的东西，一个是 bash，一个是 git，两种不同的软件.

## **二.文件的增删改查 (终端工具iterm2)**

**其它事项：**

1.程序员宿命(文件的增删改查，DOM的增删改查，HTTP的增删改查)

2.无论做什么操作首先要确定现在的位置cd ~/Desktop/

3.cmd是windows自带的命令行工具缺点多。Cmder更高级需要配置！Mac推荐用iterm2

4.救命快捷键:control+C 用于中断一个命令

5.clear 清除代码

6.~ 指用户目录，cd ~ 进入用户目录.注意只能在用户目录里操作！

7.code demo,用vscode打开文件

> 查文件

1.用命令行进入桌面 cd ~/Desktop/

2.查看当前绝对路经pwd

3.查看当前目录 文件ls

查看指定目录 子文件ls+文件名

补充: ls有个潜规则默认不显示.开头的文件,例如.git等等

4.查看指定文件内容代码 cat+文件

```
    cd demo/
    cat style.css
```

5.除了cat外，head路径/tail路径/less路径都能查看内容，区别在哪？

答：1.cat会全部展示包括多余的空格，很少用;(电影里代码一直滚动效果就是用的cat，代码太多时会一直滚)

2.less可以通过按上下键滚动查看，退出按q键，比cat智能。高亮时要先esc退出。

3.head默认展示前10行,tail默认展示后10行，但是都可以通过参数选择。例如

head style.css -n 14//展示前14行

tail style.css -n 14//展示后14行

> 增加文件

**创建文件: touch/echo**

1.创建文件touch

touch 1.txt //若不存在则创建//修改文件最后更新时间

touch 2.txt 3.txt //同时创建多个文件

2.echo回声 用于创建有内容的文件

(1)echo xxx 解析:返回xxx

例子：echo 你好 //返回你好

(2)echo xxx > 文件名 解析:创建文件内容为xxx

**例子：**
echo hello > index.html [//新建index.html](https://link.zhihu.com/?target=https%3A//xn--index-gq2j210b.html/)，代码内容为hello(会被覆盖)

(3)echo xxx >> 文件名 解析：追加新内容，不会被覆盖

**例子：**
echo 世界 >> index.html

(4)echo -e "xxx\nyyy" >> index.html 解析：一次性增加2行内容

**例子：**
echo -e "hello\nworld" >> index.html

> 创建目录：mkdir

1.mkdir 解析：创建目录

单个目录 mkdir a, 快速创建多个目录mkdir a b c

2.mkdir -p 目录名 解析：创建多层目录

**例子：**
mkdir -p a/b/c/d

3.创建多个嵌套目录 mkdir -p a/b/c a/d/c

> cp

1.拷贝文件

```
echo hi > index.html
cp index.html index2.html            
```

2.复制目录cp -r 文件1 文件2 //recursive递归的的意思

```
       mkdir demo
       cp -r demo demo2  
```

> 删除

1.删文件 rm index.html

2.删目录 rm -r demo

> 修改

1.修改文件内容1’用echo往里面追加2’用code打开3’start默认程序打开.修改和追加基本用这3种就能搞定(步骤:先设置默认打开程序为vscode,然后运行start demo)

2.清空文件 echo '' > 1.txt

3.移动和重命名是一个操作：

改文件名 mv demo demo2

[4](https://link.zhihu.com/?target=http%3A//4.ls/).ls -l 查看更新时间

修改文件最后更新时间touch style.css

**其它：**

查文档命令记不住，文档太长不想读，tldr程序会提供最常用的命令。

安装yarn global add tldr

查看版本tldr --version

使用tldr ls //查ls的用法

## **三.命令的组合**

> 命令有成功和失败，终端成功不会显示且返回值为0，失败会提示error且返回值为非0的正数。用命令: echo $? 可查看各个操作的返回值。

1.&&操作 当一条命令成功之后，执行另一条

**例子：**
touch 1.txt && echo 创建成功

**备注：**
当前面的成功之后才会继续执行下一个，也可以拼接多个

2.;操作 不管成功失败，都执行另一条

```
rm 1.txt ; echo 删除成功
```

**3.把命令变成文件chmod +x (如何把很多操作合在一起完成)**

**例子：**
新建文件a包括3个子文件最少要10s，有没有方法一键搞定？

**步骤:**

```
touch 一键搞定
code 一键搞定
```

**然后打开一键搞定添加操作：**

```
     mkdir x  //回车代替;
     cd x 
     touch index.html
     touch style.css
     touch main.js
     echo -e "<!DOCTYPE html>\n<h1>标题</h1>" >>index.html 
```

**执行：**

```
     chmod +x 一键搞定  //Mac添加可执行权限，一键搞定这个脚本它就是可执行的了    
     ./一键搞定         //当前目录下
```

**传参数**：再次执行./一键搞定则会报错，因为x已经存在了。这个问题可以通过传参数搞定。

怎么接收参数呢？把x改为$1指要传递的参数

![image.png](https://upload-images.jianshu.io/upload_images/21487050-1e0e1d761ee530b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
终端执行: ./一键搞定 xxx
```

**总结：**
这就是脚本的厉害之处，它可以把你之前通过鼠标不停重复的事情，全部写到一个文件里，通过接收一个参数一键帮你搞定。可以用sh代替./ ,sh是bash的缩写。 **命令行的本质就是个可执行的文件而已。** shebang是一行注释加上之后就可以指定用什么程序来运行当前的脚本。今天学的是bash,所以是bash脚本文件，bashscript语法。
