---
title: Git & Github
tags: Git
---

### 一.Git基本概念

#### 1.版本控制系统

- Git是开源的分布式版本控制系统，可以处理项目的版本控制

- 功能

  - 协同修改：多人并行修改服务器端的同一个文件
  - 权限控制：开发团队内外的人员拥有不同的权限
  - 历史记录：查看修改人，修改时间，修改内容等
  - 数据备份：保存当前状态，还保存每一个提交过的历史状态

  - 分支操作：多条生产线同时推进任务

- 优势

  - 在本地完成操作，不需要联网
  - 尽可能添加数据而不是删改数据
  - 数据完整性的保证(哈希算法)

#### 2.Git结构

- 工作区：写代码的区域
- 暂存区：写完但还没有提交的命令(临时存储)
- 本地库：储存历史的版本

#### 3.本地库和远程库

- 远程库位于代码托管中心

- **团队内部协作**：远程库的控制者把本地库推送到远程库，然后团队开发人员可以clone该远程库到自己的本地库，然后修改后再推送到远程库
- **跨团队协作**：团队外的人可以fork别人的远程库，然后在本地修改之后，再推送到远程库中，通过提交pull request请求，经审核通过后合并到别人的远程库中

### 二.Git命令行操作

#### 1.本地库初始化

- 命令：git init
- 在Git终端中，进入目录使用该命令，即可初始化一个本地库
- .git目录是隐藏目录，存放的是本地库相关的子目录和文件，不能删除和胡乱修改

#### 2.设置签名

- 作用：区分不同开发人员的身份

- 这里设置的签名和登录远程库的账号密码没有关系

- 项目级别/仓库级别：仅在当前本地仓库范围内有效

  - git config user.name abc
  - git config user.email abc@qq.com
  - 信息保存位置：./.git/config(cat .git/config)

- 系统用户级别：登录当前操作系统的用户范围

  - git config --global user.name abc
  - git config --global user.email abc@qq.com
  - 信息保存位置：~/.gitconfig(cat ~/.gitconfig)

- 两者必须有一个，但项目级别优先级更高

#### 3.基本操作

- 状态查看：git status(查看工作区，暂存区状态)

- 添加：git add [file name] (将工作区的"新建/修改"添加到暂存区)

- 提交：git commit -m "commit message" [file name] (将暂存区中的内容提交到本地库)

- 查看历史记录：
  - git log：显示具体信息(多屏显示，space向下翻页，b向上翻页，q退出)
  - git log --pretty=oneline：单行显示
  - git log --oneline：简化单行显示
  - git reflog：HEAD@{移动到当前版本需要多少步}

- 版本前进后退

  - 索引值：git reset --hard [局部索引值]
  
- ^符号(只能后退)：git reset --hard HEAD^ (一个^表示后退一步，n个表示后退n行)
  
- ~符号(只能后退)：git reset --hard HEAD~n(表示后退n步)
  
- reset参数：
  
  - soft：只更改head指针的位置，暂存区和工作区不变，工作区内容不会丢失
  - mixed：默认参数，让暂存区和本地库一致，但工作区内容不会丢失
  - hard：head指针，暂存区，工作区三者一致，指针指向版本后做的更新全部丢失
  
- 找回删除文件：只要commit之后就会有记录，用reflog能看到所有修改的记录

  - git reset --hard [指针位置] 可以找回一切提交过的记录版本
  - 删除操作还没提交到本地库，指针位置用HEAD
  
- 比较文件差异：如果git status中working tree clean，则没有输出
  
- git diff [文件名]：工作区的文件与暂存区的文件相比较
  - git diff [本地库中历史版本] [文件名] ：工作区的文件和本地库历史记录比较
  - 不指定文件名可以比较所有文件：git diff HEAD

#### 4.分支管理

- 分支：多条线并行推进任务，以免影响主线开发
- 优点：提升开发效率；如果一个分支开发失败，不会对主线造成任何影响，删除重新开始即可
- 创建分支：git branch [分支名]
- 查看分支：git branch -v
- 切换分支：git checkout [分支名]
- 合并分支：
  - 切换到需要被修改的分支git checkout [被合并的分支]
  - 合并git merge [有新内容的分支名]
- 解决冲突：
  - 冲突：不同分支在同一个位置进行修改
  - 1.编辑文件，删除特殊符号
  - 2.保存文件
  - 3.git add [文件名]
  - 4.git commit -m "日志信息" (不能带文件名)
- 合并操作需要在提交到本地库(commit)之后才能合并

### 三.Git基本原理

#### 1.哈希算法

- 哈希函数(散列函数)：把任意长度的输入通过散列算法转换成相同长度的输出，散列值的空间远小于原输入的空间
- 特点
  - 输出长度固定
  - 输入数据确定，则输出数据保持不变
  - 输入数据变化，则输出数据会有很大变化
  - 不同的输入可能有相同输出，所以哈希算法不可逆
- Git底层用的是SHA-1算法来保证数据的完整性

#### 2.Git保存版本的机制

- Git把数据看作小型文件系统的一组快照，每次提交更新时Git会对当前全部文件制作一个快照并保存快照的索引。
- 如果文件没有修改，则不会重新存储该文件，而是保留一个指向之前储存的文件。
- 快照的理解：当你删除一个文件后，文件原来所占的磁盘空间并不是被清空，而是被文件系统标记为“已废弃，可修改”的状态，快照的作用就相当于将旧文件所占的空间保留下来，并且保存一个引用，而新文件中会继续使用与旧文件内容相同部分的磁盘空间，不同部分则写入新的磁盘空间
- Git的数据文件管理和分支管理都用的是指针操作，效率高

### 四.Github

#### 1.创建远程库地址别名

- 查看当前所有远程地址别名：git remote -v

- 创建远程地址别名：git remote add [别名] [远程地址]

#### 2.推送

- git push [别名] [分支名]

#### 3.克隆

- git clone [远程地址]
- 效果
  - 完整的把远程库下载到本地
  - 创建远程地址别名
  - 初始化本地库

#### 4.团队成员邀请

- Settings-Collaborators-Add collaborator(输入账号)-Copy invite link发送给对方-对方打开链接接收邀请-加入团队成功

#### 5.拉取

- pull=fetch+merge
- git fetch [远程库地址别名] [远程分支名]
- git merge [远程库地址别名/远程分支名]
- git pull [远程库地址别名] [远程分支名]