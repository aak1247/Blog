title: 记一次自动测试失败之后的 debug

category: java

tags: 
 - CI
 - MAVEN

date: 2019/05/29
---

  在``gitlab-CI``中使用``maven test``遭遇了一次神奇的阻塞以后我进行了漫长的debug过程。

<!--more-->

# 现象


项目使用jdk8和maven3进行构建，测试基于jacoco内置的surefire插件，jacoco为0.7.9版本，surefire为2.2.0版本。在本地正常运行的自动测试在提交给``gitlab-runner``以后会在运行到某一个测试用例中陷入无穷等待。


``gitlab-runner``版本为11.5，使用的docker版本为18.0，运行与阿里云ECS上（8U 16G）。


## 先说结论

最后发现是起因是小伙伴在一个频繁调用的随机数生成中引入了SecureRandom，由于SecureRandom的随机种子依赖于操作系统提供的随机数据（在大部分linux系统中基于输入设备产生的熵池提供），而VPS（虚拟服务器）上输入设备非常少，在默认的阻塞方式中会导致整个过程陷入漫长的阻塞...解决就是在随机数不需要安全性的地方使用普通的random，然后在jvm的启动参数中添加``-Djava.security.egd=file:/dev/./urandom``，启用非阻塞式的随机数据获取。

# 问题发现过程

## 猜想1 操作系统

### 猜想与假设

本地windows环境下运行正常，而测试镜像基于linux构建（``maven:3-jdk-8``），有概率是因为操作系统的差异导致了行为的区别（事实证明确实是这样，但是也不完全是）。

### 验证

windows本地通过相同docker镜像进行构建，完全正常。

## 猜想2 ``gitlab-runner``环境差异

### 猜想与假设

好吧其实我是看见Stack Overflow上的老哥说jacoco内置的surefire插件默认的fork行为在``gitlab-runner``中会导致阻塞，看起来合情合理

### 验证

显式地声明surefire插件，把jacoco和surefire全都升到最新版，然后配置forkCount:1,forkReuse:never.: 我又双叒叕失败了

我甚至还尝试了把``threadCount``限制到1，把tomcat的启动线程数调低，全都没用。。。


## 猜想3 硬件资源

### 猜想与假设

自动构建的docker容器通过``gitlab-runner``启动， 而``gitlab-runner``的默认启动参数未知，有概率是由于docker中提供的资源不足导致运行无法正常运行。

### 验证

1. 在服务器上手动通过``docker``完成测试，同样阻塞（证明非启动参数差异，同时也不是``gitlab-runner``的锅）
2. 在服务器上通过直接调用``maven``进行测试，同样阻塞（证明完全不是docker的问题）
   （到这里，我发现我本地的jdk是12版本, 又通过在服务器12版本的镜像进行构建，依然异常，已经有很大概率证明是硬件资源问题了。（确实是这样，但是我依然没想到是输入设备））
3. 猜想问题在于服务器资源不足（我甚至以为是阿里云太坑），不能够提供足够的资源完成测试（naive!），于是在本地的linux下进行测试，确认能够正常运行（好像已经破案了？）
4. 于是在另一台鹅家的服务器上进行测试：1 硬件资源充足 2 基本没在用。全套跑一遍，依然失败
5. 通过``jconsole``监控服务器上运行的构建，发现无死锁，而阻塞时线程数内存占用和CPU占用都没在峰值

这个测试像是真的在睡觉。。。

## 猜想4 代码的锅

### 猜想与假设

只剩下这么想了

### 验证

没辙了，二分查错吧。debug技能极差，服务器环境下只会用log了...

然后发现是在一个``random.nextInt``那里卡住了。 
？？？全程黑人问号脸，然后终于发现这不是一个一般的random

### 结论

以后有问题还是先看代码吧。。。浪费了一天多的时间 好想哭QAQ

# 最后

学会了通过jconsole来进行监控，方法是在jvm的启动参数中加入

```shell
-Djava.rmi.server.hostname=xxx.xxx.xxx.xxx \ 
-Dcom.sun.management.jmxremote.port=8999 \ 
-Dcom.sun.management.jmxremote.authenticate=false \ 
-Dcom.sun.management.jmxremote.ssl=false
```

然后就可以通过在本地运行jconsole(jdk/bin目录下)来监控运行情况啦， 地址``xxx.xxx.xxx.xxx:8999`` 这样就好。

## Further Reading

[再谈随机数引起的阻塞问题](http://hongjiang.info/java8-nativeprng-blocking/)
[CentOS 7 下Tomcat启动超慢的原因及解决方案](https://www.jianshu.com/p/de2df2d92d92)
[JVM上的随机数与熵池策略](http://hongjiang.info/jvm-random-and-entropy-source/)
[The Right Way to Use SecureRandom](https://tersesystems.com/blog/2015/12/17/the-right-way-to-use-securerandom/)