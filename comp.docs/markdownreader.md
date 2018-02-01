## Markdown Reader

Resources
* [hahaps/markdownReader](https://github.com/hahaps/markdownReader)
* [pke/Markdownr](https://github.com/pke/Markdownr)


#### markdownr

WinJS
````
Markdownr.WindowsUniversal/WinJS/
  css/ui-dark.css
  css/ui-dark.min.css
  css/ui-light.css
  css/ui-light.min.css
  fonts/Symbols.ttf
  js/base.js
  js/base.min.js
  js/base.min.js.map
  js/en-US/ui.strings.js
  js/ui.js
  js/ui.min.js
  js/ui.min.js.map
  js/WinJS.intellisense.js
  js/WinJS.intellisense-setup.js
````

JavaScript lib
````
Markdownr.Shared/lib
  marked.min.js      
  mousetrap.min.js  
  prism.min.js        
  textile.js        
````

代码
```
Markdownr.Shared
  css/github-markdown.css
  css/prism.css
  css/shared.css
Markdownr.WindowsUniversal
  css/default.css
Markdownr.Shared
  js/default.js
  js/navigator.js
  js/TileNotification.coffee
  js/worker.js
```

#### WinJS App (通用 Windows)

尽管 WinJS App 是最基本的模板，但该模板仍包含少量文件：
* 介绍应用（它的名称、说明、磁贴、起始页、初始屏幕等等）并列出应用包含的文件的清单文件 (package.appxmanifest)。
* 用于在“开始”菜单中显示的一组徽标图像（images/Square150x150Logo.scale-200.png、images/Square44x44Logo.scale-200.png 和 images/Wide310x150Logo.scale-200.png）。
* 用于在 Windows 应用商店中表示应用的图像 (images/StoreLogo.png)。
* 用于在应用启动时显示的初始屏幕 (images/SplashScreen.scale-200.png)。
* 用于在应用启动时运行的起始页 (index.html) 和附带的 JavaScript 文件 (main.js)。


`package.appxmanifest` 定义 `<Application Id="App" StartPage="default.html">`

default.html
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <!-- WinJS references -->
    <link href="/WinJS/css/ui-light.css" rel="stylesheet" />
    <script src="/WinJS/js/base.js"></script>
    <script src="/WinJS/js/ui.js"></script>

    <!-- NavSharedApp1 references -->
    <link href="/css/shared.css" rel="stylesheet" />
    <link href="/css/default.css" rel="stylesheet" />
    <script src="/lib/mousetrap.min.js"></script>
    <script src="/js/default.js"></script>

    <!-- Shared references -->
    <script src="/js/navigator.js"></script>
</head>
<body>
</body>
</html>
```

default.js
```javascript

```


-----
Markdownr
````
.
files.txt
Markdownr
	Markdownr.Shared
		compilerconfig.json
		compilerconfig.json.defaults
		css
			github-markdown.css
			prism.css
			shared.css
		images
			badgelogo.scale-100.png
			badgelogo.scale-140.png
			badgelogo.scale-180.png
			badgelogo.scale-240.png
			kitten.png
			largelogo.scale-100.png
			largelogo.scale-140.png
			largelogo.scale-180.png
			largelogo.scale-80.png
			logo.scale-100.png
			logo.scale-140.png
			logo.scale-180.png
			logo.scale-240.png
			logo.scale-80.png
			markdownr.svg
			phonesplashscreen.scale-100.png
			phonesplashscreen.scale-140.png
			phonesplashscreen.scale-240.png
			smalllogo.scale-100.png
			smalllogo.scale-140.png
			smalllogo.scale-180.png
			smalllogo.scale-80.png
			smalltilelogo.scale-100.png
			smalltilelogo.scale-140.png
			smalltilelogo.scale-180.png
			smalltilelogo.scale-80.png
			splashscreen.scale-100.png
			splashscreen.scale-140.png
			splashscreen.scale-180.png
			square44x44logo.scale-100.png
			square44x44logo.scale-140.png
			square44x44logo.scale-240.png
			square71x71logo.scale-100.png
			square71x71logo.scale-140.png
			square71x71logo.scale-240.png
			store app tile icon.png
			storelogo.scale-100.png
			storelogo.scale-140.png
			storelogo.scale-180.png
			storelogo.scale-240.png
			widelogo.scale-100.png
			widelogo.scale-140.png
			widelogo.scale-180.png
			widelogo.scale-240.png
				widelogo.scale-80.png
		js
			default.js
			navigator.js
			TileNotification.coffee
			worker.js
		lib
			marked.min.js
			mousetrap.min.js
			prism.min.js
			textile.js
		Markdownr.Shared.projitems
		Markdownr.Shared.shproj
		pages
			filePickerPage.css
			filePickerPage.html
			filePickerPage.js
			findPage.html
			findPage.js
			homePage.html
			homePage.js
		strings
			de
				resources.resjson
			en
				resources.resjson
	Markdownr.Windows
		BundleArtifacts
			neutral.txt
		css
			default.css
		default.html
		images
			smalllogo.scale-100.png
			smalllogo.scale-140.png
			smalllogo.scale-180.png
			smalllogo.scale-80.png
			splashscreen.scale-100.png
			splashscreen.scale-140.png
			splashscreen.scale-180.png
			Square150x150Logo.scale-100.png
			Square150x150Logo.scale-140.png
			Square150x150Logo.scale-180.png
			Square150x150Logo.scale-80.png
			Square310x310Logo.scale-100.png
			Square310x310Logo.scale-140.png
			Square310x310Logo.scale-180.png
			Square310x310Logo.scale-80.png
			Square70x70Logo.scale-100.png
			Square70x70Logo.scale-140.png
			Square70x70Logo.scale-180.png
			Square70x70Logo.scale-80.png
			storelogo.scale-100.png
			storelogo.scale-140.png
			storelogo.scale-180.png
			Wide310x150Logo.scale-100.png
			Wide310x150Logo.scale-140.png
			Wide310x150Logo.scale-180.png
			Wide310x150Logo.scale-80.png
		Markdownr.Windows.jsproj
		package.appxmanifest
		Package.StoreAssociation.xml
		pages
			homePage.css
	Markdownr.WindowsPhone
		BundleArtifacts
			neutral.txt
		css
			default.css
			ui-themed.css
			ui-themed.theme-dark.css
			ui-themed.theme-light.css
		default.html
		images
			SplashScreen.scale-100.png
			SplashScreen.scale-140.png
			SplashScreen.scale-240.png
			Square150x150Logo.scale-100.png
			Square150x150Logo.scale-140.png
			Square150x150Logo.scale-240.png
			Square44x44Logo.scale-100.png
			Square44x44Logo.scale-140.png
			Square44x44Logo.scale-240.png
			Square71x71Logo.scale-100.png
			Square71x71Logo.scale-140.png
			Square71x71Logo.scale-240.png
			StoreLogo.scale-100.png
			StoreLogo.scale-140.png
			StoreLogo.scale-240.png
			Wide310x150Logo.scale-100.png
			Wide310x150Logo.scale-140.png
			Wide310x150Logo.scale-240.png
		Markdownr.WindowsPhone.jsproj
		package.appxmanifest
		Package.StoreAssociation.xml
		pages
			homePage.css
	Markdownr.WindowsUniversal
		BundleArtifacts
			neutral.txt
			Upload
				neutral.txt
		css
			default.css
		default.html
		images
			logo.scale-100.png
			logo.scale-140.png
			logo.scale-180.png
			logo.scale-80.png
			smalllogo.scale-100.png
			smalllogo.scale-140.png
			smalllogo.scale-180.png
			smalllogo.scale-80.png
			splashscreen.png
			square44x44logo.png
			Square71x71Logo.png
			storelogo.png
		Markdownr.WindowsUniversal.jsproj
		package.appxmanifest
		packages.config
		Package.StoreAssociation.xml
		pages
			homePage.css
		WinJS
			css
				ui-dark.css
				ui-dark.min.css
				ui-light.css
				ui-light.min.css
			fonts
				Symbols.ttf
			js
				base.js
				base.min.js
				base.min.js.map
				en-US
					ui.strings.js
				ui.js
				ui.min.js
				ui.min.js.map
				WinJS.intellisense.js
				WinJS.intellisense-setup.js
			License.txt
			README.md
Markdownr.sln
README.md
todo.md
````
