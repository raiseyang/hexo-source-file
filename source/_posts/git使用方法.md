title: git使用方法
author: raiseyang
tags:
  - git
categories:
  - git
date: 2017-07-28 09:05:00
---

[Git Reference](https://git-scm.com/docs)
[Git chart sheet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf)

### 设置用户名
```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
- 不指定`--global`,则为当前仓库设置用户名
- `commit`时,将指定`user.name`用户提交
<!-- more -->
### 初始化空的本地仓库
```
$ git init
```


### 查看远程仓库地址
```
git remote -v
```

### 创建本地分支并push到远程
```
git init
git add . # 添加所有文件到git仓库，建议创建.gitignore文件过滤
git commit -m '提交消息' # 提交代码到本地
git remote add origin git@github.com:raiseyang/hexo-source-file.git # 关联远程仓库
git push --set-upstream origin master # push本地分支到远程仓库
```

### clone远程仓库,并提交
```
git clone ssh://git@gitlab.adups.com:5022/yangdongsheng/TgiFotaServer.git
cd TgiFotaServer
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

### 已有项目提交到remote
```
cd existing_folder
git init
git remote add origin ssh://git@gitlab.adups.com:5022/yangdongsheng/TgiFotaServer.git
git add .
git commit -m "init"
git push -u origin master
```

参考资料:
[Git 教程 | 菜鸟教程](http://www.runoob.com/git/git-tutorial.html)
[Git教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)