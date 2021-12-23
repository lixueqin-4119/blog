<h2>git-将本地仓库更新到远程仓库</h2>

```
git init
git add .
git commit -m '更新'
git remote add origin git@github.com:lixueqin-4119/blog.git
若报错fatal: remote origin already exists.
执行以下2条命令：
git remote rm origin
git remote add origin git@github.com:lixueqin-4119/blog.git

git pull --rebase origin master
git push -u origin master
```
最后刷新github