title: ruby学习笔记（一）安装和基本语法

category: ruby

tags:
 - ruby
 - 学习笔记

date: 2017/02/11

---

## 关于ruby

### 为什么学习ruby

&nbsp;&nbsp;&nbsp;&nbsp;和Python一样，Ruby也是一个相当开发者友好的语言，遵循着最小惊讶原则设计的ruby语言甚至在一定程度上比起Python更加容易学习和使用。ruby on rails在web运用相当广泛，在学习成本不高的前提下学习ruby还是相当合算的。
<!--more-->
### 学习资源

&nbsp;&nbsp;&nbsp;&nbsp;[ruby.org](http://www.ruby-lang.org/zh_cn/documentation/)
&nbsp;&nbsp;&nbsp;&nbsp;[菜鸟教程](http://www.runoob.com/ruby/ruby-tutorial.html)

## 安装和使用

### 安装

&nbsp;&nbsp;&nbsp;&nbsp;Linux下直接``apt-get install ruby``就好，Windows下有安装包。嗯不多说了...

### 命令行

&nbsp;&nbsp;&nbsp;&nbsp;bash下输入``irb``即可进入ruby的交互式命令行工具，用来学习语法命令行很棒。退出irb命令行输入``exit``、``quit``或<kbd>CTRL</kbd>+<kbd>D</kbd>。也可以输入``ruby``进入命令行，输入<kbd>CTRL</kbd>+<kbd>D</kbd>运行,但是体验大概不太好。

### 运行脚本

&nbsp;&nbsp;&nbsp;&nbsp;ruby源文件后缀为``rb``编写一个``test.rb``然后在bash中输入``ruby test.rb``即可运行。


## 基础

### 第一个ruby程序

&nbsp;&nbsp;&nbsp;&nbsp;新建一个``helloworld.rb``在里面输入``puts "hello world"``，保存并运行。

```ruby
$ ruby helloworld.rb
hello world
```

&nbsp;&nbsp;&nbsp;&nbsp;嗯，和ruby的幸福生活开始了呢。

### 基本语法

#### 输入输出


##### 标准输入输出
&nbsp;&nbsp;&nbsp;&nbsp;输入使用``gets``，标准的输出使用``puts``或``print``，两者用法相似，不同之处在于``puts``在输出完成以后会进行一次换行，相当于Java的``println()``,但是当输出字符串结尾是换行时两者功能一致，单个字符的输出也可以使用``putc``。注意：没有``getc``。

&nbsp;&nbsp;&nbsp;&nbsp;example：

```ruby
    val=gets        #在控制台输入hello
    puts val        #输出hello(换行)
    puts "hello"    #输出hello（换行）
    print val       #输出hello（换行）
    print "hello"   #输出hello（不换行）
    putc val        #输出为h
    putc "hello"    #输出为h
```

##### here document

&nbsp;&nbsp;&nbsp;&nbsp;ruby中有一种here document用于输出多行字符串。here document通过``<<``运算符指定结束标记，输出结束标记前的所有文本。结束标记可以用双引号框起来也可以不狂

&nbsp;&nbsp;&nbsp;&nbsp;example：

```ruby
  print <<EOF
      this is a string;
  EOF
  print <<"10",<<"20"
      123456789
  10
      11 12 13 14 15 16 17 18 19
  20
  puts <<hello,<<"good bye"
 
  that's where we met
 
  hello
 
  that's where we were together
 
  good bye
```


##### 文件输入输出

1. 文件打开：File.open()和File.new()，两者的不同在于open()可以与块关联，关于块的内容留在后面介绍。
2. 文件关闭： File.close()
3. 读文件：sysread()
4. 写文件： syswrite()

&nbsp;&nbsp;&nbsp;&nbsp;看例子：
```ruby
# file new
afile = File.new("../../res/input.txt","r+")
if afile
    # read data
    content = afile.sysread(20)
    puts content
    # write data
    content = gets
    afile.rewind
    afile.syswrite(content)
else
    puts "open failure"
end
# file close
afile.close
# file.open()
File.open("../../res/input.txt","r") do |aFile|
    content = aFile.sysread(20)
    puts content
end
```

&nbsp;&nbsp;&nbsp;&nbsp;上面代码中的|aFile|用于定义局部变量。File.new()和File.open()的第二个参数为打开模式，有一下选项：
1. r: 只读模式
2. w: 只写模式（覆盖）
3. a: 只写模式（追加）
4. r+/w+/a+: 读写模式，区别是r+和w+的文件指针初始位于文件头，a+的文件指正初始位于文件尾；r+在找不到文件时会报错，w+和a+在找不到时会创建新文件。

源码链接：&nbsp;&nbsp;&nbsp;&nbsp;[源码](https://github.com/aak1247/ruby-learning/tree/master/source/chap1)


&nbsp;&nbsp;&nbsp;&nbsp;嗯...不小心入了坑，只是记录一下，应该不会花太多时间去学ruby了233