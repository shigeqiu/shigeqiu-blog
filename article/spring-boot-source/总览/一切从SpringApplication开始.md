# SpringApplication

<!-- TOC -->

- [SpringApplication](#springapplication)
  - [createApplicationContext](#createapplicationcontext)
  - [prepareContext](#preparecontext)
  - [refreshContext](#refreshcontext)

<!-- /TOC -->

SpringApplication.java 文件中，实例方法 `run(String... args)`起步。其中所属`ConfigurableApplicationContext`接口的变量`context`是阅读源码的起点，我们就从这里入手。

`run`方法中主要有以下几个关键点方法组成：

1. createApplicationContext
1. prepareContext
1. refreshContext
1. afterRefresh


## createApplicationContext

``` java 
context = createApplicationContext();
```
该行初始化实例，实例类为`AnnotationConfigServletWebServerApplicationContext`。

## prepareContext 
prepareContext方法，翻译过来是**准备context**的意思。

## refreshContext

通过调用`ConfigurableApplicationContext`定义的`refresh()`方法完成刷新，通过打断点观测，Bean的加载是在这一步完成的。

**移步 AbstractApplicationContext > refresh方法**