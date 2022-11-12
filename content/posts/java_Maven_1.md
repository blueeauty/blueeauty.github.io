---
title: "Java之Maven"
date: 2022-11-12T16:44:24+08:00
draft: false
---

##   1，Maven

<!--more-->

Maven是专门用于管理和构建Java项目的工具，它的主要功能有：

* 提供了一套标准化的项目结构

* 提供了一套标准化的构建流程（编译，测试，打包，发布……）

* 提供了一套依赖管理机制

![](/img/15.png)

### 1.1  Maven模型

* 项目对象模型 (Project Object Model)
* 依赖管理模型(Dependency)
* 插件(Plugin)

![](/img/16.png)

如上图所示就是Maven的模型，而我们先看紫色框框起来的部分，他就是用来完成 `标准化构建流程` 。如我们需要编译，Maven提供了一个编译插件供我们使用，我们需要打包，Maven就提供了一个打包插件提供我们使用等。

![](/img/17.png)

上图中紫色框起来的部分，项目对象模型就是将我们自己抽象成一个对象模型，有自己专属的坐标，如下图所示是一个Maven项目：

![](/img/18.png)

### 1.2  Maven安装配置

* 解压 apache-maven-3.6.1.rar 既安装完成

  <img src="G:\Java\day04-Maven&MyBatis\day04-1-Maven\ppt\assets\image-20210726163219682.png" alt="image-20210726163219682" style="zoom:90%;" />

  > 建议解压缩到没有中文、特殊字符的路径下。如课程中解压缩到 `D:\software` 下。

  解压缩后的目录结构如下：

  <img src="G:\Java\day04-Maven&MyBatis\day04-1-Maven\ppt\assets\image-20210726163518885.png" alt="image-20210726163518885" style="zoom:80%;" />

  * bin目录 ： 存放的是可执行命令。mvn 命令重点关注。
  * conf目录 ：存放Maven的配置文件。`settings.xml` 配置文件后期需要修改。
  * lib目录 ：存放Maven依赖的jar包。Maven也是使用java开发的，所以它也依赖其他的jar包。

* 配置环境变量 MAVEN_HOME 为安装路径的bin目录

  修改 conf/settings.xml 中的 <localRepository> 为一个指定目录作为本地仓库，用来存储jar包。

![](/img/19.png)

- 配置阿里云私服

  中央仓库在国外，所以下载jar包速度可能比较慢，而阿里公司提供了一个远程仓库，里面基本也都有开源项目的jar包。

  修改 conf/settings.xml 中的 <mirrors>标签，为其添加如下子标签：

  ```xml
  <mirror>  
      <id>alimaven</id>  
      <name>aliyun maven</name>  
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>          
  </mirror>	
  ```

### 1.3  Maven基本使用

#### 1.3.1  Maven 常用命令

> * compile ：编译
>
> * clean：清理
>
> * test：测试
>
> * package：打包
>
> * install：安装

**命令演示：**

在 `资料\代码\maven-project` 提供了一个使用Maven构建的项目，项目结构如下：

![](/img/20.png)

而我们使用上面命令需要在磁盘上进入到项目的 `pom.xml` 目录下，打开命令提示符

**编译命令演示：**

```java
compile ：编译
```

执行上述命令可以看到：

* 从阿里云下载编译需要的插件的jar包，在本地仓库也能看到下载好的插件
* 在项目下会生成一个 `target` 目录

![](/img/21.png)

同时在项目下会出现一个 `target` 目录，编译后的字节码文件就放在该目录下

**清理命令演示：**

```
mvn clean
```

执行上述命令可以看到

* 从阿里云下载清理需要的插件jar包
* 删除项目下的 `target` 目录

**打包命令演示：**

```
mvn package
```

执行上述命令可以看到：

* 从阿里云下载打包需要的插件jar包
* 在项目的 `terget` 目录下有一个jar包（将当前项目打成的jar包）

**测试命令演示：**

```
mvn test  
```

该命令会执行所有的测试代码。执行上述命令效果如下

![](/img/23.png)

**安装命令演示：**

```
mvn install
```

该命令会将当前项目打成jar包，并安装到本地仓库。执行完上述命令后到本地仓库查看结果如下：

![](/img/22.png)
