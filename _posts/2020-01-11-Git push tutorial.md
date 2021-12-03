---
title: "Git中的基本操作"
date: 2020-01-11
categories:
  - blog
tags:
  - git
toc: true
---
 
# 1.创建SSH key
打开git bash，输入以下命令创建SSH key
```
ssh-keygen -t rsa -C "youremail"
```
# 2.Github上授权
在Github中的account settings，添加SSH keys，title随便命名，将id_rsa.pub中的内容复制到key文本中。

# 3.git bash中登录github
```
ssh -T git@github.com
```
之后，会提示You've successfully authenticated, but Github does not provide shell access.
git commit命令会记录提交者的信息，所以使用git前需要添加这两条信息。
```
 git config --global user.name "你的GitHub登陆名"

 git config --global user.email "你的GitHub注册邮箱" 
```
# 4.提交文件更改
提交上述操作
```
git commit -m "log message"
```
推送所有文件到远程仓库
```
git push origin master
```
如果是第一次进行提交操作，则需要在origin前加-u操作。
# 5.删除github中的文件和文件夹
如果文件数量不多，则可以直接在github中进行删除，文件数量较多，批量处理则还是在命令行中比较的方便。首先，先将文件git到本地
```
git clone https://github.com/***
```
在clone的本地仓库进行初始化
```
git init
```
之后选择需要删除的文件即可
```
git rm FILE
git rm -rf ***
```

