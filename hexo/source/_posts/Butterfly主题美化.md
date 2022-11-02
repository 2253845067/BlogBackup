---
title: Butterfly主题美化
date: 2022-06-17
updated: 2022-06-22
description: Butterfly主题美化
tags: 
  - Butterfly
  - Hexo
categories: 
  - 教程
top_img: https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/VJYMpAyESFGR2v7.jpg
cover: https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/953ef369-0f53-417b-b8a5-0d7551ee8bda.jpg
---

# 一图流背景与顶部图修改

## 设置图片

这个是 [Butterfly](https://butterfly.js.org/) 自带的功能，修改主题的配置文件 `_config.butterfly.yml`。

编辑 `index_img`、`background`、`footer_bg` 选项。
设置网站背景，并将主页顶部图和页脚背景改为透明。
(需要将以下示例地址替换为自己的图片地址。)

```yaml
# Image (圖片設置)
# --------------------------------------

# The banner image of home page
index_img: transparent

# Beautify/Effect (美化/效果)
# --------------------------------------

# Website Background (設置網站背景)
# can set it to color or image (可設置圖片 或者 顔色)
# The formal of image: url(http://xxxxxx.com/xxx.jpg)
background: url(https://example.com/img/background.jpg)

# Footer Background
footer_bg: transparent
```

## 引入相关样式

新建一个文件，位于 `source/css/modify.styl`，并增加以下内容。
(此处只是举例，也可以使用自己已有的样式文件。)

```styl
@import 'nib'

// 顶部图
#page-header
  &, &:before
    background: transparent !important
  &.post-bg, &.not-home-page
    height: 280px !important
  #post-info
    bottom: 40px !important
  #page-site-info
    top: 140px !important

  @media screen and (max-width: 768px)
    &.not-home-page
      height: 200px !important
    #post-info
      bottom: 10px !important
    #page-site-info
      top: 100px !important

.top-img
  height: 250px
  margin: -50px -40px 50px
  border-top-left-radius: inherit
  border-top-right-radius: inherit
  background-position: center center
  background-size: cover
  transition: all 0.3s

  @media screen and (max-width: 768px)
    height: 230px
    margin: -36px -14px 36px

  [data-theme='dark'] &
    filter: brightness(0.8)

// 页脚
#footer:before
  background-color: alpha(#FFF, .5)

  [data-theme='dark'] &
    background-color: alpha(#000, .5)

#footer-wrap, #footer-wrap a
  color: #111
  transition: unset

  [data-theme='dark'] &
    color: var(--light-grey)
```

在主题配置文件 `_config.butterfly.yml` 的 `inject.head` 引入样式。

```yaml
# other (其他)
# --------------------------------------

# Inject
# Insert the code to head (before '</head>' tag) and the bottom (before '</body>' tag)
# 插入代码到头部 </head> 之前 和 底部 </body> 之前
inject:
  head:
    - <link rel="stylesheet" href="/css/modify.css">
```

`modify.styl` 会被 Hexo 渲染成 `modify.css` 文件，所以此处应为 `modify.css`。

## 增加插件脚本

因为使用了 `cheerio` 来解析 HTML，所以需要先安装此依赖。

```cmd
npm install cheerio
```

新建一个文件，位于 `scripts/modify.js`，并增加以下内容。
(此处只是举例，也可以使用自己已有的插件脚本文件。)

```js
'use strict';
const cheerio = require('cheerio');

/**
 * 在页面插入新顶部图
 * @param {cheerio.Root} $ Root
 */
function insertTopImg($) {
    let header = $('#page-header');
    if (header.length === 0) return;
    let background = header.css('background-image');
    if (!background) return;
    $('#post, #page, #archive, #tag, #category').prepend(`<div class="top-img" style="background-image: ${background};"></div>`);
}

hexo.extend.filter.register('after_render:html', function(str, data) {
    let $ = cheerio.load(str, {
        decodeEntities: false
    });
    insertTopImg($);
    return $.html();
});
```

## 大功告成

👏 恭喜你完成了修改，可以使用以下命令进行预览。

```cmd
hexo cl
hexo g
hexo s
```

## 后记

其实在早些时候，有篇最终效果差不多的修改教程。
 [@Nesxc](https://www.nesxc.com/) 大佬写的[butterfly文章页美化教程](https://www.nesxc.com/60/)。
是通过直接修改主题源文件的方式实现的。
而本文的修改方式是使用了 Hexo 的[插件](https://hexo.io/zh-cn/docs/plugins)系统实现的。

插件版的优点是更新主题时，只要结构变化不大，便无需任何操作即可继续使用。并且在管理上比较方便。
源文件版的优点是效率会略高一点，毕竟是直接修改的主题源文件。

如果像我一样，不想每次升级主题都可能要重新改一遍，或是为了方便管理，则可以选择插件版。
如果是本来就修改了源文件，也不介意再多修改这一个，则可以选择源文件版。



# Butterfly主题侧边栏增加天气和访客IP信息

## 获取天气插件

百度搜索天气插件，就会得到很多能生成代码的网页，一般都需要注册，不想注册可以选择[2345天气插件)](https://tianqi.2345.com/plugin/)，就是样式有点少。

贴上一个我当前用的

```yaml
<iframe allowtransparency="true" frameborder="0" width="195" height="96" scrolling="no" src="//tianqi.2345.com/plugin/widget/index.htm?s=1&z=1&t=0&v=0&d=1&bd=0&k=000000&f=&ltf=009944&htf=cc0000&q=1&e=1&a=1&c=57516&w=195&h=96&align=center"></iframe>
```

### 将插件使用到侧边栏

在博客根目录下的`\source\_data\widget.yml`文件添加以下代码，如果没有`widget.yml`文件就新建一个

```yaml
top:
    - class_name: user-map
      id_name: user-map
      name: 当前天气
      icon: fas fa-heartbeat
      order: 
      html: '这里填入刚刚生成的天气代码'
```

执行`hexo cl && hexo g && hexo s`本地部署调试。

## 侧边栏增加访客IP信息

和上面天气插件一样，先在博客根目录下的`\source\_data\widget.yml`文件添加以下代码，如果没有`widget.yml`文件就新建一个

```yaml
    - class_name: userip
      id_name: userip
      name: 来访者欢迎你!
      icon: fa fa-mobile
      order: 
      html: '访客信息获取中.....'
```

直接将这一段放在天气插件下面，

```yaml
top:
    - class_name: user-map
      id_name: user-map
      name: 当前天气
      icon: fas fa-heartbeat
      order: 
      html: '<iframe allowtransparency="true" frameborder="0" width="195" height="96" scrolling="no" src="//tianqi.2345.com/plugin/widget/index.htm?s=1&z=1&t=0&v=0&d=1&bd=0&k=000000&f=&ltf=009944&htf=cc0000&q=1&e=1&a=1&c=57516&w=195&h=96&align=center"></iframe>'
    - class_name: userip
      id_name: userip
      name: 来访者欢迎你!
      icon: fa fa-mobile
      order: 
      html: '访客信息获取中.....'
```

这就是整体效果

### 添加js

先在博客根目录下`\themes\butterfly\source\js\getuserip.js`文件添加以下代码，如果没有`getuserip.js`文件就新建一个

```yaml

    //TODO
    var userinfodom = document.getElementById("userip")
    // var updatetemp = temp.getElementsByClassName("item-content")[0].innerHTML
    // console.log(updatetemp)
    $.ajax({
        url:"https://ip.help.bj.cn",
        type:"GET",
        data:{},
        beforeSend:function(){
            // console.log("请求前")
        },
        success:function (data) {
        //    var data=JSON.parse(data);
           let userinfo = data.data[0]
        //   定义一个str
        //    if(userinfo != null){
            var userip = "你来自:&nbsp;&nbsp;&nbsp;"+ userinfo.nation + "&nbsp;" + userinfo.province + userinfo.city + "<br>"
            userip += "你的ip:&nbsp;&nbsp;&nbsp;" + userinfo.ip + "<br>"
            userip += "城市邮编:&nbsp;&nbsp;&nbsp;" + userinfo.adcode + "<br>"
            userip += "气温:&nbsp;&nbsp;&nbsp;" + userinfo.weather.weather + "&nbsp;" + userinfo.weather.temp
            // console.log(updatetemp)
            // updatetemp.innerHTML = userip
            userinfodom.getElementsByClassName("item-content")[0].innerHTML = userip
        //    }
        //    console.log("ajax请求");
        },
        error:function (e) {
         console.log("请求错误");
         console.log(e)
        }
    });
```

### 引入js

在主题配置文件里面`bottom:`下添加，例如

```yaml
  bottom:
    # 引入jq
     - <script src="https://cdn.jsdelivr.net/npm/jquery/dist/jquery.min.js"></script>
    # 侧边栏访客ip
     - <script charset="utf-8" type="text/javascript" src="/js/getuserip.js"></script>
```

执行`hexo cl && hexo g && hexo s`本地部署调试。

# 新增底部备案信息

##  修改配置文件

首先在Butterfly主题目录下，找到`_config.yml`配置文件，并在适当的位置添加如下配置信息

```yaml
ICP:
  enable: true  # 是否启用ICP
  url: http://www.beian.miit.gov.cn/  # 点击后的链接地址
  text: 某ICP备xxxx  # 备案号
  icon:    # 图标
NSP:
  enable: true   # 是否启用NSP
  url: http://www.beian.gov.cn/  # 点击后的链接地址
  text: 某公网安备 xxxxx号    # 公安备案号
  icon: https://blog.imashimaro.com/images/20200805012127.png  # 图标
```

## 修改主题源码

进入Butterfly主题目录后，进入并打开`layout/includes/footer.pug`文件，添加如下部分的代码

```yaml
if theme.ICP.enable
  .icp
    a(href=theme.ICP.url)
      if theme.ICP.icon
        img.icp-icon(src=url_for(theme.ICP.icon))
      span=theme.ICP.text
if theme.NSP.enable
  .icp
    a(href=theme.NSP.url)
      if theme.NSP.icon
        img.icp-icon(src=url_for(theme.NSP.icon))
      span=theme.NSP.text
```

## 重新部署

执行`hexo cl && hexo g && hexo s`本地部署调试。