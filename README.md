# **1.下载Tomcat源代码：**

https://tomcat.apache.org/download-80.cgi

 ![img](https://github.com/kunzhao3/img/blob/master/tomcat/1.png)

 

# 2. **解压以及创建必要目录和配置**

解压、新建catalina-home目录，同时将目录中的conf和webapps文件夹复制到catalina-home目录中

 **![img](https://github.com/kunzhao3/img/blob/master/tomcat/2.png)**

 

 

需要通过Maven组织文件，因此需要在根目录下创建目录中新建pom.xml文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>Tomcat8.5</artifactId>
    <name>Tomcat8.5</name>
    <version>8.5</version>
    <build>
        <finalName>Tomcat8.5</finalName>
        <sourceDirectory>java</sourceDirectory>
        <testSourceDirectory>test</testSourceDirectory>
        <resources>
            <resource>
                <directory>java</directory>
            </resource>
        </resources>
        <testResources>
           <testResource>
                <directory>test</directory>
           </testResource>
        </testResources>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.easymock</groupId>
            <artifactId>easymock</artifactId>
            <version>3.4</version>
        </dependency>
        <dependency>
            <groupId>ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.7.0</version>
        </dependency>
        <dependency>
            <groupId>wsdl4j</groupId>
            <artifactId>wsdl4j</artifactId>
            <version>1.6.2</version>
        </dependency>
        <dependency>
            <groupId>javax.xml</groupId>
            <artifactId>jaxrpc</artifactId>
            <version>1.1</version>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jdt.core.compiler</groupId>
            <artifactId>ecj</artifactId>
            <version>4.5.1</version>
        </dependency>
    </dependencies>
</project>
```

# 3. **配置IDEA运行项目**

 

通过pom.xml文件构建一个新的工程

 ![img](https://github.com/kunzhao3/img/blob/master/tomcat/3.png)

 

![img](https://github.com/kunzhao3/img/blob/master/tomcat/5.png)

 

 

 

如果编译build的时候出现Test测试代码报错，注释该代码即可。Tomcat源码util.TestCookieFilter类会报错，将其注释即可

 ![img](https://github.com/kunzhao3/img/blob/master/tomcat/4.png)

 

 

 

 

Tomcat启动的目录为一个main方法类：org.apache.catalina.startup.Bootstrap

 ![img](https://github.com/kunzhao3/img/blob/master/tomcat/6.png)

 

 

添加VM options

-Dcatalina.home=catalina-home

-Dcatalina.base=catalina-home

-Djava.endorsed.dirs=catalina-home/endorsed

-Djava.io.tmpdir=catalina-home/temp

-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager

-Djava.util.logging.config.file=catalina-home/conf/logging.properties

 

 ![img](https://img2018.cnblogs.com/blog/1540950/201907/1540950-20190722011508633-1891788386.png)

 

 

运行项目，访问http://localhost:8080，得到结果：

 ![img](https://img2018.cnblogs.com/blog/1540950/201907/1540950-20190722011531507-572168368.png)

 

 

原因是我们直接启动org.apache.catalina.startup.Bootstrap的时候没有加载org.apache.jasper.servlet.JasperInitializer，从而无法编译JSP。

解决办法是在tomcat的源码org.apache.catalina.startup.ContextConfig中的configureStart函数中手动将JSP解析器初始化：

添加代码：context.addServletContainerInitializer(new JasperInitializer(), null);

添加位置如下图：

![image-20200727143803328](/Users/zhaokun/Library/Application Support/typora-user-images/image-20200727143803328.png)

 修改完后，项目再启动，我们再在浏览器访问http://localhost:8080/ ，就可以看到我们所熟悉的经典欢迎页面了

![img](https://img2018.cnblogs.com/blog/1540950/201907/1540950-20190722011618365-147821880.png)
