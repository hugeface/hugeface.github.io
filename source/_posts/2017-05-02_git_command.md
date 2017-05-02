---
title: 常用Git命令
date: 2017-5-2 09:38:11
tags: [Git]
---

[TOC]

# 新建一个本地代码仓库，并上传到github

1. 建立本地仓库
```
git init
```
2. 把文件添加到版本库中，使用命令 git add .添加到暂存区里面去，不要忘记后面的小数点“.”，意为添加文件夹下的所有文件(夹)
```
git add .

git add <filename> <filename> ... <filename>
```

<!-- more -->

3. commit到主分支
```
git commit -m ""
```
4. 登录github，把本地仓库提交至远程仓库,接下来你要做的就是复制那个地址，然后你将本地仓库个远程仓库连接起来
```
git remote add origin git@github.com:用户名/仓库名.git
```
5. 进行第一次提交
```
git push -u origin master
```

**PS: 系统报“fatal: refusing to merge unrelated histories”解决方案**

因为远端的仓库谢了License，是本地没有的内容，push之前要去先pull。但pull得时候被认为是两个不同的仓库（没有共同祖先），系统提示这个错误。

可以在pull操作中添加--allow-unrelated-histories属性

```
git pull origin master --allow-unrelated-histories
```

# git 查看/修改用户名、密码

用户名和邮箱地址的作用

用户名和邮箱地址是本地git客户端的一个变量，不随git库而改变。

每次commit都会用用户名和邮箱纪录。

github的contributions统计就是按邮箱来统计的。

```
// 查看用户名和邮箱地址：
git config user.name
git config user.email

// 修改用户名和邮箱地址：
git config --global user.name "username"
git config --global user.email "email"
```

# 修改git已经commit的邮箱和用户名

```
// 获取需要修改的版本的commit号
git log

// 前往版本进行修改
git reset -soft <commit号>

// 修改信息
git commit --amend --author='用户名<邮箱>'

// 提交修改
git push <remote名> <branch名>
```

# 跨分支提交代码

```
cherry-pick
```

# 创建分支

```
git branch <分支名>
```

# 切换分支

```
// 方法一：该语句和上一个语句可以和起来用一个语句表示
git checkout -b <分支名>

// 方法二
git checkout <分支名>
```
# 分支合并

如果要将开发中的分支（develop），合并到稳定分支（master）

1. 首先切换的master分支
```
git checkout master
```
2. 然后执行合并操作
```
git merge develop
```
3. 如果有冲突会提示，查看冲突文件
```
git status
```
4. 解决冲突，然后调用git add或git rm将解决后的文件暂存
5. 所有冲突解决后，git commit 提交更改。

# 分支衍合

分支衍合和分支合并的差别在于，分支衍合不会保留合并的日志，不留痕迹，而 分支合并则会保留合并的日志。要将开发中的分支（develop），衍合到稳定分支（master）。

1. 切换的master分支
```
git checkout master
```
2. 后执行衍和操作
```
git rebase develop
```
3. 如果有冲突会提示，查看冲突文件
```
git status
```
4. 解决冲突，然后调用git add或git rm将解决后的文件暂存。
5. 所有冲突解决后，git rebase --continue 提交更改。

# 删除分支

1. git branch -d <分支名>
2. 如果该分支没有合并到主分支会报错，可以用以下命令强制删除
```
git branch -D <分支名>
```

# 撤销add操作

```
// 撤销所有文件的add操作
git reset HEAD .
// 撤销某个文件的add操作
git reset HEAD <文件名>
```

# 强行覆盖本地代码

```
git fetch --all
git reset --hard origin/master
git pull
```

# 强行覆盖远端代码

```
git push [远端名] [分支名] --force
```

# 远端代码强行覆盖本地代码

```git
git fetch --all     // 下载远端仓库
git reset --hard origin/master      // 把HEAD指向刚刚下载的最新版本
```
