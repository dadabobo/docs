##  ATOM

Atom 是 Github 专门为程序员推出的一个跨平台文本编辑器。具有简洁和直观的图形用户界面，并有很多有趣的特点：支持CSS，HTML，JavaScript等网页编程语言。它支持宏，自动完成分屏功能，集成了文件管理器。

**常用快捷键**
* `cmd-\` 显示或隐藏目录树
* `ctrl-0` 焦点移到目录树
* `fn-F2`  (选择tree后) 修改文件/文件夹名称
* `ctrl-shift-M` Markdown预览
* `ctrl-alt-b` 安装atom-beautify后可使用，格式化代码
* `` ctrl-` `` 安装terminal-panel后可使用，调起CLI
* `ctrl-shift-U` 调出切换编码选项
* `cmd-t`或`cmd-p` 查找文件
* `alt-cmd-[` 折叠
* `alt-cmd-]` 展开
* `alt-cmd-shift-{` 折叠全部
* `alt-cmd-shift-}` 展开全部
* `ctrl-m` 相应括号之间，html tag之间等跳转
* `alt-shift-S` 查看当前可用代码片段

**setting**
* `line-ending-selector`
* `tree-view`

**Themes**
* atom-material-ui 推荐
* atom-material-syntax 推荐
* atom-material-syntax-light 推荐
* monokai-flat 类sublime flat主题
* seti-ui 自带文件图标

Install
```
apm install atom-material-ui
apm install atom-material-syntax
apm install atom-material-syntax-light
apm install monokai-flat
apm install seti-ui
```

**Pacakges**
* `file-icons`: 文件图标
* `split-diff`： 比较
* `markdown-writer`: Maekdown文件写作
* `language-markdown`: Markdown语法高亮
* `language-gfm-enhanced`: Maekdown语法高亮  (disable language-gfm)
* `markdown-previews-plus`: Maekdown预览
* `markdown-preview-enhanced`: Maekdown预览
* `date`：Insert the current date & time.
* `language-plantuml`, `plantuml-preview`

````
apm install file-icons
apm install split-diff
apm install markdown-writer
apm install language-gfm-enhanced
apm install markdown-preview-enhanced
apm install date

apm install language-plantuml
apm install plantuml-preview
apm install plantuml-viewer
````

**misc**

```json
"*":
  core:
    disabledPackages: [
      "language-gfm"
    ]
    telemetryConsent: "no"
  "exception-reporting":
    userId: "cf13a80c-4235-4f56-8457-f22aba24de6a"
  "markdown-preview":
    openPreviewInSplitPane: false
    useGitHubStyle: true
  "markdown-preview-enhanced":
    breakOnSingleNewline: false
    enableTypographer: true
    openPreviewPaneAutomatically: false
    previewTheme: "atom-material-syntax"
    whiteBackground: true
  "tree-view":
    autoReveal: true
    hideIgnoredNames: true
    hideVcsIgnoredFiles: true
    squashDirectoryNames: true
  welcome:
    showOnStartup: false
```

设置 keymap： `file` --> `keymap`  
```
'atom-workspace atom-text-editor':
  'alt-d': 'date:datetime'
```


设置 stylesheet: `file` --> `stylesheet`

```css
.theme-one-dark-ui {
  .tab-bar { font-size: 16px; }
  .tree-view { font-size: 16px; }
  .status-bar { font-size: 14px; }
}
```


**其他Packages**
* linter 必备；代码校验工具
* esformatter 必备；统一代码格式
* atom-beautify 必备；格式化代码的，快捷键ctrl-alt-b
* minimap 推荐；就是Sublime右边那一竖块，显示缩小版的代码
* color-picker 推荐；写CSS时非常方便的调色板
* autocomplete-paths 填写路径的时候有Sug提示
* vim-mode 劳资就是喜欢zuo，所以在Atom上用vim写码:)
* docblockr 方便写注释
* emmet 必备；前端开发必备，谁用谁知道，入门地址：Emmet使用手册
* terminal-panel 不是那么好用的CLI，勉强能凑活
* git-plus Git插件；得先配置邮箱和用户名
* javascript-snippets 推荐；各种缩写，值得拥有；当然，俺用的最多的是cl命令:)
* file-icons 推荐：让文件前面有彩色图片，看着非常享受(如果使用着 seti-ui 主题，则体现不了效果哦)
