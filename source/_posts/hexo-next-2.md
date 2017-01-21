title: 如何使hexo显得自己更有逼格（二）————功能补充

tags: 

- hexo
- next主题
- 静态博客

category: 前端

date: 2017/1/21

---


主要介绍一些第三方插件的接入以及几个有趣的功能的实现。


  next主题内置了对多说评论的支持，只需要在站点配置文件中加入duoshuo_shortname字段，并填入注册多说时使用的域名后缀即可，详情可以查看[多说配置](http://theme-next.iissnan.com/third-party-services.html#duoshuo) 。

  ### 留言板

  官方的版本未提供对留言板的支持，不过这个可以通过一些方式很简单地实现。为了在导航菜单上添加留言板，需要修改主题配置文件(``path='next/'``) 的menu项，加入message字段并给出页面路径，在``menu_icons``中加入图标的映射。
<!--more-->
```yaml
  menu: 
    home: /
    categories: /categories
    about: /about
    archives: /archives
    tags: /tags
    message: /message
  menu_icons:
    enable: true
    #KeyMapsToMenuItemKey: NameOfTheIconFromFontAwesome
    home: home
    about: user
    categories: th
    schedule: calendar
    tags: tags
    archives: archive
    sitemap: sitemap
    commonweal: heartbeat
    message: comment
```

  如果这时部署，你会发现菜单栏显示的标签为"menu.message"，这时由于语言文件中无对应项所致。语言文件的路径为：``next/languages/``，如果当前使用中文，修改路径下的``zh-Hans.yml``即可

```yaml
  menu:
  
    message: 留言板
```

  完成菜单创建以后还需要创建对应页面，只需要在站点的根目录下创建一个``/message``文件夹，在文件夹下建立``index.md``文件即可，具体内容根据实现方式而定。

  #### 使用多说评论实现

  使用多说评论的实现方式比较简单而且效果比较好，可以添加最近访客，并且评论比较方便。

  ##### 添加最近访客

  对于已开启多说评论的站点加入以下代码即可，直接在``index.md``文件的结尾加入。

```html
  <div class="ds-recent-visitors" data-num-items="28" data-avatar-size="42" id="ds-recent-visitors"></div>
```

  可以修改主题默认的多说样式来定义外观，stylus文件路径为`next/source/css/_common/components/third-party/duoshuo.styl `，加入以下配置：

```css
  #ds-recent-visitors .ds-avatar {
    float:left;
  }
  #ds-recent-visitors .ds-avatar img {
    //这里是自定义的多说最近访客头像样式css
  }
```

  页面文件``index.md``可以如下定义：

```markdown
  title: 留言板
  date: 2017-01-21 15:21:45
  ---
  <blockquote class="blockquote-center">欢迎留言</blockquote>
  <br/>
  > 最近访客
  <div class="ds-recent-visitors" data-num-items="28" data-avatar-size="42" id="ds-recent-visitors"></div>
```
