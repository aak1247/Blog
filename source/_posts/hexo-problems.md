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

&nbsp;&nbsp;&nbsp;&nbsp;可以将语句写在块儿级代码中，因为hexo不会渲染&lt;pre&gt;与&lt;/pre&gt;之间的内容，markdown的块儿级代码被解析为:

```html
<pre>
	<code>
	......
	</code>
</pre>
```
&nbsp;&nbsp;&nbsp;&nbsp;也可以通过利用nunjucks的raw标签进行屏蔽。


### markdown文件书写不规范

&nbsp;&nbsp;&nbsp;&nbsp;hexo所采用的markdown渲染器要求markdown文件标签前缀与内容之间间隔一个半角空格，如果不符合此规范可能会无法渲染。如标题显示###标题而不是格式化的标题。添加一个空格即可。

### https与http混合问题

&nbsp;&nbsp;&nbsp;&nbsp;HTTPS是使用SSL的安全传输协议，自然对于页面中使用HTTP协议传输的内容存在一些限制。如果在https页面中使用http资源就可能导致资源加载异常。主要的异常有以下几种：
1. js和flash资源： 直接被浏览器屏蔽
2. 其他页面资源： 弹出提示框询问是否加载或屏蔽

&nbsp;&nbsp;&nbsp;&nbsp;当然，也有正常加载的情况。

&nbsp;&nbsp;&nbsp;&nbsp;对于HTTPS页面中的HTTP资源，可以使用以下两种方式进行加载，避免可能出现的加载异常问题。

#### 使用iframe进行加载

&nbsp;&nbsp;&nbsp;&nbsp;浏览器会阻止https页面中直接请求http资源，但是如果是在HTTPS页面中嵌入的iframe中请求就不再有问题了，可以通过一个不显示的iframe完成对http资源的请求，这样就绕过了浏览器对https页面中http请求的屏蔽。

#### 使用相对协议加载

##### 相对协议

&nbsp;&nbsp;&nbsp;&nbsp;相对协议简言之就是省略掉协议类型而直接写链接路径的请求方式，例如``<img src="//baidu.com.gif">``, 浏览器会根据页面类型请求资源，例如在HTTPS页面中就使用HTTPS协议的方式请求资源。css文件也可以使用此方式。

> 注意：&lt;link&gt; 或 @import 引入样式表时使用相对协议，IE7、IE8会下载文件两次

&nbsp;&nbsp;&nbsp;&nbsp;另外，在github page中使用不支持https的资源，如果采用相对协议可能会导致加载异常缓慢，对于一部分资源（图片等）可以采用http的方式加载，当然更好的方式是采用本地文件进行加载，或者单独建立用于存储静态文件的repo。另外github其实还有一个存储其他文件的服务器，也可以将文件放在issue中通过链接访问。