## Windows UWP 开发

Resources
* [通用 Windows 平台入门](https://developer.microsoft.com/windows/apps/getstarted)

#### WinJS

WinJS Namespace
WinJS.Application
WinJS.Binding
WinJS.Class
WinJS.Navigation
WinJS.Resources
WinJS.UI
WinJS.UI.Animation
WinJS.UI.Fragments
WinJS.UI.Pages
WinJS.UI.TrackTabBehavior Namespace
WinJS.UI.XYFocus
WinJS.UI.Utilities
WinJS.UI.Utilities.Scheduler
Windows Library for JavaScript control attributes
Windows Library for JavaScript style sheets
HTML controls for Windows Runtime apps
Remote Desktop Services objects for Windows Store apps



#### WinJS App (通用 Windows)

尽管 WinJS App 是最基本的模板，但该模板仍包含少量文件：
* 介绍应用（它的名称、说明、磁贴、起始页、初始屏幕等等）并列出应用包含的文件的清单文件 (`package.appxmanifest`)。
* 用于在“开始”菜单中显示的一组徽标图像（images/Square150x150Logo.scale-200.png、images/Square44x44Logo.scale-200.png 和 images/Wide310x150Logo.scale-200.png）。
* 用于在 Windows 应用商店中表示应用的图像 (images/StoreLogo.png)。
* 用于在应用启动时显示的初始屏幕 (images/SplashScreen.scale-200.png)。
* 用于在应用启动时运行的起始页 (`index.html`) 和附带的 JavaScript 文件 (`main.js`)。

index.html
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>HelloWorld</title>

    <!-- WinJS references -->
    <link href="lib/winjs-4.0.1/css/ui-light.css" rel="stylesheet" />
    <script src="lib/winjs-4.0.1/js/base.js"></script>
    <script src="lib/winjs-4.0.1/js/ui.js"></script>

    <!-- HelloWorld references -->
    <link href="/css/default.css" rel="stylesheet" />
    <script src="/js/main.js"></script>
</head>
<body class="win-type-body">
    <div>Content goes here!</div>
</body>
</html>
```

main.js
```JavaScript
(function () {
  "use strict";

  var app = WinJS.Application;
  var activation = Windows.ApplicationModel.Activation;
  var isFirstActivation = true;

  app.onactivated = function (args) {
    if (args.detail.kind === activation.ActivationKind.voiceCommand) {
      // TODO: Handle relevant ActivationKinds. For example, if your app can be started by voice commands,
      // this is a good place to decide whether to populate an input field or choose a different initial view.
    }
    else if (args.detail.kind === activation.ActivationKind.launch) {
      // A Launch activation happens when the user launches your app via the tile
      // or invokes a toast notification by clicking or tapping on the body.
      if (args.detail.arguments) {
        // TODO: If the app supports toasts, use this value from the toast payload to determine where in the app
        // to take the user in response to them invoking a toast notification.
      }
      else if (args.detail.previousExecutionState === activation.ApplicationExecutionState.terminated) {
        // TODO: This application had been suspended and was then terminated to reclaim memory.
        // To create a smooth user experience, restore application state here so that it looks like the app never stopped running.
        // Note: You may want to record the time when the app was last suspended and only restore state if they've returned after a short period.
      }
    }

    if (!args.detail.prelaunchActivated) {
      // TODO: If prelaunchActivated were true, it would mean the app was prelaunched in the background as an optimization.
      // In that case it would be suspended shortly thereafter.
      // Any long-running operations (like expensive network or disk I/O) or changes to user state which occur at launch
      // should be done here (to avoid doing them in the prelaunch case).
      // Alternatively, this work can be done in a resume or visibilitychanged handler.
    }

    if (isFirstActivation) {
      // TODO: The app was activated and had not been running. Do general startup initialization here.
      document.addEventListener("visibilitychange", onVisibilityChanged);

      args.setPromise(WinJS.UI.processAll().then(function completed() {
        var ratingControlDiv = document.getElementById("ratingControlDiv");
        var ratingControl = ratingControlDiv.winControl;
        ratingControl.addEventListener("change",ratingChanged, false);
      }));

      var helloButton = document.getElementById("helloButton");
      helloButton.addEventListener("click", buttonClickHandler, false);
    }

    isFirstActivation = false;
  };

})();
```

-----
default.js

```javascript
(function() {
  "use strict";
  var state = WinJS.Binding.as({
    //...
  });

  var printListener;

  // called by WinJS.Application.addEventListener("activated", function(args)
  function initPrinting(){}

  // Clipboard module
  if (Windows.ApplicationModel.DataTransfer.Clipboard) {}

  WinJS.Application.addEventListener("clipboardchanged", function(event) {}

  function saveAsync(file) {}
  function showAsync(what) {}
  function probeDataPackageAsync(data) {}
  function showShareOperationAsync(shareOperation) {}
  function safeCallAsync(object, type, asyncMethod, phoneMethod) {}

  var previousFindText;
  WinJS.Namespace.define("App", {})

  if (window.Mousetrap) {}

  WinJS.Namespace.define("App.commands", {})
  WinJS.Binding.bind(state, {})
  var shareListener;
  function initSharing() {}
  var appbar;
  function createAppBarButtons() {}
  function createBanner() {}
  function createNavigator(home) {}
  function createApp(activationKind) {}
  WinJS.Application.addEventListener("activated", function(args) {});
  WinJS.Application.oncheckpoint = function(args) {}

  WinJS.Application.start();
})();
```

navigator.js
```javascript
(function() {
  "use strict";

  var nav = WinJS.Navigation;
  WinJS.Namespace.define("Application", {
    PageControlNavigator: WinJS.Class.define()  
  });
})();  
```






-----

生成的通用应用 (Winjs)
````
.
Markdownr
	bin
		Debug
      AppxManifest.xml
      Markdownr.build.appxrecipe
      ReverseMap
        resources.pri
	bld
		Debug
  		embed.resfiles
  		embed.resfiles.intermediate
  		layout.resfiles
  		layout.resfiles.intermediate
  		Markdownr.jsproj.FileListAbsolute.txt
  		MultipleQualifiersPerDimensionFound.txt
  		priconfig.xml
  		priconfig.xml.intermediate
  		pri.resfiles
  		pri.resfiles.intermediate
  		ProjectArchitectures.txt
  		qualifiers.txt
  		qualifiers.txt.intermediate
  		ResourceHandlingTask.state
  		resources.resfiles
  		resources.resfiles.intermediate
  		winrtrefs
  			winrt2ts.rsp
	css
		default.css
	images
		LockScreenLogo.scale-200.png
		SplashScreen.scale-200.png
		Square150x150Logo.scale-200.png
		Square44x44Logo.scale-200.png
		Square44x44Logo.targetsize-24_altform-unplated.png
		StoreLogo.png
		Wide310x150Logo.scale-200.png
	index.html
	js
		main.js
	lib
		winjs-4.0.1
		css
		 	ui-dark.css
		 	ui-light.css
		fonts
		 	Symbols.ttf
		js
			base.js
			ui.js
	Markdownr.jsproj
	Markdownr_TemporaryKey.pfx
	package.appxmanifest
Markdownr.sln
````
