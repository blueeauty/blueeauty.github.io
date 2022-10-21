---
title: "hugo 博客搭建"
date: 2022-10-16T22:40:41+08:00
tags : ["hugo"]
draft: false
---

## Windows平台下安装hugo

Windows下Hugo提供了Chocolatey方式的安装，通过如下命令即可。

<!--more-->

```
choco install hugo -confirm
```

验证安装安转完成后，我们打开终端，输入如下命令进行验证是否安装成功

```
hugo version
```

如果没问题的话，会输出Hugo的版本号等一些信息。



**创建一个站点**

```
hugo new site blog
```

**添加一个主题**

```
cd themes
git clone https://github.com/vjeantet/hugo-theme-casper.git casper
```

**新建文章**

```
hugo new post/first.md
```

**实现主题本地预览**

```
hugo server -t casper -D
```

## 适配主题

**Hugo的每个主题都会有不同的参数配置，而这些配置被存放在根目录下的`config.toml`文件中**

```
BaseUrl= "https://shanetian.github.io/"
LanguageCode= "zh-cn"
Title= "I am thinking ..."
theme = "casper"
paginate = 5
DisqusShortname = "suixin-1"  # name in comments
Copyright = "All rights reserved - 2018"
canonifyurls = true
[params]
  description = "路在脚下，心向远方"
  metadescription = "Used as 'description' meta tag for both home and index pages. If not set, 'description' will be used instead"
  cover = "images/boy_bicycle.jpg"
  author = "SuiXin"
  authorlocation = "Chengdu, China"
  authorwebsite = "suixinblog.cn"
  authorbio= "Every man dies, not every man really lives."
  logo = "images/bird.png"
  # googleAnalyticsUserID = "UA-79101-12"
  # Optional RSS-Link, if not provided it defaults to the standard index.xml
  # RSSLink = "http://feeds.feedburner.com/..."
  githubName = "ShaneTian"
  email = ""
  twitterName = "ShaneTian6"
  # facebookName = ""
  # codepenName = ""
  # linkedinName = ""
  # stackoverflowId = ""
  # keybaseName = ""
  # flickrName = ""
  # instagramName = ""
  # pinterestName = ""
  # googlePlusName = ""
  # set true if you are not proud of using Hugo (true will hide the footer note "Proudly published with HUGO.....")
  hideHUGOSupport = false
  # Setting a value will load highlight.js and enable syntax highlighting using the style selected.
  # See https://github.com/isagalaev/highlight.js/tree/master/src/styles for available styles
  # A preview of above styles can be viewed at https://highlightjs.org/static/demo/
  hjsStyle = "atom-one-light"
 
 [params.social]
    twitter = "ShaneTian6"
[[menu.main]]
  name = "Home"
  weight = -1
  identifier = "blog"
  url = "/"

[[menu.main]]
  name = "Machine Learning"
  weight = -2
  identifier = "ml"
  url = "/tags/machine-learning"

[[menu.main]]
  name = "Others"
  weight = -3
  identifier = "other"
  url = "/tags/others"

[[menu.main]]
  name = "About"
  weight = -4
  identifier = "about"
  url = "/about"

  [permalinks]
  post = "/:year/:month/:day/:title/"
```

