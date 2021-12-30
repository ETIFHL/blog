---
author: ETIFHL
title: git-subtree
date: 2021-08-06 16:10:54
tags: git 技术 记录 学习
---

## 这玩意是啥

让项目里的一个目录可以放一个子项目，变动能直接提交到子项目，子项目的变动也能在项目内更新回来。

## 解决了啥问题

1. 功能模块化后，可以在一个项目内修改，不用来回切换。
2. 公共代码的快速迭代。（？）

## 跟 submodule 有啥区别

本项目内可以直接提交，更稳定的模块用`submodule`吧。
___

## 怎么玩

### 指令

```shell
git subtree add   --prefix=<prefix> <commit>
git subtree add   --prefix=<prefix> <repository> <ref>
git subtree pull  --prefix=<prefix> <repository> <ref>
git subtree push  --prefix=<prefix> <repository> <ref>
git subtree merge --prefix=<prefix> <commit>
git subtree split --prefix=<prefix> [OPTIONS] [<commit>]
```

### 举个栗子

1. 搞俩仓库 blog（主仓），theme（子仓）。
2. 主仓引入子仓（这个不是必要的，只是为了不用每次键入仓库地址，上面指令需要输入<repository>的地方用仓库别名替代即可。

```shell
git remote add -f <仓库别名> <仓库地址>
#如 git remote add -f theme git@github.com:USERNAME/your-theme.git
```

3. 主仓添加子仓库

```shell
# git subtree add   --prefix=<prefix> <repository> <ref>
git subtree add --prefix=theme/name theme main --squash
#                                     ↑这个就是前一步 remote 配置好的仓库别名啦
# --squash 代表只将本次操作在主仓库生成一条commit记录，子仓库的历史记录并不会合并进来。
```

4. 更新子仓库代码

```shell
# git subtree pull  --prefix=<prefix> <repository> <ref>
git subtree pull --prefix=theme/name theme main --squash
# 说明同 3
```

5. 提交子仓库代码

```shell
# git subtree push  --prefix=<prefix> <repository> <ref>
git subtree push --prefix=theme/name theme main --squash
# 说明同 3
```

### 问题是啥

```md
1. 指令麻烦。
2. 慢
3. 多了分支，分支管理策略在多人开发时更麻烦了。
```

### 指令麻烦的解决

配置 .git/config 用 alias 来快捷键入指令。

- `!`：表示外部命令而不是git命令，相当于直接在shell中运行!后的组合命令。
- `$1`：表示shell传进来的第一个参数，比如`git stpull demo/xxx`，那么`$1`就是`demo/xxx`。
- `:`：组合命令尾部的:很神奇，没有它，最后一个指令不会运行，所以它起到一个占位的作用。

```conf
[alias]
    stpull = !git subtree pull --prefix=$1 appfe $2 \
        && :
    stpush = !git subtree pull --prefix=$1 appfe $2 \
        && git subtree split --rejoin --prefix=$1 $2 \
        && git subtree push --prefix=$1 appfe $2 \
        && :
```

方法来源：[origin](https://www.cnblogs.com/jiaoyu121/p/7041726.html)

## 无了
