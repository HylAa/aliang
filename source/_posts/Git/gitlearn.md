---
title: Git - Git个人学习记录手册
tags:
  - Git
categories: []
date: 2021-01-21 15:15:00
updated: 2021-01-21 15:15:00
top_img: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1611214191/Git/68747470733a2f2f6769742d73636d2e636f6d2f696d616765732f6c6f676f732f646f776e6c6f6164732f4769742d4c6f676f2d32436f6c6f722e706e67_lb4xtu.png
cover: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1611214191/Git/68747470733a2f2f6769742d73636d2e636f6d2f696d616765732f6c6f676f732f646f776e6c6f6164732f4769742d4c6f676f2d32436f6c6f722e706e67_lb4xtu.png
---

{% note blue 'fas fa-bullhorn' modern %}
本篇教程是自己学习Git记录的
本篇教程不会介绍Git背景以及一些专业知识，毕竟本人时间有限哈哈哈！
如果喜欢教程就评论下或者把站点添加到你宝贵的收藏夹里面吧
{% endnote %}

### 一、安装Git

​	本人是MacOS系统这里只是演示一下，如Linux 、windows 请自行查阅资料 我这里就不展开了哈。

##### 通过homebrew安装Git

- 未安装homebrew，需安装homebrew
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

- 安装git
```
brew install git
```

- 安装完成后还需要进一步设置，打开你的终端输入：
```shell
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```
因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址。你也许会担心，如果有人故意冒充别人怎么办？这个不必担心，首先我们相信大家都是善良无知的群众，其次，真的有冒充的也是有办法可查的。

注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

### 二、认识Git
在学习Git需要清楚的几个术语
![](https://res.cloudinary.com/dt3vcmqdt/image/upload/v1611215358/Git/683090701_88630_cxjbn1.png)

**Workspace**：工作区
**Index/Stage**：暂存区，也叫索引
**Repository**：仓库区（或本地仓库），也存储库
**Remote**：远程仓库

**1. 有关几个名词解释**

**工作区**: 通过`git init`创建的代码库的所有文件但是不包括`.git`文件(版本库)
**暂存区**: 通过`git add ./*/*Xxx/Xxxx*` 添加的修改,都是进入到暂存区了,肉眼不可见 通过 `git status` 可以看到修改的状态。

**2. 什么是修改？**
比如你新增了一行，这就是一个修改，
删除了一行，也是一个修改，
更改了某些字符，也是一个修改，
删了一些又加了一些，也是一个修改，
甚至创建一个新文件，也算一个修改。

### 三、创建版本库
什么是版本库呢，以我的初中学历水平可以这样解释，可以理解版本库就是一个文件夹，这里面的文件等都会被git管理，谁也跑不掉除非你给某个文件手动排除在外这样git就不会管它了。
所以创建个版本库是不是特别简单了呢，看下面。（建个文件夹不久好了）
```shell
$ mkdir learngit
$ cd learngit
$ pwd
/Users/aliang/learngit
```
`pwd`命令用于显示当前目录。在我的Mac上，这个仓库位于`/Users/aliang/learngit`。
好了文件夹建立好了，接下来吧文件夹交给git吧，我们需要通过这个命令`git init`来初始化git版本库。
```sheel
$ git init
Initialized empty Git repository in /Users/aliang/learngit/.git/
```
瞬间Git就把仓库建好了，而且告诉你是一个空的仓库（empty Git repository），细心的读者可以发现当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。

如果你没有看到.git目录，那是因为这个目录默认是隐藏的，用`ls -ah`命令就可以看见。

##### 把文件添加到版本库
言归正传，现在我们编写一个`readme.txt`文件，内容如下：
```
//创建第一个文件---这是文件内容---
```
一定要放到learngit目录下（子目录也行），因为这是一个Git仓库，放到其他地方Git再厉害也找不到这个文件。

把一个文件放到Git仓库只需要两步。

第一步，用命令`git add`告诉Git，把文件添加到仓库：
```shell
$ git add readme.txt
```
执行上面的命令，没有任何显示，这就对了，Unix的哲学是“没有消息就是好消息”，说明添加成功。

第二步，用命令`git commit`告诉Git，把文件提交到仓库：
```shell
$ git commit -m "new aliang readme file"
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```
这样文件就添加进本地仓库了。
这小节一共学习了三个命令哦
初始化一个Git仓库，使用`git init`命令。

添加文件到Git仓库，分两步：
- 使用命令`git add <file>`，注意，可反复多次使用，添加多个文件。
- 使用命令`git commit -m <message>`，完成。

### 四、时光机器

上一小节已经吧文件添加到本地仓库了，现在我们休息回来又要继续干活了，我们继续修改readme.txt 文件 随便改点。
```
//创建第一个文件---这是文件内容---
//这里是你编写到代码嘿嘿嘿
```
现在我们查看下改动结果使用`git status`命令来查看。
```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
如果想看文件都修改了什么内容怎么办，小问题git给我们提供了一个`git diff` 命令
`git diff`顾名思义就是查看difference，显示的格式正是Unix通用的diff格式,我这里就不演示了，有时候我看diff也懵逼。。。

接下来就是提交修改到本地仓库喽，还记得哈。
第一步是`git add`：
```shell
$ git add readme.txt
```
同样没有任何输出。在执行第二步`git commit`之前，我们再运行`git status`看看当前仓库的状态：
```shell
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   readme.txt
```
git status告诉我们，将要被提交的修改包括`readme.txt`，下一步，就可以放心地提交了：
```shell
$ git commit -m "add code"
[master e475afc] add code
 1 file changed, 1 insertion(+), 1 deletion(-)
```
提交后，我们再用`git status`命令看看仓库的当前状态：
```shell
$ git status
On branch master
nothing to commit, working tree clean
```
Git告诉我们当前没有需要提交的修改，而且，工作目录是干净（working tree clean）的。
### 五、版本回退
  ·今天先更新到这里，干活去喽 持续更新！！！·