---
title: Hexo博客部署到云服务器
date: 2022-06-18
updated: 2022-06-18
description: 博客用自己的服务器托管
tags: 
  - Hexo
categories: 
  - 教程
top_img: https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/b8e19b44-9d3d-41bb-98cb-dd328ce16d89.jpg
cover: https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/a90d64cc-575e-4116-ad92-2c26433c94d5.jpg
---

# 将`Hexo`部署到云服务器

为了解决`Hexo`部署到 [GitHub](https://github.com) 访问速度慢的问题，我们可以将`Hexo`部署到云服务器上。

这篇博客使用宝塔面板将`Hexo`部署到阿里云服务器上。

# 前提

1. 已经搭建好 hexo 的相关环境，并将 hexo 部署到了 GitHub 上
2. 已经购买好云服务器
3. 已经购买并备案好域名 （可选项，没有也可以用 ip 地址访问 hexo）

# 安装宝塔面板

宝塔面板可以可视化地操作远端服务器（这样就不用自己手动装 nginx 了）

进入宝塔面板的下载页面 [宝塔面板下载](https://www.bt.cn/new/download.html) ，找到 Linux 面板，点击安装脚本 **(如果买的服务器是 Windows 的就点 Windows 的)**

根据自己服务器的类型，复制对应的代码

进入阿里云的控制台，选择自己的云服务器，点击登录，进入终端。

粘贴刚才复制的代码，回车，一路确认。

在浏览器中访问外网面板地址，使用宝塔提供的用户名和密码登录。

进入面板后，点击一键安装 LNMP。就能直接将 Nginx，MySQL 等网站所需内容一键安装好。

# 安装 并配置 Git 仓库

在远程服务器上配置好 Git 仓库后，才能将本地的 hexo push 到远端。

安装 git **（远程服务器上）**

```shell
yum install git
```

配置 git 用户

```shell
adduser git
```

赋予用户权限

```shell
chmod 740 /etc/sudoers
vi /etc/sudoers
```

输入 i 进入 insert 模式 ，找到 root ALL=(ALL) ALL ，在其下方加入一行 git ALL=(ALL) ALL

```shell
root    ALL=(ALL)       ALL
// 在这里加入代码
git     ALL=(ALL)       ALL
```

按下 ESC ，输入 wq ，保存 vim 文件。

修改 sudoers 文件权限

```shell
chmod 400 /etc/sudoers 
1
```

设置 git 用户的密码

```shell
sudo passwd git
```

给 git 用户添加 ssh 秘钥 （找到本地的 ssh 公钥，部署 hexo 到 GitHub 时有生成，以 .pub 结尾）

```shell
su - git
mkdir -p ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorzied_keys
chmod 700 ~/.ssh
vim ~/.ssh/authorized_keys    #将ssh密钥粘贴进去
```

在本地打开一个终端，以 ssh 的方式登录云服务器

```shell
ssh -v git@你的 ip 地址
// 输入密码 即可成功登录云服务器
```

创建一个 git 仓库，新建一个 post-receive 文件，用来存储本地的提交。

```shell
su root
cd /home/git
git init --bare blog.git  #在/home/git下创建新仓库blog.git
chown git:git -R blog.git #给予git用户权限
```

在 blog.git/hooks 文件夹下创建一个 post-receive 钩子，把提交到 git 仓库的文件同步到 home/hexo文件夹中

```shell
cd blog.git/hooks
vim post-receive
```

在 post-receive 文件中输入以下代码

```shell
 #!/bin/bash 
  git --work-tree=/home/hexo --git-dir=/home/git/blog.git checkout -f
```

授予 post-receive 文件可执行权限

```shell
chmod +x /home/git/blog.git/hooks/post-receive
```

至此服务端已经配置完成。

# 部署 hexo

打开 hexo 的配置文件 _config.yml 修改 Deployment 位置的配置

```yaml
deploy:
    type: git
    repo: root@你的ip地址:/home/git/blog.git    #仓库地址
    branch: master    #分支
```

保存配置文件，打开终端进入 hexo 博客所在文件夹

```shell
cd blog //进入自己博客所在文件夹
hexo clean
hexo g //编译
hexo d //部署
```

# 宝塔创建网站

如图

![image-20220607125209845](https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/image-20220607125209845.png)

`Test.com`为你的网站域名，也可用服务器IP

提交创建即可

# 网站设置

打开宝塔后台，进入网站的设置界面，点击`网站目录`，选择目录为`/home/hexo`这是我们部署到云服务器的博客根目录

# 最后

最后就是在本地执行两条命令来生成你博客的静态网页文件了。

```bash
hexo clean # 清除缓存
hexo g # 生成网页
hexo d # 部署
```

现在就可以用 你的域名来访问博客了。