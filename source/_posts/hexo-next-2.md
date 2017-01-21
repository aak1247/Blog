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
  var ghComment = $("#gh-comments") ;
  //var ghComment_count = $("gh-comments-count") ;

  function formatNumber(val, len)  {
      var num = "" + val;
      len = len || 2;
      while (num.length < len)  {
          num = "0" + num;
      }
      return num;
  }

  function formatDate(str)  {
      var date = new Date(Date.parse(str) ) ;
      return formatNumber(date.getFullYear() )  + '-' + formatNumber(date.getMonth()  + 1)  + '-' + formatNumber(date.getDate() )  + 
              " " + formatNumber(date.getHours() )  + ":" + formatNumber(date.getMinutes() )  + ":" + formatNumber(date.getSeconds() ) ;
  }

  function loadComments(data)  {
      console.log("load comments") ;
      document.getElementById("gh-comments-count") .innerHTML = data.length.toString() ;
      for (var i = 0; i < data.length; i++)  {
          var cuser = data[i].user.login;
          var cuserlink = 'https://www.github.com/' + data[i].user.login;
          var cbody = data[i].body_html;
          var cavatarlink = data[i].user.avatar_url;
          var cdate = formatDate(data[i].created_at) ;
          console.log(cbody) ;
          ghComment.append('<div class="gh-single-comment"><div class="commentgravatar"><a href="'+
          + cuserlink +'" target="_blank"><img src="' 
          + cavatarlink + '"></a></div><div class="commentheader"><a class="commentuser" href="' 
          + cuserlink + '" target="_blank">' + cuser + '</a><span class="commentdate">' 
          + cdate + '</span></div><div class="commentbody">' + cbody + '</div></div>') ;
      }
  }

  $.ajax('https://api.github.com/repos/aak1247/aak1247.github.io/issues/1/comments?per_page=100', {
      headers: {
          Accept: 'application/vnd.github.full+json'
      },
      dataType: 'json',
      success: loadComments
  }) ;
```

  将JS文件保存到``next/js``路径下。

  