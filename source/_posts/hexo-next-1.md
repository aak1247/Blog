title: 如何使hexo显得自己更有逼格（一）————开始使用

tags: 

- hexo
- next主题
- 静态博客

category: 前端

date: 2017/1/21

---



​	感谢铮神（[@zccz14](zccz14.com) ）的博客，成功使用Travis CI和github实现了博客的自动部署。最近几天一直在折腾hexo，就讲一下自己的一点经验。因为我只用了[next主题](http://theme-next.iissnan.com)  ，所以以下所有的配置也都是针对next主题的，其他主题也大致相似。

## 开始使用Hexo与主题配置

优先参考 [官方文档](https://hexo.io/zh-cn/docs/) ，大部分问题都可以在官方文档中找到答案。

### 开始使用

#### 安装

hexo使用node.js编写，所以要正常地使用首先需要安装node。同时也需要安装Git，对于后续的部署和版本控制都十分有用，并且hexo的一些命令需要基于Git命令来完成，关于Git的使用可以参考[Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)  。安装完成后在命令行中使用node附带的包管理器安装hexo:``npm install -g hexo-cli``。安装完成后即可使用hexo命令。
  ​<!--more-->
