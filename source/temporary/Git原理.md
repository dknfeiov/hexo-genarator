---
title: Git常用命令整理
date: 2018-11-12 13:26:12
tags: Git
---



1. git init 初始化一个git仓库 #创建master分支
2. git clone http://github,com/dknfeiov/bootstrap.git
3. cd bootstrap （切换目录 ）
4. git fetch origin master（下载分支，首先进入目录）
5. git clone https://github.com/dknfeiov/nodejs-demo.git -b me （直接下载分支）
6. git branch mybranch (创建分支)
7. git branch -d mybranch (删除分支)
8. git branch -a 可以查看现有的所有分支
9. git checkout 目录名  （切换分支） 
10. git checkout -b test-dkn (新建分支并切换)
11. git add . (递归的将执行命令时所处目录的所有文件添加到暂存区)
12. git commit -m "修改点"（提交 ， -m 指定修改message）
13. git merge 合并分支
14. git push (提交代码至github) 
15. git fetch 相当于是从远程获取最新版本到本地，不会自动merge
16. git pull --rebase (更新代码) 本地与服务器端同步 ,会自动merge
17. git stash drop (删除暂存区) git stash 暂存
18. git status 查看已修改（添加，删除等）
19. git log 查看提交记录
20. git log --pretty=oneline 查看提交记录 单行显示
21. git log -p filename 查看文件修改详情
22. $ git reset --hard 版本号 回退
23. git remote add origin ssh地址 （添加外部地址）
24. git remote -v 显示所有仓库？
25. git config --global --list    查看系统config
26. git config --local  --list  查看当前仓库配置信息
27. git config --global user.name "username"'
28. git config --global user.email "youxiang@xxx.com”
29. git checkout . 清空本地修改
30. git diff —raw 比较变更


git show ‘log’ 查看某次log详情

打标签
git tag -a “tagname” -m “info"
git push origin —tags



git clean -n 删除预演，提示哪些文件将被删除 
git clean -f  删除当前目录下没有被track过的文件
git clean -f path 删除某个目录下没有被track的文件
git clean -df 删除当前目录下没有被track过的文件和文件夹
git clean -xf 同上，包括.gitignore中指定的文件夹和文件

添加新项目到git上

git remote add  添加远程仓库



bash（linux命令）
rm -rf  删除文件夹

编辑文件：vim file 
o 编辑
:wq 保存退出