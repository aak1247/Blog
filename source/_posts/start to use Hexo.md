        感谢铮神（[@zccz14](zccz14.com)）的博客，成功使用Travis CI和github实现了博客的自动部署。最近几天一直在折腾hexo，就讲一下自己的一点经验。因为我只用了[next主题](http://theme-next.iissnan.com)，所以以下所有的配置也都是针对next主题的，其他主题也大致相似。

## 开始使用Hexo与主题配置

        优先参考[官方文档](https://hexo.io/zh-cn/docs/)，大部分问题都可以在官方文档中找到答案。

### 开始使用

#### 安装

        hexo使用node.js编写，所以要正常地使用首先需要安装node。同时也需要安装Git，对于后续的部署和版本控制都十分有用，并且hexo的一些命令需要基于Git命令来完成，关于Git的使用可以参考[Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)。安装完成后在命令行中使用node附带的包管理器安装hexo:``npm install -g hexo-cli``。安装完成后即可使用hexo命令。

#### 使用步骤 

1.  建立文件夹``<folder>``，然后输入``$ hexo init <folder>``命令，初始化文件夹内部结构。

   其中``_config.yml``是项目的站点配置文件，记录一些全局的变量信息，可以使用yaml和json格式书写；

```yaml
   title: 黑境
   url: https://aak1247.coding.me/
   root: /
     
   author: aak1247
   description: Still waters run deep
   avatar: /images/avatar.gif
   language: zh-Hans
   theme: next
```

   ​

   ``package.json``文件是项目的node包信息，主要记录当前版本号和依赖项，是nodejs默认生成的一个东西，也是执行``npm install``命令时的依据文件，npm会根据它包含的依赖项自动安装包。

```json
   {
     "name": "hexo-site",
     "version": "0.0.0",
     "private": true,
     "hexo": {
       "version": ""
     },
     "dependencies": {
       "hexo": "^3.0.0",
       "hexo-generator-archive": "^0.1.0",
       "hexo-generator-category": "^0.1.0",
       "hexo-generator-index": "^0.1.0",
       "hexo-generator-tag": "^0.1.0",
       "hexo-renderer-ejs": "^0.1.0",
       "hexo-renderer-stylus": "^0.2.0",
       "hexo-renderer-marked": "^0.2.4",
       "hexo-server": "^0.1.2"
     }
   }
```

   ​

2.  修改配置和发布文章，这也是博客使用的主要内容。

3.  通过修改_config.yml文件修改配置。
    2. 官方推荐的方式为使用``hexo new article``建立新文章。也可以手动在source/_posts/路径下建立markdown文件来撰写文章。

4.  使用 ``hexo generate``命令生成静态博客，所有的内容位于public目录下。站点路径下以下划线开头的文件和文件夹不会被渲染。生成以后可以将public目录下的文件部署到服务器来建立可访问的博客。完成以后可以通过``hexo server``来启用本地服务器预览博客。

### 自动部署

        这一部分内容参考[@zccz14](zccz14.com)的博客[优雅地使用Hexo](https://zccz14.com/2016/12/30/%E4%BC%98%E9%9B%85%E5%9C%B0%E4%BD%BF%E7%94%A8Hexo/)，使用Travis CI自动执行脚本的方式，动态生成静态博客内容并push到git repo中，完成部署。由于github主动屏蔽了百度的内容抓取，没有域名的情况下推荐使用coding.net提供的coding pages服务（[Coding Pages 介绍](https://coding.net/help/doc/pages/index.html) ），具体使用与Github pages相同。

        在项目根目录下建立.travis.yml文件如下:

```typescript
language: node_js
node_js: 
    - "6"
before_install:
    - git config --global user.email $GITHUB_EMAIL
    - git config --global user.name $GITHUB_USERNAME
    - npm i hexo-cli -g
    - git clone $THEME_URI themes/$THEME_NAME
script:
    - hexo config theme $THEME_NAME
    - hexo generate
after_success:
    - cd public
    - git init
    - git add .
    - git commit -m "Travis Deploy"
    - git push -f -q https://$GITHUB_USERNAME:$GITHUB_PASSWORD@github.com/$GITHUB_USERNAME/$GITHUB_USERNAME.github.io master
    - git push -f -q https://$CODING_USERNAME:$CODING_PASSWORD@git.coding.net/$CODING_USERNAME/$CODING_USERNAME.coding.me master
```

        登录Travis CI，在setting中设置变量，在.travis.yml中可以通过$var方式调用变量值。配置好以后每一次对项目的提交都会使Travis CI进行一次自动部署，将生成的静态博客推送到github pages对应repo和coding pages对应repo。

### 简化配置

        站点的配置可以参考[官方文档](https://hexo.io/zh-cn/docs/)的说明，大部分设置项都有不需要修改的默认值，可以参考我上面的<a href="#使用步骤">_config.yml</a>内容，需要添加第三方插件时直接加入相关配置即可。

## 功能补充

        主要介绍一些第三方插件的接入以及几个有趣的功能的实现。

### 评论

### 

        next主题内置了对多说评论的支持，只需要在站点配置文件中加入duoshuo_shortname字段，并填入注册多说时使用的域名后缀即可，详情可以查看[多说配置](http://theme-next.iissnan.com/third-party-services.html#duoshuo)。

### 留言板

        官方的版本未提供对留言板的支持，不过这个可以通过一些方式很简单地实现。为了在导航菜单上添加留言板，需要修改主题配置文件(``path='next/'``)的menu项，加入message字段并给出页面路径，在menu_icons中加入图标的映射。

```yaml
# When running the site in a subdirectory (e.g. domain.tld/blog), remove the leading slash (/archives -> archives)
menu:
  home: /
  categories: /categories
  about: /about
  archives: /archives
  tags: /tags
  message: /message

# Enable/Disable menu icons.
# Icon Mapping:
#   Map a menu item to a specific FontAwesome icon name.
#   Key is the name of menu item and value is the name of FontAwsome icon. Key is case-senstive.
#   When an question mask icon presenting up means that the item has no mapping icon.
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
......
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

        效果参考：

<img src="http://ojtxs7ajx.bkt.clouddn.com/snipaste20170121_152642.png" class="full-image">

#### 使用github issue实现

        使用github issue也可以实现这一功能，github API为获取github的内容提供了支持，可以通过获取issue list或单个issue来实现。以单个issue的情况为例。

**RESTful API: **

```http
GET /repos/:owner/:repo/issues/:number
```

**Response:**

```json
Status: 200 OK

{
  "id": 1,
  "url": "https://api.github.com/repos/octocat/Hello-World/issues/1347",
  "repository_url": "https://api.github.com/repos/octocat/Hello-World",
  "labels_url": "https://api.github.com/repos/octocat/Hello-World/issues/1347/labels{/name}",
  "comments_url": "https://api.github.com/repos/octocat/Hello-World/issues/1347/comments",
  "events_url": "https://api.github.com/repos/octocat/Hello-World/issues/1347/events",
  "html_url": "https://github.com/octocat/Hello-World/issues/1347",
  "number": 1347,
  "state": "open",
  "title": "Found a bug",
  "body": "I'm having a problem with this.",
  "user": {
    "login": "octocat",
    "id": 1,
    "avatar_url": "https://github.com/images/error/octocat_happy.gif",
    "gravatar_id": "",
    "url": "https://api.github.com/users/octocat",
    "html_url": "https://github.com/octocat",
    "followers_url": "https://api.github.com/users/octocat/followers",
    "following_url": "https://api.github.com/users/octocat/following{/other_user}",
    "gists_url": "https://api.github.com/users/octocat/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/octocat/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/octocat/subscriptions",
    "organizations_url": "https://api.github.com/users/octocat/orgs",
    "repos_url": "https://api.github.com/users/octocat/repos",
    "events_url": "https://api.github.com/users/octocat/events{/privacy}",
    "received_events_url": "https://api.github.com/users/octocat/received_events",
    "type": "User",
    "site_admin": false
  },
  "labels": [
    {
      "id": 208045946,
      "url": "https://api.github.com/repos/octocat/Hello-World/labels/bug",
      "name": "bug",
      "color": "f29513",
      "default": true
    }
  ],
  "assignee": {
    "login": "octocat",
    "id": 1,
    "avatar_url": "https://github.com/images/error/octocat_happy.gif",
    "gravatar_id": "",
    "url": "https://api.github.com/users/octocat",
    "html_url": "https://github.com/octocat",
    "followers_url": "https://api.github.com/users/octocat/followers",
    "following_url": "https://api.github.com/users/octocat/following{/other_user}",
    "gists_url": "https://api.github.com/users/octocat/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/octocat/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/octocat/subscriptions",
    "organizations_url": "https://api.github.com/users/octocat/orgs",
    "repos_url": "https://api.github.com/users/octocat/repos",
    "events_url": "https://api.github.com/users/octocat/events{/privacy}",
    "received_events_url": "https://api.github.com/users/octocat/received_events",
    "type": "User",
    "site_admin": false
  },
  "milestone": {
    "url": "https://api.github.com/repos/octocat/Hello-World/milestones/1",
    "html_url": "https://github.com/octocat/Hello-World/milestones/v1.0",
    "labels_url": "https://api.github.com/repos/octocat/Hello-World/milestones/1/labels",
    "id": 1002604,
    "number": 1,
    "state": "open",
    "title": "v1.0",
    "description": "Tracking milestone for version 1.0",
    "creator": {
      "login": "octocat",
      "id": 1,
      "avatar_url": "https://github.com/images/error/octocat_happy.gif",
      "gravatar_id": "",
      "url": "https://api.github.com/users/octocat",
      "html_url": "https://github.com/octocat",
      "followers_url": "https://api.github.com/users/octocat/followers",
      "following_url": "https://api.github.com/users/octocat/following{/other_user}",
      "gists_url": "https://api.github.com/users/octocat/gists{/gist_id}",
      "starred_url": "https://api.github.com/users/octocat/starred{/owner}{/repo}",
      "subscriptions_url": "https://api.github.com/users/octocat/subscriptions",
      "organizations_url": "https://api.github.com/users/octocat/orgs",
      "repos_url": "https://api.github.com/users/octocat/repos",
      "events_url": "https://api.github.com/users/octocat/events{/privacy}",
      "received_events_url": "https://api.github.com/users/octocat/received_events",
      "type": "User",
      "site_admin": false
    },
    "open_issues": 4,
    "closed_issues": 8,
    "created_at": "2011-04-10T20:09:31Z",
    "updated_at": "2014-03-03T18:58:10Z",
    "closed_at": "2013-02-12T13:22:01Z",
    "due_on": "2012-10-09T23:39:01Z"
  },
  "locked": false,
  "comments": 0,
  "pull_request": {
    "url": "https://api.github.com/repos/octocat/Hello-World/pulls/1347",
    "html_url": "https://github.com/octocat/Hello-World/pull/1347",
    "diff_url": "https://github.com/octocat/Hello-World/pull/1347.diff",
    "patch_url": "https://github.com/octocat/Hello-World/pull/1347.patch"
  },
  "closed_at": null,
  "created_at": "2011-04-22T13:33:48Z",
  "updated_at": "2011-04-22T13:33:48Z",
  "closed_by": {
    "login": "octocat",
    "id": 1,
    "avatar_url": "https://github.com/images/error/octocat_happy.gif",
    "gravatar_id": "",
    "url": "https://api.github.com/users/octocat",
    "html_url": "https://github.com/octocat",
    "followers_url": "https://api.github.com/users/octocat/followers",
    "following_url": "https://api.github.com/users/octocat/following{/other_user}",
    "gists_url": "https://api.github.com/users/octocat/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/octocat/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/octocat/subscriptions",
    "organizations_url": "https://api.github.com/users/octocat/orgs",
    "repos_url": "https://api.github.com/users/octocat/repos",
    "events_url": "https://api.github.com/users/octocat/events{/privacy}",
    "received_events_url": "https://api.github.com/users/octocat/received_events",
    "type": "User",
    "site_admin": false
  }
}
```



        可以利用异步加载提高载入速度，js文件如下：

```javascript
var ghComment = $("#gh-comments");
//var ghComment_count = $("gh-comments-count");

function formatNumber(val, len) {
    var num = "" + val;
    len = len || 2;
    while (num.length < len) {
        num = "0" + num;
    }
    return num;
}

function formatDate(str) {
    var date = new Date(Date.parse(str));
    return formatNumber(date.getFullYear()) + '-' + formatNumber(date.getMonth() + 1) + '-' + formatNumber(date.getDate()) + 
            " " + formatNumber(date.getHours()) + ":" + formatNumber(date.getMinutes()) + ":" + formatNumber(date.getSeconds());
}

function loadComments(data) {
    console.log("load comments");
    document.getElementById("gh-comments-count").innerHTML = data.length.toString();
    for (var i = 0; i < data.length; i++) {
        var cuser = data[i].user.login;
        var cuserlink = 'https://www.github.com/' + data[i].user.login;
        var cbody = data[i].body_html;
        var cavatarlink = data[i].user.avatar_url;
        var cdate = formatDate(data[i].created_at);
        console.log(cbody);
        ghComment.append('<div class="gh-single-comment"><div class="commentgravatar"><a href="'+
        + cuserlink +'" target="_blank"><img src="' 
        + cavatarlink + '"></a></div><div class="commentheader"><a class="commentuser" href="' 
        + cuserlink + '" target="_blank">' + cuser + '</a><span class="commentdate">' 
        + cdate + '</span></div><div class="commentbody">' + cbody + '</div></div>');
    }
}

$.ajax('https://api.github.com/repos/aak1247/aak1247.github.io/issues/1/comments?per_page=100', {
    headers: {
        Accept: 'application/vnd.github.full+json'
    },
    dataType: 'json',
    success: loadComments
});
```

将JS文件保存到``next/js``路径下。

        添加issue.swig文件到``next/latout/_custom/``，其内容为：

```scss
<blockquote class="blockquote-center leave-message">快来留言吧！~请在 <a href="https://github.com/aak1247/aak1247.github.io/issues/1" target="_blank">Github issue</a> 页面完成操作</blockquote>
<div id="messages">
    <div id="gh-comments-info">
            <a href="javascript:void(0);">
            <span id="gh-comments-count">0</span>条留言</a>
    </div>
    <div id="gh-comments" style="opacity: 1"></div>
</div>
<script src="https://cdn.jsdelivr.net/jquery/2.1.3/jquery.min.js"></script>
<script type="text/javascript" src="{{ url_for(theme.js) }}/src/issue.js"></script>
```

        修改page.swig文件，在``<div id="posts" class="posts-expand">``中，找到``{% endif %}``在其上方加入如下：

```scss
    {% elif page.type === 'issue' %}
      {% include '_custom/issue.swig' %}
```

        使添加的issue.swig文件能够包含进来。

        然后在``index.md``中写入：

```markdown
title: 留言板
date: 2017-01-17 16:27:30
type: "issue"
comments: false
---
```

          效果如下：

<img src="http://ojtxs7ajx.bkt.clouddn.com/snipaste20170121_163304.png" class="full-image"/>       

### 搜索功能

        next主题内置了对algolia-search/tinysearch/swiftsearch的支持，同时也提供了一个local search的插件，考虑到第三方服务的不稳定性以及可能付费等等问题，推荐使用local search，只需要在站点配置文件中加入：

```yaml
search: 
  path: search.xml
  field: post
  format: html
  limit: 10000
```

        同时在package.json中加入``"hexo-generator-search": "^1.0.2"``依赖项即可。本地部署的可以手动输入``npm i``完成依赖项的安装，使用Travis自动部署时无需修改配置脚本。效果可以在[黑境](http://aak1247.coding.me/)中预览。

### 简繁切换

        这是一个通用的方法，使用js处理可以避免后端语言的差异，代码来自网上。只需要将 cookdomain修改为你的页面地址即可。链接：[tw-cn.js](https://github.com/aak1247/warehouse/blob/master/codes/js/tw-cn.js)

### 站点地图

        只需要在站点配置文件中加入：

```yaml
sitemap: 
  path: sitemap.xml
baidusitemap: 
  path: baidusitemap.xml
```

        在``package.json``中加入依赖项``"hexo-generator-sitemap": "^1.1.2"``即可。

## 优化策略

        考虑到站点的稳定性和加载速度等，可以使用延迟加载图片等方式提高响应速度。hexo还提供了一个最小化静态文件的插件``hexo-all-minifier``可以压缩html、css、js和图像文件，删除文件中多余的换行等。通过在**站点**配置文件中加入以下配置：

```yaml
html_minifier:
  enable: true
  exclude: 

css_minifier:
  enable: true
  exclude: 
    - '*.min.css'

js_minifier:
  enable: true
  mangle: true
  output:
  compress:
  exclude: 
    - '*.min.js'

image_minifier:
  enable: true
  interlaced: false
  multipass: false
  optimizationLevel: 2
  pngquant: false
  progressive: false
```

        然后安装插件（通过在``package.json``中加入``"hexo-all-minifier": "^0.0.14"``依赖项）即可实现压缩静态内容。

## 自定义方法

        以next主题为例，可以直接修改``.swig``文件修改对应页面，通过修改``page.swig``文件可以更改页面的主体内容模板，通过``page.type``字段可以进行页面类型的定义和判断。可以自定义新的``type``加入判断即可。自定义新的``.swig``文件，通过``{% include xxx %}``引入。css和js文件可以在``next/layout/_partials/custom_head.swig``中引入。建议对于已有的样式定义更高优先级的样式进行覆盖，不建议直接修改原样式。另外css的修改可以直接在``next/source/css/_custom/custom.styl``中添加。

        侧边栏的链接图标可以在``next/layout/_macro/sidebar.swig``中修改，fontawesome对于国内平台的支持不是很全面，可以使用[阿里巴巴矢量图库](http://www.iconfont.cn)添加更多图标。在文件中找到如下内容：

```scss
        <div class="links-of-author motion-element">
          {% if theme.social %}
            {% for name, link in theme.social %}
              <span class="links-of-author-item">
                <a href="{{ link }}" target="_blank" title="{{ name }}">
                  {% if theme.social_icons.enable %}
					//这里是修改的结果：
                    <svg class="icon-symbol" aria-hidden="true">
                      <use xlink:href="#icon-{{theme.social_icons[name] | default('link') | lower}}"></use>
                    </svg>
					//以上
                  {% endif %}
                  {{ name }}
                </a>
              </span>
            {% endfor %}
          {% endif %}
        </div>
```

然后在custom.styl中加入``.icon-symbol``的样式表：

```css
.icon-symbol {
   width: 1em; height: 1em;
   vertical-align: -0.15em;
   fill: currentColor;
   overflow: hidden;
}
```

随后在登录[阿里巴巴矢量图库](http://www.iconfont.cn)创建应用，并引入图库，获取到产生svg图像的js代码，并引入``next/layout/_partials/custom_head.swig`` ，即可使用与原生fontawesome图标类似的方式定义图标。