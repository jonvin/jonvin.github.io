---
title: git 的使用笔记
date: 2019-06-05 17:19:42

tags:
---

前言：现在发现周围的朋友都有写博客的习惯。在朋友和同事的感染下，为了能更好的记录学习中的点点滴滴，今天自己也搭了一个。第一篇笔记就写常用的 git 命令吧！

### 下载和安装

1.官网上下载 git [点我](https://git-scm.com/)

2.本机安装对应版本的 git。

3.在码云或者 github 之类的代码管理工具书上创建项目
注意，新建项目时去掉 readme 的选项

4.跳转到教程页面

5.打开安装完成的 git，windows 系统建议键入
git config –global core.autocrlf false
来设置一下 crlf，如果不设置容易出现：LF will be replaced by CRLF 错误；

### 初始化和创建项目

6.然后依次输入以下命令进行初始化：
git init
git add .
touch README.md

7.输入以下命令进行添加所有文件：
git add -A

8.输入以下命令进行提交注释：
git commit -a -m “初始提交”

9.输入以下命令进行初次建库，需要与远程仓库关联，输入如下命令关联：
git remote add origin https://git.oschi[自己代码版本控制的地址]
如果出现 origin exitss，我们可以输入 git remote rm origin，再进行关联语句的执行

git remote -v 查看代码是属于哪个仓库地址

10.输入以下命令进行将代码仓库上传到代码库：
git push origin master

恭喜你 到这里就上传成功啦！

### 开发过程

11.先后运行 git add 和 git commit 命令将更改提交到本地仓库；

12.关联本地仓库和远程仓库：git remote add origin https://git.oschi[自己代码版本控制的公钥地址]

13.运行 git push -u origin master 将更改提交到远程仓库。

14.仓库建好，并与码云关联后，如果有新增文件只需重复如下命令：
git add -A
git commit -a -m “初始提交”
git push origin master

15.在使用 git push origin master 提交时，如果出现 failed to push some refs to git ：
输入 git pull –rebase origin master；
再输入 git push origin master 提交。

16.如果遇到了 代码冲突等问题可以执行以下 3 行命令
git stash
git pull
git stash pop ：只会恢复 存储最新的 stash

退回到指定版本

git reset --hard HEAD^

查看 git ssh key 公钥

cd ~/.ssh

ls

如果没有则需要生成公钥

ssh-keygen

再次查看是否生成

cat id_rsa.pub

git stash list ：查看 stash 了哪些存储
git stash show ：显示做了哪些改动，默认 show 第一个存储,如果要显示其他存贮，后面加 stash@{$num}，比如第二个 git stash show stash@{1}
git stash apply :应用某个存储,但不会把存储从存储列表中删除，默认使用第一个存储,即stash@{0}，如果要使用其他个，git stash apply stash@{$num} ， 比如第二个：git stash apply stash@{1}

git config --global http.postBuffer 157286400 上传文件内容过大，配置上传限制

查看用户名和邮箱

git config user.name

git config user.email

修改用户名和邮箱

git config --global user.name "XXX"

git config --global user.email "xxxx"

### 新建分支删除分支等操作

17.git checkout -b '分支名'   
18.git push origin dev 提价到新建的分支中
19.git merge dev 把 dev 分支的代码合并到当前分之上
20.git branch -a 查看已有的本地及远程分支
21.git push origin --delete dev 删除远程分支
22.git branch -d dev 删除本地分支

### 在多人配合开发时 越过别人的 eslint 报错 提交代码

23.git commit -m '' --no-verify

### 修改已提交的 commit 注释

24.git commit --amend 最后一次最新的 commit 注释
出现有注释的界面，输入 i 进入修改模式，修改好注释后，按 Esc 键 退出编辑模式，输入 :wq 保存并且退出。 OK 修改完成。

25.git rebase -i HEAD~2  
 最后的数字 2 指的是显示到倒数第几次 比如这个输入的 2 就会显示倒数的两次注释

### node 版本控制(只支持 mac)

sudo npm cache clean -f //清除 nodejs 的 cache：
sudo npm install -g n //使用 npm 安装 n 模块
npm view node versions // node 所有版本
sudo n latest // 升级到最新版本
sudo n stable // 升级到稳定版本
sudo n xx.xx // 升级到具体版本号
