---
title: 记录ASP.NET项目和Vue项目部署到IIS的过程
date: 2019-07-26 15:37:01
tags:
  - vue.js
  - ASP.NET
categories:
  - 笔记
---
> 近来需要部署项目，虽然还没定好部署到哪里，但先记录一下部署到IIS上面吧，感觉自己的记性越来越不好用了。

<!--more-->

## 一、安装IIS

 1. 打开控制面板，找到 “程序”，点进去

  ![图1-1](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0301.png)

 2. 在 “程序和功能” 中找到 “启动或关闭Windows功能”

  ![图1-2](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0302.png)

 3. 启动 “Internet Information Services” 下的服务，如下图所示，设置好后点击确认

  ![图1-3](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0303.png)
以上，IIS已经安装了，可以在window10的搜索框搜索IIS打开IIS管理器

---

## 二、部署ASP.NET项目

 1. 项目右键选择 “发布”

  ![图2-1](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0304.png)

 2. “发布方法” 选择 “文件系统”， 目标位置自行选择, 然后点击 “保存” 即可
  
  ![图2-2](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0305.png)

 3. 打开IIS管理器，右键 “网站”， 新建一个网站，然后类比下图，给网站取一个名字，选择应用程序池（应用程序池也可以右键新建一个），物理路径就是刚刚ASP.NET项目发布的文件路径，再设置好端口号即可
  
  ![图2-3](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0306.png)

以上，ASP.NET项目部署完成，可以在浏览器输入： http://localhost:设置的端口号 访问

---

## 三、部署Vue项目

 1. 将项目打包，进入项目根目录，输入以下命令，然后会在项目根目录下生成一个 “dist” 文件夹，将 “dist” 复制粘贴到自己放置发布文件的文件夹中

```Node
yarn run build 
或者
npm run build
```

  ![图3-1](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0307.png)

 2. 打开IIS管理器，新建一个网站，物理路径选择刚刚放置 “dist” 文件夹

 3. 在IIS管理器中安装 “URL Rewrite” 模块和 “Application Request Routing” 模块

安装过程稍微有点慢，耐心等待一会儿。[URL Rewrite下载地址](https://www.iis.net/downloads/microsoft/url-rewrite)，[Application Request Routing下载地址](https://www.iis.net/downloads/microsoft/application-request-routing)

  ![图3-2](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0308.png)

  ![图3-3](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0309.png)

 4. URL重写

点开IIS管理器左边侧栏中，与Vue项目相应的网站，找到 “URL 重写”，双击打开，然后新建规则（新建一个空白规则）

  ![图4-0-1](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0310.png)
  ![图4-0-2](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0311.png)

“匹配 URL” 处配置如下图，如果请求的api地址全都是为'/api/---'，那 “模式” 那里填写
  
  ```html
  ^((?!(api)).)*$
  ```

即可，若是'/api/---'或'/mvc/---'，则填写

  ```html
  ^((?!(api|mvc)).)*$
  ```

  ![图4-1](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0312.png)

“条件” 处配置如下，添加一个条件

  ![图4-2](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0313.png)
  ![图4-3](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0314.png)

“操作” 处配置如下，都指向'/index.html'

  ![图4-4](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0315.png)

 5. 代理设置

在IIS管理器的主页中可以看到 “Application Request Routing”，双击打开，然后在右边侧栏中找到 “Server Proxy Settings”，点进去勾选 “Enable proxy”

  ![图5-0-1](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0316.png)
  ![图5-0-2](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0317.png)
  ![图5-0-2](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0318.png)

然后在上一步操作过的 “URL 重写” 再新建一个规则（空白规则），配置如下，“重写 URL” 处根据自己要请求的后端实际地址填写，可以先在测试模式中测试一下

  ![图5-1](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0319.png)
  ![图5-2](https://raw.githubusercontent.com/MonyaPeng/uploadimage/master/blogimg/post3/0320.png)
至此，所有的设置都已经完成，前后端可以连通。（ps：虽然网上有很多说要在Vue项目中进行配置，但我没有进行额外的配置就成功部署了，如果有坑的话，后期再来填）。

本文参考：
[vue项目部署在IIS上面的心得](https://blog.csdn.net/weixin_33888907/article/details/88859668)