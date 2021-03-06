---
title: 利用Git分支管理博客资源
date: 2019-03-18 19:14:18
tags:
- myblog
categories:
- 博客维护
photo:
---

{% note default %}
从搭建好个人博客，经过间断性的更新一些布局和少数博客之后，在这个过程中发现更好的管理个人博客的一种方式，也就是通过分支来管理，将生成的静态文件提交到GitHub个人仓库的master分支，源代码提交到同一个仓库的source分支。当然，master是主分支（但是因为master一开始用来提交静态文件，所以后面我将source设为默认分支）。我将记录在这个过程中一些步骤和需要注意的地方。
{% endnote %}

<!-- more -->
### 1. 初始化本地的版本库
用到git命令：git init
首先是要在你的博客根目录的路径下执行这个命令，成功之后会在产生一个.git文件夹，git\refs\heads\下是你的本地分支(这里说的可能不对啊，有待考证)，master（主分支）是初始化库的时候自动默认生成的，.git\refs\remotes下是你的远程仓库
这个时候输入命令：git remote
会输出与你本地库相关联的远程仓库，当然，如果第一次是没有的。

### 2. 建立与远程仓库的连接
接下来就可以建立与远程仓库的连接，使用命令：git remote add origin 你的GitHub仓库地址
origin是定义远程仓库在本地的名字，当然也可以指定其他的名字，我的是myblog(之前是blog，包括下面3.还是blog)。
成功之后，再执行：git remote
会看到已经建立关联的远程仓库，当然名字是你自己取的，在我这，查看.git\refs\remotes\下会看到myblog文件夹。(反斜杠是在windows环境下，Linux环境下是斜杠，我有时候用cmd，有时候用git工具，斜杠不用关注)
这个时候我们输入git remote看一下：
```
10184@DESKTOP-KPP8OQ8 MINGW64 ~/blog (master)
$ git remote
myblog
```
这个myblog是我们在本地**称呼**关联的那个远程仓库

### 3. 提交源文件
这个时候其实已经可以提交文件了，常用使用命令：
```bash
git add .
git commit -m "1 push code"
git push -u blog HEAD:source
```
这三个命令就是提交文件要用到的git命令，commit -m 后面是本次提交的注明信息，可以不写，但是好习惯还是要写上。
因为我们的目的就是用不同分支来充分管理我们的博客，所以最后一条命令对于第一次这么做的人是比较重要的。
HEAD是版本库的头指针的意思，-u 意思是让本地分支与远程分支建立连接，所以我们后面紧跟着你要在你远程仓库建立的新分支，在这里我是souorce，这条命令的意思就是将本地库的最新版本代码提交到远程仓库blog（这里blog是远程仓库在本地的名字,）的source分支上去。
当然，这个时候远程仓库还没有source分支，命令执行，就在远程仓库建立一个名叫source的分支，并将它与本地**当前**分支建立关联。注意，**这个时候我本地当前分支是master，所以现在与远程分支source建立连接的是我本地的master**。

### 4. 设置GitHub仓库的默认分支为新建的分支
这个时候，其实已经将本地的源文件提交到远程仓库的source分支下了。具体去查看会发现你的仓库多了一个source分支，点击后，会出现你的博客源文件，说明已经成功了。这个时候还需要把你的这个仓库的默认分支设为新建的分支（我这是source），这样后续直接git push就OK了。
20190701更新，最近我直接git push不行了。目前还不知道是什么原因。

