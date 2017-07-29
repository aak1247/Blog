title: ubuntu 服务脚本编写

category: java

tags: 
    - java
    - 笔记
    - Linux

date: 2017/07/29

---

在进行服务器部署时，常常会需要让程序作为服务启动。使用服务的方式运行程序有以下优点：
    1. 随时控制程序运行、重启；
    2. 方便开机自启动；
    3. 启动命令更方便。


# systemd 和 init

init是一个由内核启动的用户级进程。 

　　内核自行启动（已经被载入内存，开始运行，并已初始化所有的设备驱动程序和数据结构等）之后，就通过启动一个用户级程序init的方式，完成引导进程。所以,init始终是第一个进程（其进程编号始终为1）。 
init进程作为服务的启动进程由来已久，但是其存在启动较慢、编写复杂的问题。


Systemd 就是为了解决这些问题而诞生的。它的设计目标是，为系统的启动和管理提供一套完整的解决方案。

Systemd 并不是一个命令，而是一组命令，涉及到系统管理的方方面面，对于其命令，这里不作过多介绍。在一些比较新版本的Linux中，systemd 取代了init，其进程编号为1，故服务的启动也是通过``systemd``进行管理。
可以使用``systemctl --version``命令查看``systemd``的版本。

# 主要步骤

1. 配置服务启动脚本
2. 配置命令处理脚本
3. 开机自启


# 配置服务启动脚本

ubuntu LTS 16.04的服务依赖于``systemd``的服务管理。
听说，``systemd``的配置位于``/usr/lib/systemd/system``目录下。但是在ubuntu 16.04中，服务的目录为``/lib/systemd/system``,要建立一个名为``hello``的服务，就在此目录下添加一个文件名为``hello.service``的文件即可。其内容包含：

```shell
[Unit]
Description=xxxxxxxxxxxxxxxx #描述服务 
After=network.target #用于指定服务启动的前置条件
Documentation=  #帮助文件的地址如http://baidu.com/ ，可缺省

[Service]
# User=  启动用户  
# Group=  启动用户组
ExecStart=  #服务启动命令，此项必填
# ExecStop=  服务终止命令，可缺省


# Recommended limits for for mongod as specified in
# http://docs.mongodb.org/manual/reference/ulimit/#recommended-settings

[Install] #用来定义如何启动，以及是否开机启动。
WantedBy=multi-user.target #当服务开机启动后，会放入什么文件夹，影响启动顺序
```


## 举一个例子: java web服务器

```shell
[Unit]
Description=A java server
After=network.target

[Service]
ExecStart=/usr/bin/java -jar hello.jar

# file size
LimitFSIZE=infinity
# cpu time
LimitCPU=infinity
# virtual memory size
LimitAS=infinity
# open files
LimitNOFILE=64000
# processes/threads
LimitNPROC=64000
# total threads (user+kernel)
TasksMax=infinity
TasksAccounting=false

[Install]
WantedBy=multi-user.target
```

需要注意的是， 服务启动命令需要输入绝对路径的形式，因为在服务启动时，可能相关的环境变量还未加载。

在``/lib/systemd/system``路径下添加此文件并保存为``hello.service``后，就可以通过``service hello start``命令启动服务了。
## 更详细的设定

[Unit]区块通常是配置文件的第一个区块，用来定义 Unit(在这里就是服务) 的元数据，以及配置与其他 Unit 的关系。它的主要字段如下。

    Description：简短描述
    Documentation：文档地址
    Requires：当前 Unit 依赖的其他 Unit，如果它们没有运行，当前 Unit 会启动失败
    Wants：与当前 Unit 配合的其他 Unit，如果它们没有运行，当前 Unit 不会启动失败
    BindsTo：与Requires类似，它指定的 Unit 如果退出，会导致当前 Unit 停止运行
    Before：如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之后启动
    After：如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之前启动
    Conflicts：这里指定的 Unit 不能与当前 Unit 同时运行
    Condition...：当前 Unit 运行必须满足的条件，否则不会运行
    Assert...：当前 Unit 运行必须满足的条件，否则会报启动失败


[Service]区块用来 Service 的配置，只有 Service 类型的 Unit 才有这个区块。它的主要字段如下。

    Type：定义启动时的进程行为。它有以下几种值。
    Type=simple：默认值，执行ExecStart指定的命令，启动主进程
    Type=forking：以 fork 方式从父进程创建子进程，创建后父进程会立即退出
    Type=oneshot：一次性进程，Systemd 会等当前服务退出，再继续往下执行
    Type=dbus：当前服务通过D-Bus启动
    Type=notify：当前服务启动完毕，会通知Systemd，再继续往下执行
    Type=idle：若有其他任务执行完毕，当前服务才会运行
    ExecStart：启动当前服务的命令
    ExecStartPre：启动当前服务之前执行的命令
    ExecStartPost：启动当前服务之后执行的命令
    ExecReload：重启当前服务时执行的命令
    ExecStop：停止当前服务时执行的命令
    ExecStopPost：停止当其服务之后执行的命令
    RestartSec：自动重启当前服务间隔的秒数
    Restart：定义何种情况 Systemd 会自动重启当前服务，可能的值包括always（总是重启）、on-success、on-failure、on-abnormal、on-abort、on-watchdog
    TimeoutSec：定义 Systemd 停止当前服务之前等待的秒数
    Environment：指定环境变量


[Install]通常是配置文件的最后一个区块，用来定义如何启动，以及是否开机启动。它的主要字段如下。

    WantedBy：它的值是一个或多个 Target，当前 Unit 激活时（enable）符号链接会放入/etc/systemd/system目录下面以 Target 名 + .wants后缀构成的子目录中
    RequiredBy：它的值是一个或多个 Target，当前 Unit 激活时，符号链接会放入/etc/systemd/system目录下面以 Target 名 + .required后缀构成的子目录中
    Alias：当前 Unit 可用于启动的别名
    Also：当前 Unit 激活（enable）时，会被同时激活的其他 Unit


完整配置可以查看
[官方文档](https://www.freedesktop.org/software/systemd/man/systemd.unit.html)

# 配置其他命令处理脚本

其他命令的处理与旧版本的ubuntu类似，脚本位于``/etc/init.d/``路径下，如``hello``服务，其脚本应保存为``hello``或``hello.sh``。编写脚本处理命令即可。

#  举一个栗子

```shell
# !/bin/bash
# chkconfig - 85 15
# description: hello service
# processname: hello

case "$1" in
        bye)
                echo "bye"
                exit 0
        ;;
        hello)
                echo "hello"
                java -jar hello.jar&
                exit 0
        ;;
        *)
                echo "不存在的"
        ;;
esac

exit 0
```

# 自启动

要使得服务自启动，输入

```shell 
systemctl enable hello.service
```
即可，关闭自启动输入
```shell
systemctl disable hello.service
```