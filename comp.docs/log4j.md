## Log4J

Log4J的配置文件(Configuration File)就是用来设置记录器的级别、存放器和布局的，它可接key=value格式的设置或xml格式的设置信息。通过配置，可以创建出Log4J的运行环境。

参考文档
[log4j.properties配置详解](http://www.cnblogs.com/ITEagle/archive/2010/04/23/1718365.html)

**log4j.properties 实例文件**

参考路径：
* `src/main/resources/log4j.properties`
* `src/test/resources/log4j.properties`

```bash
log4j.rootCategory=INFO, stdout

# Configure logging for testing: optionally with log file
#log4j.rootLogger=WARN, stdout
#log4j.rootLogger=WARN, stdout, logfile

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %40.40c:%4L - %m%n

#log4j.appender.logfile=org.apache.log4j.FileAppender
#log4j.appender.logfile.File=build/spring.log
#log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
#log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - %m%n
```

-----

#### 配置文件
Log4J配置文件的基本格式如下：
````shell
#配置根Logger
log4j.rootLogger = [level], appenderName1, appenderName2, ...

#配置日志信息输出目的地Appender
log4j.appender.appenderName = fully.qualified.name.of.appender.class
    log4j.appender.appenderName.option1 = value1
    ...
    log4j.appender.appenderName.optionN = valueN 

#配置日志信息的格式（布局）
log4j.appender.appenderName.layout = fully.qualified.name.of.layout.class
    log4j.appender.appenderName.layout.option1 = value1
    ...
    log4j.appender.appenderName.layout.optionN = valueN
````

**日志输出级别** `level`  
共有5级：`FATAL`、`ERROR`、`WARN`、`INFO`、`DEBUG`
* `FATAL`
* `ERROR` 为严重错误 主要是程序的错误
* `WARN` 为一般警告，比如session丢失
* `INFO` 为一般要显示的信息，比如登录登出
* `DEBUG` 为程序的调试信息

**日志输出目的地** `Appender`  
Log4j提供的appender有以下几种：
* `org.apache.log4j.ConsoleAppender`（控制台）
* `org.apache.log4j.FileAppender`（文件）
* `org.apache.log4j.DailyRollingFileAppender`（每天产生一个日志文件）
* `org.apache.log4j.RollingFileAppender`（文件大小到达指定尺寸的时候产生一个新的文件）
* `org.apache.log4j.WriterAppender`（将日志信息以流格式发送到任意指定的地方）

**日志输出格式** `Layout`  
Log4j提供的layout有以下几种：
* `org.apache.log4j.HTMLLayout`（以HTML表格形式布局）
* `org.apache.log4j.PatternLayout`（可以灵活地指定布局模式）
* `org.apache.log4j.SimpleLayout`（包含日志信息的级别和信息字符串）
* `org.apache.log4j.TTCCLayout`（包含日志产生的时间、线程、类别等等信息）

控制台选项
````
Threshold=DEBUG         指定日志消息的输出最低层次。
ImmediateFlush=true     默认值是true,意谓着所有的消息都会被立即输出。
Target=System.err       默认情况下是：System.out,指定输出控制台
````

FileAppender 选项
````
Threshold=DEBUF         指定日志消息的输出最低层次。
ImmediateFlush=true     默认值是true,意谓着所有的消息都会被立即输出。
File=mylog.txt          指定消息输出到mylog.txt文件。
Append=false            默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。
````

RollingFileAppender 选项
````
Threshold=DEBUG         指定日志消息的输出最低层次。
ImmediateFlush=true     默认值是true,意谓着所有的消息都会被立即输出。
File=mylog.txt          指定消息输出到mylog.txt文件。
Append=false            默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。
MaxFileSize=100KB       后缀可以是KB, MB 或者是 GB. 在日志文件到达该大小时，将会自动滚动。
MaxBackupIndex=2        指定可以产生的滚动文件的最大数。
````

**打印参数**  
Log4J采用类似C语言中的printf函数的打印格式格式化日志信息，如下:
````
%m   输出代码中指定的消息
%p   输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL 
%r   输出自应用启动到输出该log信息耗费的毫秒数 
%c   输出所属的类目，通常就是所在类的全名 
%t   输出产生该日志事件的线程名 
%n   输出一个回车换行符，Windows平台为“\r\n”，Unix平台为“\n” 
%d   输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式.
     比如：%d{yyy MMM dd HH:mm:ss, SSS}，输出类似：2002年10月18日  22 ： 10 ： 28, 921
%l   输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。
     举例：Testlog4.main(TestLog4.java: 10 ) 
````

#### 在代码中初始化Logger:
1. 在程序中调用`BasicConfigurator.configure()`方法：给根记录器增加一个`ConsoleAppender`，输出格式通过`PatternLayout`设为`"%-4r [%t] %-5p %c %x - %m%n"`，还有根记录器的默认级别是`Level.DEBUG`.
2. 配置放在文件里，通过命令行参数传递文件名字，通过`PropertyConfigurator.configure(args[x])`解析并配置；
3. 配置放在文件里，通过环境变量传递文件名等信息，利用log4j默认的初始化过程解析并配置；
4. 配置放在文件里，通过应用服务器配置传递文件名等信息，利用一个特殊的servlet来完成配置。


#### 为不同的 Appender 设置日志输出级别：
`log4j.properties`
````shell
### set log levels ###
log4j.rootLogger = debug, stdout, D, E

### 输出到控制台 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern =  %d{ABSOLUTE} %5p %c{ 1 }:%L - %m%n

### 输出到日志文件 ###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = logs/log.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = DEBUG ## 输出DEBUG级别以上的日志
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

### 保存异常信息到单独文件 ###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = logs/error.log ## 异常日志文件名
log4j.appender.D.Append = true
log4j.appender.D.Threshold = ERROR ## 只输出ERROR级别以上的日志!!!
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
````

**使用代码**
````java
public class TestLog4j {
    public static void main(String[] args)  {
        PropertyConfigurator.configure("./conf/log4j.properties");
        Logger logger = Logger.getLogger(TestLog4j.class);
        logger.debug("debug");
        logger.error("error");
    }
}
````


**文件配置Sample1**
````
log4j.rootLogger=DEBUG,A1,R
#log4j.rootLogger=INFO,A1,R
# ConsoleAppender 输出
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%-d{yyyy-MM-dd HH:mm:ss,SSS} [%c]-[%p] %m%n
# File 输出 一天一个文件,输出路径可以定制,一般在根路径下
log4j.appender.R=org.apache.log4j.DailyRollingFileAppender
log4j.appender.R.File=blog_log.txt
log4j.appender.R.MaxFileSize=500KB
log4j.appender.R.MaxBackupIndex=10
log4j.appender.R.layout=org.apache.log4j.PatternLayout
log4j.appender.R.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss,SSS} [%t] [%c] [%p] - %m%n
````

