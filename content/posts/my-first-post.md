---
title: "My First Post"
date: 2022-10-16T22:40:41+08:00
draft: false
---

Github下载

我们可以直接从Releases · gohugoio/hugo (github.com)页面下载对应的二进制文件,然后把它放在你的PATH目录里即可使用。这个支持任何平台，根据自己的平台选择相应的二进制包即可。



Mac平台下

Mac下Hugo提供了homebrew安装的方式，非常简便。

- 

```
brew install hugo
```

Debian and Ubuntu平台下

- 

```
sudo apt-get install hugo
```

Windows平台下

Windows下Hugo提供了Chocolatey方式的安装，通过如下命令即可。

- 

```
choco install hugo -confirm
```

Hugo 中文文档 | Hugo 中文网 (gohugo.cn)



验证安装

安转完成后，我们打开终端，输入如下命令进行验证是否安装成功

hugo version

如果没问题的话，会输出Hugo的版本号等一些信息。



创建一个站点

- 

```
hugo new site blog
```

添加一个主题

- 
