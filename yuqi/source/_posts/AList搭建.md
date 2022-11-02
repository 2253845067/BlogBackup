---
title: AList搭建
date: 2022-06-01
updated: 2022-06-02
description: 将许多个人网盘集中至一个网页管理的程序
tags: 
  - Alist
categories: 
  - 教程
top_img: https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/k7v9yq.jpg
cover: https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/can_circle.svg
---

# 一、项目展示

GitHub项目地址：https://github.com/Xhofe/alist

Demo：https://alist.nn.ci

Alist文档地址：https://alist-doc.nn.ci/en/

![成品预览](https://yuqi1.oss-cn-chengdu.aliyuncs.com/img/image-20220602195814680.png)

# 二、搭建环境

服务器：阿里云香港轻量应用服务器24元/月VPS一台（最好是选非大陆的服务器，无需备案）

系统：Centos7

域名一枚，并做好解析到服务器上

# 三、搭建方式

## 1、一键脚本安装（本次使用）

仅支持Linux-x86_64/aarch64平台。

》》**安装**

```bash
curl -fsSL "https://nn.ci/alist.sh" | bash -s install
```

注意：

已经安装过再次执行安装会删除之前的数据，更新请使用更新命令。

》》**更新**

```bash
curl -fsSL "https://nn.ci/alist.sh" | bash -s update
```

》》**卸载**

```bash
curl -fsSL "https://nn.ci/alist.sh" | bash -s uninstall
```

》》**自定义路径**

```bash
curl -fsSL "https://nn.ci/alist.sh" | bash -s install
```

默认安装在/opt/alist，要自定义安装路径，添加安装路径为第二个参数，必须是绝对路径（路径以alist结尾时直接安装到给定路径，否则会安装在给定路径alist目录下），如安装到/root：
```bash
# 安装

curl -fsSL "https://nn.ci/alist.sh" | bash -s install /root

# 更新

curl -fsSL "https://nn.ci/alist.sh" | bash -s update /root

# 卸载

curl -fsSL "https://nn.ci/alist.sh" | bash -s uninstall /root
```



## 2、手动搭建

参考文档：https://alist-doc.nn.ci/docs/install/manual



## 3、Docker

参考文档：https://alist-doc.nn.ci/docs/install/docker



# 四、开始搭建（以一键脚本安装为例子）

## 1、运行安装代码

》》**输入以下命令等待自动安装**

```bash
curl -fsSL "https://nn.ci/alist.sh" | bash -s install
```

注意：安装完成会输出默认后台密码。

## 2、放行相关端口

遇到访问不了，请在宝塔面板的防火墙和服务商的后台防火墙里打开对应端口。

## 3、配置反向代理

以宝塔为例，新建一个网站，域名使用你自己的域名，在配置文件里面注释掉Nginx配置文件中的这部分内容：

```c#
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
    {
        expires      30d;
        error_log /dev/null;
        access_log off;
    }

    location ~ .*\.(js|css)?$
    {
        expires      12h;
        error_log /dev/null;
        access_log off; 
    }
```

然后再下面粘贴这段代码：

```c#
location / {
    proxy_pass http://127.0.0.1:5244/;
    rewrite ^/(.*)$ /$1 break;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Upgrade-Insecure-Requests 1;
    proxy_set_header X-Forwarded-Proto https;
}
```

## 4、访问域名配置账号

此时，输入反向代理的域名就能正常访问网站了，输入刚刚安装完成之后终端输出的密码就能正常登陆后台了。为了方便起见，建议更改一个自己能记住的密码。

# 五、结束

祝大家用得开心，有问题可以去GitHub提 [Issues](https://github.com/Xhofe/alist/issues)，也可以在评论区互相交流探讨。
