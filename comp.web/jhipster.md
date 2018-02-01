## JHipster
参考
* [JHipster](https://jhipster.github.io/)
* [JHipster框架的简要搭建与说明](http://www.cnblogs.com/libingbin/p/5937514.html)
* [如何自学JHipster框架](https://www.zhihu.com/question/41219717)]
* [如何看待JHipster框架](https://www.zhihu.com/question/51082079)
* [JHipster简介](http://www.jdon.com/dl/best/jhipster.html)

内容
* [JHipster简介](#intro)
* [安装](#install)
* [Setting up your environment](#setup)
* [Learn JHipster in 12 minutes with Matt Raible](#minutes12)
* [Technology stack](#Technologystack)


<span id="intro"></span>
#### 简介

**JHipster的亮点**
* 风头超劲，席卷欧美，最新全能Java Web开发程式产生器 (java web generator)。
* 由Java专家累积的开发经验，配上各类实用的框架技术，去繁取精的运用，全方位的配置，制成出完备的开发应用程式。
* 完美Java体系架构，适合各行各业项目，尤其以适用于面向服务的体系结构(SOA)更为胜任。
* 不论菜鸟，老牛或专家，极容易上手，只要你可以下载及建立以下要求的工作环境。
* 快速建成一个制作就绪的基本项目工作模版，令你可以用有限的精力专注业务上的运作。
* 使用技术：jHipster3.8+Spring4.2.6+Spring Boot1.3.5+Hibernate4.3.11+MySQL5.7.12+AngularJs1.5.8

JHipster或者称Java Hipster，是一个应用代码产生器，能够创建Spring Boot + AngularJS的应用。
开源项目地址：[JHipster/Github](http://jhipster.github.io/)。

JHipster使用Node.js和Yeoman产生Java应用代码，使用Maven(Gradle)运行产生的代码，产生代码有如下关键特征：
* src/main/java 目录有Spring Boot 配置类在config包中，JHipster使用Spring的Java 配置，没有XML配置。
* JPA实体或MongoDB文档类是在domain包. JPA实体使用缓存和auto-generated 主键配置. 如果你使用JHipster产生你的JPA实体, 可以创建1:N和N:N关系。
* 在repostiory包中是Spring Data 仓储.
* 可选，你有通常@Service-beans 在服务层. 这些服务通常是配置为事务的 安全的业务对象。
* REST 端点存在web.rest 包中, 支持Spring MVC的REST
* JHipster也产生 Liquibase 改变日志文件，用来处理数据库更新，增加一个实体将创建特定的schema更新，这将会版本化，当应用重启时可被执行。
* 集成Spring的 Test 上下文测试支持.
* JHipster 创建完整可用的AngularJS 前端，使用CRUD来管理你产生的实体。

**客户端技术栈**  

单页面Web应用:
* 响应式页面设计
* HTML5 Boilerplate
* Twitter Bootstrap
* AngularJS
* 兼容 IE9+ 和其他现代浏览器
* 完整的国际化支持，基于 Angular Translate
* 可选 Sass 用于 CSS 设计
* 可选 Spring Websocket 来实现 WebSocket

强大的 Yeoman 开发工作流:
* 使用 Bower 可以轻松的安装 JavaScript 类库
* 使用 Gulp.js 构建, 优化项目, 支持 live reload
* 使用 Karma and PhantomJS 进行测试

那么，如果单页面应用不能满足你的需求呢？
* 支持 Thymeleaf 模板引擎, 用于在服务端渲染页面

**服务端技术栈**

一个完整的 Spring 应用:
* Spring Boot 用于简化应用配置
* Maven 或者 Gradle 用于构建，测试和运行应用
* "development" 和 "production" 配置文件 (支持 Maven 和 Gradle)
* Spring Security
* Spring MVC REST + Jackson
* 可选的 WebSocket 支持 -- 基于 Spring Websocket
* Spring Data JPA + Bean 验证
* 使用 Liquibase 实现数据库自动更新
* Elasticsearch 支持对数据库的搜索功能
* 支持像MongoDB 这样的 document-oriented NoSQL 数据库
* 支持像Cassandra 这样的 column-oriented NoSQL 数据库

支持生产环境：
* Monitoring with Metrics 监控运行状态
* 支持 ehcache (本地缓存) 或者 hazelcast (分布式缓存)
* 可选的 HTTP session 集群 -- 基于 hazelcast
* 优化的静态资源(gzip filter, HTTP cache headers)
* 日志管理 Logback, 可在运行时配置
* HikariCP 连接池，用于性能优化
* 可以将应用构建成一个标准的 WAR 文件或者一个可执行的 JAR 文件


<span id="minutes12"></span>
## Learn JHipster in 12 minutes with Matt Raible
#### What is JHipster?
JHipster is a development platform to generate, develop and deploy `Spring Boot` + `Angular` Web applications and Spring microservices.

#### Goal
Our goal is to generate for you a complete and modern Web app or microservice architecture, unifying:
* A high-performance and robust Java stack on the server side with Spring Boot
* A sleek, modern, mobile-first front-end with Angular and Bootstrap
* A robust microservice architecture with JHipster Registry, Netflix OSS, ELK stack and Docker
* A powerful workflow to build your application with Yeoman, Webpack/Gulp and Maven/Gradle

#### Sample & Sources
You can checkout a sample generated AngularJS 1 application [here](https://github.com/jhipster/jhipster-sample-app).

You can checkout a sample generated Angular 4 application [here](https://github.com/jhipster/jhipster-sample-app-ng2).

JHipster is Open Source, and all development is done on [GitHub](https://github.com/jhipster/generator-jhipster)
* If you want to code with us, feel free to join!
* If you like the project, please give us a star on GitHub

#### Client side options
* HTML5
* CSS3
* Bootstrap
* AngularJS
* Angular
* JQuery
* Websockets
* Yarn
* Webpack
* Bower
* Gulp
* Sass
* Browsersync
* Karma
* Protractor

#### Server side options
* Spring Boot
* Spring Security
* Netflix OSS
* Consul
* Gradle
* Maven
* Hibernate
* Liquibase
* MySQL
* MariaDB
* PostgreSQL
* Oracle
* MS SQL
* MongoDB
* Cassandra
* EhCache
* Hazelcast
* Infinispan
* ElasticSearch
* Kafka
* Swagger
* ELK Stack
* Prometheus
* Thymeleaf
* Gatling
* Cucumber

#### Deployment options
* Docker
* Kubernetes
* Heroku
* CloudFoundry
* AWS
* Boxfuse
* Rancher

#### JHipster Quick Start
* Install JHipster: `yarn global add generator-jhipster`
* Create a new directory and go into it: `mkdir myApp && cd myApp`
* Run JHipster and follow instructions on screen: `jhipster`
* Model your entities with `JDL Studio` and download the resulting `jhipster-jdl.jh` file
* Generate your entities with `jhipster import-jdl jhipster-jdl.jh`
* Assuming you have already installed `Java`, `Git`, `Node.js`, `Yarn` and `Yeoman`.  
  For AngularJS 1, you will also need `Bower` and `Gulp`

#### Modules
* Entity Audit
* Nav Element
* Bootstrap Material Design
* Swagger2markup
* Elasticsearch Reindexer
* Docker
* Darktheme
* Bootswatch
* Google Analytics
* Swagger Cli
* Module
* Fortune
* Ci
* Hatch Entitlements
* Db Helper
* Angular Datatables


<span id="Technologystack"></span>
## Technology stack
#### Technology stack on the client side
Single Web page application:
* `AngularJS v1.x` or `Angular 4`
* Responsive Web Design with `Twitter Bootstrap`
* `HTML5 Boilerplate`
* Compatible with IE11 and modern browsers
* Full internationalization support
* Optional `Sass` support for CSS design
* Optional WebSocket support with `Spring Websocket`

With the great development workflow:
* Easy installation of new JavaScript libraries with `Bower` or `Yarn`
* Build, optimization and live reload with `Gulp.js` or `Webpack`
* Testing with `Karma`, `PhantomJS` and `Protractor`

And what if a single Web page application isn’t enough for your needs?
* Support for the `Thymeleaf` template engine, to generate Web pages on the server side

#### Technology stack on the server side
A complete `Spring application`:
* `Spring Boot` for easy application configuration
* `Maven` or `Gradle` configuration for building, testing and running the application
* `“development”` and `“production”` profiles (both for Maven and Gradle)
* `Spring Security`
* `Spring MVC REST` + `Jackson`
* Optional WebSocket support with `Spring Websocket`
* `Spring Data JPA` + Bean Validation
* Database updates with `Liquibase`
* `Elasticsearch` support if you want to have search capabilities on top of your database
* `MongoDB` support if you’d rather use a document-oriented NoSQL database instead of JPA
* `Cassandra` support if you’d rather use a column-oriented NoSQL database instead of JPA
* `Kafka` support if you want to use a publish-subscribe messaging system

Ready to go into production:
* Monitoring with `Metrics`
* Caching with `ehcache` (local cache), `hazelcast` or `Infinispan`
* Optional HTTP session clustering with `hazelcast`
* Optimized static resources (gzip filter, HTTP cache headers)
* Log management with `Logback`, configurable at runtime
* Connection pooling with `HikariCP` for optimum performance
* Builds a standard WAR file or an executable JAR file
* Support for all major cloud providers: AWS, Cloud Foundry, Heroku, Kubernetes, Docker…

<span id="setup"></span>
## 安装 JHipster
#### 安装类型
我们提供4种使用JHipster的方法：
1. 一个“Yarn本地安装”，这是与JHipster合作的经典方式。一切安装在您的机器上，这可能有点复杂的设置，但这是大多数人通常工作的方式。如有疑问，请选择此安装。
2. “NPM本地安装”，与古典“Yarn本地安装”相同，但使用NPM代替Yarn
3. 一个基于Vagrant的“`development box`”，所有的工具都已经在Ubuntu的虚拟机中设置了。
4. 一个“ Docker ”容器，为您带来一个轻量级的虚拟化容器，并安装了JHipster。

#### 本地安装
安装前置条件
* `JDK 8+`; `Maven`或者`Gradle`（可选）; `Git` 或 `SourceTree`;
* [NodeJs](http://nodejs.org/)
* [Yarn](https://yarnpkg.com/en/docs/install)

使用Yarn本地安装
```
yarn global add yo
yarn global add bower
yarn global add gulp-cli
yarn global add generator-jhipster
```
使用NPM进行本地安装
```
npm install -g yo
npm install -g bower
npm install -g gulp-cli
npm install -g generator-jhipster
```

#### Vagrant development box
[jhipster/jhipster-devbox](https://github.com/jhipster/jhipster-devbox)
`git clone https://github.com/jhipster/jhipster-devbox`


#### Docker installation (for advanced users only)
JHipster has a specific [Dockerfile](https://github.com/jhipster/generator-jhipster/blob/master/Dockerfile), which provides a Docker image.

```
docker image pull jhipster/jhipster

mkdir ~/jhipster
docker container run --name jhipster -v ~/jhipster:/home/jhipster/app -v ~/.m2:/home/jhipster/.m2 -p 8080:8080 -p 9000:9000 -p 3001:3001 -d -t jhipster/jhipster

docker container stop jhipster
docker container rm jhipster
docker image pull jhipster/jhipster
docker container run --name jhipster -v ~/jhipster:/home/jhipster/app -v ~/.m2:/home/jhipster/.m2 -p 8080:8080 -p 9000:9000 -p 3001:3001 -d -t jhipster/jhipster
```
