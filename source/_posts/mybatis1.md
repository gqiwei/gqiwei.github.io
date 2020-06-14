---
title: mybatis源码笔记（一）
categories:
  - 源码
tags:
  - java
  - mybatis
date: 2020-04-17 17:06:47
---

mybatis是现在流行的持久层框架，现在这家公司也从之前的ssh项目慢慢转成了ssm项目，而我之前用过ibatis，ibatis正是mybatis的前身。故想找时间来阅读下其源码，但是每天能抽出来的时间有限，只能慢慢的记录下来。
<!-- more -->
# 源码调试
mybatis是一个开源的框架，所以我们直接从github上拉取代码，甚至可以fork到自己的仓库里，也方便自己在上面添加注释。  
我使用IDEA开发工具拉取代码，版本为3.5.3。  
拉取完成后可以找下`org.apache.ibatis.autoconstructor.AutoConstructorTest`
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/mybatis/mybatis01/1.png)
`AutoConstructorTest`是单元测试类，任意一个单元测试方法都可以直接开始调试。它使用的是HSQLDB。
# 开始阅读
``` java 
@BeforeAll
static void setUp() throws Exception {
  // create a SqlSessionFactory
  try (Reader reader = Resources.getResourceAsReader("org/apache/ibatis/autoconstructor/mybatis-config.xml")) {
    sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
  }

  // populate in-memory database
  BaseDataTest.runScript(sqlSessionFactory.getConfiguration().getEnvironment().getDataSource(),
      "org/apache/ibatis/autoconstructor/CreateDB.sql");
}
```
`Resources.getResourceAsReader()`是读取xml配置。  
进入`new SqlSessionFactoryBuilder().build()`,会发现有很多`build()`是有很多重载的方法，但最终都会去执行`build(Reader reader, String environment, Properties properties)`，所以直接看这个好了。  
在此方法里，会创建一个`XMLConfigBuilder`，直接点进去看其构造函数。
它有很多构造函数，随便挑一个来看。
``` java 
public XMLConfigBuilder(Reader reader, String environment, Properties props) {
  this(new XPathParser(reader, true, props, new XMLMapperEntityResolver()), environment, props);
}
```
这里有两个重要的对象，一个是`XPathParser`和`XMLMapperEntityResolver`。  
`XPathParser`是mybatis基于XPath解析器，封装了XPath、Document和EntityResolver，用来解析配置文件mybatis-config.xml以及Mapper.xml，属于mybatis的基础支持层中的解析器模块。`XMLMapperEntityResolver`是`EntityResolver`的实现类，核心是`resolveEntity()`方法，来读取DTD文档。DTD文件主要验证xml文件编写的合法性。  
查看`XPathParser`的源码，首先它有很多构造函数，也随便挑一个来看，
``` java 
public XPathParser(Reader reader, boolean validation, Properties variables, EntityResolver entityResolver) {
  commonConstructor(validation, variables, entityResolver);
  this.document = createDocument(new InputSource(reader));
}
```
`commonConstructor()`为初始化XPathParser的几个参数。  
`createDocument()`是将xml解析成Document对象。  
在调用`createDocument()`方法前，一定要先调用`commonConstructor()`。  

回到XMLConfigBuilder构造函数中来。
``` java
private XMLConfigBuilder(XPathParser parser, String environment, Properties props) {
  super(new Configuration()); //创建Configuration对象
  ErrorContext.instance().resource("SQL Mapper Configuration");
  this.configuration.setVariables(props); //设置Configuration对象的variables属性。
  this.parsed = false;
  this.environment = environment;
  this.parser = parser;
}
```
接下来看看XMLConfigBuilder.parse()方法。
``` java
public Configuration parse() {
  if (parsed) {
    throw new BuilderException("Each XMLConfigBuilder can only be used once.");
  }
  parsed = true;
  parseConfiguration(parser.evalNode("/configuration"));
  return configuration;
}
```
`parsed`表示xml是否已经解析过了 。  
`parseConfiguration()`方法则是解析 XML中的configuration节点。  
进入`parseConfiguration()`。  
``` java
private void parseConfiguration(XNode root) {
  try {
    //解析properties标签
    propertiesElement(root.evalNode("properties"));
    //解析settings标签
    Properties settings = settingsAsProperties(root.evalNode("settings"));
    //暂且不知道啥作用
    loadCustomVfs(settings);
    loadCustomLogImpl(settings);
    //解析typeAliases标签
    typeAliasesElement(root.evalNode("typeAliases"));
    //解析plugins标签
    pluginElement(root.evalNode("plugins"));
    //解析objectFactory标签
    objectFactoryElement(root.evalNode("objectFactory"));
    //解析objectWrapperFactory标签
    objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
    //解析reflectorFactory标签
    reflectorFactoryElement(root.evalNode("reflectorFactory"));
    settingsElement(settings);
    //解析environments标签
    environmentsElement(root.evalNode("environments"));
    //解析databaseIdProvider标签
    databaseIdProviderElement(root.evalNode("databaseIdProvider"));
    //解析typeHandlers标签
    typeHandlerElement(root.evalNode("typeHandlers"));
    //解析mappers标签
    mapperElement(root.evalNode("mappers"));
  } catch (Exception e) {
    throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
  }
}
```
解析完xml返回Configuration对象。  
最终`SqlSessionFactoryBuilder.bulid()`返回`SqlSessionFactory`对象。  
至此第一部分代码执行结束，剩下的部分就比较简单了。
``` java 
BaseDataTest.runScript(sqlSessionFactory.getConfiguration().getEnvironment().getDataSource(),
      "org/apache/ibatis/autoconstructor/CreateDB.sql");
```
从`sqlSessionFactory`中获取到数据库连接，并执行指定路径下的sql文件，为测试类生成测试数据。

好了，以上就是是简单的加载mybatis-config文件流程。其中`XMLConfigBuilder`中还有一些方法，放到后面在看。








  
<br/><br/>
参考文献
* Mybatis技术内幕