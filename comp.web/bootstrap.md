## Bootstrap

参考
* [Bootstrap](http://getbootstrap.com/)
* [Bootstrap 中文网](http://www.bootcss.com/)
* [Code snippets for Bootstrap](https://bootsnipp.com/)



### Bootstrap 教程

#### Bootstrap 简介
Bootstrap 包的内容
* 基本结构：Bootstrap 提供了一个带有网格系统、链接样式、背景的基本结构。这将在 Bootstrap 基本结构 部分详细讲解。
* CSS：Bootstrap 自带以下特性：全局的 CSS 设置、定义基本的 HTML 元素样式、可扩展的 class，以及一个先进的网格系统。这将在 Bootstrap CSS 部分详细讲解。
* 组件：Bootstrap 包含了十几个可重用的组件，用于创建图像、下拉菜单、导航、警告框、弹出框等等。这将在 布局组件 部分详细讲解。
* JavaScript 插件：Bootstrap 包含了十几个自定义的 jQuery 插件。您可以直接包含所有的插件，也可以逐个包含这些插件。这将在 Bootstrap 插件 部分详细讲解。
* 定制：您可以定制 Bootstrap 的组件、LESS 变量和 jQuery 插件来得到您自己的版本。

#### Bootstrap CSS

```html
<!-- 移动设备友好支持 -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- 响应式图像 -->
<img src="..." class="img-responsive" alt="响应式图像">
```

Bootstrap 3 的 container class 用于包裹页面上的内容。让我们一起来看看 bootstrap.css 文件中的这个 .container class。


#### Bootstrap 网格系统
网格系统通过一系列包含内容的行和列来创建页面布局。你的内容就可以放入这些创建好的布局中。下面介绍 Bootstrap 网格系统的工作原理：
* **行（row）** 必须包含在 `.container` （固定宽度）或 `.container-fluid` （100% 宽度）中，以便为其赋予合适的排列（aligment）和内补（padding）。
* 通过 **行（row）** 在水平方向创建一组 **列（column）**。
* 你的内容应当放置于 **列（column）** 内，并且，只有 **列（column）** 可以作为 **行（row）** 的直接子元素。
* 类似 `.row` 和 `.col-xs-4` 这种预定义的类，可以用来快速创建栅格布局。Bootstrap 源码中定义的 **mixin** 也可以用来创建语义化的布局。
* 通过为 **列（column）** 设置 `padding` 属性，从而创建列与列之间的 **间隔（gutter）**。通过为 `.row` 元素设置负值 `margin` 从而抵消掉为 `.container` 元素设置的 `padding`，也就间接为 **行（row）** 所包含的 **列（column）** 抵消掉了 `padding`。
* 负值的 `margin` 就是下面的示例为什么是向外突出的原因。在栅格列中的内容排成一行。
* 栅格系统中的列是通过指定`1`到`12`的值来表示其跨越的范围。例如，三个等宽的列可以使用三个 `.col-xs-4` 来创建。
* 如果一 **行（row）** 中包含了的 **列（column）** 大于 `12`，多余的 **列（column）** 所在的元素将被作为一个整体另起一行排列。
* 栅格类适用于与屏幕宽度大于或等于分界点大小的设备 ， 并且针对小屏幕设备覆盖栅格类。 因此，在元素上应用任何 `.col-md-*` 栅格类适用于与屏幕宽度大于或等于分界点大小的设备 ， 并且针对小屏幕设备覆盖栅格类。 因此，在元素上应用任何 `.col-lg-*` 不存在， 也影响大屏幕设备。


基本的网格结构
````html
<div class="container">
   <div class="row">
      <div class="col-*-*"></div>
      <div class="col-*-*"></div>      
   </div>
   <div class="row">...</div>
</div>
<div class="container">....
````



使用 BootCDN 提供的免费 CDN 加速服务
````html
<!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
<link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

<!-- 可选的 Bootstrap 主题文件（一般不用引入） -->
<link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap-theme.min.css" integrity="sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp" crossorigin="anonymous">

<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="https://cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
````


#### Bootstrap 基本模板
````html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
    <title>Bootstrap 101 Template</title>

    <!-- Bootstrap -->
    <link href="css/bootstrap.min.css" rel="stylesheet">

    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
      <script src="https://cdn.bootcss.com/html5shiv/3.7.3/html5shiv.min.js"></script>
      <script src="https://cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
  </head>
  <body>
    <h1>Bootstrap</h1>
    <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
    <script src="https://cdn.bootcss.com/jquery/1.12.4/jquery.min.js"></script>
    <!-- Include all compiled plugins (below), or include individual files as needed -->
    <script src="js/bootstrap.min.js"></script>
  </body>
</html>
````


### Bootstrap

#### 起步
简要介绍 Bootstrap，以及如何下载、使用，还有基本模版和案例，等等。


#### 全局CSS样式
设置全局 CSS 样式；基本的 HTML 元素均可以通过 class 设置样式并得到增强效果；还有先进的栅格系统。


#### 组件
无数可复用的组件，包括字体图标、下拉菜单、导航、警告框、弹出框等更多功能。


#### JavaScript 插件
jQuery 插件为 Bootstrap 的组件赋予了“生命”。可以简单地一次性引入所有插件，或者逐个引入到你的页面中。

#### 定制
通过自定义 Bootstrap 组件、Less 变量和 jQuery 插件，定制一份属于你自己的 Bootstrap 版本吧。



#### 网站实例
Bootstrap 优站精选
* [Grunt](https://gruntjs.com/)
* [themeclue.com](http://themeclue.com/)
* [segmentfault.com](http://segmentfault.com/)
* [mashable.com](http://mashable.com/)
* [腾讯大讲堂](http://djt.qq.com/)
* [The Charlotte Observer](http://www.charlotteobserver.com/)
* [coursera](https://www.coursera.org/)
* [Laravel Recipes](http://laravel-recipes.com/)
* [Microsoft Power BI](http://powerbi.microsoft.com/)
* [imgix](https://www.imgix.com/)
* [IBM @ Github](http://ibm.github.io/)
* [fullstory](https://www.fullstory.com/)
* [jetsetter](http://www.jetsetter.com/)
* [godaddy](https://www.godaddy.com/)
* [mongodb](https://www.mongodb.com/)
* [CSDN JOB](http://job.csdn.net/)
* [Food.com](http://www.food.com/)
* [LaraBase](http://laravelbase.com/)
* [onreplaytv](http://onreplaytv.com/)
* [breakingnews](http://breakingnews.com/)
* [Mozilla Learning](https://teach.mozilla.org/)
* [Business Insider](http://expo.bootcss.com/business-insider/)
* [西安市政府](http://www.xa.gov.cn/)
* [Launching Next](http://www.launchingnext.com/)
* [云驾网](http://www.uyunjia.com/)
* [Lead to SMS](http://leadtosms.com/)
* [Open Source Initiative](http://expo.bootcss.com/page/46/Open%20Source%20Initiative)
* [沙发网](http://www.shafa.com/)
* [房多多](http://www.fangdd.com/)
* [Airbnb](https://www.airbnb.com/)
* [Airbnb China](https://www.airbnbchina.cn/)
* [Grace](http://flagpolesky.com/)
* [米课](http://micourse.net/)
* [CodeIgniter](https://codeigniter.com/)
* [Magpie](https://getmagpie.com/)
* [Build.com](http://www.build.com/)
* [GenesisUI](https://genesisui.com/)
* [MathJax](https://www.mathjax.org/)
* [全国公安机关互联网站安全服务平台](http://beian.gov.cn/)
* [悦合同](http://www.yuehetong.com/)
* [Flow XO](https://flowxo.com/)
* [易公司](http://www.egongsi.cn/)
* [Data Scraping Studio](https://www.datascraping.co/)
* [CakeResume](https://www.cakeresume.com/)
* [EGGER](https://www.egger.com/shop/zh_CN/)
*
