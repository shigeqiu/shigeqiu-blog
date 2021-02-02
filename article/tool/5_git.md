# Git

<!-- TOC -->

- [Git](#git)
  - [参考](#参考)
  - [常用命令](#常用命令)
  - [追踪关系](#追踪关系)
  - [更新远程分支列表](#更新远程分支列表)
  - [git remote](#git-remote)
  - [Git中全局忽略.DS_Store文件](#git中全局忽略ds_store文件)
    - [一、 前言 :](#一-前言-)
    - [二、 .gitignore文件用于忽略文件,其规范如下](#二-gitignore文件用于忽略文件其规范如下)
    - [三、glob模式要点:](#三glob模式要点)
    - [四、全局配置](#四全局配置)

<!-- /TOC -->

## 参考

http://www.ruanyifeng.com/blog/2014/06/git_remote.html

## 常用命令
- git push
- git pull
- git fetch 
- git merge
- git branch 
- git diff

查看远程分支

```
$ git branch -a
```


查看本地分支

```
$ git branch

```



查看工作目录树中所有的变动：

　　
```
$ git status


$ git push <远程主机名> <本地分支名>:<远程分支名>


$ git pull <远程主机名> <远程分支名>:<本地分支名>


$ git fetch <远程主机名> <远程分支名>:<本地分支名>
```




- git checkout . #本地所有修改的。没有的提交的，都返回到原来的状态
- git stash #把所有没有提交的修改暂存到stash里面。可用git stash pop回复。
- git reset --hard HASH #返回到某个节点，不保留修改。
- git reset --soft HASH #返回到某个节点。保留修改

## 追踪关系


添加追踪关系


```
$ git branch --set-upstream-to=origin/20160825_gmmc 20160825_gmmc

```
- origin/20160825_gmmc  远程分支
- 20160825_gmmc         本地分支




   查看追踪关系
 
```
git branch -vv
```

## 更新远程分支列表

有时会遇到git branch -a时总是不出现新的分支或者远程已经没有的分支在本地还有，这时就需要更新下本地的git分支保持和远程分支一致，使用下面命令即可：
```
git remote update origin --prune
```

## git remote

git remote add命令用于添加远程主机。

```
$ git remote add <主机名> <网址>
```


## Git中全局忽略.DS_Store文件

### 一、 前言 :

关于.DS_Store是什么可以参考另一文章 如果删除GIT中的.DS_Store

简单的说Mac每个目录都会有个文件叫.DS_Store，它是用于存储当前文件夹的一些Meta信息。所以每次查看Git目录的状态，如果没有add这个.DS_Store文件，会有Untracked files:的提示，add了它，又会常有Changes not staged for commit:的提示，是不是像个苍蝇一样特别烦？要解决这个烦人的小妖精，我们需要用到.gitignore文件去配置Git目录中需要忽略的文件。

###  二、 .gitignore文件用于忽略文件,其规范如下

- 1.所有空行或者以注释符号 ＃ 开头的行都会被 Git 忽略。
- 2.可以使用标准的 glob 模式匹配。
- 3.匹配模式最后跟反斜杠（/）说明要忽略的是目录。
- 4.要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

### 三、glob模式要点:

- *:任意个任意字符,
- []:匹配任何一个在方括号中的字符,
- ?:匹配一个任意字符，
- \[0-9]:匹配字符范围内所有字符


所以，我们只需要在对应的Git目录下，创建一个.gitignore文件，然后配置上.DS_Store即可，步骤如下:
- 1.命令行下使用touch .gitignore创建.gitignore文件(已有的直接到第二步)
wdw:test wdw$ touch .gitignore
- 2.open .gitignore，输入.DS_Store 换行再输入*/.DS_Store
    > 没有.gitignore直接添加保存、本来已有.gitignore的在文件最后添加即可
- 3.保存即可生效，这里是忽略了当前目录的.DS_Store以及其子目录的.DS_Store

### 四、全局配置

这样问题就解决了么？并没有，因为在今后的使用中，我发现我需要在所有Git目录下加这样的.gitignore配置，心都操碎了。有没有办法可以全局配置呢？让这个配置对所有的Git目录都生效。git config可以帮到我们。

`git config --list`命令可以让你查看现有的配置，（在这里我们就先忽略其他的配置项了，有兴趣的同学可以戳这里做更多的了解）实际上它是一个文件，文件的位置在用户根目录，名称是.gitconfig。以下是添加全局忽略文件的步骤：

```
1 创建~/.gitignore_global文件，把需要全局忽略的文件写入该文件，语法和.gitignore一样
2 在~/.gitconfig中引入.gitignore_global文件
[core] 
excludesfile = /Users/reon/.gitignore_global 
3 也可以通过git config --global core.excludesfile/Users/reon/.gitignore_global命令来实现
配置成功，可以去验证是否生效了
```
到这里，忽略文件的操作已经搞定了.
