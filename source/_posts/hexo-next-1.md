title: 如何使hexo显得自己更有逼格（一）——开始使用

tags: 

- hexo
- next主题
- 静态博客

category: 前端

date: 2017/1/20

---



​	感谢铮神（[@zccz14](http://zccz14.com/) ）的博客，成功使用Travis CI和github实现了博客的自动部署。最近几天一直在折腾hexo，就讲一下自己的一点经验。因为我只用了[next主题](http://theme-next.iissnan.com)  ，所以以下所有的配置也都是针对next主题的，其他主题也大致相似。

## 开始使用Hexo与主题配置

优先参考 [官方文档](https://hexo.io/zh-cn/docs/) ，大部分问题都可以在官方文档中找到答案。
<!--more-->
### 开始使用

#### 安装

hexo使用node.js编写，所以要正常地使用首先需要安装node。同时也需要安装Git，对于后续的部署和版本控制都十分有用，并且hexo的一些命令需要基于Git命令来完成，关于Git的使用可以参考[Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)  。安装完成后在命令行中使用node附带的包管理器安装hexo:``npm install -g hexo-cli``。安装完成后即可使用hexo命令。
  ​
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


``package.json``文件是项目的node包信息，主要记录当前版本号和依赖项，是nodejs默认生成的一个东西，也是执行``npm install``命令时的依据文件，npm会根据它包含的依赖项自动安装包。

```json
  {
    "name": "hexo-site",
    "version": "0.0.0",
    "private": true,
    "hexo": {
      "version": "3.2.2"
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
    1. 官方推荐的方式为使用``hexo new article``建立新文章。也可以手动在source/_posts/路径下建立markdown文件来撰写文章。

4.  使用 ``hexo generate``命令生成静态博客，所有的内容位于public目录下。站点路径下以下划线开头的文件和文件夹不会被渲染。生成以后可以将public目录下的文件部署到服务器来建立可访问的博客。完成以后可以通过``hexo server``来启用本地服务器预览博客。

### 自动部署

这一部分内容参考[@zccz14](zccz14.com) 的博客[优雅地使用Hexo](https://zccz14.com/2016/12/30/%E4%BC%98%E9%9B%85%E5%9C%B0%E4%BD%BF%E7%94%A8Hexo/) ，使用Travis CI自动执行脚本的方式，动态生成静态博客内容并push到git repo中，完成部署。由于github主动屏蔽了百度的内容抓取，没有域名的情况下推荐使用coding.net提供的coding pages服务（[Coding Pages 介绍](https://coding.net/help/doc/pages/index.html)  ），具体使用与Github pages相同。

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

站点的配置可以参考[官方文档](https://hexo.io/zh-cn/docs/) 的说明，大部分设置项都有不需要修改的默认值，可以参考我上面的<a href="#使用步骤">_config.yml</a>内容，需要添加第三方插件时直接加入相关配置即可。