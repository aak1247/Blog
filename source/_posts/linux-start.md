title: ubuntu Linux 装机完成后的工作

category: linux

tags: 
    - ubuntu
    - 装机
    - Linux

date: 2017/07/27

---

记录一下一些我比较常用的配置。

<!--more-->

# 语言环境

## Java配置

### 安装openjdk/openjdk-jre

安装运行环境：``$ sudo apt-get install openjdk-8-jre``, 可以更改为其他版本。

安装开发环境：``$ sudo apt-get install openjdk-8-jdk``。

使用openjdk的优点在于免去手动下载和配置环境变量的步骤，方便很多，但是openjdk只包含最精简的jdk。

### 安装jdk/jre

#### 首先下载jdk

[官网](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

可以先配置FTP然后通过FTP上传到服务器，也可以直接用wget命令下载，附[下载链接](#下载链接)

##### 下载链接
[Java SE 8 server sdk for x64](http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/server-jre-8u144-linux-x64.tar.gz)

[Java SE 8 JDK for X64](http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz)

[Java SE 8 jre for x64](http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jre-8u144-linux-x64.tar.gz)

# 数据库配置

## mongodb 数据库配置

### 首先引入mongodb的GPL公钥
``sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10``

### 创建list文件

ubuntu 12.04
``$ echo "deb http://repo.mongodb.org/apt/ubuntu precise/mongodb-org/3.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.0.list``

ubuntu 14.04
``$ echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.0.list``

ubtuntu 16.04
``$ echo "deb http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.0.list``

### 更新源列表并安装mongodb

```shell
$ sudo apt-get update # 更新源列表
$ sudo apt-get install -y mongodb-org # 安装mongodb
# 要安装特定版本的mongodb命令如：sudo apt-get install -y mongodb-org=3.0.15 mongodb-org-server=3.0.15 mongodb-org-shell=3.0.15 mongodb-org-mongos=3.0.15 mongodb-org-tools=3.0.15
# 安装完成以后，可以将版本固定为当前，输入：
# echo "mongodb-org hold" | sudo dpkg --set-selections
# echo "mongodb-org-server hold" | sudo dpkg --set-selections
# echo "mongodb-org-shell hold" | sudo dpkg --set-selections
# echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
# echo "mongodb-org-tools hold" | sudo dpkg --set-selections
```

### 配置数据库文件路径和日志文件路径

配置文件为：``/etc/mongod.conf``。

### 运行mongodb数据库

``$ sudo service start mongod start``


# 服务器配置


# 其他配置

## maven 配置

### 下载maven

可以通过浏览器/ftp下载或者直接wget。终端可以使用浏览器``w3m``访问[官网](http://maven.apache.org/download.cgi)，直接使用``sudo apt install w3m``即可安装，使用十分方便。

### 解压maven包

```shell
$ sudo tar zxvf apache-maven-3.5.0-bin.tar.gz
$ mv ./apache-maven-3.5.0 /opt/  #并移到opt目录下
```

### 创建软链接

```shell
$ cd /bin
$ ln -s /opt/apache-maven-3.5.0/bin/mvn mvn
```

### 配置环境变量

```shell
$ sudo vim /etc/profile
......
export M2_HOME=/opt/apache-maven-3.5.0
export PATH=${M2_HOME}/bin:$PATH
......
```

建议先备份一下配置文件``sudo cp /etc/profile /etc/profile.bk``

### 加载配置

```shell
$ source /etc/profile
```

### 测试一下

```shell
$ mvn -v
```
