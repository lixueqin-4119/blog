> Git 包括 本地仓库 和 远程仓库github, git 这个工具有个特点, 它的本地与远程是完全隔开的。在学习远程之前必须先搞熟本地。
> 
> 代码需要储存在云端，比如你需要在公司和家里写代码，比如你的笔记本被奶茶泡坏了，比如你不小心运行了rm-rf/ …

Github可以存储你的代码，只需要2行命令：

```
git remote add origin git@xxxxxxxx
git push -u origin master
```

## 一.SSH key验证身份

> GitHub 是如何知道是 "你"在操作 “你的代码”?
> 
> 有人说可以通过验证用户名和密码呀。每次上传时都要输入用户名和密码过于繁琐，有没有别的能验证身份的方式？
> 有，SSH key验证

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-b9bcb9cb08dc6551?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**那SSH key怎么验证的？**

如上图所示，我们需要生成一个公钥上传到GitHub，然后生成一个私钥放到电脑上。当我们上传代码时，代码会用这个私钥加密代码上传。GitHub看不懂加密后的代码便用账号里面的公钥解密。如果能解开就证明是本人，解不开就是别人想偷你代码伪造上传的。公钥私钥是配对的，公钥加密只能找私钥解，私钥加秘只能找公钥。

**如何生成ssh key?**  [Github帮助文档](https://docs.github.com/cn/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

过程如下

**1.终端运行**

```
ssh-keygen -t ed25519 -C "your_email@example.com" //修改为你的邮箱

```

接下来一直回车即可。

出现下图所示即代表成功。

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-7b26614fd45120c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

进入.ssh目录可**查看当前目录**，id_rsa.pub 公钥可以给任何人看, id_rsa私钥 泄露别人就能伪造你的身份篡改你的代码！
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-9c5d8d1086928d2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.打出公钥id_rsa.pub的内容并复制其内容**

```
cat ~/.ssh/id_rsa.pub

```

**3.打开GitHub，在设置页面的“SSH and GPG keys”选项卡里填入公钥：**

点击按钮“New SSH key”，把copy好的公钥粘贴进 Key，点击“Add SSH key”

**测试公私钥是否配对成功**

```
ssh -T git@github.com
```

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-a4f72ee4ca0b4722.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里GitHub也给我一个公钥证明它是它，我们两方都在证明自己的身份，输入yes接受它的公钥。当出现“Hi xxx!You are successfully…”代表已成功关联。

**结束！**

## 二.将本地仓库上传到 GitHub

设置了公钥就能上传和下载了，通过git pull/git push/ git clone。

上传代码 git remote add 和git push

**过程**
**第1步. 登陆github，新建一个空仓库，用来存放本地代码。**
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-f7cda14300a55d87?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.打开本地仓库，新建终端依次执行这2句话。**
补充：确保你的代码此时已经commit了，才能继续进行这步。
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-7064a6af570794f7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

备注：运行时把main改为master！GitHub 已经把master分支改名为main了，因为master有奴隶主的意思，涉嫌歧视黑人。现在刷新你的GitHub就能看到你的项目啦

> 其它
> 
> 1.git push -u repo2 master -f //强制push，不要用，有影响
> 
> 2.git pull //可选，确认同事不会改远程代码时可省掉
> 
> 3.上传其它分支git push origin x:x //源头:目标,从本地x到远程x
> 
> 4.cat ~/.bashrc //打印出内容
> 
> 5.冒号无法退出(英文状态下)输入q即可
> 
> 6.**source ~/.bashrc //source执行下这个文件**

## 三.下载 git clone

所有代码都能用ssh和https协议下载，只是ssh协议传输速率高一些.

**1.只有GitHub代码链接，如何下载代码？**
![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-92854ee0802782c1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**复制SSH地址,终端执行**

```
 git clone git@github.com:lixueqin-4119/git-demo1.git //SSH地址

```

**切换分支，没有分支可跳过**

```
 cd git-demo1/    //不要忘记cd
  ls
  git checkout x   //切分支
  git branch       //查分支

```

**2.如何下载某个分支？**

先下载整个仓库，然后git checkout.每次clone下载的是所有分支(如果有分支的话),git checkout可用于切换分支。

**3.下载速度很慢怎么办？**

[git clone满速下载教程](https://github.com/sun-shadow/Surf_the_Internet/blob/master/%E5%91%BD%E4%BB%A4%E8%A1%8C%E7%AF%87.md)

**4.git clone三种变形**

```
1.git clone git@?/xxx.git     //以原始目录名为目录名
2.git clone git@?/xxx.git yyy //重命名目录为yyy
3.git clone git@?/xxx.git .    //把远程代码塞到已建好的空目录里

```

常用命令git clone下载/git pull更新已下载的代码/git push上传

## 四.上传到两个远程仓库

> 国内Github的替代品：
> 
> 1’coding.net腾讯战略投资2’gitlab.com3’码云gitee.com开源中国

上传到2个远程仓库，只需要2句话：

新建2个GitHub远程仓库，1个编辑好的本地仓库。

```
vscode终端依次运行
git remote add origin git@github.com:lixueqin-4119/git-demo2.git
git remote add origin2 git@github.com:lixueqin-4119/git-demo3.git
git push -u origin master
git push -u origin2 master
```

如果提示git pull,说明你新建项目时创建了一些文件。
你只需要运行git pull之后再运行刚刚的命令。

**这样做有什么意义？**

某些软件有2种版本，免费版和收费版。

## 五.git高级操作，那些只有老手才知道的操作

**1.使用bash alias简化git命令**

如何实现？ga/gc/gl/gp/gco/gst

运行以下命令即可

```
touch ~/.bashrc
echo 'alias ga="git add"'>> ~/.bashrc     //可以根据自己的习惯调整缩写哦
echo 'alias gc="git commit -v"'>> ~/.bashrc
echo 'alias gl="git pull"'>> ~/.bashrc
echo 'alias gp="git push"'>> ~/.bashrc
echo 'alias gco="git checkout"'>> ~/.bashrc
echo 'alias gst="git status -sb"'>> ~/.bashrc
```

然后重启命令行，或者运行`source ~/.bashrc` ,现在你就可以用这些缩写了。

**解析：**

touch摸了下bashrc命令行的配置文件。

echo ‘…’ >> ~/.bashrc //把这句话追加到bashrc命令行配置文件里

alias ga=“git add” //当输入ga时等于输入了git add

**2.好看的glog**

如何优化git log?

首先复制下面代码：

```
alias glog="git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit -- | less"

```

然后终端运行

```
 code ~/.bashrc   //编辑bashrc

```

最后在打开文件的最后一行直接粘贴刚刚复制的代码并关闭小窗口。

**3.美化历史命令 git rebase -i xxxx**

出错怎么办？


1.看log提示，里面说了怎么解决

2.中止 git rebase --abort 可以取消rebase

3.继续 git rebase --continue 可以继续

**4.通灵术**

> 你不想提交代码又不想删除代码，那么可以找个空间把代码临时藏起来 可多次git stash，弹出来时顺序从后往前。

```
git stash      //隐藏
git stash pop  //弹出来

```

## 六.GitHub搭建个人博客

> 其它
> 
> 1.git clone 和 git pull 都是下载，区别是什么？ git clone 是在我没有代码的前提下，下载整个仓库到本地 git pull 是在我已经拥有本地仓库的前提下，只下载本地没有的那一部
> 
> 2.忠告：不要一边写一边看效果，这样很浪费时间！
> 
> 3.图片或链接 ![]()

**GitHub功能之1，可以直接预览markdown**

步骤
1.打开vscode新建文件README.md, **安装markdown**

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-9fb21eb12545caeb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.编辑好内容后，快捷键command+shift+p输入 **>markdown preview**, 选择打开侧边预览。

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/21487050-e887c5e74d962e97?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

装了插件后vs code就成了最好用的 **markdown编译器**

编辑好内容上传后就可以直接预览啦，后缀名是.md的都可以直接预览，不一定要叫README.md。
