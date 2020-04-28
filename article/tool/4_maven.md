# Maven

<!-- TOC -->

- [Maven](#maven)
  - [参考网址](#%e5%8f%82%e8%80%83%e7%bd%91%e5%9d%80)
  - [阿里远程镜像](#%e9%98%bf%e9%87%8c%e8%bf%9c%e7%a8%8b%e9%95%9c%e5%83%8f)
  - [用户的自定义localRepository的路径](#%e7%94%a8%e6%88%b7%e7%9a%84%e8%87%aa%e5%ae%9a%e4%b9%89localrepository%e7%9a%84%e8%b7%af%e5%be%84)
  - [mvn命令](#mvn%e5%91%bd%e4%bb%a4)
  - [eclipse执行mvn命令](#eclipse%e6%89%a7%e8%a1%8cmvn%e5%91%bd%e4%bb%a4)
  - [pom配置](#pom%e9%85%8d%e7%bd%ae)
    - [Dependency的配置说明](#dependency%e7%9a%84%e9%85%8d%e7%bd%ae%e8%af%b4%e6%98%8e)
    - [relativePath](#relativepath)
  - [指定模块](#%e6%8c%87%e5%ae%9a%e6%a8%a1%e5%9d%97)
  - [跳过单元测试](#%e8%b7%b3%e8%bf%87%e5%8d%95%e5%85%83%e6%b5%8b%e8%af%95)
    - [方式一](#%e6%96%b9%e5%bc%8f%e4%b8%80)
    - [方式二](#%e6%96%b9%e5%bc%8f%e4%ba%8c)

<!-- /TOC -->

## 参考网址
官网： http://maven.apache.org/index.html


## 阿里远程镜像

 conf/setting.xml 的配置

``` xml
<mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.    
     |
    <mirror>
      <id>mirrorId</id>
      <mirrorOf>repositoryId</mirrorOf>
      <name>Human Readable Name for this Mirror.</name>
      <url>http://my.repository.com/repo/path</url>
    </mirror>
     -->
     <mirror>  
	  <id>alimaven</id>  
	  <name>aliyun maven</name>  
	  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>  
	  <mirrorOf>central</mirrorOf>  
	</mirror>  
  </mirrors>
```

## 用户的自定义localRepository的路径


``` xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

	<pluginGroups />
	<proxies />
	<servers />
	<mirrors />
	<localRepository>D:/m2/repository</localRepository>
</settings>

```

## mvn命令

**常用命令**
1. mvn archetype:create ：创建 Maven 项目
1. mvn compile ：编译源代码
1. mvn test-compile ：编译测试代码
1. mvn test ： 运行应用程序中的单元测试
1. mvn site ： 生成项目相关信息的网站
1. mvn clean ：清除目标目录中的生成结果
1. mvn package ： 依据项目生成 jar 文件
1. mvn dependency:sources：下载项目依赖的jar包的源码包
2. `mvn dependency:resolve -Dclassifier=javadoc` 尝试下载对应的javadocs
3. mvn install ：在本地 Repository 中安装 jar

**不常用命令**
```
mvn eclipse:eclipse ：生成 Eclipse 项目文件
mvn generate-sources：开发环境与代码分离，很少使用，执行这个命令可以通过查看.classpath和.project两个文件来查看变化。
mvn tomcat*:run：启动tomcat，前提是在项目的pom.xml文件中添加了tomcat插件
```


## eclipse执行mvn命令

> ***Goals中输入你要执行的maven命令，不用输入mvn的开头，直接输入后面的参数即可***

- 右击需要执行maven命令的工程，选择"Debug As"或"Run As"，再选择"Maven build..."

属性 | 说明
---|---
Name | 可以给这个操作命令命名，每执行一个maven命令都会被保存。
Goals | 输入我们需要执行的maven命令，一次执行多个命令用空格隔开。
复选框 | 下面的复选框可以让我们进行一些选择性的操作，如上图跳过测试。

我们执行过的命令可以选择`"Run Configutations..."`来查看，在这里我们可以看到这个所有执行过的命令，命令对应的工程，执行的命令等。在这个对话框中，我们可以 ***删除命令，重命名，修改命令等***操作。

执行过的命令都会被保存，如果我们要执行以前执行过的命令，可以选择`"Maven build"`：
如果我们右击的工程只执行了一条命令，那么不会弹出对话框，直接执行。如果执行了多条命令，那么会弹出一个对话框，供我们选择需要执行哪个命令。


## pom配置

### Dependency的配置说明

**scope**

在POM 4中，<dependency>中还引入了<scope>，它主要管理依赖的部署。目前<scope>可以使用5个值：
* compile，缺省值，适用于所有阶段，会随着项目一起发布。
* provided，类似compile，期望JDK、容器或使用者会提供这个依赖。如servlet.jar。
* runtime，只在运行时使用，如JDBC驱动，适用运行和测试阶段。
* test，只在测试时使用，用于编译和运行测试代码。不会随项目发布。
* system，类似provided，需要显式提供包含依赖的jar，Maven不会在Repository中查找它。
* import **注意：** `import scope`只能用在dependencyManagement里面

**type**


**optional 可选依赖**


**exclusions 依赖排除**

### relativePath

parent节点中的一个配置：
 > 父项目的pom.xml文件的相对路径。默认值为`../pom.
 xml`。maven首先从当前构建项目开始查找父项目的pom
 文件，然后从本地仓库，最有从远程仓库。RelativePat
 h允许你选择一个不同的位置。

## 指定模块

- `mvn clean deploy -pl .,imp-commons` 多个模块以逗号分割，`.`表示当前模块

## 跳过单元测试

### 方式一

使用maven.test.skip，不但跳过单元测试的运行，也跳过测试代码的编译。

```
mvn package -Dmaven.test.skip=true 
```

也可以在pom.xml文件中修改

``` xml
<plugin>  
    <groupId>org.apache.maven.plugin</groupId>  
    <artifactId>maven-compiler-plugin</artifactId>  
    <version>2.1</version>  
    <configuration>  
        <skip>true</skip>  
    </configuration>  
</plugin>  
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-surefire-plugin</artifactId>  
    <version>2.5</version>  
    <configuration>  
        <skip>true</skip>  
    </configuration>  
</plugin> 
```

### 方式二

使用 `mvn package -DskipTests` 跳过单元测试，但是会继续编译；如果没时间修改单元测试的bug，或者单元测试编译错误。使用上面的，不要用这个

也可以在pom.xml文件中修改

``` xml
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-surefire-plugin</artifactId>  
    <version>2.5</version>  
    <configuration>  
        <skipTests>true</skipTests>  
    </configuration>  
</plugin> 
```