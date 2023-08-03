---
title: Git相关
description: Git
date: 2023-04-02
tags:
  - Git
---



## 

## 一台电脑双github配置

知乎 [一台电脑双GitHub账户配置 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/107341502)

编辑`~/.ssh/config`文件。如果该文件不存在的话，直接创建一个就好。里面的内容如下：

```text
Host github.com:WestonTonks  
Hostname ssh.github.com
User 1013091547@qq.com
IdentityFile ~/.ssh/id_ed25519
```

## 生成ssh密钥

1. 打开Git Bash。

2. 粘贴下面的文本（替换为您的 GitHub 电子邮件地址）。

   ```shell
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

## 将克隆的项目提交到自己的仓库

- 远程地址关联本地项目
  `git remote set-url origin 仓库地址`
- 显示远程存储库列表及其相应的 URL
  `git remote -v`
- 推送到远程地址
  `git push`

## 将新创建的项目提交到自己的仓库

- 初始化Git仓库
  `git init`
- 添加文件 将文件添加到Git仓库的暂存区
  `git add .`
- 提交文件 将文件提交到Git仓库
  `git commit -m "提交信息"`
- 配置远程仓库地址
  `git remote add origin 远程仓库地址`
- 推送到远程仓库
  `git push -u origin master（第一次提交，需要加 -u 选项）`

## 提交代码

- 把暂存的改动提交到本地的版本库
  `git add .`
- 把暂存的改动提交到本地的版本库
  `git commit -m 'xxx'`
- 将本地的分支版本上传到远程并合并
  `git push`

## 将克隆的项目提交到自己的仓库

- 远程地址关联本地项目
  `git remote set-url origin 仓库地址`
- 显示远程存储库列表及其相应的 URL
  `git remote -v`
- 推送到远程地址
  `git push`

## 将新创建的项目提交到自己的仓库

- 初始化Git仓库
  `git init`
- 添加文件 将文件添加到Git仓库的暂存区
  `git add .`
- 提交文件 将文件提交到Git仓库
  `git commit -m "提交信息"`
- 配置远程仓库地址
  `git remote add origin 远程仓库地址`
- 推送到远程仓库
  `git push -u origin master（第一次提交，需要加 -u 选项）`

