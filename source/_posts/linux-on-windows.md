title: 启用Win10的Linux子系统

category: Windows

tags: 
 - Windows
 - Linux

date: 2017/02/06

---

<img src="//ojtxs7ajx.bkt.clouddn.com/627e58c18900b84e667585fc21cf4e7b.jpg" class="full-image">

## Linux on Windows简介

&nbsp;&nbsp;&nbsp;&nbsp;Win10一周年版推出了用于Windows的Linux子系统这一功能，最近体验了一下感觉极佳。不说别的，有一个完整的Linux环境就可以完爆一切bash环境了，痛快地卸载了Git for Windows/Cygwin/minGW/msys/babun吧！

<!--more-->

&nbsp;&nbsp;&nbsp;&nbsp;Linux子系统和Windows的结合真是有一种神互补。子系统可以挂载windows的文件系统，于是就可以在现有的文件系统下干一个完整的Linux环境所能干的事情。软件丰富更适合日常使用的Windows与开源且适合开发的Linux结合使得开发更加简便，不用再考虑双系统虚拟机这种效率不高的结合方式了，Linux on Windows用一种应用软件的方式实现了系统软件的功能，这一点可以说让人惊艳了。重要的是这样同时运行的两个系统并不是相互独立的，而是可以协作的，以后完全可以使用Windows下的工具进行编码，然后在Linux子系统进行Git提交，当然，它所能够发挥的作用远远不仅于此。


## 如何启用

### 启用Linux子系统

#### 启用开发者模式

&nbsp;&nbsp;&nbsp;&nbsp;系统模式的设定位于所有设置->更新和安全->针对开发人员, 更改后如下：

<img src="//ojtxs7ajx.bkt.clouddn.com/snipaste20170206_210255.png" width="100%">

#### 更改系统功能

&nbsp;&nbsp;&nbsp;&nbsp;使用``win+X``快捷键调出系统管理菜单后点击程序与功能，选择启用或关闭Windows功能，修改“适用于Windows的Linux子系统一项”，勾选之，确认并重启。

#### 安装系统

&nbsp;&nbsp;&nbsp;&nbsp;使用cmd下载Linux系统。打开功能以后系统中其实还没有安装Linux，需要使用cmd完成安装，这一步可能比较漫长。如果PC中已经安装了bash工具如msys(git for Windows)，打开管理员权限的cmd，输入``bash``，按照提示一步一步下载即可。下载的过程比较漫长，耐心等待。

下载完成以后配置用户名密码即可愉快地使用ubuntu啦！

### 启用root权限并建立用户

&nbsp;&nbsp;&nbsp;&nbsp;接下来可以输入``sudo passwd root``建立root用户（最高权限）。如果需要的话可以再使用``adduser username``建立一个新user(不建议使用useradd), 用户的切换通过``su username``完成。

切换到root用户输入``su``即可。

### 完成升级

&nbsp;&nbsp;&nbsp;&nbsp;启用了root用户，接下来操作就比较方便了，先使用``su``切到root用户，然后直接使用apt-get完成软件包的升级。（``apt-get update``和``apt-get upgrade``）。

&nbsp;&nbsp;&nbsp;&nbsp;如果升级过程中出现连接错误（_\_Assertion\__、_method dies_），先把命令再输一次试试。如果cmd保持一个类似于死机的状态太久（比如20分钟），先按一次``ctrl+C``试试。

## 基本配置

### 修改源

&nbsp;&nbsp;&nbsp;&nbsp;源的修改有两种方式：
1. 通过修改源列表；
2. 通过``apt-add-repository``命令。

&nbsp;&nbsp;&nbsp;&nbsp;源列表文件位于``/etc/apt/sources.list``，可以使用vim直接完成修改，建议先保存原文件``cp /etc/apt/sources.list /etc/apt/sources.list.backup``,然后再使用vim编辑，将找到的源直接加在列表后面。使用cmd开vim比较方便的在于可以直接将系统剪贴板的内容帖进去。在插入模式下(按``i``进入)将光标移到合适的位置，然后鼠标右键点击cmd标题栏，编辑->粘贴即可。修改完成以后按``Esc``退出插入模式，输入``:x``并回车保存并退出。完成以后可以``apt-get update``和``apt-get upgrade``来一次。

