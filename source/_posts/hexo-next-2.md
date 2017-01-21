title: 如何使hexo显得自己更有逼格（二）————功能补充

tags: 

- hexo
- next主题
- 静态博客

category: 前端

date: 2017/1/21

---


主要介绍一些第三方插件的接入以及几个有趣的功能的实现。

  ### 评论

  ### 

  next主题内置了对多说评论的支持，只需要在站点配置文件中加入duoshuo_shortname字段，并填入注册多说时使用的域名后缀即可，详情可以查看[多说配置](http://theme-next.iissnan.com/third-party-services.html#duoshuo) 。

  ### 留言板

  官方的版本未提供对留言板的支持，不过这个可以通过一些方式很简单地实现。为了在导航菜单上添加留言板，需要修改主题配置文件(``path='next/'``) 的menu项，加入message字段并给出页面路径，在menu_icons中加入图标的映射。
<!--more-->
