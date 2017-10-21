title: git使用方法
author: raiseyang
tags:
  - git
categories:
  - git
date: 2017-07-28 09:05:00
---
## 创建本地分支并push到远程
```
git init
git add . # 添加所有文件到git仓库，建议创建.gitignore文件过滤
git commit -m '提交消息' # 提交代码到本地
git remote add origin git@github.com:raiseyang/hexo-source-file.git # 关联远程仓库
git push --set-upstream origin master # push本地分支到远程仓库
```

参考资料:
[Git 教程 | 菜鸟教程](http://www.runoob.com/git/git-tutorial.html)
[Git教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)