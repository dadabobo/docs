## Sublime Text

#### Package Control
* Click the `Preferences > Browse Packages…` menu
* Browse up a folder and then into the `Installed Packages/` folder
* Download [Package Control.sublime-package](https://packagecontrol.io/Package%20Control.sublime-package) and copy it into the `Installed Packages/` directory
* Restart Sublime Text 


#### 常用插件
* IMEsupport
* emmet
* alignment


#### Markdown 插件
* Markdown Editing
* Markdown Preview

点击 `Preferences` --> 选择 `Package Control: Intall Packages`，然后再插件库中分别选择和安装`Markdown Editing`和`Markdown Preview`即可。


**自定义快捷键**

点击 Preferences --> 选择 Key Bindings User，输入：
````json
[
    { "keys": ["ctrl+shift+m"], "command": "markdown_preview", "args": { "target": "browser"} },
    { "keys": ["ctrl+\\"], "command": "toggle_side_bar" }
]
````


**
在`Preferences` -> `Settings` -> `Preferencews.sublime-settings -- User`中添加如下参数：
```json
{
    "default_line_ending": "unix",
    "rulers": [80,100,120],
    "word_wrap": true,
    "wrap_width": 120,    
    "ignored_packages":
    [
        "Vintage"
    ]
}
```

**设置语法高亮和mathjax支持**

在`Preferences` -> `Package Settings` -> `Markdown Preview` -> `Setting` - `User`中添加如下参数：
````json
{
    "enable_mathjax": true,
    "enable_highlight": true,
}
````


**Markdown Editing**  

在`Preferences` -> `Package Settings` -> `Markdown Editing` -> `Markdown GFM Settings - User`中添加如下参数：
````json
{
    "default_line_ending": "unix",
    "rulers": [80,100,120],
    "word_wrap": true,
    "wrap_width": 120
}
````


#### 安装与注册
Sublime Text Build 3126 x64 Setup

**Sublime Text 3 最新可用注册码**

````
—– BEGIN LICENSE SJOLZY.CN —–
Anthony Sansone
Single User License
EA7E-878563
28B9A648 42B99D8A F2E3E9E0 16DE076E
E218B3DC F3606379 C33C1526 E8B58964
B2CB3F63 BDF901BE D31424D2 082891B5
F7058694 55FA46D8 EFC11878 0868F093
B17CAFE7 63A78881 86B78E38 0F146238
BAE22DBB D4EC71A1 0EC2E701 C7F9C648
5CF29CA3 1CB14285 19A46991 E9A98676
14FD4777 2D8A0AB6 A444EE0D CA009B54
—— END LICENSE ——

—– BEGIN LICENSE SJOLZY.CN —–
Alexey Plutalov
Single User License
EA7E-860776
3DC19CC1 134CDF23 504DC871 2DE5CE55
585DC8A6 253BB0D9 637C87A2 D8D0BA85
AAE574AD BA7D6DA9 2B9773F2 324C5DEF
17830A4E FBCF9D1D 182406E9 F883EA87
E585BBA1 2538C270 E2E857C2 194283CA
7234FF9E D0392F93 1D16E021 F1914917
63909E12 203C0169 3F08FFC8 86D06EA8
73DDAEF0 AC559F30 A6A67947 B60104C6
—— END LICENSE ——
````
