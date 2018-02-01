

## 分类

#### Node.js


## JavaScript包管理器

在线文档
* [NPM](https://docs.npmjs.com/)
* [Yarn](https://yarnpkg.com/en/docs)

在 JavaScript 领域有多个包管理器，举几个来说： npm ， bower ， component 和 volo 。

截至写本文，最流行的 JavaScript 包管理器是 npm。npm 客户端可以访问 npm 源里成千上万的代码库。只是最近，Facebook 推出了新的 JavaScript 包管理器 Yarn ， 号称是更快，更可靠，比现有的 npm 客户端更安全。 在这篇文章，你将学习到你能用 Yarn 做的五件事情。

Yarn是 Facebook 推出的新的 JavaScript 包管理器。 她为开发者使用 JavaScript 开发应用提供了快速、安全、可靠性高的依赖管理。你可以用 Yarn 处理这五件事。

### NPM 包管理
NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题，常见的使用场景有以下几种：
* 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
* 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
* 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。

**npm常用命令**
* `npm install <package>`  安装`nodejs`的依赖包
* `npm install <package> -g`  将包安装到全局环境中
* `npm install <package> --save`  安装的同时，将信息写入`package.json`中
* `npm init`  会引导你创建一个`package.json`文件，包括名称、版本、作者这些信息等
* `npm uninstall <package>` 移除
* `npm update <package>` 更新
* `npm ls` 列出当前安装的了所有包
* `npm root` 查看当前包的安装路径
* `npm root -g`  查看全局的包的安装路径
* `npm help`  帮助，如果要单独查看`install`命令的帮助，可以使用的`npm help install`

### Yarn (Nodejs包新管理工具)
在`yarn`发布之前，所有Nodejs开发者用的都是`npm`包管理工具，而`npm`工具存在挺多难以忍受的诟病，包括安装速度慢、每次都要在线重新安装等问题，而`yarn`也是为了解决`npm`当前所存在的问题而出现的。

**安装**
使用`npm`安装，`npm install -g yarn`
也可下载安装。

**配置**
安装`yarn`之后默认的包安装源是`https://registry.yarnpkg.com`，可用查看命令
  `yarn config get registry`

若想提高`yarn`安装的速度，可将包安装源修改为`cnpm`的安装源，执行以下命令即可:
  `yarn config set registry 'https://registry.npm.taobao.org'`

**使用**
* 初始化某个项目
  `npm init`
  `yarn init`
* 默认的安装依赖操作
  `npm install/link`
  `yarn install/link`
* 安装某个依赖，并且默认保存到`package`
  `npm install [pacakge] —save`
  `yarn add [pacakge]`
* 移除某个依赖项目
  `npm uninstall [pacakge] —save`
  `yarn remove xxx`
* 安装某个开发时依赖项目
  `npm install --save-dev [pacakge]`
  `yarn add [pacakge] —dev`
* 更新某个依赖项目
  `npm update --save [pacakge]`
  `yarn upgrade [pacakge]`
* 安装某个全局依赖项目
  `npm install -g [pacakge]`
  `yarn global add [pacakge]`
* 发布/登录/登出，一系列NPM Registry操作
  `npm publish/login/logout`
  `yarn publish/login/logout`
* 运行某个命令
  `npm run/test`
  `yarn run/test`

### Bower
[bower解决js的依赖管理](http://blog.fens.me/nodejs-bower-intro/)

Bower是twitter的又一个开源项目，使用nodejs开发，用于web包管理。
Bower是一个客户端技术的软件包管理器，它可用于搜索、安装和卸载如JavaScript、HTML、CSS之类的网络资源。它擅长前端的包管理，通过其API展示了包依赖模型。使得项目不存在系统级的依赖，不同的应用程序间也不会共享依赖，整个依赖树是扁平的。

**安装Bower**
`npm install bower -g`
`yarn global add bower`

**安装package**
Install packages with` bower install`. Bower installs packages to `bower_components/`.
```bash
# installs the project dependencies listed in bower.json
$ bower install
# registered package
$ bower install jquery
# GitHub shorthand
$ bower install desandro/masonry
# Git endpoint
$ bower install git://github.com/user/package.git
# URL
$ bower install http://example.com/script.js
```

**Commands**
[Bower Commands](https://bower.io/docs/api/)

**Search packages**
[Search Bower packages](https://bower.io/search) and find the registered package names for your favorite projects.

**Save packages**
Create a `bower.json` file for your package with `bower init`.
Then save new dependencies to your `bower.json` with bower `install PACKAGE --save`

**Use packages**
How you use packages is up to you. We recommend you use Bower together with [Grunt, RequireJS, Yeoman, and lots of other tools](https://bower.io/docs/tools/) or build your own workflow with [the API](https://bower.io/docs/api/). You can also use the installed packages directly, like this, in the case of `jquery`:
```html
<script src="bower_components/jquery/dist/jquery.min.js"></script>
```


---
## Yeoman
[Yeoman：Web 应用开发流程与工具](https://www.ibm.com/developerworks/cn/web/1402_chengfu_yeoman/index.html)
[Yeoman学习与实践笔记 ](http://www.cnblogs.com/cocowool/archive/2013/03/09/2952003.html)
[Yeoman：构建漂亮Web应用的工具和框架](http://www.infoq.com/cn/news/2012/09/yeoman)

Yeoman 的最大优势在于它整合了各种流行的实用工具，提供了一站式的解决方案，使得 Web 应用开发中的很多方面变得简单。Yeoman 使得开发人员可以专注于应用本身的实现，而不用在搭建应用的基础结构、进行任务构建和其他辅助任务上花费过多的时间和精力。`Yeoman` 同时也把一些好的最佳实践自动地引入到项目的开发中。比如当需要在应用中使用第三方的 JavaScript 库时，一般的做法是直接到库的网站上进行下载。而 `Yeoman` 中基于 `Bower` 进行依赖管理的做法则是更好的实践方式。

Yeoman 的功能由其所包含的工具来实现。下面分别介绍 Yeoman 中包含的 `Yo`、`Grunt` 和 `Bower` 等工具。

#### Grunt
Grunt 是一个 JavaScript 任务执行工具，其核心理念是自动化。在 Web 应用开发过程中，会有很多不同的任务需要执行。这些任务与 Web 应用开发中的不同类型的组件和所处的阶段相关。比如对 JavaScript 来说，在开发阶段会需要使用 JSLint 和 JSHint 这样的工具来检查 JavaScript 代码的质量；在构建阶段，从前端性能的角度出发，会需要把多个 JavaScript 文件在合并之后进行压缩。对于 CSS 文件也有类似的任务需要执行。其他的任务还包括压缩图片、合并压缩和混淆 JavaScript 代码以及运行自动化单元测试用例等。所有这些任务都需要进行相应的配置，并通过对应的方式来运行。不同任务的运行方式并不相同，取决于任务本身使用的技术。比如一些与 JavaScript 相关的任务，如 JSLint 和 JSHint，通过 JavaScript 引擎来运行。对于一般的基于 Java 平台的 Web 应用，如果需要执行 JSLint 任务，需要使用 Rhino 这样的引擎来执行 JavaScript 代码，同时与 Apache Ant、Maven 或 Gradle 这样的构建工具进行集成。这种方式的问题在于不同的任务的配置方式都不相同，并且需要与已有的构建系统进行集成。开发人员需要查询很多的文档才能知道如何配置并使用这些任务。

Grunt 基于流行的 NodeJS 平台来运行。所有的任务执行都基于统一的平台。Grunt 的优势在于集成了非常多的任务插件。这些插件有些是 Grunt 团队开发的，更多的是由社区贡献的。这些插件使用 NodeJS 标准的模块机制来分发，只需要使用 npm 就可以进行管理。Web 应用只需要通过一个文件来声明所要执行的任务并进行相应的配置，Grunt 会负责任务的运行。通过这种方式，所有任务的配置都在一个文件中管理。

Grunt 的安装过程很简单。只需要运行“`npm install -g grunt-cli`”命令就可以安装。在安装 Yeoman 时，Grunt 就已经作为一部分被自动安装了。对于一个应用来说，使用 Grunt 需要两个文件。一个是 npm 使用的 `package.json`。该文件中包含了应用的相关元数据。在该文件中需要通过 `devDependencies` 来声明对 Grunt 及其他插件的依赖。另外一个文件是 `Gruntfile`，可以是一个 JavaScript 或 CoffeeScript 文件。该文件的作用是配置应用中所需要执行的任务。在 `package.json` 文件中声明依赖并安装 Grunt 插件之后，就可以在 `Gruntfile` 中配置并加载这些任务。通过 grunt 命令可以运行这些任务。不同任务的配置方式相对类似，只是所提供的配置项并不相同。

#### 任务配置
Gruntfile 中的相关配置都包含在一个 JavaScript 方法中。在这个方法中，通过 `grunt.initConfig` 方法可以对使用的插件进行配置。由于在 Gruntfile 文件中进行配置时，通常会需要使用 `package.json` 文件中的某些值，一般的做法是把 `package.json` 的内容读入到某个属性中，方便在代码的其他部分中使用。代码清单1给出了 `Gruntfile` 的基本结构。调用 `initConfig` 方法的参数对象中的 `pkg` 属性表示的是 `package.json` 的内容。

清单 1. Gruntfile 的基本结构
```
module.exports = function(grunt) {
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json')
  });
};
```

在配置对象中可以包含任意的属性值。不过重要的是执行不同任务的插件所对应的配置项。每个插件的配置项在配置对象中的属性名称与插件的名称相对应。比如 `grunt-contrib-concat` 插件所对应的配置项属性名称为 concat，如代码清单2所示。插件 `grunt-contrib-concat` 的作用是把多个 JavaScript 文件拼接在一起组成单个文件。在该插件的配置项中，`src` 和 `dest` 属性分别表示要拼接的 JavaScript 文件和生成的目标文件的名称。其中 `src` 属性的值使用通配符指定了一系列文件，`dest` 属性的值中通过 `pkg.name` 引用了 `package.json` 文件中定义的属性 `name` 的值。“`<%= %>`”是 Grunt 提供的字符串模板的语法格式，用来根据变量值生成字符串。

清单 2. 插件 `grunt-contrib-concat` 的配置
```
concat: {
  src: ['src/**/*.js'],
  dest: 'dist/<%= pkg.name %>.js'
}
```
Grunt 的模板使用“`<% %>`”来进行表达式的分隔，同时也支持表达式的嵌套。在解析模板中包含的内容时，整个配置对象被作为解析时的上下文。也就是说配置对象中包含的属性都可以直接在模板中引用。除此之外，grunt 对象及其包含的方法也可以在模板中使用。模板有两种形式：第一种是“`<%= %>`”，用来引用配置对象中的属性值；第二种是“`<% %>`”，用来执行任意的 JavaScript 代码，通常用来控制代码执行流程。

有的插件允许同时定义多个不同的配置，称为不同的“目标（target）”。这是因为某些任务在不同的条件下所使用的配置并不相同。对于这些不同的目标，可以在配置对象中添加相应名称的属性来表示。代码清单3给出了 `grunt-contrib-concat` 插件的另一种配置方式。代码中定义了 `common` 和 `all` 两个不同的目标。每个目标的配置并不相同。在运行任务时，通过“`grunt concat:common`”和“`grunt concat:all`”来运行不同的目标。如果没有指定具体的目标，而是通过“`grunt concat`”来直接运行，则会依次执行所有的目标。

清单 3. 插件 grunt-contrib-concat 的多目标配置
```
concat: {
  common: {
    src: ['src/common/*.js'],
    dest: 'dist/common.js'
  },
  all: {
    src: ['src/**/*.js'],
    dest: 'dist/all.js'
  }
}
```

对于包含了多个目标的配置来说，可以通过 `options` 属性来配置不同目标的默认属性值。在目标中也可以通过 `options` 属性来覆写默认值。

#### 任务创建与执行
在对插件进行配置之后，需要在 `Gruntfile` 中创建相关的任务。这些任务由 Grunt 负责执行。在加载了 Grunt 插件之后，该插件提供的任务可以被执行。也可以通过 `grunt.registerTask` 方法来定义新的任务，或是为已有的任务创建别名。在定义一个任务时，需要提供任务的名称和所执行的方法。任务的描述是可选的。代码清单4中给出了一个简单的任务。当通过“`grunt sample`”运行该任务时，会在控制台输出相应的提示信息。

清单 4. 简单的 Grunt 任务
```javascript
grunt.registerTask('sample', 'My sample task', function() {
  grunt.log.writeln('This is a sample task.');
});
```
在定义任务时可以声明任务运行时所需的参数，在通过 `grunt` 运行任务时可以指定这些参数的值。代码清单5给出了一个包含参数的任务的示例。任务 `profile` 在运行时需要提供 2 个参数 `name` 和 `email`。在通过 `grunt` 运行时，使用“`grunt profile:alex:alex@example.org`”可以把参数值“`alex`”和“`alex@example.org`”分别传递给参数 `name` 和 `email`。不同的参数之间通过“`:`”分隔。

清单 5. 包含参数的 Grunt 任务
```javascript
grunt.registerTask('profile', 'Print user profile', function(name, email) {
  grunt.log.writeln('Name -> ' + name + '; Email -> ' + email);
});
```
如果要定义的任务类似 grunt-contrib-concat 插件可以支持多个不同的目标，只需要使用 grunt.registerMultiTask 方法来进行定义即可。

除了定义新的任务之外，还可以通过为已有的任务添加别名的方式来创建新的任务。代码清单6给出了一个示例。名为 `default` 的任务在执行时，会依次执行 `jshint`、`qunit`、`concat` 和 `uglify` 等任务。当运行 `grunt` 命令时，如果没有指定任务名称，会尝试运行名为 `default` 的任务。

清单 6. 使用添加别名的方式创建的任务
```javascript
grunt.registerTask('default', ['jshint', 'qunit', 'concat', 'uglify']);
```
大部分任务是同步执行的，也可以用异步的方式来执行。如果任务中的某部分需要比较长的时间完成，可以通过异步的方式来完成。代码清单7给出了一个异步执行的任务的示例。通过调用 `async` 方法可以把当前任务的执行变成异步的。调用 `async` 方法的返回值是一个 JavaScript 方法。当任务执行完成之后，调用该 JavaScript 方法来通知 `grunt`。

清单 7. 异步执行的任务
```javascript
grunt.registerTask('asynctask', function() {
  var done = this.async();
  setTimeout(function() {
    grunt.log.writeln('Done!');
    done();
  }, 1000);
});
```

一个任务可以依赖其他任务的成功执行。当某个任务执行失败之后，剩下的其他任务不会被执行，除非在执行 `grunt` 命令时使用了“`--force`”参数。在任务代码中可以通过 `grunt.task.requires` 方法来声明对其他任务的依赖。如果所依赖的任务没有成功执行，当前任务也不会被执行。当任务对应的 JavaScript 方法在执行时返回 `false` 时，该任务被认为执行失败。对于异步执行的任务，只需要在调用 `async` 返回的回调方法时传入 `false` 参数即可。比如在代码清单7中，可以使用“`done(false);`”来声明异步任务执行失败。

为了能够在调用 `grunt` 时使用插件提供的任务，需要使用 `grunt.loadNpmTasks` 方法来加载插件。代码清单8给出了加载 `grunt-contrib-watch` 和 `grunt-contrib-concat` 插件的示例。

清单 8. 插件加载示例
```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
grunt.loadNpmTasks('grunt-contrib-concat');
```

#### Bower
在 Web 应用开发中，一般都会使用很多第三方 JavaScript 库，比如 `jQuery` 和 `Twitter Bootstrap` 这样的常见库。传统的做法是从这些库的网站上直接下载所需版本的 JavaScript 库文件，放到 Web 应用的某个目录中，然后在 HTML 页面中引用。这种做法的问题在于引入了很多额外的工作量，包括查找所需的 JavaScript 库文件、下载和管理等。一些 JavaScript 库有很多个版本，也依赖于其他 JavaScript 库。对于给定版本的某个 JavaScript 库，需要找到它所依赖的兼容版本的其他 JavaScript 库。这可能是一个递归的过程，会花费很多的时间。Bower 是一个前端库管理工具，可以很好的解决在 Web 应用中引用第三方库时可能遇到的问题。Bower 所提供的功能类似于 Java 开发中会用到的 Apache Ivy、Apache Maven 或 Gradle 等工具。

Bower 也是基于 NodeJS 开发的。只需要使用“`npm install -g bower`”命令即可安装。Yeoman 在安装时也包含了 Bower。在安装完 Bower 之后，就可以在命令行使用 `bower` 命令来管理所需的库。通过“`bower help`”可以查看 Bower 命令行所支持的操作。一般的做法是首先通过“`bower search`”命令来搜索需要使用的库。如“`bower search jquery`”可以用来搜索名称中包含 `jquery` 的库。当找到所需的库之后，可以通过“`bower install`”命令来安装。如“`bower install jquery-ui`”可以用来安装 `jQuery UI` 库。在安装时可以指定库的版本，如“`bower install jquery-ui#1.9.2`”可以安装 `jQuery UI` 的 `1.9.2` 版本。在使用名称来安装库时，要求该库已经注册到 Bower。Bower 也支持从远程或本地 Git 仓库和本地文件中安装库。Bower 会把下载的库文件放在 `bower_components` 目录中。当库有更新时，通过“`bower update`”命令来进行更新。当不需要一个库时，通过“`bower uninstall`”命令来删除。使用“`bower list`”命令可以列出来当前应用中已经安装的库的信息。
在通过 Bower 安装库之后，可以直接在 HTML 页面中引用，如代码清单 9所示。这要求 Bower 的下载目录是可以公开访问的。

清单 9. HTML 页面中引入 `Bower` 管理的 JavaScript 库
```
<script src="/bower_components/jquery/jquery.min.js"></script>
```
与逐一安装所需的库相比，更好的方式是在 `bower.json` 文件中定义所依赖的库，然后运行“`bower install`”命令来安装所有的这些库。`bower.json` 文件的作用类似于 NodeJS 中 `package.json`。可以直接创建该文件，也可以通过“`bower init`”命令来以交互式的方式创建。代码清单10给出了 `bower.json` 文件的示例。使用 `dependencies` 来声明所依赖的库及其版本。有了 `bower.json` 文件之后，只需要运行一次“`bower install`”命令就可以安装所需的全部库。

清单 10. bower.json 文件示例
```json
{
  "name": "yeoman-sample",
  "version": "0.1.0",
  "dependencies": {
    "sass-bootstrap": "~3.0.0",
    "requirejs": "~2.1.8",
    "modernizr": "~2.6.2",
    "jquery": "~1.10.2"
  },
  "devDependencies": {}
}
```
Bower 本身的配置可以通过`.bowerrc` 文件来完成。该文件以 JSON 格式来进行配置。代码清单11给出了`.bowerrc` 文件的示例。该示例中通过 directory 定义了 Bower 下载库的目录。

清单 11. 配置 Bower 的.bowerrc 文件
```
{
  "directory": "app/bower_components"
}
```

#### Yo
当打算开始开发一个 Web 应用时，初始的目录结构和基础文件很重要，因为这些是应用开发的基础。有些开发人员选择从零开始进行，或是复制已有的应用。更好的选择是基于已有的模板。很多 Web 应用程序使用 `HTML5 Boilerplate` 这样的模板来生成初始的代码结构。这样做的好处是可以直接复用已有的最佳实践，避免很多潜在的问题，为以后的开发打下一个良好的基础。`Yo` 是一个 Web 应用的架构（scaffolding）工具。它提供了非常多的模板，用来生成不同类型的 Web 应用。这些模板称为生成器（generator）。社区也贡献了非常多的生成器，适应于各种不同的场景。通过 `Yo` 生成的应用使用 `Grunt` 来进行构建，使用 `Bower` 进行依赖管理。

以基本的 Web 应用生成器为例，只需要使用“`yo webapp`”命令就可以生成一个基本的 Web 应用的骨架。运行该命令之后，会有一些提示信息来对生成的应用进行基本的配置，可以选择需要包含的功能。默认生成的 Web 应用中包含了 `HTML5 Boilerplate`、`jQuery`、`Modernizr` 和 `Twitter Bootstrap` 等。只需要一个简单的命令，就可以生成一个能够直接运行的 Web 应用。后续的开发可以基于生成的应用骨架来进行。这在很大程度上简化了应用的开发工作，尤其是某些原型应用。

在生成的 Web 应用中包含了一些常用的 `Grunt` 任务。这些任务可以帮助快速的进行开发。这些任务包括：
* `grunt server`：启动支持 Live Reload 技术的服务器。当本地的文件有修改时，所打开的页面会自动刷新来反映最新的改动。这免去了每次手动刷新的麻烦，使得开发过程变得更加方便快捷。
* `grunt test`：运行基于 `Mocha` 的自动化测试。
* `grunt build`：构建整个 Web 应用。其中所执行的任务包括 JavaScript 和 CSS 文件的合并、压缩和混淆等操作，以及添加版本号等。


#### Yeoman
Yeoman 的重要之处在于把各种不同的工具整合起来，形成一套完整的前端开发的工作流程。使用 Yeoman 之后的开发流程可以分成如下几个基本的步骤。

在开发的最初阶段需要确定前端的技术选型。这包括选择要使用的前端框架。在绝大部分的 Web 应用开发中，都需要第三方库的支持。有的应用可能只使用 jQuery，有的应用会增加 Twitter Bootstrap 库，而有的应用则会使用 AngularJS。在确定了技术选型之后，就可以在 Yeoman 中查找相应的生成器插件。一般来说，基于常见库的生成器都可以在社区中找到。比如使用 `AngularJS`、`Backbone`、`Ember` 和 `Knockout` 等框架的生成器。

所有的生成器都使用 `npm` 来进行安装。生成器的名称都使用“`generator-`”作为前缀，如“`generator-angular`”、“`generator-backbone`”和“`generator-ember`”等。安装完成之后，通过 `yo` 命令就可以生成应用的骨架代码，如“`yo angular`”用来生成基于 `AngularJS` 的应用骨架。

生成的应用骨架中包含了一个可以运行的基本应用。只需要通过“`grunt server`”命令来启动服务器就可以查看。应用的骨架搭建完成之后，把生成的代码保存到源代码仓库中。团队可以在这个基础上进行开发。开发中的一些常用任务可以通过 `Yeoman` 来简化。当需要引入第三方库时，通过 `Bower` 来搜索并添加。

#### 小结
面对复杂的 Web 应用的开发，良好的流程和工具支持是必不可少的，可以让日常的开发工作更加顺畅。Yeoman 作为一个流行的工具集，在整合了 Yo、Grunt 和 Bower 等工具的基础上，定义了一个更加完备和清晰的工作流程。通过把一些最佳实践引入到 Web 应用中，有助于创建高质量和可维护的应用。


## 测试
#### 跨页工具
* Selenium: 超级健壮，有丰富的测试支持,但配置较复杂
* PhantomJS:确实提供了一个非客户端Webkit浏览器（跟Chrome和Safari用的是相同的引擎），所以跟Selenium一样，它也呈现出了非常高水平的现实性。然而它还没提供我们所需的简单的测试断言
* Zombie:没有使用已有的浏览器引擎，所以它不适合用来测试浏览器的功能特性，但用它来测试基本功能是非常好的; 但目前不支持window;

---
## IntelliJ IDEA
下载并安装， [IntelliJ IDEA的官网](https://www.jetbrains.com).
[IntelliJ IDEA介绍](http://www.tiantianbianma.com/develop-tool/tool-idea/)

下载一个 JetbrainsCrack-2.6.3_proc.jar 破解补丁。放在你的安装idea下面的bin的目录下面。[下载链接](http://idea.lanyus.com/).

在安装的`idea`下面的`bin`目录下面有2个文件 ： 一个是`idea64.exe.vmoptions`，还有一个是`idea.exe.vmoptions`

用记事本打开（蓝色框） 分别在最下面一行增加一行
```
-javaagent:C:\Program Files\JetBrains\IntelliJ IDEA 2017.1.1\bin\JetbrainsCrack-2.6.3_proc.jar
```
`“C:\Program Files\JetBrains\IntelliJ IDEA 2017.1.1\bin\JetbrainsCrack-2.6.3_proc.jar”`是对应的`JetbrainsCrack-2.6.3_proc.jar`的位置。

**目录结构**
```
bin       是 IDEA 的可执行代码目录。
help      是 IDEA 的帮助文件目录。
jre64     是 IDEA 自带的 JRE 环境，故 IDEA 可在未安装 JDK 的计算机上进行 PHP、Python等语言的编码。
lib       是 IDEA 依赖的库文件目录，里面有很多的 Jar 文件。
license   是 IDEA 的许可证文件目录。
plugins   是 IDEA 的插件目录。
redist    是 IDEA 中索引机制所依赖的 redist 库目录。
两个 .txt 说明文件和一个注册表项文件。
```

**`bin`下重要文件**
```
idea.exe              文件是 IntelliJ IDEA 32位的可执行文件。
idea.exe.vmoptions    文件是 IntelliJ IDEA 32位的可执行文件的 JVM 配置文件。
idea.properties       文件是 IntelliJ IDEA 的一切全局属性的配置文件。
idea64.exe            文件是 IntelliJ IDEA 64位的可执行文件。
idea64.exe.vmoptions  文件是 IntelliJ IDEA 64位的可执行文件的 JVM 配置文件。
```

**`idea.properties`文件**
* 该文件中使用了几个属性变量，比如 `$(idea.home.path)` 代表了 IDEA 安装的顶级目录，`$(user.home)` 表示用户的根目录等。
* `idea.config.path=${user.home}/.IntelliJIdea/config` 指向 IntelliJ IDEA 的个性化配置目录，默认不启用。
* `idea.system.path=${user.home}/.IntelliJIdea/system` 指向 IntelliJ IDEA 的系统文件目录，默认不启用。
* `idea.max.intellisense.filesize=2500` 文件超过此处设置的大小后，关闭该文件的智能检查和提示等功能，有效消除大文件的卡顿问题。
* `idea.cycle.buffer.size=1024` 设置控制输出台的缓存大小，解决大项目时，控制台缓存溢出的问题。

#### 快捷键汇总与榜单
智能提示
```
Ctrl+Space            基本的代码提示
Ctrl+Shift+Space      更智能的按照类型信息提示
F2 和 Shift+F2        自动定位到代码错误提示处
Alt+Enter             快速自动修复错误代码
Ctrl+Shift+Enter      自动补全末尾的字符，包括行尾的反括号和分号
```

代码重构
```
Ctrl+Shift+Alt+T      重构功能大汇总，又称为 Refactor This
Shift+F6              重命名
Ctrl+Alt+V            提取变量
```

代码生成
```
fori+Tab              for 循环
sout+Tab              System.out.println 语句
psvm+Tab              main 方法
Ctrl+J                查询所有代码模板
Alt+Insert            自动生成构成函数、toString函数、getter/seter、重写父类方法等
```

高效编辑
```
Ctrl+W 和 Ctrl+Shift+W   根据语言的语法特性来扩展或收缩光标所选范围
Left 和 Right            以 字符 为单位进行前后移动
Ctrl+Left 和 Ctrl+Right  以 单词 为单位进行前后移动
Ctrl+/ 和 Ctrl+Shift+/   以 代码块 为单位进行前后移动
Ctrl+Y                   删除当前行
Ctrl+D                   复制当前行并插入在下面一行
```

查找打开
```
Ctrl+N 和 Ctrl+Shift+N  打开类、文件等资源
Shift+Shift             全局搜索（Serarch Every Where）
Ctrl+H                  打开当前类的继承层次窗口
Ctrl+B 和 Ctrl+Alt+B    在类的继承层次窗口进行跳转
Ctrl+F12                查看当前类的所有方法
Alt+F7                  查找类或者方法的使用
Ctrl+F                  当前窗口中进行文本查找
Ctrl+Shift+F            全工程中进行文本查找
F3 和 Shift+F3          在查找的所有匹配处间进行移动
```

基础功能
```
Ctrl+Shift+A        查询所有的 IntelliJ IDEA 命令和对应快捷键
Alt+Insert          自动新建类、文件、文件夹等资源
Ctrl+Alt+O          优化 import 列表
Ctrl+Alt+L          格式化代码
ESC                 聚焦到编辑窗口中
Alt+NUM             聚焦到工具窗口中
Ctrl+Tab            在编辑窗口的标签页间进行切换
Ctrl+E 和 Ctrl+Shift+E 打开最近访问过或者编辑过的文件
Ctrl+Alt+T          创建单元测试用例
Alt+Shift+F10       开始运行程序
Shift+F9            开始调试程序
Ctrl+F2             停止程序
F7/F8/F9  对应 Step into、Step over、Continue  调试程序时
```

最终榜单
```
Ctrl+Shift+Space      智能补全
Alt+Enter             智能修复
Ctrl+Shift+Alt+T      重构一切
Alt+Insert            万能插入
Ctrl+Shift+Enter      自动完成
Shift+Shift           全局搜索
Ctrl+Shift+A          命令查找
Template/Postfix+Tab  模板触发
Ctrl+W                智能选取
Ctrl+Tab              切换标签
```
