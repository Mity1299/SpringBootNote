# 1.     实践配置

参考：

l  [MyBatis Ehcache Adapter - Reference Documentation](http://www.mybatis.org/ehcache-cache/)

l  [MyBatis高级篇之整合ehcache缓存框架](https://www.cnblogs.com/zp-uestc/p/8950244.html)

l  [SpringBoot整合Ehcache](https://blog.csdn.net/u014042066/article/details/76861102)

## 1.1.    引入cache依赖

在pom.xml中引入依赖。

```xml
<dependency>

   <groupId>org.springframework.boot</groupId>

   <artifactId>spring-boot-starter-cache</artifactId>

</dependency>

   <dependency>

    <groupId>org.mybatis.caches</groupId>

    <artifactId>mybatis-ehcache</artifactId>

    <version>1.1.0</version>

</dependency>

 
```



## 1.2.    启动类开启缓存

在Spring Boot主类中增加@EnableCaching注解开启缓存功能，如下：

```java
@SpringBootApplication

@EnableCaching

public class Application {

 
    public static void main(String[] args) {

        SpringApplication.run(Application.class, args);

    }

 

}
```



## 1.3.    ehcache.xml

参考：[史上最全面的Spring Boot Cache使用与整合](https://www.cnblogs.com/yueshutong/p/9381540.html#4模板编程)

在resources目录下新建ehcache.xml，注释啥的应该可以说相当详细了。

```xml
<ehcache>
    <!--
        磁盘存储:将缓存中暂时不使用的对象,转移到硬盘,类似于Windows系统的虚拟内存
        path:指定在硬盘上存储对象的路径
        path可以配置的目录有：
            user.home（用户的家目录）
            user.dir（用户当前的工作目录）
            java.io.tmpdir（默认的临时目录）
            ehcache.disk.store.dir（ehcache的配置目录）
            绝对路径（如：d:\\ehcache）
        查看路径方法：String tmpDir = System.getProperty("java.io.tmpdir");

     -->

    <diskStore path="java.io.tmpdir" />
    <!--

​        defaultCache:默认的缓存配置信息,如果不加特殊说明,则所有对象按照此配置项处理

        maxElementsInMemory:设置了缓存的上限,最多存储多少个记录对象

        eternal:代表对象是否永不过期 (指定true则下面两项配置需为0无限期)

        timeToIdleSeconds:最大的发呆时间 /秒

        timeToLiveSeconds:最大的存活时间 /秒

        overflowToDisk:是否允许对象被写入到磁盘

        说明：下列配置自缓存建立起600秒(10分钟)有效 。

        在有效的600秒(10分钟)内，如果连续120秒(2分钟)未访问缓存，则缓存失效。

        就算有访问，也只会存活600秒。

     -->

    <defaultCache maxElementsInMemory="10000" eternal="false"

                  timeToIdleSeconds="600" timeToLiveSeconds="600" overflowToDisk="true" />

 

    <cache name="myCache" maxElementsInMemory="10000" eternal="false"

                  timeToIdleSeconds="120" timeToLiveSeconds="600" overflowToDisk="true" />

 

</ehcache>

 
```

在这里我配置为：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

​         xsi:noNamespaceSchemaLocation="ehcache.xsd">

​    <diskStore path="java.io.tmpdir/Tmp_EhCache" />

​    <defaultCache 

​        eternal="false" 

​        maxElementsInMemory="1000" 

​        overflowToDisk="false" 

​        diskPersistent="false"

​        timeToIdleSeconds="0" 

​        timeToLiveSeconds="3600" 

​        memoryStoreEvictionPolicy="LRU" />

​    <cache name="content"

​           maxEntriesLocalHeap="200"

​           timeToLiveSeconds="3600">

​    </cache>

</ehcache> 
```



## 1.4.    mapper.xml

在用到的mapper.xml文件里添加cache标签，内容如下

```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```

添加的位置如下：

![1558884585648](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1558884585648.png)

​                                                  

## 1.5.    业务类添加

```java
@Cacheable(value ="content",

​           key = "targetClass + methodName + #p0",

​           condition = "#p0%2==0")

@Override

public User selectByIdCache2(Long id) {

​    // TODO Auto-generated method stub

​    User user = userMapper.selectByPrimaryKey(id);

​    return user;

}
```

 

