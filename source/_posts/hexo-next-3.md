title: 如何使hexo显得自己更有逼格（三）——自定义与优化

tags: 

- hexo
- next主题
- 静态博客

category: 前端

date: 2017/1/22

---


## 优化策略

  考虑到站点的稳定性和加载速度等，可以使用延迟加载图片等方式提高响应速度。hexo还提供了一个最小化静态文件的插件``hexo-all-minifier``可以压缩html、css、js和图像文件，删除文件中多余的换行等。通过在**站点**配置文件中加入以下配置：
<!--more-->
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

  以next主题为例，可以直接修改``.swig``文件修改对应页面，通过修改``page.swig``文件可以更改页面的主体内容模板，通过``page.type``字段可以进行页面类型的定义和判断。可以自定义新的``type``加入判断即可。自定义新的``.swig``文件，通过
  ```scss
    {% include xxx %}
  ```
  引入。css和js文件可以在``next/layout/_partials/custom_head.swig``中引入。建议对于已有的样式定义更高优先级的样式进行覆盖，不建议直接修改原样式。另外css的修改可以直接在``next/source/css/_custom/custom.styl``中添加。

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