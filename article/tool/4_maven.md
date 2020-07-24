# Maven

<!-- TOC -->

- [Maven](#maven)
  - [参考网址](#参考网址)
  - [阿里远程镜像](#阿里远程镜像)
  - [用户的自定义localRepository的路径](#用户的自定义localrepository的路径)
  - [mvn命令](#mvn命令)
  - [eclipse执行mvn命令](#eclipse执行mvn命令)
  - [pom配置](#pom配置)
    - [Dependency的配置说明](#dependency的配置说明)
    - [relativePath](#relativepath)
  - [指定模块](#指定模块)
  - [跳过单元测试](#跳过单元测试)
    - [方式一](#方式一)
    - [方式二](#方式二)
  - [传参](#传参)
  - [清理项目依赖的本地仓库的maven包](#清理项目依赖的本地仓库的maven包)

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



今天打开一个新项目，idea一片红，很多已经导入的包还是报错。网上看到很多人都是说设置里面换maven 清缓存。但都试过了。最后在命令行输入 `mvn idea:idea` 然后 file–invalidate caches 重启就可以了

## 传参

**-D代表（Properties属性）**

Maven中的 `-D`（Properties属性）和 `-P` （Profiles配置文件）

使用命令行设置属性-D的正确方法是：

`mvn -DpropertyName=propertyValue clean package`

- 如果propertyName不存在pom.xml，它将被设置。
- 如果propertyName已经存在pom.xml，其值将被作为参数传递的值覆盖-D。
- 
要发送多个变量，请使用 **多个空格分隔符** 加 `-D`：

```
mvn -DpropA=valueA -DpropB=valueB -DpropC=valueC clean package
```

例如你的pom.xml如下：

``` xml
<properties>
    <theme>myDefaultTheme</theme>
</properties>
```

那么在这个执行过程中 `mvn -Dtheme=halloween clean package` 会覆盖theme的值，具有如下效果：

``` xml
<properties>
    <theme>halloween</theme>
</properties>
```

**-P代表（Profiles配置文件）**

也就是说在 `<profiles>` 指定的 `<id>` 中，可以通过 `-P` 进行传递或者赋值。

例如,你的pom.xml如下：

``` xml
<profiles>
    <profile>
        <id>test</id>
        ...
    </profile>
  </profiles>
```

执行 `mvn test -Ptest` 为触发配置文件。

``` xml
<profile>
  <id>test</id>
  <activation>
    <property>
        <name>env</name>
        <value>test</value>
    </property>
  </activation>
</profile>
```

## 清理项目依赖的本地仓库的maven包

```
mvn dependency:purge-local-repository
```
这个命令会清理pom.xml中的包，并重新下载，但是并不清理不在pom.xml中的依赖包。

```
mvn dependency:purge-local-repository -DreResolve=false 
```

reResolve是否重新解析依赖关系

``` 
mvn dependency:purge-local-repository -DactTransitively=false -DreResolve=false 
```

actTransitively是否应该对所有传递依赖性起作用。默认值为true。

例如：
```
mvn dependency:purge-local-repository -Dinclude=org.slf4j:slf4j-api

mvn dependency:purge-local-repository -Dinclude=org.slf4j:slf4j-api,org.slf4j:log4j-over-slf4j

```

**Manual purge**

还可以通过使用 `purge-local-repository` 目标并设置“ manualIncludes”或“ manualInclude”参数来清除不属于当前项目依赖关系树的特定依赖关系。任何手动包括的清除工件都将从本地存储库中删除，直到需要时才会重新解析。例如，这对于刷新父pom，导入的pom或Maven插件很有用。

警告，如果从本地存储库中删除了依赖关系，但在以后的构建过程中需要，则在正常的构建过程中使用此目标可能会带来风险。通常，在构建结束时或在构建清理过程中，此目标是安全的。

```
mvn dependency:purge-local-repository -DmanualInclude=org.slf4j:slf4j-api,org.slf4j:log4j-over-slf4j
```


> 插件地址：http://maven.apache.org/plugins/maven-dependency-plugin/purge-local-repository-mojo.html
> https://maven.apache.org/plugins/maven-dependency-plugin/examples/purging-local-repository.html#