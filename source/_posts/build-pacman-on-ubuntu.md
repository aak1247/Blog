title: 在 ubuntu 上编译安装 pacman

category: linux

tags: 
 - 笔记

date: 2019/04/04
---

为了编译一个arch的包，迫不得已需要在现有的机器上安装pacman，具体的编译过程如下：

<!--more-->

```shell
sudo apt install libarchive-dev bsdtar

git clone git://projects.archlinux.org/pacman.git
git checkout e6f72c61

cd pacman
./autogen.sh
./configure --disable-doc
make
sudo make install
```

其中可能会遇到一些依赖缺少，比如``gettext``, ``automake``, ``autopoint``, ``libcurl4-openssl-dev``, ``libarchive-dev``, ``bsdtar``，依赖补全以后重新configure即可。
