## Spring Boot How-to指南

本章节将回答一些常见的"我该怎么做"类型的问题，这些问题在我们使用Spring Boot时经常遇到。这虽然不是一个详尽的列表，但它覆盖了很多方面。

如果遇到一个特殊的我们没有覆盖的问题，你可以查看stackoverflow.com，看是否已经有人给出了答案；这也是一个很好的提新问题的地方（请使用 springboot标签）。

我们也乐意扩展本章节；如果想添加一个'how-to'，你可以给我们发一个pull请求。

### 1. Spring Boot应用
* 创建自己的FailureAnalyzer
* 解决自动配置问题
* 启动前自定义Environment或ApplicationContext
* 构建ApplicationContext层次结构（添加父或根上下文）
* 创建一个非web（non-web）应用

### 2. 属性与配置
* 运行时暴露属性
* 外部化SpringApplication配置
* 改变应用程序外部配置文件的位置
* 使用'short'命令行参数
* 使用YAML配置外部属性
* 设置生效的Spring profiles
* 根据环境改变配置
* 发现外部属性的内置选项

### 3. 内嵌servlet容器
* 为应用添加Servlet，Filter或Listener
* 改变HTTP端口
* 使用随机未分配的HTTP端口
* 发现运行时的HTTP端口
* 配置SSL
* 配置访问日志
* 在前端代理服务器后使用
* 配置Tomcat
* 启用Tomcat的多连接器
* 使用Tomcat的LegacyCookieProcessor
* 使用Jetty替代Tomcat
* 配置Jetty
* 使用Undertow替代Tomcat
* 配置Undertow
* 启用Undertow的多监听器
* 使用@ServerEndpoint创建WebSocket端点
* 启用HTTP响应压缩
* *使用Tomcat 7.x或8.0*
* *使用Jetty9.2*
* *使用Jetty 8*

### 4. Spring MVC
* 编写JSON REST服务
* 编写XML REST服务
* 自定义Jackson ObjectMapper
* 自定义@ResponseBody渲染
* 处理Multipart文件上传
* 关闭Spring MVC DispatcherServlet
* 关闭默认的MVC配置
* 自定义ViewResolvers
* *Velocity*
* *使用Thymeleaf 3*


### 5. HTTP 客户端
* 配置RestTemplate使用代理

### 6. 日志
* 配置Logback
* 配置Log4j

### 7. 数据访问
* 配置数据源
* 配置两个数据源
* 使用Spring Data仓库
* 从Spring配置分离 @Entity 定义
* 配置JPA属性
* 使用自定义EntityManagerFactory
* 使用两个EntityManagers
* 使用普通的persistence.xml
* 使用Spring Data JPA和Mongo仓库
* 将Spring Data仓库暴露为REST端点
* 配置JPA使用的组件


### 8. 数据库初始化
* 使用JPA初始化数据库
* 使用Hibernate初始化数据库
* 使用Spring JDBC初始化数据库
* 初始化Spring Batch数据库
* 使用高级数据迁移工具

### 9. Messaging
* Disable transacted JMS session

### 10. 批处理应用
* 在启动时执行Spring Batch作业

### 11. 执行器（Actuator）
* 改变HTTP端口或执行器端点的地址
* 自定义WhiteLabel错误页面
* Actuator和Jersey

### 12. 安全
* 关闭Spring Boot安全配置
* 改变AuthenticationManager并添加用户账号
* 当前端使用代理服务器时启用HTTPS

### 13. 热交换
* 重新加载静态内容
* 在不重启容器的情况下重新加载模板
* 应用快速重启
* 在不重启容器的情况下重新加载Java类

### 14. 构建
* 生成构建信息
* 生成Git信息
* 自定义依赖版本
* 使用Maven创建可执行JAR
* 将Spring Boot应用作为依赖
* 在可执行jar运行时提取特定的版本
* 使用排除创建不可执行的JAR
* 远程调试使用Maven启动的Spring Boot项目
* 远程调试使用Gradle启动的Spring Boot项目
* 使用Ant构建可执行存档（不使用spring-bootantlib）

### 15. 传统部署
* 创建一个可部署的war文件
* 为老的servlet容器创建一个可部署的war文件
* 将现有的应用转换为Spring Boot
* 部署WAR到Weblogic
* 部署WAR到老的(Servlet2.5)容器
