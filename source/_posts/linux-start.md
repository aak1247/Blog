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

# 基础配置

## shell

主要配置 zsh 替代默认的bash, 配置地址[shell start up](https://github.com/aak1247/shell-set-up)。
```shell
sudo apt install zsh git &&\
sh -c "$(wget https://raw.githubusercontent.com/aak1247/shell-set-up/master/install.sh -O -)"
```

## vim

![截图](http://qiniu.cdn.hive-intel.com/aak1247/%E6%B7%B1%E5%BA%A6%E6%88%AA%E5%9B%BE_20180920140817.png)

简单的基本vim配置，语法高亮、自动缩进等、状态栏配置等。
```shell
git clone --depth=1 https://github.com/aak1247/vimrc.git ~/.vim_runtime
sh ~/.vim_runtime/install_awesome_vimrc.sh
```

## tmux

很好看的tmux配置。

```shell
sh -c "$(wget https://raw.githubusercontent.com/aak1247/.tmux/master/install.sh -O -)"
```


# 语言环境

## node配置

### 安装nvm

```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

### 通过 nvm 安装 node

```shell
nvm install stable
nvm use stable
```

在zsh下，可能需要（如果也安装了bash）手动将 nvm 环境变量加入 ~/.zshrc文件末尾:

```bashrc
export NVM_DIR="$HOME/.nvm"
 [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
 [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

然后更新一下配置：
```shell
source ~/.zshrc
````

### 安装yarn

```shell
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update && sudo apt-get install --no-install-recommends yarn
```

### 配置淘宝镜像

#### npm 更换源

```shell
npm config set registry http://registry.npm.taobao.org/
```

#### 安装 cnpm

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

#### yarn 更换源

```shell
yarn config set registry http://registry.npm.taobao.org/
```

## python配置

### 安装 python/pip 和 pipenv

可以通过apt方式安装

## Java配置

### 安装 openjdk / openjdk-jre

安装运行环境：``$ sudo apt-get install openjdk-8-jre``, 可以更改为其他版本 更改数字即可。

安装开发环境：``$ sudo apt-get install openjdk-8-jdk``。

使用openjdk的优点在于免去手动下载和配置环境变量的步骤，方便很多，但是``openjdk``与oracle jdk所包含的库有所差异，jdk9及以后版本的openjdk还存在一些其他问题。

### 安装oracle jdk/jre

#### 首先下载jdk

[官网](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

可以先配置``FTP``然后通过``FTP``上传到服务器，也可以直接用``wget``命令下载，附[下载链接](#下载链接)

##### 下载链接
[Java SE 8 server sdk for x64](http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/server-jre-8u144-linux-x64.tar.gz)

[Java SE 8 JDK for X64](http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz)

[Java SE 8 jre for x64](http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jre-8u144-linux-x64.tar.gz)

## python环境

### 安装
```shell
sudo apt install python-pip #安装自带Python2.7对应的pip
sudo apt install python3 #安装python3
```

### anaconda

```shell
curl -O https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh
sh Anaconda3-5.2.0-Linux-x86_64.sh
```

### coda

```shell
wget https://developer.nvidia.com/compute/cuda/10.0/Prod/local_installers/cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64
sudo dpkg -i cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64.deb

```

### pytorch



### tensorflow



# 数据库配置

## mongodb 

``sudo apt install mongodb``

### 配置数据库文件路径和日志文件路径

配置文件为：``/etc/mongodb.conf``。

### 运行mongodb数据库

``$ sudo service start mongodb start``


# 服务器配置

留坑


# 其他配置

## maven 配置

似乎不用怎么配置，直接安装即可
``sudo apt install maven``

```shell
$ mvn -v
```

# 干货

## tmux+vim+oh-my-zsh安装脚本

留坑待填

## 全家桶脚本

留坑填