**文件配置Sample2**  
下面给出的Log4J配置文件实现了输出到控制台，文件，回滚文件，发送日志邮件，输出到数据库日志表，自定义标签等全套功能。
````
log4j.rootLogger=DEBUG,CONSOLE,A1,im
#DEBUG,CONSOLE,FILE,ROLLING_FILE,MAIL,DATABASE
log4j.addivity.org.apache=true
###################
# Console Appender
###################
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.Threshold=DEBUG
log4j.appender.CONSOLE.Target=System.out
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=[framework] %d - %c -%-4r [%t] %-5p %c %x - %m%n
#log4j.appender.CONSOLE.layout.ConversionPattern=[start]%d{DATE}[DATE]%n%p[PRIORITY]%n%x[NDC]%n%t[THREAD] n%c[CATEGORY]%n%m[MESSAGE]%n%n
#####################
# File Appender
#####################
log4j.appender.FILE=org.apache.log4j.FileAppender
log4j.appender.FILE.File=file.log
log4j.appender.FILE.Append=false
log4j.appender.FILE.layout=org.apache.log4j.PatternLayout
log4j.appender.FILE.layout.ConversionPattern=[framework] %d - %c -%-4r [%t] %-5p %c %x - %m%n
# Use this layout for LogFactor 5 analysis
########################
# Rolling File
########################
log4j.appender.ROLLING_FILE=org.apache.log4j.RollingFileAppender
log4j.appender.ROLLING_FILE.Threshold=ERROR
log4j.appender.ROLLING_FILE.File=rolling.log
log4j.appender.ROLLING_FILE.Append=true
log4j.appender.ROLLING_FILE.MaxFileSize=10KB
log4j.appender.ROLLING_FILE.MaxBackupIndex=1
log4j.appender.ROLLING_FILE.layout=org.apache.log4j.PatternLayout
log4j.appender.ROLLING_FILE.layout.ConversionPattern=[framework] %d - %c -%-4r [%t] %-5p %c %x - %m%n
####################
# Socket Appender
####################
log4j.appender.SOCKET=org.apache.log4j.RollingFileAppender
log4j.appender.SOCKET.RemoteHost=localhost
log4j.appender.SOCKET.Port=5001
log4j.appender.SOCKET.LocationInfo=true
# Set up for Log Facter 5
log4j.appender.SOCKET.layout=org.apache.log4j.PatternLayout
log4j.appender.SOCET.layout.ConversionPattern=[start]%d{DATE}[DATE]%n%p[PRIORITY]%n%x[NDC]%n%t[THREAD]%n%c[CATEGORY]%n%m[MESSAGE]%n%n
########################
# Log Factor 5 Appender
########################
log4j.appender.LF5_APPENDER=org.apache.log4j.lf5.LF5Appender
log4j.appender.LF5_APPENDER.MaxNumberOfRecords=2000
########################
# SMTP Appender
#######################
log4j.appender.MAIL=org.apache.log4j.net.SMTPAppender
log4j.appender.MAIL.Threshold=FATAL
log4j.appender.MAIL.BufferSize=10
log4j.appender.MAIL.From=chenyl@yeqiangwei.com
log4j.appender.MAIL.SMTPHost=mail.hollycrm.com
log4j.appender.MAIL.Subject=Log4J Message
log4j.appender.MAIL.To=chenyl@yeqiangwei.com
log4j.appender.MAIL.layout=org.apache.log4j.PatternLayout
log4j.appender.MAIL.layout.ConversionPattern=[framework] %d - %c -%-4r [%t] %-5p %c %x - %m%n
########################
# JDBC Appender
#######################
log4j.appender.DATABASE=org.apache.log4j.jdbc.JDBCAppender
log4j.appender.DATABASE.URL=jdbc:mysql://localhost:3306/test
log4j.appender.DATABASE.driver=com.mysql.jdbc.Driver
log4j.appender.DATABASE.user=root
log4j.appender.DATABASE.password=
log4j.appender.DATABASE.sql=INSERT INTO LOG4J (Message) VALUES ('[framework] %d - %c -%-4r [%t] %-5p %c %x - %m%n')
log4j.appender.DATABASE.layout=org.apache.log4j.PatternLayout
log4j.appender.DATABASE.layout.ConversionPattern=[framework] %d - %c -%-4r [%t] %-5p %c %x - %m%n
log4j.appender.A1=org.apache.log4j.DailyRollingFileAppender
log4j.appender.A1.File=SampleMessages.log4j
log4j.appender.A1.DatePattern=yyyyMMdd-HH'.log4j'
log4j.appender.A1.layout=org.apache.log4j.xml.XMLLayout
###################
#自定义Appender
###################
log4j.appender.im = net.cybercorlin.util.logger.appender.IMAppender
log4j.appender.im.host = mail.cybercorlin.net
log4j.appender.im.username = username
log4j.appender.im.password = password
log4j.appender.im.recipient = corlin@yeqiangwei.com
log4j.appender.im.layout=org.apache.log4j.PatternLayout
log4j.appender.im.layout.ConversionPattern =[framework] %d - %c -%-4r [%t] %-5p %c %x - %m%n
````
