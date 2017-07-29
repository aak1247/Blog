title: cmder使用笔记

category: 工具

tags: 
    - cmder
    - 笔记

date: 2017/04/10

---

## cmder简介

<img class="full-image" src="http://cmder.net/img/main.jpg"/>


&nbsp;&nbsp;&nbsp;&nbsp;留坑待填

## 关于alias的配置

&nbsp;&nbsp;&nbsp;&nbsp;cmd下的配置位于``cmder/config/user-alias.cmd``，bash下的配置可以通过在``cmder/config/user-profile.sh``中加入如：``alias la='ls --all'``来进行添加。也可以在vender中对应的shell客户端配置中添加。

**黑科技**: 添加一条``alias myalias='echo "alias $*">>${config position}'``，其中``${config position}``为当前配置文件的路径，即可在以后通过``myalias``命令添加永久alias。
  
<!--more-->





