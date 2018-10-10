title: win10的优雅使用

category: 配置

tags:
 - windows
 - 配置

date: 2018/09/14
---

![AutoHotKey](http://ojtxs7ajx.bkt.clouddn.com/ahk_wallpaper_reduced.jpg)

长期使用 mac/deepin 这样的系统以后再回windows会有非常大地不适应感，不够好在windows提供的灵活性使得它能够在应用层很容易具有其他系统的一些优雅特性，比如mac的全局搜索、热区、全局快捷键等。简单记录一下折腾windows的一些基本配置.



<!--more-->



# 软件篇

## wox

[wox](http://www.wox.one/)提供了类似于osx上Alfred/聚焦搜索的功能，功能非常强大。 

![wox](http://www.wox.one/images/wox_preview.jpg)



其他选项：
 [cerebro](https://github.com/KELiON/cerebro)

## cmder

[cmder](http://cmder.net)是一个windows下的命令行工具，具有一些linux终端、item具有的多标签、半透明、全局唤醒等等基本特性，另外针对windows环境下不统一的命令行环境还提供了丰富的可配置项，能够支持各种命令行，内置了``git bash``、[``wsl``](#wsl), ``powershell``, ``cmd``等命令行的支持，支持自定义新的命令行环境。

可以参考[cmder的配置](/note-about-cmder.html) (汗...还没填坑)

可以使用 wox 来定义全局的快捷键调用 cmder。

## AutoHotkey

[AutoHotkey](https://www.autohotkey.com/)是一个脚本工具，它可以通过鼠标和键盘事件来实现丰富的功能。一个比较简单的应用就是通过定时获取鼠标的位置来判定鼠标是否触及了屏幕边缘、角等，以此来实现一些任务，比如显示桌面、切换工作区、显示多任务窗口等等。

[config](https://github.com/aak1247/autoHotkeyConfig)一个能够实现鼠标移到屏幕边缘自动切换工作区、左上角显示多任务窗口等功能的脚本模板。

## chocolatey

[chocolatey](https://chocolatey.org/)是一个windows下的包管理器，基于powershell来实现，对于常用的软件尤其是编程中使用的工具支持已经非常好了。这是安装chocolatey的命令：

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

需要用管理员权限打开powershell执行。

# 功能篇

## wsl

wsl 是 windows 下的 linux 子系统，基于 windows 系统 API 实现了一个 POSIX 的内核，所以虚拟化程度比起虚拟机要低得多，但是能提供更大的权限和灵活性，比如使用 linux 下的工具和软件来操作 windows 下的文件，比起虚拟机共享数据要方便得多，也能省去双系统切换系统的麻烦。

安装过程见[启用Win10的Linux子系统](http://aak1247.coding.me/linux-on-windows.html)。

当然，缺点也是显而易见的，较低的虚拟化程度带来了安全性问题，有时候也会因为文件系统的差异（文件系统还是 ntfs ）而引起一些非常难以发现的 bug 。

# 配置篇

## 字体

可以下载[meslo](https://raw.githubusercontent.com/aak1247/shell-set-up/master/font/Meslo%20LG%20M%20Regular%20Nerd%20Font%20Complete.otf) 以支持命令行显示炫酷的Unicode字符。