### 本人犯的错误
其实这个时候已经完全OK了，但是由于我对git的不熟悉，导致我看到本地的Git Bash里命令符最后是（master）之后，我误以为这是远程分支master，也就是远程仓库的分支master。紧接着我以为这样子我提交文件是提交到远程分支master，真是把我学糊涂了，搞懵了。
这里的master就是本地分支master，是你初始化本地版本库的时候自动生成的，经过一系列操作之后，其实已经OK了，后续也可以**继续通过本地master提交文件到远程分支source**。
但是，我当时已经懵了，所以我一顿操作猛如虎，恍然大悟二百五。**这也造成了我后面的深刻理解**。
我当时想，我得把（master）变成（source）才对啊。
于是，我又去找各种相关的信息，终于新建立了一个<font color="red">**本地分支**</font>source（滑稽）。
然后我觉得已经OK了，完美，然后，
出错，后面懂了，当前是本地分支master跟踪远程分支source，也就是说当前是本地master与远程source建立关联
而我新建的这个**本地source**是没有与远程source建立关联的。
所以我又一次：git push -u blog HEAD:source，提示：
{% note default %}
Branch source set up to track remote branch source from myblog
{% endnote %}
意思是**设置分支“source”来跟踪来自“myblog”的远程分支“source”**。
### 问题又来了
我以为大功告成，但是由于我之前的懵逼，中间做了一些懵逼举动。导致我以为终于OK了之后，再次提交，在commit的时候就出错
提示我没有改动，也就是不能提交一模一样的，所以会报错，根本就提交不上去，但是我之前改了呀（我是在本地master状态下改了但是没有提交，想一想，应该是这样的），我去看看改的地方，发现它恢复了？

我还以为还是出错了呢
后面我试着重新改了，再次提交，这时候提交成功！这个时候完全不知道怎么回事，持续懵逼。
虽然成功了，我还是很疑惑，所以我想会不会是本地的两个分支存储的信息不一样？我就在当前分支下
又改了文件，这个时候我没有提交，而是直接去切换分支，我试着把当前分支改为之前的master，结果报错：
{% note default %} 
$ git checkout master
error: Your local changes to the following files would be overwritten by checkout:
source/\_posts/测试.md
Please commit your changes or stash them before you switch branches.
Aborting
{% endnote %}
意思是在当前分支下的改动没有被提交，而切换分支会覆盖掉当前的改动，
请先将改动提交到远端。命令终止

当我看到这的时候，我发现两个分支所保存的本地信息是不一样的,证明我的猜想是对的。
后面我看了出自<a href="https://blog.csdn.net/AsheAndWine/article/details/79003270">这里</a>的文章，逐渐知道了我之前的错误。这篇文章主要是讲**Git如何在不提交当前分支的情况下切换到其它分支进行操作——git stash**
看了之后，我就逐渐懂了本地分支。
但是我不明白为什么呀。
于是，又看了出自<a href="https://www.cnblogs.com/matengfei123/p/8252128.html">这篇</a>文章，讲的是**对于Git分支的理解**
主要就是说不同的本地分支是在一条时间线上，我这里，master和source其实就是指针，指向各自的时间线上的版本。HEAD指向当前本地分支。
所以当我不停更改内容时，source已经走远了，但是master还在原地。也就解释了切换本地master为什么会出错。具体看图比较清晰。
图出自**对于Git分支的理解**
<img src="https://images2015.cnblogs.com/blog/925240/201604/925240-20160423181854210-135268393.png">
图的意思是，**dev分支做了改动，提交了之后切换到master。所以这时候master还是改动之前的内容！**
讲的十分不错。对于Git分支也有了自己的理解。最后也发现了自己之前的撞树行为，但是，我也学到了一些东西，也十分不错。
这里把后续补上：
```bash
git add .
git commit -m "3 push code"
git push
```
提示成功!

切换回master(玩玩，哈哈)
{% note default %}
$ git checkout master //切换分支到master
Switched to branch 'master'
M       themes/next
M       themes/next-reloaded
Your branch and 'myblog/source' have diverged,
and have 1 and 2 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)
{% endnote %}
意思是：
切换到分支“master”
主题/下一个
主题/ next-reloaded
你的分支和“我的博客/资源”已经分道扬镳，
分别有1个和2个不同的提交。
(使用“git pull”将远程分支合并到您的分支中)
看到这的时候我基本上都懂了，也就是说基本的一些机制和原理我已经知道了，也明白了为什么出错，以及该如何去解决。

20190701更新（这个问题其实很早就解决了），上述其实讲的就是git这个版本控制管理工具的核心。但是我后面觉得我就搞个博客没有必要，然后想把本地分治source合并到master，然后删除本地分支source，但是合并出了问题。具体问题想不起来了，好像就是因为我的本地master分支上多了一些文件或是少了一些文件，与source上不一样（source删除或者增加了）。导致不能合并还是啥，反正就是合并要满足的一些东西。后面我好像是删了本地库，重新来了一遍。心累。这个东西用的不熟悉，还是谨慎为好。
后面再学习学习。

后期更新或另起博文。
--- 

