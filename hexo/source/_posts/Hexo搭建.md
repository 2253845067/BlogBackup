---
title: Hexo搭建
date: 2022-06-07
updated: 2022-06-07
description: 从头开始部署一个属于自己的博客页面
tags: 
  - Hexo
categories: 
  - 教程
top_img: https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/z8odwg.jpg
cover: https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/5O1day2nriDzjSu.png
---

# 一、前言

作为基础教程，本文以本地搭建 Hexo 环境生成静态文件推送到 GitHub 仓库并使用 GitHub Pages 托管为例，了解了基本流程后，后续您可以更换自己喜欢的主题、使用其他平台托管网站等等

# 二、简介

Hexo 是一款基于 Node.js 快速、简洁且高效的博客框架，通过 Node.js 将本地的每一篇 Markdown 文章渲染成 html 网页文件，这样我们的博客就是纯静态的网页，如果我们需要修改文章直接修改对应的 Markdown 文件再重新生成静态网页就好了。Node.js 默认的包管理器是 npm ，[笔者](https://www.dejavu.moe/) 建议使用 yarn 做为 Hexo 的包管理器，当然根据自己喜好即可。如果使用 Git 管理文章的话，就不用担心文章丢失了，也可以随意回滚版本，这属于 Git 的范畴，本文不做深入讨论。

[Hexo官方页面](https://hexo.io/zh-cn/index.html)

# 三、准备工作

- 一个域名（最好有 最好备案）
- 一台电脑并且联网
- 一个github账号
- 一个腾讯云账号

# 四、本地安装

## 1、安装node.js、git

- [Node.js](http://nodejs.org/) (Node.js 版本需不低于 10.13，建议使用 Node.js 12.0 及以上版本)

- [Git](http://git-scm.com/)

- 自行查询官网文档 一直下一步即可

  ```bash
  node -v
  v16.15.0
  
  git version
  git version 2.31.1.windows.1
  ```

cmd下能查出版本号就是安装成功。

## 2、安装Hexo

node安装完后即可使用npm安装hexo

```bash
npm install -g hexo-cli
```

安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

```bash
hexo init hexo
cd hexo
npm install
```

`hexo`为安装目录，可以自定义。

在`_config.yml`中编辑你网站参数.

`scaffolds`模版文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。

Hexo的模板是指在新建的文章文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。

至此 你就可以用 `hexo n yourfilename`去创建一篇新的文章了,`yourfilename`是你文章的名字。

hexo使用`markdown`语法进行编写。

# 五、使用github page搭建博客（可能对国内用户直接访问不太友好）

设置git参数 `your name`为github用户名 `email`为登录邮箱

```bash
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

新建一个repository

![](https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/0.png)

**项目名一定是 你的用户名.github.io**

在`_config.yml`中最后的地方设置你的git地址

```bash
deploy:
  - type: 'git'
    repo:
      github: 'your git'
    branch: master
```

注意空格 缩进表示层级关系

```bash
hexo clean # 清除缓存
hexo g # 生成网页
hexo d # 发布到deploy配置的地址
hexo s # 创建本地网站服务
```

**正常发布和生成本地网站前都要清除缓存和生成网页**

找到设置

![image-20220607123530611](https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/image-20220607123530611.png)

选择`master`分支

可以在`Custom domain`绑定你的域名

在域名中用`CNAME`解析到 你的昵称.github.io即可访问

**注意**

如果你本地的`source`文件中没有`CNAME`那么当你用`hexo d`推送的时候会删除github中的`CNAME`文件

**解决办法**

- 在本地新`source`建一个CNAME文件 用记事本编辑 里面填上你的域名即可

- 下载github中的`CNAME`文件到本地的`source`

  # 六、使用自己的云服务器托管

## 1、安装宝塔以及微力同步

[宝塔面板](https://www.bt.cn/new/download.html)

服务器端直接一键安装

```bash
#Centos安装脚本
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh ed8484bec
#Ubuntu/Deepin安装脚本
wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && sudo bash install.sh ed8484bec
#Debian安装脚本
wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && bash install.sh ed8484bec
#万能安装脚本
if [ -f /usr/bin/curl ];then curl -sSO https://download.bt.cn/install/install_panel.sh;else wget -O install_panel.sh https://download.bt.cn/install/install_panel.sh;fi;bash install_panel.sh ed8484bec
```



[微力同步下载页面](http://www.verysync.com/download.html)

PC端下载下来解压后打开就能使用

服务器端运行以下命令

```bash
#(如果需要指定索引存放位置请在最后面添加-d 路径 如 -d /data/verysync)
curl http://www.verysync.com/shell/verysync-linux-installer/go-installer.sh > go-installer.sh
chmod +x go-installer.sh
./go-installer.sh
```

安装完成后就可以用浏览器打开 [http://你的IP地址:8886](http://xn--ip-0p3ck01akcu41v:8886/) 管理微力内容了

**注意**

如果不能访问服务器端的微力同步页面，一定要检查防火墙是否打开8886端口，还有宝塔安全页面是否放行。

## 2、宝塔创建网站

如图

![image-20220607125209845](https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/image-20220607125209845.png)

`Test.com`为你的网站域名，也可用服务器IP

提交创建即可

记住网站的根目录，等下会用到

## 3、同步

打开PC端微力同步，`新建同步` `标准文件夹`然后选择你的hexo的主目录，点击确定，然后会生成一个链接地址。

打开服务器端微力同步后台，`新建同步` `连接密钥或链接`输入刚刚PC端生成的链接，保存的位置就填你网站的根目录，点击连接。

## 4、网站设置

打开宝塔后台，进入网站的设置界面，点击`网站目录`，选择到你hexo目录里面的`public`文件夹，点击保存。

## 5、最后

最后就是在本地执行两条命令来生成你博客的静态网页文件了。

```bash
hexo clean # 清除缓存
hexo g # 生成网页
```

生成好后会自动同步到服务器本地，然后访问你的域名就可以看见服务器端部署的博客了。
