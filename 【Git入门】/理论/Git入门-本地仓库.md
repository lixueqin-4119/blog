
**git与gitbash的关系，傻傻分不清？**

git 就是一个命令而已，bash就是包含命令的黑框框。

这个黑框框可以运行很多命令其中就包括git，我们在gitbash里面运行git，gitbash相当于Mac的iterm2。

使用github要先学会git，git无需安装，有cmder/gitbash即可，comder内置了gitbash。

## 一. 配置

**所有软件都是要先配置的，git的配置如下：**

终端iterm2运行这6行代码：(出错可以重新再运行，运行完这6行才能开始用git)

```js
git config --global user.name 你的英文名 
git config --global user.email 你的邮箱     //最常用的邮箱
git config --global push.default simple
git config --global core.quotepath false
git config --global core.editor "code --wait"     //code是vscode的缩写
git config --global core.autocrlf input
```

备注：英文名/邮箱跟GitHub无关,可以跟GitHub的用户名和邮箱保持一致。

**测试查看是否配置成功：**

```js
git config —-global —-list
```

## 二.git init & git add & git commit

**git解决了什么问题,为什么要git？**

版本控制,git可以让你的代码有版本可以随时退到某个版本。


1.  `git init` //初始化，创建.git目录放置代码快照(版本)
2.  快照实现存储版本怎么做？
    `git add`
    `git add + 路径`
   
    解析：选择要提交的文件，路径可以是 **.和*** /**绝对路径**/**相对路径**
3.  **.gitignore 处理不需要提交的文件**
    例子：git add会把当前所有文件打包，但有的文件不想提交怎么办？
![image.png](https://upload-images.jianshu.io/upload_images/21487050-b873917e85d7104d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


答：如上图所示，新建.gitignore 文件，添加不想提交的文件，这样打包时就不会把不需要的文件提交了。

4.git add后查看当前文件提交的状态 `git status`

5.git commit提交的2种方式：

`git commit -m “字符串(版本号)”`

注释：提交并说明理由,注意字符串里如果有空格就要用引号包起来，没有空格引号可省略

`git commit -v` 
更正式，关闭窗口自动保存，如下图所示：

![image.png](https://upload-images.jianshu.io/upload_images/21487050-f39453893f03df42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


6.查看当前所有已提交的版本
 `git log`

**7.多次提交**
只需要重复`git add .` 和 `git commit -v`

## 三.如何实现多版本切换？

`git reset —-hard xxxxxx` //版本号前6位

**注意：该操作会使没有commit过的变动消失，确保要先commit ！**

步骤：
1.查看当前所有版本 `git log`

![image](https://upload-images.jianshu.io/upload_images/21487050-148257f29075b57c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.选择要查看的版本号的前6位并复制(前4位也行但不能重复)

例子：`git reset --hard d9711a` //选择查看版本1

3.返回 `git reflog`

现在git log后也只能查看刚刚复制的这个版本了，**那别的版本怎么找呢？**

`git reflog` 

解析：不仅要看**当前的历史**，还要看**御剑飞行的记录**

![image.png](https://upload-images.jianshu.io/upload_images/21487050-85081f346fe0e696.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


4.飞完了，我现在想回去**走访**怎么办？

复制想要回访的版本号的前6位，esc q键出去，

然后git reset --hard ac8c36b回到**版本2**

5.来回切换
`git reflog` +复制版本号+esc q键出去+`git reset --hard xxxxxx`

**结束！**

> 总结：
> 
> 1.`git add`选择提交内容
> 
> 2.**.gitignore** 文件描述不提交的内容
> 
> 3.`git commit -v`用来提交
> 
> 4.`git log` 用来查看历史
> 
> 5.`git reset --hard xxxxxx` 用来切换版本
> 
> 6.`git reflog`用来查看所有历史

## 四.如何同时两条线开发？

`git branch x` 可以创造平行时间线`x`,术语叫`分支`

![image.png](https://upload-images.jianshu.io/upload_images/21487050-3defb551dc8767d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**过程如下**

1.回到版本1**创建分支x**

代码：

```
git log   
git reset --hard xxxxxx //切换版本 退出esc q 
git branch x
```

2.开始老板的需求并提交

```
git add index.html   
git commit -m v1
```

3.回到**老板娘**分支

```
git checkout x //注意此时会回到最初的设计页面
```

开始老板娘的需求并提交

```
git add index.html    
git commit -m v2
```

4.再次切回**老板**的需求

```
git checkout master
```

完成老板的需求后再次提交

```
git add .    
git commit -m v11
```

7.再切到**老板娘**需求

```
git checkout x
```

继续设计并提交

```
git add .    
git commit -m v22
```

```
history  //可查看历史操作
```

**结束！**

> 总结：
> 1.老板分支为`master`，老板娘分支为`x`
> 2.`git branch` 基于当前commit 创建一个新的时间线(分支)
> `git branch x` //创建分支x
> 3.`git checkout` //用于切换分支`x/master`

**如何查看当前所在分支？**

代码：`git branch` // ***表示当前所在的分支**

![image.png](https://upload-images.jianshu.io/upload_images/21487050-105739abc9f300c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


如上图所示，当前所在分支为x

**合并分支**

2个分支如何合并？用git merge将另一个分支合并到当前分支

```
  git checked master //保留主分支
  git merge x
```

## 五.关于冲突 CONFLICT

> 当前目录有未提交的代码，只要跟另一个分支不冲突就无须理会。
> 
> 如果冲突了呢？及时commit或使git stash合并冲突

**发现冲突**
在合并分支的时候会得到conflict提示，使用git status -sb查看哪个/哪些文件冲突了

```
git status 或者 git status -sb
```

**解决冲突**
解决冲突后

```
git add .  
git status   //查看是否还有冲突，有git status -sb 没有就git commit   
git commit   //自动填充理由，关掉小窗口   
git log      //检测当前所有版本是否合并成功
```

**合并完后删除无用的分支**

![image.png](https://upload-images.jianshu.io/upload_images/21487050-1f2a91402c11b264.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
git branch       //查看分支     
git branch -d x     
git branch       //再查看分支，此时只有一个master了
```

**结束！**

> 总结
> 
> **1.分支可以合并**
> 
> 进入要保留的分支，运行git merge x, 合并完后删除无用的分支git branch -d x
> 
> 2.**本地仓库**就是 git init 创建的那个 .git 目录
> 
> 3.`git branch x` 的作用是什么？
> 
> 会基于本地仓库里最新一次 commit（提交），创建一个新的分支 x
> 
> 4.`git checkout x` 的作用是什么？
> 
> 会让当前目录的内容变成本地仓库里 x 分支的最新内容（可能会删除当前目录里的一些文件）

## 六.总结

今天学到的命令

![image.png](https://upload-images.jianshu.io/upload_images/21487050-ada984b29a237543.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

