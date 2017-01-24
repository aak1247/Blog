title: hexo部署中遇到的一些问题

tags: 
 - hexo
 - 问题
 - 错误记录

category: 
 - 前端

date: 2017/01/22
---


&nbsp;&nbsp;&nbsp;&nbsp;有一种遍地是坑的感觉，还是``too young`` ，hexo官方提供的[错误文档](https://hexo.io/docs/troubleshooting.html)提供了一些提示，不过没有提供错误的log很难定位属于哪一种。遇到过的错误如下
<!--more-->
### 配置文件缺少逗号

&nbsp;&nbsp;&nbsp;&nbsp;报错信息如下：

```sh
FATAL missed comma between flow collection entries at line 33, column 11:

              "fields": [

              ^

YAMLException: missed comma between flow collection entries at line 33, column 11:

              "fields": [

              ^
```

&nbsp;&nbsp;&nbsp;&nbsp;yaml文件语言要求在每一个冒号后面追加一个空格，这也是一种良好地代码风格。

### 配置文件缺少空格

&nbsp;&nbsp;&nbsp;&nbsp;报错信息如下：

```shell
YAMLException: end of the stream or a document separator is expected at line x, column y:
```



### 渲染器的bug

&nbsp;&nbsp;&nbsp;&nbsp;如下符合swig/nunjucks语法的代码：

```scss
{{ xxx }}
{% xxx %}
```

&nbsp;&nbsp;&nbsp;&nbsp;可能会引发报错。如果写在块儿级代码块儿中没有问题，但是如果写在行内代码中\`\`之间\`\`  就可能引发报错，报错信息如：

```log
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html

Template render error: (unknown path) [Line 7, Column 23]
```

或

```log
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html

Template render error: (unknown path)
```

&nbsp;&nbsp;&nbsp;&nbsp;目前只能将语句写在块儿级代码中，因为hexo不会渲染&lt;pre&gt;与&lt;/pre&gt;之间的内容，markdown的块儿级代码被解析为:

```html
<pre>
	<code>
	......
	</code>
</pre>
```

### markdown文件书写不规范

&nbsp;&nbsp;&nbsp;&nbsp;markdown文件要求标签前缀与内容之间间隔一个半角空格，如果不符合此规范可能会无法渲染。如标题显示###标题而不是格式化的标题。添加一个空格即可。