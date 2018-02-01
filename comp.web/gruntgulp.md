## Grunt
JavaScript 世界的构建工具

* 为何要用构建工具？
  一句话：自动化。对于需要反复重复的任务，例如压缩（minification）、编译、单元测试、linting等，自动化工具可以减轻你的劳动，简化你的工作。当你在 [Gruntfile](http://www.gruntjs.net/sample-gruntfile) 文件正确配置好了任务，任务运行器就会自动帮你或你的小组完成大部分无聊的工作。
* 为什么要使用Grunt？
  Grunt生态系统非常庞大，并且一直在增长。由于拥有数量庞大的插件可供选择，因此，你可以利用Grunt自动完成任何事，并且花费最少的代价。如果找不到你所需要的插件，那就自己动手创造一个Grunt插件，然后将其发布到npm上吧。先看看[入门文档](http://www.gruntjs.net/getting-started)吧。
* 可用的Grunt插件
  你所需要的大多数task都已经作为Grunt插件被开发了出来，并且每天都有更多的插件诞生。插件列表页面列出了[完整的清单](http://www.gruntjs.net/plugins)。



## Gulp
### 入门指南
1. 全局安装 `gulp`  
    ```
    $ npm install --global gulp
    $ npm install --save-dev gulp
    ```
2. 在项目根目录下创建一个名为 `gulpfile.js` 的文件：
    ```javascript
    var gulp = require('gulp');

    gulp.task('default', function() {
      // 将你的默认的任务代码放在这
    });
    ```
4. 运行 `gulp`：
    ```
    gulp
    ```
    默认的名为 `default` 的任务（task）将会被运行，在这里，这个任务并未做任何事情。
    想要单独执行特定的任务（task），请输入 `gulp <task> <othertask>`。


    