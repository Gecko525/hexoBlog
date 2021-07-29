---
title: Electron + Vue开发跨平台桌面应用
date: 2021-07-29 14:05:17
tags: [electron, vue]
---

### Electron

Electron是一个使用JavaScript、HTML和CSS构建桌面应用程序的框架。通过将Chromium和Node.js嵌入其二进制文件，Electron允许您维护一个JavaScript代码库，并创建在Windows、macOS和Linux上工作的跨平台应用程序—无需本地开发经验。[Electron 官网](https://www.electronjs.org/docs)

<!-- more -->

### Electron + Vue

在使用 Electron 进行开发之前，需要安装 [Node.js](https://nodejs.org/en/download/)。

安装之后可以在终端输入以下命令检查是否正确安装：

```bash
node -v

v14.17.0
```

### 创建 Vue 项目

我们直接使用脚手架工具 [vue-cli](https://cli.vuejs.org/zh/)

```
vue create myproject

Vue CLI v4.5.13
? Please pick a preset: (Use arrow keys)
> Default ([Vue 2] babel, eslint)
  Default (Vue 3) ([Vue 3] babel, eslint)
  Manually select features
```

这里我们直接选择 `Default ([Vue 2] babel, eslint)`，如果有需要可以选择其他选项进行自定义。

### 自动安装 Electron

使用 vue-cli 自动安装 electron，网络原因有可能会不成功，可选择手动安装。

```
cd myproject
vue add electron-builder
```

然后会让你选择什么版本的 Electron，这里我们直接选择最新的版本。

```
? Choose Electron Version (Use arrow keys)
  ^11.0.0
  ^12.0.0
> ^13.0.0
```

下图表示安装成功：

![安装成功](./installSuccess.png)

**安装完成后，查看项目的目录结构，会发现自动在 src 目录下生成了 backgroug.js 并修改了 package.json。**

![package.json](./packagejson.png)

### 手动安装 Electron

修改package.json，添加以下几行

```json
"scripts": {
    "electron:build": "vue-cli-service electron:build",
    "electron:serve": "vue-cli-service electron:serve",
    "postinstall": "electron-builder install-app-deps",
    "postuninstall": "electron-builder install-app-deps"
},
"main": "background.js",
"devDependencies": {
    "electron": "^5.0.6",
    "vue-cli-plugin-electron-builder": "^1.3.5"
 }
```

src下创建文件backgound.js

```js
'use strict'

import { app, protocol, BrowserWindow } from 'electron'
import { createProtocol } from 'vue-cli-plugin-electron-builder/lib'
import installExtension, { VUEJS_DEVTOOLS } from 'electron-devtools-installer'
const isDevelopment = process.env.NODE_ENV !== 'production'

// Scheme must be registered before the app is ready
protocol.registerSchemesAsPrivileged([
  { scheme: 'app', privileges: { secure: true, standard: true } }
])

async function createWindow() {
  // Create the browser window.
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      
      // Use pluginOptions.nodeIntegration, leave this alone
      // See nklayman.github.io/vue-cli-plugin-electron-builder/guide/security.html#node-integration for more info
      nodeIntegration: process.env.ELECTRON_NODE_INTEGRATION,
      contextIsolation: !process.env.ELECTRON_NODE_INTEGRATION
    }
  })

  if (process.env.WEBPACK_DEV_SERVER_URL) {
    // Load the url of the dev server if in development mode
    await win.loadURL(process.env.WEBPACK_DEV_SERVER_URL)
    if (!process.env.IS_TEST) win.webContents.openDevTools()
  } else {
    createProtocol('app')
    // Load the index.html when not in development
    win.loadURL('app://./index.html')
  }
}

// Quit when all windows are closed.
app.on('window-all-closed', () => {
  // On macOS it is common for applications and their menu bar
  // to stay active until the user quits explicitly with Cmd + Q
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', () => {
  // On macOS it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (BrowserWindow.getAllWindows().length === 0) createWindow()
})

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.on('ready', async () => {
  if (isDevelopment && !process.env.IS_TEST) {
    // Install Vue Devtools
    try {
      // await installExtension(VUEJS_DEVTOOLS)
    } catch (e) {
      console.error('Vue Devtools failed to install:', e.toString())
    }
  }
  createWindow()
})

// Exit cleanly on request from parent process in development mode.
if (isDevelopment) {
  if (process.platform === 'win32') {
    process.on('message', (data) => {
      if (data === 'graceful-exit') {
        app.quit()
      }
    })
  } else {
    process.on('SIGTERM', () => {
      app.quit()
    })
  }
}
```

先删除之前的``node_modules``，重新安装依赖即可。

### 运行

自动安装的项目默认会安装 vue devtools 插件，需要科学上网才能安装，否则会自动尝试 5 次，这里我们先把安装命令注释掉即可。

在`\myproject\src\background.js`中把下面这行注释掉：

![安装插件](./installExtension.png)

启动项目：

```
npm run electron:serve
```

启动成功后会自动弹出窗口：

![窗口](./window.png)

### 打包

```
npm run electron:build
```

第一次会遇到下面这种情况，依赖下载不下来：

![下载失败](downloadfail.png)

解决办法是手动下载这个文件，然后解压到`C:\Users\86157\AppData\Local\electron-builder\Cache\winCodeSign`文件夹下：

![解压文件](./unzip.png)

再次执行打包命令：

![打包成功](./build.png)

打包成功之后可以在`\myproject\dist_electron`文件夹中看到打包出来的.exe 文件。

![exe文件](./exe.png)
