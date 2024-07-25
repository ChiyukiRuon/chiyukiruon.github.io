---
title: 如何在Hexo下写一篇博客
date: 2024-07-26 01:15:39
tags:
  - Hexo
categories:
  - 教程
---
## 写在开头
由于更新博客的时间间隔实在是过于长了，每次想写东西的时候都忘了应该怎么写。于是专门写一篇教程给自己看（

## 开始
首先命令行进入项目根目录，执行下列指令新建一篇文章
```shell
hexo new [layout] <title>
```
这里的`layout`指的是文章的布局，我使用的是默认的布局，所以如果要发布一篇正常的博客，使用`post`就可以了  
而`title`显而易见指的是文章的标题

## 写作
新建完文章后，`_posts`目录下就会多出一个`<title>.md`的文件以及同名的文件夹，我们就可以开始在md文件中进行写作了，而文章中需要用到的图片等资源则可以放入和md文件同名的文件夹中，并以`/<year>/<month>/<day>/<title>/file`的形式进行调用就可以插入图片了

<div class="admonition warning "> 
    <p class="admonition-title">注意</p>
    <p>需要在配置文件中将post_asset_folder配置为true才会为每篇文章生成独立的资源文件夹</p>
    <p>图片引用路径也因配置文件而异</p>
</div>

## 添加的一些插件

- Hexo-admonition  
  一个提示块插件，虽然按照文档里的语法不知道为什么不生效，但是我还可以直接写html语法（

  到时候有空再研究研究这玩意怎么回事（）  
  [文档](https://github.com/lxl80/hexo-admonition)
- hexo-tag-color-block  
  一个可以显示色块的插件 （找提示块插件时找到的）~~不知道有什么用但是既然装都装了那就这样吧~~

  [文档](https://github.com/patrick330602/hexo-tag-color-block)