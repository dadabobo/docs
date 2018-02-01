## Node.js `(nodejs.md)`
参考
* [Node.js - Official Site](https://nodejs.org/en/)
* [Node.js 中文网](http://nodejs.cn/)
* [npm](https://docs.npmjs.com/)
* [Yarn](https://yarnpkg.com/en/docs)
* [Express](http://expressjs.com/)
* [Express中文网](http://www.expressjs.com.cn/)
* [Node.js开发框架Express4.x](http://blog.fens.me/nodejs-express4/)
* [Express的安装和初始化](http://www.cnblogs.com/Victor-Zxk/p/5579726.html)
* [crocodilejs/crocodile-node-mvc-framework](https://github.com/crocodilejs/crocodile-node-mvc-framework)
* [bower解决js的依赖管理](http://blog.fens.me/nodejs-bower-intro/)
* [关于node.js的误会](http://www.cnblogs.com/dolphinX/p/3475090.html)
* [node.js module初步理解](http://www.cnblogs.com/dolphinX/p/3485260.html)
* [7-days-nodejs](http://nqdeng.github.io/7-days-nodejs/)


## Node 技术

#### Node.js 关系数据库ORM 框架
* [sequelize](https://github.com/sequelize/sequelize)
* [node-orm2](https://github.com/dresende/node-orm2)
* [knex](https://github.com/tgriesser/knex)
* [waterline](https://github.com/balderdashy/waterline)

**sequelize**
* [http://docs.sequelizejs.com/](http://docs.sequelizejs.com/)
* [Sequelize 和 MySQL 对照](https://segmentfault.com/a/1190000003987871)
* [Sequelize 开源中国社区](http://www.oschina.net/p/sequelize)
* [Sequelize 中文API文档](https://itbilu.com/nodejs/npm/VkYIaRPz-.html)
  - [1. 快速入门、Sequelize类](https://itbilu.com/nodejs/npm/VkYIaRPz-.html)
  - [2. Model 的定义、使用与Model类的API](https://itbilu.com/nodejs/npm/V1PExztfb.html)
  - [3. 模型（表）之间的关系/关联](https://itbilu.com/nodejs/npm/41qaV3czb.html)
  - [4. 查询与原始查询](https://itbilu.com/nodejs/npm/VJIR1CjMb.html)
  - [5. 实例的使用、Instance类介绍](https://itbilu.com/nodejs/npm/N1sdaHTzb.html)
  - [6. 事务的使用与Transaction类](https://itbilu.com/nodejs/npm/EJO6CcCM-.html)
  - [7. Scopes 作用域的使用](https://itbilu.com/nodejs/npm/E1Eft20MW.html)
  - [8. 钩子函数的使用、Hooks相关API](https://itbilu.com/nodejs/npm/NJoieZl7Z.html)
  - [9. 数据类型类 DataTypes 及其API](https://itbilu.com/nodejs/npm/N1XuSG-QW.html)
  - [10. Migrations 数据迁移与QueryInterface对象](https://itbilu.com/nodejs/npm/VyqgRUVf7.html)
* [node.js sequelize查询问题](https://q.cnblogs.com/q/73287/)




#### module exports

```javascript
// app.js 代码片段
var cartValidation = require('./lib/cartValidation.js');

app.use(cartValidation.checkWaivers);
app.use(cartValidation.checkGuestCounts);
```

`cartValidation.js`
```javascript
module.exports = {
  checkWaivers: function(req, res, next){
    var cart = req.session.cart;
    if(!cart) return next();
    if(cart.some(function(item){ return item.product.requiresWaiver; })){
      if(!cart.warnings) cart.warnings = [];
      cart.warnings.push('One or more of your selected tours requires a waiver.');
    }
    next();
  },

  checkGuestCounts: function(req, res, next){
    var cart = req.session.cart;
    if(!cart) return next();
    if(cart.some(function(item){ return item.guests > item.product.maximumGuests; })){
      if(!cart.errors) cart.errors = [];
      cart.errors.push('One or more of your selected tours cannot accommodate the ' +
        'number of guests you have selected.');
    }
    next();
  }
};
```

```javascript
// 代码片段
var emailService = require('./lib/email.js')(credentials);

// ...
  res.render('email/cart-thank-you',
    { layout: null, cart: cart }, function(err,html){
      if( err ) console.log('error in email template');
      emailService.send(cart.billing.email,
        'Thank you for booking your trip with Meadowlark Travel!',
        html);
    }
// ...
```

```javascript
var nodemailer = require('nodemailer');

module.exports = function(credentials){

  var mailTransport = nodemailer.createTransport({
    service: 'Gmail',
    auth: {
      user: credentials.gmail.user,
      pass: credentials.gmail.password,
    }
  });

  var from = '"Meadowlark Travel" <info@meadowlarktravel.com>';
  var errorRecipient = 'youremail@gmail.com';

  return {
    send: function(to, subj, body){
      // ...
    },
    emailError: function(message, filename, exception){
      // ....
    },
  };
};
```



`auth.js`
```javascript
var User = require('../models/user.js'),
  passport = require('passport'),
  FacebookStrategy = require('passport-facebook').Strategy,
  GoogleStrategy = require('passport-google-oauth').OAuth2Strategy;

passport.serializeUser(function(user, done){
  done(null, user._id);
});

// 。。。

module.exports = function(app, options){

  // if success and failure redirects aren't specified,
  // set some reasonable defaults
  if(!options.successRedirect)
    options.successRedirect = '/account';
  if(!options.failureRedirect)
    options.failureRedirect = '/login';

  return {

    init: function() {
      // 。。。
      app.use(passport.initialize());
      app.use(passport.session());
    },

    registerRoutes: function(){
      // 。。。
    },

  };
};
```

```javascript
// 代码片段
// authentication
var auth = require('./lib/auth.js')(app, {
  baseUrl: process.env.BASE_URL,
  providers: credentials.authProviders,
  successRedirect: '/account',
  failureRedirect: '/unauthorized',
});
// auth.init() links in Passport middleware:
auth.init();

// now we can specify our auth routes:
auth.registerRoutes();
```


## Node 基础
#### 安装与建立项目
安装好 node, npm  
`node -v`  
`npm -v`

**全局模式安装的意义**  
npm安装的时候区分本地模式安装和全局模式安装，“`-g`”就表示全局模式安装，这种模式会被安装在node安装目录的`lib`所在目录的`node_modules`文件夹中，全局使用。

非全局那么就是本地模式安装，这类模式安装会被安装在当前目录的`node_modules`文件夹中，非全局使用。

#### 项目配置 `package.json`
`package.json`用于项目依赖配置及开发者信息，`scripts`属性是用于定义操作命令的，可以非常方便的增加启动命令，比如默认的`start`，用`npm start`代表执行`node ./bin/www`命令。  

查看`package.json`文件。
```json
{
  "name": "nodejs-demo",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "body-parser": "~1.17.1",
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.3",
    "ejs": "~2.5.6",
    "express": "~4.15.2",
    "morgan": "~1.8.1",
    "serve-favicon": "~2.4.2"
  }
}
```

#### npm 包管理
npm 注册库
[http://registry.npmjs.org](http://registry.npmjs.org)  

包创建发布过程
* 注册账号: `npm adduser`  
  user: `wangwg2`
  email: `wangwg2@msn.com`  
* 验证测试 `npm whoami`
* 交互式生成 `package.json`文件: `npm init`
* 发布: `npm publish`
* 搜索：访问 [http://search.npmjs.org/](http://search.npmjs.org/)
* 安装：`npm install package_name`
* 取消发布: `npm unpublish`


---
## Express
#### 安装
npm安装express , `-g` 指令让express进入全局安装：  
`npm install -g express`  
`npm install -g express-generator`  
`express --version`

**安装测试**  
建立项目目录 `myproject`  
```bash
cd myproject
express -e nodejs-demo
cd nodejs-demo
npm install
npm start
```

#### Express 项目目录结构
* `bin`, 存放启动项目的脚本文件
* `node_modules`, 存放所有的项目依赖库。
* `public`，静态文件(css,js,img)
* `routes`，路由文件(MVC中的C,controller)
* `views`，页面文件(Ejs模板)
* `package.json`，项目依赖配置及开发者信息
* `app.js`，应用核心配置文件

#### 项目配置 `package.json`
`package.json`用于项目依赖配置及开发者信息，`scripts`属性是用于定义操作命令的，可以非常方便的增加启动命令，比如默认的`start`，用`npm start`代表执行`node ./bin/www`命令。  

查看`package.json`文件。
```json
{
  "name": "nodejs-demo",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "body-parser": "~1.17.1",
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.3",
    "ejs": "~2.5.6",
    "express": "~4.15.2",
    "morgan": "~1.8.1",
    "serve-favicon": "~2.4.2"
  }
}
```

#### app.js核心文件
```js
// 加载依赖库，原来这个类库都封装在connect中，现在需地注单独加载
var express = require('express');
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

// 加载路由控制
var routes = require('./routes/index');
var users = require('./routes/users');

// 创建项目实例
var app = express();

// 定义EJS模板引擎和模板文件位置，也可以使用jade或其他模型引擎
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

// 定义icon图标
app.use(favicon(__dirname + '/public/favicon.ico'));
// 定义日志和输出级别
app.use(logger('dev'));
// 定义数据解析器
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
// 定义cookie解析器
app.use(cookieParser());
// 定义静态文件目录
app.use(express.static(path.join(__dirname, 'public')));

// 匹配路径和路由
app.use('/', routes);
app.use('/users', users);

// 404错误处理
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// 开发环境，500错误处理和错误堆栈跟踪
if (app.get('env') === 'development') {
  app.use(function(err, req, res, next) {
    res.status(err.status || 500);
    res.render('error', {
      message: err.message,
      error: err
    });
  });
}

// 生产环境，500错误处理
app.use(function(err, req, res, next) {
  res.status(err.status || 500);
  res.render('error', {
    message: err.message,
    error: {}
  });
});

// 输出模型app
module.exports = app;
```


---
<!-- pagebreak -->
## Node.js应用场景

#### 1. Web开发：Express + EJS + Mongoose/MySQL
* `express` 是轻量灵活的Nodejs Web应用框架，它可以快速地搭建网站。Express框架建立在Nodejs内置的Http模块上，并对Http模块再包装，从而实际Web请求处理的功能。
* `ejs` 是一个嵌入的Javascript模板引擎，通过编译生成HTML的代码。
* `mongoose` 是MongoDB的对象模型工具，通过Mongoose框架，可以进行访问MongoDB的操作。
* `mysql` 是连接MySQL数据库的通信API，可以进行访问MySQL的操作。
通常用Nodejs做Web开发，需要3个框架配合使用，就像Java中的SSH。

#### 2. REST开发：Restify
`restify` 是一个基于Nodejs的REST应用框架，支持服务器端和客户端。restify比起express更专注于REST服务，去掉了express中的template, render等功能，同时强化了REST协议使用，版本化支持，HTTP的异常处理。

#### 3. Web聊天室(IM)：Express + Socket.io
`socket.io` 是一个基于Nodejs架构体系的，支持websocket的协议用于时时通信的一个软件包。socket.io 给跨浏览器构建实时应用提供了完整的封装，socket.io完全由javascript实现。

#### 4. Web爬虫：Cheerio/Request
`cheerio` 是一个为服务器特别定制的，快速、灵活、封装jQuery核心功能工具包。Cheerio包括了 jQuery核心的子集，从jQuery库中去除了所有DOM不一致性和浏览器不兼容的部分，揭示了它真正优雅的API。Cheerio工作在一个非常简 单，一致的DOM模型之上，解析、操作、渲染都变得难以置信的高效。基础的端到端的基准测试显示Cheerio大约比JSDOM快八倍(8x)。 Cheerio封装了@FB55兼容的htmlparser，几乎能够解析任何的 HTML 和 XML document。

#### 5. Web博客：Hexo
`Hexo` 是一个简单地、轻量地、基于Node的一个静态博客框架。通过Hexo我们可以快速创建自己的博客，仅需要几条命令就可以完成。
发布时，Hexo可以部署在自己的Node服务器上面，也可以部署github上面。对于个人用户来说，部署在github上好处颇多，不仅可以省 去服务器的成本，还可以减少各种系统运维的麻烦事(系统管理、备份、网络)。所以，基于github的个人站点，正在开始流行起来….

#### 6. Web论坛: nodeclub
`Node Club` 是用 Node.js 和 MongoDB 开发的新型社区软件，界面优雅，功能丰富，小巧迅速， 已在Node.js 中文技术社区 CNode 得到应用，但你完全可以用它搭建自己的社区。

#### 7. Web幻灯片：Cleaver
`Cleaver` 可以生成基于Markdown的演示文稿。如果你已经有了一个Markdown的文档，30秒就可以制作成幻灯片。Cleaver是为Hacker准备的工具。

#### 8. 前端包管理平台: bower.js
`Bower` 是 twitter 推出的一款包管理工具，基于nodejs的模块化思想，把功能分散到各个模块中，让模块和模块之间存在联系，通过 Bower 来管理模块间的这种联系。

#### 9. OAuth认证：Passport
`Passport` 项目是一个基于Nodejs的认证中间件。Passport目的只是为了“登陆认证”，因此，代码干净，易维护，可以方便地集成到其他的应用中。Web应用 一般有2种登陆认证的形式：用户名和密码认证登陆,OAuth认证登陆。Passport可以根据应用程序的特点，配置不同的认证机制。本文将介绍，用户 名和密码的认证登陆。

#### 10. 定时任务工具: later
`Later` 是一个基于Nodejs的工具库，用最简单的方式执行定时任务。Later可以运行在Node和浏览器中。

#### 11. 浏览器环境工具: browserify
`Browserify` 用 Browserify 的操作，分为3个步骤。1. 写node程序或者模块, 2. 用Browserify 预编译成 bundle.js, 3. 在HTML页面中加载bundle.js。

#### 12. 命令行编程工具：Commander
`commander` 是一个轻巧的nodejs模块，提供了用户命令行输入和参数解析强大功能。commander源自一个同名的Ruby项目。commander的特性：自 记录代码,自动生成帮助,合并短参数（“ABC”==“-A-B-C”）,默认选项,强制选项??,命令解析,提示符。

#### 13. Web控制台工具: tty.js
`tty.js` 是一个支持在浏览器中运行的命令行窗口，基于node.js平台，依赖socket.io库，通过websocket与linux系统通信。特性：支持多 tab窗口模型; 支持vim,mc,irssi,vifm语法; 支持xterm鼠标事件; 支持265色显示; 支持session。

#### 14. 客户端应用工具: node-webwit
`Node-Webkit` 是NodeJS与WebKit技术的融合，提供一个跨Windows、Linux平台的客户端应用开发的底层框架，利用流行的Web技术 （Node.JS，JavaScript，HTML5）来编写应用程序的平台。应用程序开发人员可以轻松的利用Web技术来实现各种应用程序。Node- Webkit性能和特色已经让它成为当今世界领先的Web技术应用程序平台。

#### 15. 操作系统: node-os
`NodeOS` 是采用NodeJS开发的一款友好的操作系统，该操作系统是完全建立在Linux内核之上的，并且采用shell和NPM进行包管理，采用NodeJS不 仅可以很好地进行包管理，还可以很好的管理脚本、接口等。目前，Docker和Vagrant都是采用NodeOS的首个版本进行构建的。

---
<!-- pagebreak -->
## Nodejs学习路线图
我们看到Nodejs已经被广发地应用在各种的场景了，针对Nodejs的应用场景，我们应该如何学习Nodejs呢？

以下内容是我整理的文档和教程，每个软件包对应一篇文章，大家可以根据自己的需要进行阅读，完整的文章列表，可以查看：从零开始nodejs系列文章。

* 项目管理：npm,grunt, bower, yeoman
* Web开发：express,ejs,hexo, socket.io, restify, cleaver, stylus, browserify,cheerio
* 工具包：underscore,moment,connet,later,log4js,passport,passport(oAuth),domain,require,reap,
commander,retry
* 数据库：mysql,mongoose,reids
* 异步：async,wind
* 部署：forever,pm2
* 测试：jasmine,karma
* 跨平台：rio,tty
* 内核：cluster,http,request
* 算法：ape-algorithm(快速排序),ape-algorithm(桶排序)

Nodejs在快速的发展着，软件包版本升级的很快，文章有运行不通的地方请参考官方文档解决。我也会不定期更新文章，尽量保持文章代码的可用性。

### Node.js开发指南
* 第1章 Node.js 简介
* 第2章 安装和配置Node.js
* 第3章 Node.js 快速入门
* 第4章 Node.js 核心模块
* 第5章 使用 Node.js 进行 Web 开发
* 第6章 Node.js 进阶话题


### Node与Express开发
* 第 1 章 初识Express
* 第 2 章 从Node 开始
* 第 3 章 省时省力的Express
* 第 4 章 工欲善其事，必先利其器
* 第 5 章 质量保证
* 第 6 章 请求和响应对象
* 第 7 章 Handlebars 模板引擎
* 第 8 章 表单处理
* 第 9 章 Cookie 与会话
* 第 10 章 中间件
* 第 11 章 发送邮件
* 第 12 章 与生产相关的问题
* 第 13 章 持久化
* 第 14 章 路由
* 第 15 章 REST API 和 JSON
* 第 16 章 静态内容
* 第 17 章 在Express 中实现MVC
* 第 18 章 安全
* 第 19 章 集成第三方API
* 第 20 章 调试
* 第 21 章 正式启用
* 第 22 章 维护
* 第 23 章 其他资源

### Web Development with Node and Express Leveraging the JavaScript Stack
* Chapter 1. Introducing Express
* Chapter 2. Getting Started with Node
* Chapter 3. Saving Time with Express
* Chapter 4. Tidying Up
* Chapter 5. Quality Assurance
* Chapter 6. The Request and Response Objects
* Chapter 7. Templating with Handlebars
* Chapter 8. Form Handling
* Chapter 9. Cookies and Sessions
* Chapter 10. Middleware
* Chapter 11. Sending Email
* Chapter 12. Production Concerns
* Chapter 13. Persistence
* Chapter 14. Routing
* Chapter 15. REST APIs and JSON
* Chapter 16. Static Content
* Chapter 17. Implementing MVC in Express
* Chapter 18. Security
* Chapter 19. Integrating with Third-Party APIs
* Chapter 20. Debugging
* Chapter 21. Going Live
* Chapter 22. Maintenance
* Chapter 23. Additional Resources


### MEAN Web Development (Second Edition)
Chapter 1: Introduction to MEAN
* Three-tier web application development
* The evolution of JavaScript
* Introduction to ECMAScript 2015
* Introducing MEAN
* Installing MongoDB
* Installing Node.js
* Introducing npm

Chapter 2: Getting Started with Node.js
* Introduction to Node.js
* JavaScript Closures
* Node modules
* Developing Node.js web applications

Chapter 3: Building an Express Web Application
* Introducing Express
* Installing Express
* Creating your first Express application
* The application, request, and response objects
* External middleware
* Implementing the MVC pattern
* Configuring an Express application
* Rendering views
* Serving static files
* Configuring sessions

Chapter 4: Introduction to MongoDB
* Introduction to NoSQL
* Introducing MongoDB
* Key features of MongoDB
* MongoDB shell
* MongoDB databases
* MongoDB collections
* MongoDB CRUD operations

Chapter 5: Introduction to Mongoose
* Understanding Mongoose schemas
* Extending your Mongoose schema
* Defining custom model methods
* Model validation
* Using Mongoose middleware
* Using Mongoose ref fields

Chapter 6: Managing User Authentication Using Passport
* Introducing Passport
* Understanding Passport strategies
* Understanding Passport OAuth strategies

Chapter 7: Introduction to Angular
* Introducing Angular 2
* Introduction to TypeScript
* Angular 2 Architecture
* The project setup
* Managing authentication

Chapter 8: Creating a MEAN CRUD Module
* Introducing CRUD modules
* Setting up the Express components
* Using the HTTP client
* The ReactiveX library
* Implementing the Angular module
* Wrapping up

Chapter 9: Adding Real-time Functionality Using Socket.io
* Introducing WebSockets
* Introducing Socket.io
* Installing Socket.io
* Building a Socket.io chat
* Creating the Chat Angular module

Chapter 10: Testing MEAN Applications
* Introducing JavaScript testing
* Testing your Express application
* Installing Mocha
* Testing your Angular application

Chapter 11: Automating and Debugging MEAN Applications
* Using NPM scripts
* Introducing Webpack
* Introducing ESLint
* Using Nodemon
* Debugging Express with V8 inspector
* Debugging your application
* Debugging Angular applications with Angular Augury


### Getting MEAN with Mongo, Express, Angular, and Node
**PART 1** -  SETTING THE BASELINE
* **Chapter 1** Introducing full-stack development
* **Chapter 2** Designing a MEAN stack architecture

**PART 2** - BUILDING A NODE WEB APPLICATION
* **Chapter 3**: Creating and setting up a MEAN project
* **Chapter 4**: Building a static site with Node.js and Express
* **Chapter 5**: Building a data model with MongoDB and Mongoose
* **Chapter 6**: Writing a REST API: Exposing your MongoDB database to the application
* **Chapter 7**: Consuming a REST API: Using an API from inside Express

**PART 3** - ADDING A DYNAMIC FRONT END WITH ANGULAR
* **Chapter 8**: Creating an Angular application with TypeScript
* **Chapter 9**: Building a Single Page Application with Angular: Foundations
* **Chapter 10**: Building a Single Page Application with Angular: The next level

**PART 4** - MANAGING AUTHENTICATION AND USER SESSIONS
* **Chapter 11**: Authenticating users, managing sessions and securing APIs


### Express in Action
**Part 1:  Introduction**
* 1  What is Express?
* 2  The Basics of Node.js
* 3  Foundations of Express

**Part 2:  Core Express**
* 4  Middleware
* 5  Routing
* 6  Building APIs

**Part 3:  Express in Context**
* 7  Views & Templates : Jade & EJS
* 8  Persisting your data with MongoDB
* 9  Testing Express Applications
* 10  Security
* 11  Deployment: Assets & Heroku
* 12  Best practices

**Appendixes:**
* A  Other Helpful Modules
