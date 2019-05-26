# Log4j #

##Log4j的必要性
>参考链接：[https://www.jianshu.com/p/0427c2666657](https://www.jianshu.com/p/0427c2666657 "为什么要用Log4j来替代System.out.println")

此部分解释为什么要用Log4j来代替System.out.println。


1. 	更方便、可分级。Sys.out…，输出一堆掺杂的信息，不易寻找。

1. 	绝大部分情况下，项目是脱离ide启动的，不能看到控制台打印的语句，而Log4j可以生成文件。

1. 	一旦哪天程序员决定不要显示Sys.out…的东西了，只能一行一行地把这些垃圾语句注释掉。如果哪天有需要调试变量，则只能再一行一行地去掉这些注释恢复Sys.out…语句。而Log4j可以通过一个配置文件来灵活地配置，而不需要修改应用代码。


关于log4j 的五个问题及答案：


1. 	什么情况使用log4j、什么情况下使用System.out.println?
System.out.println在开发中可以使用，部署项目后用日志文件。最好不要用System.out.println。

1. 	log4j是否会影响性能？
是的，log4j会拖慢进程响应时间。

1. 	为什么log4j日志的最高级是error，但error却是“虽然发生错误事件，但仍然不影响系统的继续运行”？
error 指出虽然发生错误事件，但仍然不影响系统的继续运行。
	
 日志有7个等级：
A：off 最高等级，用于关闭所有日志记录。
B：fatal 指出每个严重的错误事件将会导致应用程序的退出。
C：error 指出虽然发生错误事件，但仍然不影响系统的继续运行。
D：warm 表明会出现潜在的错误情形。
E：info 一般和在粗粒度级别上，强调应用程序的运行全程。
F：debug 一般用于细粒度级别上，对调试应用程序非常有帮助。
G：all 最低等级，用于打开所有日志记录。
而log4j建议使用四种：debug、info、warn、error

1. log4j 的文件保存位置？
没有固定的位置，我放在了：
项目文件夹/logs/log.log

## Log4j的详解
>参考链接:[https://blog.csdn.net/u013870094/article/details/79518028](https://blog.csdn.net/u013870094/article/details/79518028 "最详细的Log4J使用教程")

Log4j支持两种配置文件格式，一种是XML格式的文件，一种是Java特性文件（键=值）。下面我们介绍使用Java特性文件做为配置文件的方法，即配置log4j.properties，这个文件放置在src/main/resources里。	


1. 配置根Logger，其语法为：
```
log4j.rootLogger = [ level ] , appenderName, appenderName,``` 
其中，level 是日志记录的优先级，分为OFF、FATAL、ERROR、WARN、INFO、DEBUG、ALL或者您定义的级别。Log4j建议只使用四个级别，优先级从高到低分别是ERROR、WARN、INFO、DEBUG。
通过在这里定义的级别，您可以控制到应用程序中相应级别的日志信息的开关。比如在这里定 义了INFO级别，则应用程序中所有DEBUG级别的日志信息将不被打印出来。 appenderName就是指B日志信息输出到哪个地方。您可以同时指定多个输出目的地。

1. 配置日志信息输出目的地Appender，其语法为： 
	```
	log4j.appender.appenderName = fully.qualified.name.of.appender.class  
	log4j.appender.appenderName.option1 = value1  
	  
	log4j.appender.appenderName.option = valueN
	```
其中，Log4j提供的appender有以下几种：
```
org.apache.log4j.ConsoleAppender（控制台），  
org.apache.log4j.FileAppender（文件），  
org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件），  
org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件），  
org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）
```

1. 配置日志信息的格式（布局），其语法为：
```
log4j.appender.appenderName.layout = fully.qualified.name.of.layout.class  
log4j.appender.appenderName.layout.option1 = value1  
…  
log4j.appender.appenderName.layout.option = valueN
其中，Log4j提供的layout有以e几种：
org.apache.log4j.HTMLLayout（以HTML表格形式布局），  
org.apache.log4j.PatternLayout（可以灵活地指定布局模式），  
org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串），  
org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）
```
Log4J采用类似C语言中的printf函数的打印格式格式化日志信息，打印参数如下： %m 输出代码中指定的消息
```
%p 输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL  
%r 输出自应用启动到输出该log信息耗费的毫秒数  
%c 输出所属的类目，通常就是所在类的全名  
%t 输出产生该日志事件的线程名  
%n 输出一个回车换行符，Windows平台为“rn”，Unix平台为“n”  
%d 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss,SSS}，输出类似：2002年10月18日 22：10：28，921  
%l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main(TestLog4.java:10)
```

##Log4j的使用
>主参考:[http://blog.didispace.com/springbootlog4j/](http://blog.didispace.com/springbootlog4j/ "Spring boot中使用log4j记录日志 ")
>参考项目：
>- [https://gitee.com/micai/micai-springboot/tree/master/micai-springboot-log4j-8](https://gitee.com/micai/micai-springboot/tree/master/micai-springboot-log4j-8)
>
- [https://github.com/bycuimiao/springboot2-log4j2-demo](https://github.com/bycuimiao/springboot2-log4j2-demo)

1. 引入Log4j依赖
在创建Spring Boot工程时，我们引入了spring-boot-starter，其中包含了spring-boot-starter-logging，该依赖内容就是Spring Boot默认的日志框架Logback，所以我们在引入log4j之前，需要先排除该包的依赖，再引入log4j的依赖，就像下面这样：

	```
	<dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-starter</artifactId>
	    <exclusions>
	        <exclusion> 
	            <groupId>org.springframework.boot</groupId>
	            <artifactId>spring-boot-starter-logging</artifactId>
	        </exclusion>
	    </exclusions>
	</dependency>
	
	<dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-starter-log4j</artifactId>
	</dependency>
	```

1. 配置log4j.properties
在引入了log4j依赖之后，只需要在src/main/resources目录下加入log4j.properties配置文件，就可以开始对应用的日志进行配置使用。
	```
	# 配置根Logger，输出的日志最低级别为INFO，输出stdout,errorfile这两个日志
	log4j.rootCategory=INFO, stdout, errorfile
	log4j.category.team.softwarede.confersys=DEBUG, confersys
	log4j.logger.error=errorfile
	
	# 配置stdout日志
	# print in console
	log4j.appender.stdout=org.apache.log4j.ConsoleAppender
	log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
	log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss,SSS} %5p %c{1}:%L - %m%n
	
	# 配置errorfile
	# 配置日志输出目的地
	log4j.appender.errorfile=org.apache.log4j.DailyRollingFileAppender
	# 配置文件的输出位置
	# 这样配置会将error.log放在项目文件夹/logs下
	log4j.appender.errorfile.file=logs/error.log
	# 配置输出的日期格式	
	log4j.appender.errorfile.DatePattern='.'yyyy-MM-dd
	# 配置输出的日志的级别
	log4j.appender.errorfile.Threshold = ERROR
  	# 配置日志信息的格式
	log4j.appender.errorfile.layout=org.apache.log4j.PatternLayout
	log4j.appender.errorfile.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss,SSS} %5p %c{1}:%L - %m%n
	
	# package
	log4j.appender.confersy=org.apache.log4j.DailyRollingFileAppender
	log4j.appender.confersy.file=logs/my.log 
	log4j.appender.confersy.DatePattern='.'yyyy-MM-dd
	log4j.appender.confersy.layout=org.apache.log4j.PatternLayout
	log4j.appender.confersy.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss,SSS} %5p %c{1}:%L ---- %m%n
	
	#开启mybatis的打印SQL的功能
	#print SQL
	logging.level.com.spring.jiminshiro.mapper=debug
	```