### 升级系统

&nbsp;&nbsp;&nbsp;&nbsp;输入``lsb_release -a``可以看到当前的系统版本为：ubuntu 14.04.4 LTS, 最新版本为16.04.1 LTS, 可以更新一下。

1. ``apt-get install update-manager``安装更新管理器；
2. ``do-release-upgrade -f DistUpgradeViewNonInteractive -d``完成更新。

如果上述步骤没有达到更新效果，可以再尝试以下步骤：
1. ``dpkg --configure -a``
2. ``apt-get update``，更新包列表，这一步可能会询问是否继续，YES即可。
3. ``apt-get upgrade``，升级软件包；
4. ``apt-get dist-upgrade``，完成升级；
5. ``apt-get autoremove``，自动删除不需要的包。

如果遇到网络问题，参照[完成升级](#完成升级) ，解决。完成以后再输入``lsb_release -a``就是金光闪闪的16.04啦~

## 优雅地使用 

### 使用zsh

&nbsp;&nbsp;&nbsp;&nbsp;ubuntu的默认shell使用bash，但是外观不太好看而且功能不够强大。zsh是一个相对好用一点的shell，可以使用zsh替换默认shell。

#### 使用步骤

##### 安装zsh

```shell
    $ apt-get install zsh
```

##### 安装wget

```shell
    $ apt-get install wget
```

##### 执行oh-my-zsh配置脚本

```shell
    $ wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

##### 替换默认shell

```shell
    $ chsh -s /bin/zsh
```

&nbsp;&nbsp;&nbsp;&nbsp;到这里zsh的安装就完成啦，可以关闭命令行然后重新输入``bash``进入Linux，然后输入``su``登录root账户查看效果。这时可以输入``echo $SHELL``查看当前的shell版本。

&nbsp;&nbsp;&nbsp;&nbsp;**注意**：输入``bash``进入Linux后其实是一种未登录的状态，需要输入``su username``切换到指定用户才能正常使用。

#### zsh配置

&nbsp;&nbsp;&nbsp;&nbsp;尽管zsh非常强大，但是默认设定还是不够完美，可以通过修改``/root/.zshrc``文件完成配置的更改，修改完成以后输入``source /root/.zshrc``即可查看效果。

&nbsp;&nbsp;&nbsp;&nbsp;zsh的主题可以在[这里](https://github.com/robbyrussell/oh-my-zsh/wiki/themes) 找到，修改文件中的``ZSH_THEME="robbyrussell"``即可更改主题。不过由于cmd本身使用单个字体文件显示字符，所以一些很漂亮的主题可能不太好用，建议选择没有太多特殊符号的主题。由于zsh的配置其实是一件非常有趣的事情，故其包含的内容也非常多，这里就不多说了。

### cmd设定

&nbsp;&nbsp;&nbsp;&nbsp;尽管zsh非常漂亮，但是在cmd中的显示依然要通过cmd的支持，所以建议对cmd也进行一些修改，右键标题栏->默认值可以设定默认属性，属性项里的设定可以设定本次会话的显示效果。cmd默认使用点阵字体，但是不太好看，推荐使用TrueTypeFont字体。下载好TrueTypeFont字体文件并安装后如果在cmd中无对应选项，可以修改注册表中关于concole的设定。

&nbsp;&nbsp;&nbsp;&nbsp;找到``HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Console\TrueTypeFont\``添加项即可。如添加000项，值为要添加的字体名。

## 进阶

&nbsp;&nbsp;&nbsp;&nbsp;[w2qw](https://www.reddit.com/user/w2qw)  提供了一种通过bash运行图形界面软件的方法。主要步骤如下：
1. 在Linux中安装图形界面的程序（如Firefox）
2. 安装[ Xming X Server for Windows ](https://sourceforge.net/projects/xming/)
3. 在Linux中输入``DISPLAY=:0 firefox``）。

&nbsp;&nbsp;&nbsp;&nbsp;有兴趣可以体验一下~