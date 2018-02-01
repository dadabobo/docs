## Visual Studio Code

`settings.json`
```json
{
  "vsicons.presets.foldersAllDefaultIcon": true,
  "editor.tabSize": 2,
  "indent_size": 2,
  "editor.insertSpaces": true,
  "editor.detectIndentation": true,
  "files.eol": "\n",
  // "workbench.iconTheme": "vs-seti",
  "workbench.iconTheme": "vscode-icons",
  "java.home": "C:\\Program Files\\Java\\jdk1.8.0_131",
  // "terminal.integrated.shell.windows": "C:\\WINDOWS\\Sysnative\\bash.exe"
  "terminal.integrated.shell.windows": "C:\\WINDOWS\\sysnative\\WindowsPowerShell\\v1.0\\powershell.exe",
  "java.errors.incompleteClasspath.severity": "ignore",
  "window.zoomLevel": 0,
  "atomKeymap.promptV3Features": true,
  "editor.multiCursorModifier": "ctrlCmd",
  "editor.formatOnPaste": true,
  "window.menuBarVisibility": "default",
  "workbench.startupEditor": "newUntitledFile",
  "extensions.ignoreRecommendations": false
}
```

`launch.json`
```json
{
  // Use IntelliSense to learn about possible Node.js debug attributes.
  // Hover to view descriptions of existing attributes.
  // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "启动一个独立的 Chrome 以调试 frontend",
      "url": "http://localhost:8091/",
      "webRoot": "${workspaceRoot}/frontend"
    },
    {
      "type": "chrome",
      "request": "attach",
      "name": "监听一个已经启动调试模式的 Chrome",
      "port": 9222,
      "url": "http://localhost:8091/",
      "webRoot": "${workspaceRoot}/frontend"
    },
    {
      "type": "node",
      "request": "launch",
      "name": "启动 Node 服务器",
      "program": "${workspaceRoot}/server/app.js"
    },
    {
      "type": "node",
      "request": "attach",
      "name": "附加于已启动的 Node 服务器（debug模式）",
      "port": 5858,
      "restart": true
    },
    {
      "type": "node",
      "request": "attach",
      "name": "附加于已启动的 Node 服务器（inspect模式）",
      "port": 9229,
      "restart": true
    }
  ]
}
```

`package.json`
```json
{
  "name": "expressapp",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www",
    "frontend": "anywhere -p 8091 -d ./frontend",
    "backend": "node server/app.js",
    "backend:debug": "nodemon --debug server/app.js",
    "backend:inspect": "nodemon --inspect server/app.js"
  },
  "dependencies": {
    "bluebird": "^3.5.0",
    "body-parser": "~1.17.1",
    "connect-mongo": "^1.3.2",
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.3",
    "ejs": "~2.5.6",
    "express": "~4.15.2",
    "express-session": "^1.15.5",
    "mongoose": "^4.11.8",
    "morgan": "~1.8.1",
    "mysql2": "^1.4.2",
    "sequelize": "^4.8.0",
    "serve-favicon": "~2.4.2",
    "xmlhttprequest": "^1.8.0"
  },
  "devDependencies": {
    "anywhere": "^1.4.0",
    "nodemon": "^1.11.0"
  }
}
```
