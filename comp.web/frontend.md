## frontend

参考
* [www.w3cplus.com](https://www.w3cplus.com/)
* [runoob](http://www.runoob.com/)
* [NPM 使用介绍](http://www.runoob.com/nodejs/nodejs-npm.html)
* [Yeoman：Web 应用开发流程与工具](https://www.ibm.com/developerworks/cn/web/1402_chengfu_yeoman/index.html)
* [Yarn JS 包管理器](https://sheerdevelopment.com/posts/facebook-js-5)
* [yarn](https://yarn.bootcss.com/)
* [使用YEOMAN创建属于自己的前端工作流](https://segmentfault.com/a/1190000004896264)
* [Yeoman官方教程：用Yeoman和AngularJS做Web应用](http://blog.jobbole.com/65399/)
* [使用Yeoman快速启动AngularJS项目开发](http://www.cnblogs.com/owenChen/p/3405799.html)
* [Yeoman学习与实践笔记 ](http://www.cnblogs.com/cocowool/archive/2013/03/09/2952003.html)
* [Brackets 10款常用插件](http://www.open-open.com/lib/view/open1414886577762.html)
* [emmet使用](https://www.w3cplus.com/tools/emmet-cheat-sheet.html)

软件说明
* Yeoman  
  Yeoman is a generic scaffolding system allowing the creation of any kind of app. It allows for rapidly getting started on new projects and streamlines the maintenance of existing projects.
* Yarn  
  Yarn is a package manager for your code. It allows you to use and share code with other developers from around the world. Yarn does this quickly, securely, and reliably so you don’t ever have to worry.
* Bower  
  A package manager for the web
* Gulp

**YARN**  
YARN 是一个新的包管理工具，它可替换现有 npm 客户 端的机制，同时兼容 npm 注册表。 如果使用 npm 客户 端，根据依赖库的不同安装顺序，它会在 node_modules 下得到一个不同的树结构。 这种非确定性的特点可能导 致“在我的机器上能工作”的问题。 通过将安装步骤分解 为解析、获取和链接， Yarn 使用确定性算法和 lockfiles 避免了这些问题，从而确保重复安装的一致性。因为它对 已经下载的包进行缓存，我们还看到在持续集成（CI）环境 中的构建速度明显更快。

**Yeoman**  
Yeoman 致力于通过简化脚手架、构建、活动和包管理 等活动，来提高 Web 应用开发人员的生产效率。 Yeoman 工具系列包括 Yo、Grunt 和 Bower，这些工 具需要配套使用。

**Grunt**  
Grunt 是一个基于任务的JavaScript工程命令行构建工具

**Gulp**  
gulp的官方定义非常简洁：基于文件流的构建系统。这里强调了 streaming，也就是gulp与grunt的在构建流程上的主要区别。



#### Yeoman


#### Yarn
安装  
`npm install -g yarn`

替换镜像  
`yarn config get registry`  
`yarn config set registry 'https://registry.npm.taobao.org'`

#### npm

NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题，常见的使用场景有以下几种：
* 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
* 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
* 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。

由于新版的nodejs已经集成了npm，所以之前npm也一并安装好了。同样可以通过输入 "npm -v" 来测试是否成功安装。命令如下，出现版本提示表示安装成功。

使用 npm 命令安装模块  
`$ npm install <Module Name>`

全局安装与本地安装  
npm 的包安装分为本地安装（local）、全局安装（global）两种，从敲的命令行来看，差别只是有没有`-g`而已

查看安装信息  
`npm list -g`



#### 前端工程目录结构
```
.
├── package.json                 
├── README.md                    
├── gulpfile.js                  // gulp 配置文件
├── webpack.config.js            // webpack 配置文件
├── doc                          // doc  目录：放置应用文档
├── test                         // test 目录：测试文件
├── dist                         // dist 目录：放置开发时候的临时打包文件
├── bin                          // bin  目录：放置 prodcution 打包文件
├── mocks                        // 数据 mock 相关  
├── src                          // 源文件目录
│   ├── html                     // html 目录 
│   │   ├── index.html
│   │   └── page2.html
│   ├── js                       // js 目录 
│   │   ├── common               // 所有页面的共享区域，可能包含共享组件，共享工具类
│   │   ├── home                 // home 页面 js 目录
│   │   │   ├── components
│   │   │   │   ├── App.js
│   │   │   ├── index.js         // 每个页面会有一个入口，统一为 index.js
│   │   ├── page2                // page2 页面 js 目录
│   │   │   ├── components
│   │   │   │   ├── App.js
│   │   │   └── index.js
│   └── style                    // style 目录
│       ├── common               // 公共样式区域
│       │   ├── varables.less    // 公共共享变量
│       │   ├── index.less       // 公共样式入口
│       ├── home                 // home 页面样式目录    
│       │   ├── components       // home 页面组件样式目录
│       │   │   ├── App.less 
│       │   ├── index.less       // home 页面样式入口
│       ├── page2                // page2 页面样式目录
│       │   ├── components       
│       │   │   ├── App.less
│       │   └── index.less       
├── vendor
│   └── bootstrap
└── └── jquery
```


#### 前端工具
**JS模块化方案**：
* 在线"编译" 模块的方案: `seajs`  / `require`
* 预编译模块的方案: `browserify` / `webpack`
* 前端构建工具：`grunt` / `gulp`

## 工具

Brackets - 强大免费的开源跨平台Web前端开发工具IDE (HTML/CSS/Javascript代码编辑器)


#### HTTP 工具

* wireshark 协议分析
* fiddler 是专门抓取http的，如果安装信任了他的根证书，还可以抓取https包。
* charles (mac 下)
* http debug pro


#### Brackets 插件
* file-icon
* emmet
* beatuify
* [ui-too-small](https://github.com/1beb/ui-too-small) 调整UI字体大小


ui-too-small: `main.js`
```javascript
define(function (require, exports, module) {
    "use strict";

    var ExtensionUtils = brackets.getModule("utils/ExtensionUtils");

    ExtensionUtils.addEmbeddedStyleSheet("#sidebar *, #main-toolbar *, #titlebar *, #problems-panel *,"+
	"#find-in-files-results *, #e4b-main-panel *, #status-bar *,"+
	"#main-toolbar *, #context-menu-bar *, #codehint-menu-bar *,"+
	"#quick-view-container *, #function-hint-container *  { font-size: 16px !important;"+
	" line-height: 18px !important; }"+
	".sidebar li { min-height: 18px !important;}"+
	".sidebar-selection, .filetree-selection { min-height: 20px !important; margin-top: 1px;}"+
	""
	);
});
```
