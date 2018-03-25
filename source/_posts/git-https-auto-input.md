title: Git完成HTTPS的自动账号密码填充

category: linux

tags:
 - git
 - linux
 - expect

date: 2018/03/09
---

# 前言

    本文主要介绍如何通过expect来在交互环境中实现自动填充账号密码，解决HTTPS方式需要手动输入账号密码的问题，不过能用SSH的话最好还是用ssh吧。

# 关于expect

## 简介

Expect是一个用来处理交互的命令。借助Expect，我们可以将交互过程写在一个脚本上，使之自动化完成。形象的说，ssh登录，ftp登录等都符合交互的定义。Expect的作者Don Libes在1990年 开始编写Expect时对Expect做有如下定义：Expect是一个用来实现自动交互功能的软件套件 (Expect [is a] software suite for automating interactive tools)。使用它系统管理员 的可以创建脚本用来实现对命令或程序提供输入，而这些命令和程序是期望从终端（terminal）得到输入，一般来说这些输入都需要手工输入进行的。 Expect则可以根据程序的提示模拟标准输入提供给程序需要的输入来实现交互程序执行。甚至可以实现实现简单的BBS聊天机器人。 

## 命令

1. ``send``命令
 
 接收一个字符串参数，并将该参数发送到进程。

2. ``expect``命令

``expect``通常是用来等待一个进程的反馈。expect可以接收一个字符串参数，也可以接收正则表达式参数。

    基于expect和send就可以完成与标准输入输出的交互，如：
```shell
    #!/usr/bin/expect -f 
    expect {     //返回信息匹配 
        "*yes/no" { send "yes\r"; exp_continue} //第一次ssh连接会提示yes/no,继续 
        "*password:" { send "$password\r" }  //出现密码提示,发送密码 
    } 
```
但是在git push、ftp等场景下，需要同其他进程进行交互。而要同其他进程进行交互，需要用到``spawn``命令。
3. ``spawn``命令

``spawn``命令就是用来启动新的进程的。``spawn``后的``send``和``expect``命令都是和``spawn``打开的进程进行交互的。

有了``send``, ``expect``, ``spawn``命令，就可以完成自动账号密码填充了。

4. ``interact``命令

``interact``命令用于从expect接管输入输出的自动状态转移到用户交互状态， 可以在自动提交完成以后加入``interact``命令作为结束。

# 脚本

下面就是干货：
1. 首先安装expect

```shell
$ sudo apt install expect
```

2. 编写脚本

在git 仓库底下编写push.sh:
```shell
    #!/usr/bin/expect
    set username "abc"
    set password "123456789"
    spawn git push
    expect {
            "Username*:" { send "$username\r";exp_continue}
            "Password*:" { send "$password\r"}
    }
    interact
```
每次提交时运行脚本即可。

不过最好的方式，当然还是用``ssh``。
