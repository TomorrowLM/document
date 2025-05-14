---

slug: app
title: app
description: app学习
keywords: uniapp,electron
category: uniapp,electron
tags: [uniapp,electron]
author: liming
date: 25-September-2020
---

# Hybrid

## webview

WebView是原生应用内置的浏览器控件，在应用程序中嵌入WebView，用于加载和显示网页内容 ，实现前端的混合式开发，大多数 **Hybrid App** 混合式开发框架都是基于WebView模式进行二次开发的。  

> WebView 可以简单理解为页面里的 iframe 。原生app与 WebView 的交互可以简单看作是页面与页面内 iframe 页面进行的交互。就如页面与页面内的 iframe 共用一个 Window  一样，原生与  WebView  也共用了一套原生的方法。

**Android容器** 在安卓客户端中，webView容器与手机自带的浏览器内核一致，多为android-chrome。不存在兼容性和性能问题。 

**RN容器** 在react-native开发中，从rn 0.37版本开始官方引入了组件，在安卓中调用原生浏览器，在IOS中默认调用的是UIWebView容器。从IOS12开始，苹果正式弃用UIWebView，统一采用WKWebView。 

**优点**‌：

- ‌**跨平台**‌：只需开发一套HTML、CSS和JavaScript页面，就能在多个平台上运行（如Android和iOS）。
- ‌**快速开发**‌：可以快速上线功能，尤其是在需要频繁更新的活动页面中。
- ‌**动态加载**‌：支持实时从服务器加载内容，方便更新业务逻辑和页面内容。
- ‌**扩展性**‌：WebView支持前端和后端技术的整合，并通过JSBridge实现与原生功能的交互‌。

‌**缺点**‌：

- ‌**兼容性问题**‌：不同语言开发的APP，甚至是不同手机厂商的APP，提供的WebView浏览器内核可能有所不同，对H5的支持程度可能不同，可能会导致兼容性问题‌
- 性能瓶颈：相比原生 UI 组件，WebView 的渲染性能较低，尤其在加载大量图片或复杂动画时。
- 安全性：如果未正确处理跨域、注入等安全问题，WebView 可能成为攻击目标。

### WebView 的应用场景

- 混合开发（Hybrid App）：
  - 在原生应用中嵌入 WebView，用于快速开发跨平台功能。
  - 示例：电商类 App 的活动页面、用户协议、帮助文档等页面。
- 微信小程序中的 <web-view>：
  - 用于在小程序中加载 H5 页面或外部网页。
  - 示例：在微信小程序中加载第三方支付页面或推广页面。
- 跨平台框架：
  - 一些跨平台框架（如 React Native、Flutter）利用 WebView 实现 H5 页面和原生功能的整合。
  - 示例：React Native 提供了 WebView 组件，可以直接加载网页。
- 加载动态内容：
  - 当内容需要经常更改且由服务端提供时，可以使用 WebView 加载动态网页。
- 离线应用：
  - WebView 可以加载本地 HTML 文件（嵌入在应用包中），从而支持部分离线功能。

## 通信

### JSBridge

是 Native 和非 Native 之间的桥梁 ，简单来讲，主要是给 JavaScript 提供调用 Native 功能的接口，让混合开发中的『前端部分』可以方便地使用原生的系统API，如地址位置、摄像头甚至支付等 Native 功能。 

WebView通过[JSBridge](https://www.baidu.com/s?rsv_dl=re_dqa_generate&sa=re_dqa_generate&wd=JSBridge&rsv_pq=d5cbda3b00200998&oq=前端 webview是什么&rsv_t=adcc/t2BO+0h4HGx6oB1c4o0LZywCflORJS9QgtM7IvPJnPBGiRTndwkmDGGIZ2rEayiugSKAK6t&tn=62095104_41_oem_dg&ie=utf-8)等机制实现前端与原生代码的双向通信，使得网页内容可以与原生应用的功能进行交互‌

### 前端通知客户端 

在H5页面里触发链接跳转，App Webview检测到链接跳转再进行拦截，因此可以通过URL上携带参数来告知App下一步应该做些什么。 

```react
import React, { Component } from "react";

export default class App extends Component {
    componentDidMount() {
        location.href = "lsbox://toast?msg=页面加载完毕"; // 通知App
    }
    render() {
        return (
            <div className="app">
                <button type="button" onClick={this.openMask.bind(this)}>点它</button>
            </app>
        );
    }
    openMask() {
        location.href = "lsbox://mask?toggle=1"; // 通知App
    }
}
```

以上执行了`location.href = "lsbox://mask?toggle=1"`来通知App打开遮罩层

- `lsbox`：前端和客户端统一定义链接跳转的协议(喜欢怎样定义协议都行)
- `mask`：App需要执行的动作(喜欢怎样定义动作都行)
- `toggle=1`：动作执行参数(自定义参数，用于告知App怎样做)

```
如果同步触发两个或以上的location.href(下一个location.href接着上一个location.href)，App可能只会接收到一个location.href发出的通知，所以需要对下一个location.href使用setTimeout设置通知时间间隔(可使用Async/Await封装优化)
```

### 客户端通知前端 

使用`window.handleFunc = function() {}`这样的形式来定义注入的方法。 

```
import React, { Component } from "react";

export default class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            list: [0, 1, 2, 3, 4]
        };
    }
    componentDidMount() {
        window.addNum = this.addNum.bind(this); // 暴露方法给App
    }
    render() {
        return (
            <div className="app">
                <ul>{this.state.list.map(v => <li key={v}>{v}</li>)}</ul>
            </div>;
        );
    }
    addNum(num) {
        this.setState(prevState => ({
            list: prevState.list.concat(num);
        }));
    }
}
```

以上在组件加载完成后通过`window.addNum = this.addNum.bind(this)`将指定方法全局暴露到`window`上，App Webview可直接操作这些方法来控制H5页面 





# electron

## 项目搭建

package.json 中指定的 [`main`](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#main) 文件是 Electron 应用的入口（打包文件）。 这个文件控制 **主程序 (main process)**，它运行在 Node.js 环境里，负责控制您应用的生命周期、显示原生界面、执行特殊操作并管理渲染器进程 (renderer processes)

在 package.json 的 [`scripts`](https://docs.npmjs.com/cli/v7/using-npm/scripts) 字段中添加一个 `start` 命令，内容为 `electron .` 。 这个命令会告诉 Electron 在当前目录下寻找主脚本，并以开发模式运行它。

```json
{
  "name": "my-electron-app",
  "version": "1.0.0",
  "description": "Hello World!",
  "main": "main.js",
  "scripts": {
    "start": "electron .",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Jane Doe",
  "license": "MIT",
  "devDependencies": {
    "electron": "23.1.3"
  }
}
```

### vue3

- vue3+elc模板：npm create vite@latest electron-vue3-demo -- --template vue-ts

-  npm install electron -D
- electron-builder打包工具：将已有的electron应用打包成msi格式和exe可执行文件的工具
  npm install electron-builder -D
- vite 结合 electron 的库
  npm install vite-plugin-electron -D

#### Vue3项目转electron

https://www.bilibili.com/video/BV1SS4y1h7CL/?vd_source=f2241cf7d6cc0ae541b49e6fd828988b

### vue2

- electron

- vue-cli-plugin-electron-builder：https://nklayman.github.io/vue-cli-plugin-electron-builder/guide/configuration.html

### 调试

https://www.electronjs.org/zh/docs/latest/tutorial/tutorial-first-app#%E5%8F%AF%E9%80%89%E4%BD%BF%E7%94%A8-vs-code-%E8%B0%83%E8%AF%95

### 依赖工具

- rimraf：快速删除文件或目录工具

- asar：在打包生成的文件夹中，会有一个app.asar，它是Electron应用程序的主业务文件压缩包，要知道项目中哪些文件被pack到安装包，可以通过解压app.asar进行查看
  npm i asar -g
  asar extract app.asar ./app-folder
  
- electron-devtools-installer：electron 开发工具

- electron-updater模块检测更新

### 配置

#### vue.config.js

适合定义**全局**的electron配置

```
    electronBuilder: {
      nodeIntegration: true,
      preload: './electron/preload/index.ts',
      mainProcessFile: './electron/main/index.ts', // 主进程入口文件
      mainProcessWatch: ['./electron/main/index.ts'], // 检测主进程文件在更改时将重新编译主进程并重新启动
      // rendererProcessFile: './src/main.ts', // 渲染进程入口文件
      outputDir: 'build/electron',
      builderOptions: {
        // productName: require('./package.json').productName, //项目名，也是生成的安装文件名
        copyright: 'Copyright © 2023', //版权信息
        directories: {
          output: 'build/electron/releases'
          // buildResources: 'build/releases',
          // app: 'build/electron/bundled'
        },
        extraResources: [
          {
            from: 'D:/times/package/xjar.exe',
            to: 'backend/collect_backend.exe'
          },
          {
            from: 'D:/times/package/boot-1.0.0.xjar',
            to: 'backend/boot-1.0.0.xjar'
          },
          {
            from: 'D:/times/package/db/times.db',
            to: 'backend/db/times.db'
          },
          {
            from: 'D:/times/package/config/application.properties',
            to: 'backend/application.properties'
          },
          {
            from: 'D:/times/package/jre',
            to: 'backend/jre'
          }
        ]
        // asar: false
      }
    }
```

##### nodeIntegration

- **启用 Node.js 集成**：当 `nodeIntegration` 设置为 `true` 时，Electron 渲染进程（即 Vue 应用所在的网页环境）将能够直接使用 Node.js 的 API 和模块。这意味着你可以在前端代码中直接调用 `require`、`fs`、`child_process` 等 Node.js 内置模块。
- **安全风险**：虽然 `nodeIntegration` 提供了强大的功能，但它也带来了潜在的安全风险。如果启用了 `nodeIntegration`，恶意代码可能会利用这一点执行任意的本地系统命令或访问文件系统。因此，在生产环境中应谨慎使用，并确保应用的安全性。
- **与上下文菜单和开发者工具的兼容性**：在某些情况下，启用 `nodeIntegration` 可能会影响 Electron 的上下文菜单和开发者工具的功能。如果你遇到相关问题，可以尝试调整其他相关配置，如 `contextIsolation`。

> 如果没有显式在 `BrowserWindow` 构造函数中设置 `webPreferences/nodeIntegration`，则会使用这个配置。是一种 **全局/默认级别的配置**，适用于开发模式和生产模式下的窗口创建。

##### preload

指定预加载脚本的路径。

##### mainProcessFile

- 指定主进程入口文件的路径。
- 主进程负责管理应用的生命周期、窗口创建等

##### mainProcessWatch

- 指定需要监听的主进程文件列表。
- 当这些文件发生变化时，会重新编译主进程代码并重启应用。

##### outputDir

指定 Electron 构建输出目录

##### builderOptions

- copyright：设置生成的安装包的版权信息

- directories

  - output：指定最终构建产物的输出目录

- extraResources定义需要打包到 Electron 应用中的额外资源文件。路径是相对于应用的 **`resources` 目录**（Electron 应用的资源目录）

  每个资源对象包含两个字段：    - **`from`**：源文件路径。    - **`to`**：目标路径（相对于打包后的应用目录）

  ```
  extraResources: [
    {
    	from: 'D:/times/package/xjar.exe',
    	to: 'backend/collect_backend.exe'
    },
  ]
  ```

#### electron-builder.json

`electron-builder.json` 文件相比较于`vue.config.js`中的electronBuilder配置更加细粒化，它能根据不同平台定义了如何构建、打包和分发 Electron 应用程序。

##### appId

- **作用**：指定应用的唯一标识符。
- **值**：`com.dweb.my-app`
- **说明**：用于在操作系统中唯一标识你的应用，特别是在 Windows 和 macOS 上

##### asar

- **作用**：是否将应用资源（渲染线程的资源）打包为 ASAR（Archive）文件，会放在D:\work\cidsi\data-plat\iot-integration\fe-iot-integration\build\electron\releases\win-unpacked\resources。
- **值**：`false`
- **说明**：ASAR 是一种简单的存档格式，用于将应用文件打包成一个文件。设置为 `false` 表示不使用 ASAR 归档。

##### extraFiles

##### 平台特定配置

```
"linux": {
    "icon": "./public/logo.ico"
  },
  "mac": {
    "artifactName": "${productName}_${version}.${ext}",
    "icon": "./public/logo.ico",
    "target": [
      "dmg"
    ]
  },
  "win": {
    "artifactName": "${productName}_${version}.${ext}",
    "icon": "./public/logo.ico",
    "target": [
      {
        "target": "nsis",
        "arch": [
          "x64"
        ]
      }
    ],
    "asarUnpack": [
      "src/**",
      "README.md"
    ],
  },
```



- `icon`

  - **作用**：指定 Windows 平台的图标路径。
  - **值**：`./public/logo.ico`
  - **说明**：同 Linux 和 macOS 配置。

- `target`

  - **作用**：指定安装包格式和架构。

  - 值：

    ```
    json[
      {
        "target": "nsis",
        "arch": ["x64"]
      }
    ]
    ```

  - **说明**：生成 NSIS 格式的安装包，仅支持 `x64` 架构。

- `asarUnpack`

  - **作用**：指定哪些文件或目录不应被打包到 ASAR 归档中。
  - **值**：`["src/**", "README.md"]`
  - **说明**：`src` 目录下的所有文件和 `README.md` 文件将保持独立文件形式。

- `artifactName`

  - **作用**：定义 Windows 安装包的文件名模板。
  - **值**：`${productName}_${version}.${ext}`
  - **说明**：同 macOS 配置。

##### nsis

**`oneClick`**

- **作用**：是否启用一键安装模式。
- **值**：`false`
- **说明**：如果为 `true`，则安装过程将简化为一个确认步骤。

3.2 **`allowElevation`**

- **作用**：是否允许安装程序请求管理员权限。
- **值**：`true`
- **说明**：如果为 `true`，则在需要时会提示用户提升权限。

3.3 **`allowToChangeInstallationDirectory`**

- **作用**：是否允许用户更改安装目录。
- **值**：`true`
- **说明**：如果为 `true`，则用户可以在安装过程中选择安装位置。

3.4 **`installerIcon` 和 `uninstallerIcon`**

- **作用**：分别指定安装程序和卸载程序的图标路径。
- **值**：`./public/logo.ico`
- **说明**：确保图标文件存在并正确引用。

3.5 **`createDesktopShortcut`**

- **作用**：是否在桌面上创建快捷方式。
- **值**：`true`
- **说明**：如果为 `true`，则会在用户桌面上创建应用程序的快捷方式。

3.6 **`createStartMenuShortcut`**

- **作用**：是否在开始菜单中创建快捷方式。
- **值**：`true`
- **说明**：如果为 `true`，则会在开始菜单中创建应用程序的快捷方式。

3.7 **`shortcutName`**

- **作用**：指定快捷方式的名称。
- **值**：`${productName}`
- **说明**：`${productName}` 是一个变量，表示应用名称，快捷方式名称会根据应用名称动态生成。

#### 加载资源

https://www.jianshu.com/p/43e8a2f04422

- **添加files配置项**：**electron-builder配置项files中包含的文件都在`应用程序根目录/resources/app.asar/`目录下**。

  ```
  //electron-builder.json
  {
      productName: 'xxx',
      appId: 'xxx',
      files: ['./main', './build'], // 包含指定文件
  }
  ```

- 打包额外的文件

  `extraFiles` 和 `extraResources` 是两个用于指定额外文件复制的配置项
  
  - extraFiles`extraFiles`为额外文件

    配置`extraFiles`后，**electron-builder会在打包时将`extraFiles`中指定的文件复制到打包后应用程序的`根目录下(Windows/Linux)`，或者`Content目录下(MacOS)`**
  
    -  适用场景：
       - 需要在启动时直接访问的可执行文件、脚本、依赖库等。
       - 比如一些启动脚本、批处理文件、第三方依赖等。
  
  - extraResources`extraResources`为额外资源
  
    配置`extraResources`后，**electron-builder会在打包时将`extraResources`中指定的文件复制到打包后应用程序的“资源目录”`根目录/resources文件夹下(Windows)`，或者`Content/resources文件夹下(MacOS)`**
  
    -  适用场景：放置只读资源文件，如：数据库模板、配置文件、图标、证书等。
  

### 安装包放置

##### 服务器

```js
第一步、在build中配置"publish"字段：

"build": {
  ...
  "publish": [
    {
       "provider": "generic",
       "url": "http://127.0.0.1:9005/" 
    }
  ]
}
第二步、在应用程序主进程中调用electron-updater模块检测更新
```

##### github

```js
第一步，依然是配置"publish"字段。

"build": {
    ...
    "publish": ['github']
}
第二步、在"scripts"中配置新的指令，由于github权限控制，需要GH_TOKEN，可以在 https://github.com/settings/t... 中生成GH_TOKEN。

"scripts": {
    ...
    "release": "cross-env GH_TOKEN=ghp_KmVD3.......W2k3Pd4vV electron-builder"
}
第三步、npm run release，就会在打包后，将资源上传到github，生成release draft，你在github项目中，找到这个draft，publish release就可以了。
```

##### 检查更新

```js
const { autoUpdater } = require('electron-updater')
function checkUpdate(){
  //检测更新
  autoUpdater.checkForUpdates()
  
  //监听'error'事件
  autoUpdater.on('error', (err) => {
    console.log(err)
  })
  
  //监听'update-available'事件，发现有新版本时触发
  autoUpdater.on('update-available', () => {
    console.log('found new version')
  })
  
  //默认会自动下载新版本，如果不想自动下载，设置autoUpdater.autoDownload = false
  
  //监听'update-downloaded'事件，新版本下载完成时触发
  autoUpdater.on('update-downloaded', () => {
    dialog.showMessageBox({
      type: 'info',
      title: '应用更新',
      message: '发现新版本，是否更新？',
      buttons: ['是', '否']
    }).then((buttonIndex) => {
      if(buttonIndex.response == 0) {  //选择是，则退出程序，安装新版本
        autoUpdater.quitAndInstall() 
        app.quit()
      }
    })
  })
}

app.on('ready', () => {
  //每次启动程序，就检查更新
  checkUpdate()
}

```



## 概念

Electron 是利用 web 前端技术进行桌面应用开发的一套框架，它是由 Github 开发的，利用HTML、CSS、JavaScript 来构建跨平台桌面应用程序的一个开源库。**Electron 通过将 Chromium 和 Node.js 合并到同一个运行时环境中**，并将其打包成 Mac、Windowns、Linux 系统下的应用来实现这一目的。



chrome 和 chromium 用户界面几乎一摸一样，但是还是有一些差异的。比如 chrome 的系统标题和边框是被默认禁用的，而 chromium 是默认开启的。另外chrome地址栏里有分享功能，我们可以分享网站，但是 chromium 没有这个功能。界面的细微差别主要是这两个浏览器面向的用户是不一样的，一般来说使用 chrome 的用户是普通用户，用它来浏览网页使用，而使用 chromium 大部分是极客、开发人员和体验新功能的的用户。



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/12/23/16f3040792c0936c~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.awebp)

典型的三层结构, 和 MVC 非常相似：

- **M -- 通用混合层**。 C/C++ 封装核心、通用的业务模块以及业务数据存储。
- **V -- UI 层**。视图层，使用跨平台视图解决方案，对于性能要求较高的部分使用原生实现。比如 Electron
- **C -- 平台桥接层**。介于 M 和 V 之间，桥接`通用混合层`接口，同时也为 UI 层暴露一些**平台相关**的特性。比如在桌面端，这里会通过 Node 原生模块桥接通用混合层, 同时也补充一些 Electron 缺失或不完美的功能。



## Main

Electron 的主进程是一个拥有着完全操作系统访问权限的 Node.js 环境。 除了 [Electron 模组](https://www.electronjs.org/zh/docs/latest/api/app) 之外，您也可以访问 [Node.js 内置模块](https://nodejs.org/dist/latest/docs/api/) 和所有通过 npm 安装的包。

### app

Electron 的许多核心模块都是 Node.js 的[事件触发器](https://nodejs.org/api/events.html#events)，遵循 Node.js 的异步事件驱动架构。 app 模块就是其中一个。在 Electron 中，只有在 app 模块的 [`ready`](https://www.electronjs.org/zh/docs/latest/api/app#event-ready) 事件触发后才能创建 BrowserWindows 实例。 您可以通过使用 [`app.whenReady()`](https://www.electronjs.org/zh/docs/latest/api/app#appwhenready) API 来监听此事件，并在其成功后调用 `createWindow()` 方法。

#### 关闭所有窗口时退出应用

在 Windows 和 Linux 上，我们通常希望在关闭一个应用的所有窗口后让它退出。 要在您的Electron应用中实现这一点，您可以监听 app 模块的 [`window-all-closed`](https://www.electronjs.org/zh/docs/latest/api/app#event-window-all-closed) 事件，并调用 [`app.quit()`](https://www.electronjs.org/zh/docs/latest/api/app#appquit) 来退出您的应用程序。此方法不适用于 macOS。

```js
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit()
})
```

#### 没有窗口打开则打开一个窗口

因为窗口无法在 `ready` 事件前创建，你应当在你的应用初始化后仅监听 `activate` 事件。 要实现这个，仅监听 `whenReady()` 回调即可。

```js
app.whenReady().then(() => {
  createWindow()

  app.on('activate', () => {
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})
```

### BrowserWindow

[BrowserWindow](https://www.electronjs.org/zh/docs/latest/api/browser-window)，它负责创建和管理应用窗口。每个窗口展示一个页面，后者可以来自本地的 HTML，也可以来自远程 URL

```js
const { app, BrowserWindow } = require('electron')

const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600
  })

  win.loadFile('index.html')//将您的页面加载到新的 BrowserWindow 实例
}

app.whenReady().then(() => {
  createWindow()
})
```

#### webPreferences

##### nodeIntegration

- **含义**：是否允许在渲染进程中使用 Node.js API（如 `require`, `process`, `fs` 等）。
- **默认值**：`false`（Electron 5+ 开始默认关闭）
- 效果：
  - 如果为 `true`，渲染进程可以直接访问 Node.js。
  - 如果为 `false`，渲染进程无法直接使用 Node.js 功能。

**可以根据需要为每个窗口单独设置不同的 `webPreferences`，实现更细粒度的安全控制或功能支持。**

##### contextIsolation

- **含义**：是否启用上下文隔离，将预加载脚本和网页内容运行在不同的 JavaScript 上下文中。
- **默认值**：`true`（推荐开启）
- 效果：
  - **即使启用了 `nodeIntegration`，网页中的代码也无法直接访问 Node.js 模块。**
  - 预加载脚本可以访问 Node.js，但网页中不能直接访问这些模块。

```
webPreferences: {
  nodeIntegration: false, //node集成
  contextIsolation: true,
  webSecurity: false, //允许跨域
  preload: path.join(__dirname, 'preload.js')
},
```

在preload使用 `contextBridge` ，可以安全地将部分 Electron API 暴露给渲染进程，而无需开启 `nodeIntegration`。这种方式更安全，能够限制渲染进程的权限，并减少潜在的安全风险。你可以在主进程中使用 `contextBridge.exposeInMainWorld` 方法将需要的 Electron API 暴露给渲染进程，然后在渲染进程中使用 `window.api` 来访问这些 API。

## preload

预加载脚本运行在渲染进程中，但它具有访问主进程的权限，可用于**安全地**暴露部分主进程功能给渲染进程。

| 阶段           | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| 运行时机       | 在渲染进程初始化时、页面加载前执行                           |
| 执行环境       | 预加载脚本运行在具有 HTML DOM 和 Node.js、Electron API 的有限子集访问权限的环境中 |
| 主要用途       | 初始化 IPC 通信、暴露安全接口给前端、注入全局功能            |
| 是否可重复运行 | 每次窗口加载页面时都会运行一次                               |

### 渲染器加载时的DOM展示

```js
function domReady(
  condition: DocumentReadyState[] = ["complete", "interactive"]
) {
  return new Promise((resolve) => {
    if (condition.includes(document.readyState)) {
      resolve(true);
    } else {
      document.addEventListener("readystatechange", () => {
        if (condition.includes(document.readyState)) {
          resolve(true);
        }
      });
    }
  });
}

const safeDOM = {
  append(parent: HTMLElement, child: HTMLElement) {
    if (!Array.from(parent.children).find((e) => e === child)) {
      return parent.appendChild(child);
    }
  },
  remove(parent: HTMLElement, child: HTMLElement) {
    if (Array.from(parent.children).find((e) => e === child)) {
      return parent.removeChild(child);
    }
  },
};

/**
 * https://tobiasahlin.com/spinkit
 * https://connoratherton.com/loaders
 * https://projects.lukehaas.me/css-loaders
 * https://matejkustec.github.io/SpinThatShit
 */
function useLoading() {
  const className = `loaders-css__square-spin`;
  const styleContent = `
@keyframes square-spin {
  25% { transform: perspective(100px) rotateX(180deg) rotateY(0); }
  50% { transform: perspective(100px) rotateX(180deg) rotateY(180deg); }
  75% { transform: perspective(100px) rotateX(0) rotateY(180deg); }
  100% { transform: perspective(100px) rotateX(0) rotateY(0); }
}
.${className} > div {
  animation-fill-mode: both;
  width: 50px;
  height: 50px;
  background: #fff;
  animation: square-spin 3s 0s cubic-bezier(0.09, 0.57, 0.49, 0.9) infinite;
}
.app-loading-wrap {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #282c34;
  z-index: 9;
}
    `;
  const oStyle = document.createElement("style");
  const oDiv = document.createElement("div");

  oStyle.id = "app-loading-style";
  oStyle.innerHTML = styleContent;
  oDiv.className = "app-loading-wrap";
  oDiv.innerHTML = `<div class="${className}"><div></div></div>`;

  return {
    appendLoading() {
      safeDOM.append(document.head, oStyle);
      safeDOM.append(document.body, oDiv);
    },
    removeLoading() {
      safeDOM.remove(document.head, oStyle);
      safeDOM.remove(document.body, oDiv);
    },
  };
}

const { appendLoading, removeLoading } = useLoading();
domReady().then(appendLoading);

setTimeout(removeLoading, 4999);
```



### contextBridge通信

如果你想为渲染器添加需要特殊权限的功能，可以通过 [contextBridge](https://www.electronjs.org/zh/docs/latest/api/context-bridge) 接口定义 [全局对象](https://developer.mozilla.org/en-US/docs/Glossary/Global_object)。

```js
//创建一个将应用中的 Chrome、Node、Electron 版本号暴露至渲染器的预加载脚本

const { contextBridge } = require('electron')

contextBridge.exposeInMainWorld('versions', {
  node: () => process.versions.node,
  chrome: () => process.versions.chrome,
  electron: () => process.versions.electron
  // 除函数之外，我们也可以暴露变量
})

//安全
contextBridge.exposeInMainWorld('versions', {
   ping: () => ipcRenderer.invoke('ping')
})
//可以注意到我们使用了一个辅助函数来包裹 ipcRenderer.invoke('ping') 调用，而并非直接通过 context bridge 暴露 ipcRenderer 模块。 你永远都不会想要通过预加载直接暴露整个 ipcRenderer 模块。 这将使得你的渲染器能够直接向主进程发送任意的 IPC 信息，会使得其成为恶意代码最强有力的攻击媒介。
```

```js
//为了将脚本附在渲染进程上，在 BrowserWindow 构造器中使用 webPreferences.preload 传入脚本的路径。
const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })
```

```js
const information = document.getElementById('info')
information.innerText = `This app is using Chrome (v${window.versions.chrome()}), Node.js (v${window.versions.node()}), and Electron (v${window.versions.electron()})`
```

### 与Typescript一同使用

在这个 `preload.ts` 脚本中：

preload.ts

```typescript
contextBridge.exposeInMainWorld('electronAPI', {
  loadPreferences: () => ipcRenderer.invoke('load-prefs')
})
```

您可以创建一个 `renderer.d.ts` 类型声明文件，并且全局增强 `Window` 接口。

renderer.d.ts

```typescript
export interface IElectronAPI {
  loadPreferences: () => Promise<void>,
}

declare global {
  interface Window {
    electronAPI: IElectronAPI
  }
}
```

以上所做皆是为了确保在您编写渲染进程的脚本时， TypeScript 编译器将会知晓`electronAPI`合适地在您的全局`window`对象中

renderer.ts

```typescript
window.electronAPI.loadPreferences()
```

## IPC进程间通信

### 进程模型

![在这里插入图片描述](https://img-blog.csdnimg.cn/94160f9fbbdc4ce0a9bab4e95689f54a.png)

- 主进程：每个 Electron 应用都有一个单一的主进程，作为应用程序的入口点。 主进程在 Node.js 环境中运行，这意味着它具有 `require` 模块和使用所有 Node.js API 的能力。
  - 进程间通信
  - 窗口管理。使用 [`BrowserWindow`](https://www.electronjs.org/zh/docs/latest/api/browser-window) 模块创建和管理应用程序窗口
  - 全局通用服务。
  - 一些只能或适合在主进程做的事情。例如浏览器下载、全局快捷键处理、托盘、session。
  - 维护一些必要的全局状态
  - 上面说的`通用混合层`也跑在这个进程。通过 Node C++ 插件暴露接口。

- 渲染进程(Renderer Process)：每个 Electron 应用有一个或多个渲染进程, 对应多个 Web 页面

- 除此之外还有 GPU 进程、扩展进程等等。

### 概念

[ipcMain](https://link.juejin.cn?target=https%3A%2F%2Fwww.electronjs.org%2Fdocs%2Fapi%2Fipc-main)从主进程到渲染进程的异步通信。`ipcMain` 是一个 [EventEmitter](https://link.juejin.cn/?target=https%3A%2F%2Fnodejs.org%2Fapi%2Fevents.html%23events_class_eventemitter) 的实例。 当在主进程中使用时，它处理从渲染器进程（网页）发送出来的**异步和同步**信息。 从渲染器进程发送的消息将被发送到该模块。

[ipcRenderer](https://link.juejin.cn?target=https%3A%2F%2Fwww.electronjs.org%2Fdocs%2Fapi%2Fipc-renderer)：从渲染器进程到主进程的异步通信。`ipcRenderer` 是一个 [EventEmitter](https://link.juejin.cn/?target=https%3A%2F%2Fnodejs.org%2Fapi%2Fevents.html%23events_class_eventemitter) 的实例。 你可以使用它提供的一些方法从渲染进程 (web 页面) 发送**同步或异步**的消息到主进程。 也可以接收主进程回复的消息。

[webContents](https://link.juejin.cn?target=https%3A%2F%2Fwww.electronjs.org%2Fdocs%2Fapi%2Fweb-contents%23contentssendchannel-args)

在 Electron 中，`webContents`、`ipcMain` 和 `ipcRenderer` 是用于进程间通信（IPC）和窗口管理的核心模块，它们分别运行在不同的进程中，并承担不同的职责。

------

#### ✅ 1. `webContents`

- 📍 所属模块：

  - 属于 `BrowserWindow` 实例的属性。

  - 运行在 **主进程** 中。

- 📌 功能说明：

  - 用于控制和操作渲染进程的内容（即页面内容）。

  - 可以执行如加载 URL、执行 JS 脚本、监听页面事件等操作。

- 🧠 常见用途：

  ```
  tsconst win = new BrowserWindow({ ... });
  win.webContents.loadURL('https://example.com');
  win.webContents.send('event-name', data); // 向渲染进程发送事件
  ```

- 🔁 通信方向：
  - 主进程 ➜ 渲染进程（单向）

------

#### ✅ 2. `ipcMain`

- 📍 所属模块：

  - 主进程模块。

  - 在主进程中使用。

- 📌 功能说明：

  - **用于监听来自渲染进程的异步和同步消息（通过 `ipcRenderer.send()` 发送）。**

  - 可以响应请求并返回数据给渲染进程。

- 🧠 常见用途：

```
ts// 主进程
ipcMain.on('some-event', (event, arg) => {
  console.log(arg); // 接收到渲染进程发来的消息
  event.reply('some-reply', 'response data'); // 回复
});
```

- 🔁 通信方向：
  - 渲染进程 ➜ 主进程（双向或单向）

- api

  **ipcMain.on(channel, listener)** 主进程监听来自渲染进程的通信，

  **ipcMain.once(channel, listener)**只监听一次事件

  **ipcMain.removeListener(channel, listener)** 参数跟定义时的监听函数一致才能被移除

  **ipcMain.removeAllListeners([channel])**

- 异步的来回通信

  **ipcMain.handle(channel, listener)** 主进程的监听函数

  **ipcMain.handleOnce(channel, listener)** 单个执行一次

  **ipcMain.removeHandler(channel)**移除监听函数

------

#### ✅ 3. `ipcRenderer`

- 📍 所属模块：

  - 渲染进程模块。

  - 在渲染进程中使用（或在 `preload.js` 中使用）。

- 📌 功能说明：
  - 用于向主进程发送消息，并可以监听主进程的回复。

- 🧠 常见用途：

```
//  preload.js

//渲染进程发送给主进程
ipcRenderer.send('some-event', 'hello from renderer');

//渲染进程监听主进程
ipcRenderer.on('some-reply', (event, data) => {
  console.log('Received:', data);
});
```

- 🔁 通信方向：
  - 渲染进程 ➜ 主进程（双向或单向）

------

#### 🔄 总结对比表

| 模块          | 所属进程 | 使用位置                | 通信方向  | 主要功能                   |
| :------------ | :------- | :---------------------- | :-------- | :------------------------- |
| `webContents` | 主进程   | 主进程                  | 主 ➜ 渲染 | 控制窗口内容、发送事件     |
| `ipcMain`     | 主进程   | 主进程                  | 渲染 ➜ 主 | 监听渲染进程消息、处理逻辑 |
| `ipcRenderer` | 渲染进程 | 渲染进程 / `preload.js` | 渲染 ➜ 主 | 发送请求、接收主进程响应   |

####  IPC 通信流程

主进程（main.js）

```
ipcMain.on('get-data', (event) => {
  event.reply('return-data', { info: 'Hello from main' });
});
```

渲染进程 / Preload 脚本

```
ipcRenderer.send('get-data');

ipcRenderer.on('return-data', (event, data) => {
  console.log(data.info); // 输出: Hello from main
});
```



### 渲染进程->主进程并返回数据

异步的来回通信

**ipcMain.handle(channel, listener)** 主进程的监听函数

**ipcRenderer.invoke(channel, ...args)** 渲染进程的发送消息的函数

```js
//preload.js
const { contextBridge, ipcRenderer } = require('electron')

contextBridge.exposeInMainWorld('versions', {
  ping: () => ipcRenderer.invoke('ping')
  // 除函数之外，我们也可以暴露变量
})
```

```js
//main.js
const { app, BrowserWindow, ipcMain } = require('electron')
const path = require('path')

const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })
  win.loadFile('index.html')
}
app.whenReady().then(() => {
  ipcMain.handle('ping', () => 'pong')
  createWindow()
})
```

```js
//renderer.js
const func = async () => {
  const response = await window.versions.ping()
  console.log(response) // 打印 'pong'
}

func()
```

### 渲染器进程到主进程（单向）

**渲染线程** 到 **主线程** 需要通过  `ipcRenderer.send`发送  ---> `ipcMain.on`来监听

> ipcMain.on 和 ipcRenderer.send
> 示例描述：从 渲染进程中操作主进程的关闭操作，也就是从渲染进程中关闭electron

（1）主进程：electron/main.js

```
const { ipcMain } = require('electron');
ipcMain.on('operation-window',function(event,operationType){
    switch(operationType){
        case "max"://窗口 最大化
            win.maximize()
            break;
        case 'restoreDown'://窗口 向下还原
             win.unmaximize()
            break;
        case "min"://窗口 最小化
             win.minimize()
            break;
        case 'close'://窗口 关闭
             win.close()
        break;
    }
})

```

（2）预加载文件：electron/preload.js

```
//向渲染器进程暴露一个全局的 window.electronAPI 变量。
const { contextBridge, ipcRenderer } = require('electron');
contextBridge.exposeInMainWorld('electronAPI', {
    operationWindow: (operationType) => ipcRenderer.send('operation-window', operationType),
})
```


（3）渲染进程文件：xxx.vue

```
<div>
    <button @click="onClickClose">关闭窗口</button>
</div>
<script lang="ts" setup>
    const onClickClose = () => {
        if(window.electronAPI && window.electronAPI.operationWindow){
            window.electronAPI.operationWindow('close')                            
        }
    }
</script>
```

### 渲染器进程到主进程（双向）

> ipcMain.handle 和 ipcRenderer.invoke
> 示例描述：从渲染进程中 调用 主进程的弹窗选择文件后返回文件路径给 渲染进程
>
> - 在主进程中，我们将创建一个 handleFileOpen() 函数，它调用electron的 dialog.showOpenDialog 并返回用户选择的文件路径值。
> - 每当渲染器进程通过 dialog:openFile 通道发送 ipcRender.invoke 消息时，此函数被用作一个回调。然后，返回值将作为一个 Promise 返回到最初的 invoke 调用。

1）主进程：electron/main.js

```
const { ipcMain,dialog } = require('electron');
async function handleFileOpen() {
  const { canceled, filePaths } = await dialog.showOpenDialog()//调用electron的打开文件弹窗
  if (canceled) {
    return
  } else {
    return filePaths[0] // 返回文件名给渲染进程
  }
}
ipcMain.handle('dialog:openFile',handleFileOpen)
```


（2）预加载文件：electron/preload.js

```
import { contextBridge, ipcRenderer } from 'electron'

contextBridge.exposeInMainWorld('electronAPI', {
  openFile: () => ipcRenderer.invoke('dialog:openFile')
})
```


（3）渲染进程文件：xxx.vue

```
<div>
    <button @click="onSelectFile">选择文件</button>
</div>
<script lang="ts" setup>
    const onSelectFile = async () => {
        if(window.electronAPI && window.electronAPI.openFile){
            const filePath = await window.electronAPI.openFile()  
            console.log("选择的文件路径是：" + filePath)                          
        }
    }
</script>
```





### 主进程到渲染器进程

**主线程** 到 **渲染线程** 通过 `webContents.send` 来发送 --->`ipcRenderer.on` 来监听

### Electron 中的消息端口Remote 模块

在渲染进程里如果要使用到 **BrowserWindow** 这些属性的话就必须使用 `remote`，使用 `remote` 模块, 你可以调用 `main` **进程对象的方法**

## 配置

### 窗口

https://juejin.cn/post/6888907382806544392#heading-12

### 顶部标题栏配置

```
new BrowserWindow({
    icon: path.join(__static,"./zero.ico"),  
})
```

标题栏title和html中的title是一样的

#### 自定义

https://www.toutiao.com/article/7146401504377799180/?wid=1690858382324

```
new BrowserWindow({
    frame: false // 去掉默认的标题栏
})
```

```vue
<template>
  <div id="title-bar">
    <div id="title">My Application</div>
    <div id="buttons">
      <i class="el-icon-minus mr-10" id="minimize"></i>
      <div class="button" id="maximize"></div>
      <i class="el-icon-close" id="close"></i>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
@Component({})
export default class VueCom extends Vue {
  mounted() {
    const remote = require('electron').remote;

    document.getElementById('minimize').addEventListener('click', () => {
      remote.getCurrentWindow().minimize();
    });

    document.getElementById('maximize').addEventListener('click', () => {
      const win = remote.getCurrentWindow();
      if (win.isMaximized()) {
        win.unmaximize();
      } else {
        win.maximize();
      }
    });

    document.getElementById('close').addEventListener('click', () => {
      remote.app.quit();
    });
  }
}
</script>
<style lang="scss">
#title-bar {
  -webkit-app-region: drag;
  height: 28px;
  // background-color: #333;
  // color: white;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
  font-size: 18px;
}
#title-bar #buttons {
  display: flex;
}
#title-bar .button {
  width: 14px;
  height: 14px;
  margin-left: 10px;
}
// #title-bar #minimize {
//   background-color: #f0ad4e;
// }
// #title-bar #maximize {
//   background-color: #5cb85c;
// }
// #title-bar #close {
//   background-color: #d9534f;
// }
</style>

```



### 菜单

https://www.misterma.com/archives/896/

https://zhuanlan.zhihu.com/p/426436147

#### 菜单栏图标

```

{
label: "显示主窗口", 
icon: path.join(__static,"./logo.png"),      
click: function() {
mainWindow.show();         
} //打开相应页面
},
{//打开相应页面
label: "检查更新",
type:'checkbox',
checked: true,
click: function() { } 
},
```

### 托盘

#### 系统托盘图标

```
let iconPath = path.join(__static, "./zero.ico");
let appTray = new Tray(iconPath);


win.on('close', (event) => {
    if (
      dialog.showMessageBoxSync(win, {
        type: 'info',
        buttons: ['最小化到托盘', '直接退出'],
        title: '提示',
        message: '确定要退出吗？',
        defaultId: 0,
        cancelId: 1
      }) === 0
    ) {
      event.preventDefault();
      win.hide();
    } else {
      app.exit();
    }
  });
```

#### 系统托盘自动消失

https://blog.csdn.net/liu19721018/article/details/109046186

### dialog

```
ipcMain.on('logout',(e)=>{
    dialog.showMessageBox({
      type: 'info',
      noLink:true, // windows 下的传统样式
      title: '提示信息',
      icon:path.join(__static,"./zero.ico"),
      defaultId: 0,
      message:"确定要退出登录吗？",
      buttons:['确定','取消']
    },(index) => {
      if(index === 1){
        // 什么都不做
        e.preventDefault(); // 阻止默认行为            
      }else{
        mainWindow.loadURL(winURL)
        mainWindow.setSize(400,400);
      }
    });
  })

```

## 权限

管理员，普通用户之间对于应用的操作权限

## 请求

## API

### 生命周期

ready: app 初始化完成

dom-ready: 一个窗口中的文本加载完成

did-finish-load: 导航完成时触发

closed: 当窗口关闭时触发，此时应删除窗口引用

window-all-closed: 所有窗口都被关闭时触发



#### 退出

- before-quit: 在关闭窗口之前触发

- will-quit: 在窗口关闭并且应用退出时触发
- quit: 当所有窗口被关闭时触发

退出方式

- 默认退出

  - 如果没有监听`window-all-closed`事件并且所有窗口都关闭了，默认的行为是退出程序

  - 如果监听了`window-all-closed`事件，那么在你的所有窗口都关闭时会去执行

- quit退出

  当开发者调用了 app.quit()，如果此时所有窗口已经关闭，直接触发quit事件；否则Electron 会首先触发before-quit，然后开始关闭所有的窗口并且触发 will-quit事件，在这种情况下 window-all-closed 事件不会被触发，所以你可以放心在window-all-closed里使用app.quit()，而不用担心会出现无限递归。

  调用app.quit()并不能保证程序一定会退出，在before-quit，will-quit中调用event.preventDefault()或者在window的close事件回调函数中阻止窗口关闭，都可以使程序退出失败。也就是说quit不是独裁者，更像是众议院，老大提出退出操作，然后众人商量，退出的愿望可能被否决

- exit退出

  既然有`app.quit`这种温和的退出方式，那么也会有更加粗暴的退出方案，那就是`app.exit`。

  与`quit`这种民主相比，`exit`更像是独裁者，他会直接执行退出操作，不跟任何人商量，只要保证你的程序中的逻辑合理，是完全可以拿来使用的



### process

 [`process.platform`](https://nodejs.org/api/process.html#process_process_platform) 变量，您可以针对特定平台运行特定代码。 请注意，Electron 目前只支持三个平台：`win32` (Windows), `linux` (Linux) 和 `darwin` (macOS) 。

### cookies

> electron webview clear cookies

Electron Webview 是 Electron 框架中的一个标签，用于在桌面应用中显示网页内容。要清除 Webview 中的 cookies，可以使用以下方法之一：

1. 在 Webview 加载网页之前，使用 `session.defaultSession.cookies.remove()` 方法删除所有 cookies。例如：

```lua
lua复制代码const {session} = require('electron')

session.defaultSession.cookies.remove('http://example.com', 'CookieName', (error) => {
  if (error) console.error(error)
})
```

1. 在 Webview 加载网页之后，使用 `webview.getWebContents().session.clearStorageData()` 方法清除所有本地存储数据，包括 cookies。例如：

```go
go复制代码const {webContents} = require('electron')

webview.getWebContents().session.clearStorageData({
  storages: ['cookies']
}, (error) => {
  if (error) console.error(error)
})
```

注意：需要确保在调用这些方法之前已经加载了 Webview 标签。

### 网站信息

app.setAboutPanelOptions(options)

- 选项 对象
  - applicationName string (可选) - 应用程序的名字
  - applicationVersion string (可选) - 应用程序版本
  - copyright string (可选) - 版权信息
  - version string (可选) macOS - 应用程序版本号
  - credits string (可选) macOS Windows - 信用信息。
  - authors string[] (可选) Linux - 应用程序作者列表。
  - website string (可选) Linux - 应用程序的网站。
  - iconPath string (可选) Linux Windows - 以JPEG 或 PNG 文件格式为应用程序图标路径。 在 Linux 上，将显示为 64x64 像素，同时保留纵横比。



### 事件

(1)、ready 当electron完成初始化时被触发

app.on('ready', () => {
  	createWindow();
})

(2)、window-all-closed 所有窗口被关闭

app.on('window-all-closed', function () {
})

(3)、quit 在所有应用程序退出时发出

app.on('window-all-closed', function () {
  	if (process.platform !== 'darwin') {
    	app.quit()
  	}
})

(4)、open-file 在应用中要打开一个文件时发出

(5)、open-url 应用中要打开一个URL网址时发出

(6)、activate 当应用被激活时发出



### webContents

did-finish-load 导航完成时触发，即选项卡的旋转器将停止旋转，并指派 onload 事件后

mainWindow.webContents.on('did-finish-load',()=>{
	console.log("did-finish-load")
})

dom-ready 一个框架中的文本加载完成后触发该事件

mainWindow.webContents.on('dom-ready',()=>{
	console.log("dom-ready")
})



# uniapp

## 目录结构

一个uni-app工程，默认包含如下目录及文件：

```
┌─cloudfunctions        云函数目录（阿里云为aliyun，腾讯云为tcb，详见uniCloud）
│─components            符合vue组件规范的uni-app组件目录
│  └─comp-a.vue         可复用的a组件
├─hybrid                存放本地网页的目录，详见
├─platforms             存放各平台专用页面的目录，详见
├─pages                 业务页面文件存放的目录
│  ├─index
│  │  └─index.vue       index页面
│  └─list
│     └─list.vue        list页面
├─static                存放应用引用静态资源（如图片、视频等）的目录，注意：静态资源只能存放于此
├─wxcomponents          存放小程序组件的目录，详见
├─main.js               Vue初始化入口文件
├─App.vue               应用配置，用来配置App全局样式以及监听 应用生命周期
├─manifest.json         配置应用名称、appid、logo、版本等打包信息，详见
└─pages.json            配置页面路由、导航条、选项卡等页面类信息，详见
```

## 资源路径说明

- 编译到任意平台时，`static` 目录下的文件均会被打包进去，非 `static` 目录下的文件（vue、js、css 等）被引用到才会被包含进去。

- `static` 目录下的 `js` 文件不会被编译，如果里面有 `es6` 的代码，不经过转换直接运行，在手机设备上会报错。

- `css`、`less/scss` 等资源同样不要放在 `static` 目录下，建议这些公用的资源放在 `common` 目录下。

- ```
  <!-- 绝对路径，/static指根目录下的static目录，在cli项目中/static指src目录下的static目录 -->
  <image class="logo" src="/static/logo.png"></image>
  <image class="logo" src="@/static/logo.png"></image>
  <!-- 相对路径 -->
  <image class="logo" src="../../static/logo.png"></image>
  
  // 绝对路径，@指向项目根目录，在cli项目中@指向src目录
  import add from '@/common/add.js'
  ```

- 本地背景图片/字体文件的引用路径推荐使用以 ~@ 开头的绝对路径。

## 页面样式与布局

`uni-app` 支持的通用 css 单位包括 px、rpx

- px 即屏幕像素

- rpx 即响应式px，一种根据屏幕宽度自适应的动态单位。以750宽的屏幕为基准，750rpx恰好为屏幕宽度。屏幕变宽，rpx 实际显示效果会等比放大，但在 App 端和 H5 端屏幕宽度达到 960px 时，默认将按照 375px 的屏幕宽度进行计算，

  ```
  {
    "globalStyle": {
      "rpxCalcMaxDeviceWidth": 960, // rpx 计算所支持的最大设备宽度，单位 px，默认值为 960
      "rpxCalcBaseDeviceWidth": 375, // rpx 计算使用的基准设备宽度，设备实际宽度超出 rpx 计算所支持的最大设备宽度时将按基准宽度计算，单位 px，默认值为 375
      "rpxCalcIncludeWidth": 750 // rpx 计算特殊处理的值，始终按实际的设备宽度计算，单位 rpx，默认值为 750
    },
  }
  ```

  - match-media组件  为组件指定一组 media query 媒体查询规则

vue页面支持下面这些普通H5单位，但在nvue里不支持：

- rem 根字体大小可以通过 [page-meta](https://uniapp.dcloud.io/component/page-meta?id=page-meta) 配置
- vh viewpoint height，视窗高度，1vh等于视窗高度的1%
- vw viewpoint width，视窗宽度，1vw等于视窗宽度的1%

```
若设计稿宽度为 750px，元素 A 在设计稿上的宽度为 100px，那么元素 A 在 uni-app 里面的宽度应该设为：750 * 100 / 750，结果为：100rpx。
```

**tip:**

- rpx不支持动态横竖屏切换计算，使用rpx建议锁定屏幕方向

## 生命周期

https://uniapp.dcloud.io/collocation/frame/lifecycle?id=%e5%ba%94%e7%94%a8%e7%94%9f%e5%91%bd%e5%91%a8%e6%9c%9f

- `uni-app` 支持如下**应用生命周期**函数：

  | 函数名               | 说明                                                         |
  | :------------------- | :----------------------------------------------------------- |
  | onLaunch             | 当`uni-app` 初始化完成时触发（全局只触发一次）               |
  | onShow               | 当 `uni-app` 启动，或从后台进入前台显示                      |
  | onHide               | 当 `uni-app` 从前台进入后台                                  |
  | onError              | 当 `uni-app` 报错时触发                                      |
  | onUniNViewMessage    | 对 `nvue` 页面发送的数据进行监听，可参考 [nvue 向 vue 通讯](https://uniapp.dcloud.io/use-weex?id=nvue-向-vue-通讯) |
  | onUnhandledRejection | 对未处理的 Promise 拒绝事件监听函数（2.8.1+）                |
  | onPageNotFound       | 页面不存在监听函数                                           |
  | onThemeChange        | 监听系统主题变化                                             |

  **注意**

  - 应用生命周期仅可在`App.vue`中监听，在其它页面监听无效。
  - onlaunch里进行页面跳转，如遇白屏报错，请参考https://ask.dcloud.net.cn/article/35942

- **页面生命周期**

  | onInit                              | 监听页面初始化，其参数同 onLoad 参数，为上个页面传递的数据，参数类型为 Object（用于页面传参），触发时机早于 onLoad | 百度小程序                                                   | 3.1.0+ |
  | ----------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------ |
  | onLoad                              | 监听页面加载，其参数为上个页面传递的数据，参数类型为 Object（用于页面传参），参考[示例](https://uniapp.dcloud.io/api/router?id=navigateto) |                                                              |        |
  | onShow                              | 监听页面显示。页面每次出现在屏幕上都触发，包括从下级页面点返回露出当前页面 |                                                              |        |
  | onReady                             | 监听页面初次渲染完成。注意如果渲染速度快，会在页面进入动画完成前触发 |                                                              |        |
  | onHide                              | 监听页面隐藏                                                 |                                                              |        |
  | onUnload                            | 监听页面卸载                                                 |                                                              |        |
  | onResize                            | 监听窗口尺寸变化                                             | App、微信小程序                                              |        |
  | onPullDownRefresh                   | 监听用户下拉动作，一般用于下拉刷新，参考[示例](https://uniapp.dcloud.io/api/ui/pulldown) |                                                              |        |
  | onReachBottom                       | 可在pages.json里定义具体页面底部的触发距离[onReachBottomDistance](https://uniapp.dcloud.io/collocation/pages)，比如设为50，那么滚动页面到距离底部50px时，就会触发onReachBottom事件。 |                                                              |        |
  | onTabItemTap                        | 点击 tab 时触发，参数为Object，具体见下方注意事项            | 微信小程序、支付宝小程序、百度小程序、H5、App（自定义组件模式） |        |
  | onShareAppMessage                   | 用户点击右上角分享                                           | 微信小程序、百度小程序、字节跳动小程序、支付宝小程序         |        |
  | onPageScroll                        | 监听页面滚动，参数为Object                                   | nvue暂不支持                                                 |        |
  | onNavigationBarButtonTap            | 监听原生标题栏按钮点击事件，参数为Object                     | App、H5                                                      |        |
  | onBackPress                         | 监听页面返回，返回 event = {from:backbutton、 navigateBack} ，backbutton 表示来源是左上角返回按钮或 android 返回键；navigateBack表示来源是 uni.navigateBack ；详细说明及使用：[onBackPress 详解](http://ask.dcloud.net.cn/article/35120)。支付宝小程序只有真机能触发，只能监听非navigateBack引起的返回，不可阻止默认行为。 | app、H5、支付宝小程序                                        |        |
  | onNavigationBarSearchInputChanged   | 监听原生标题栏搜索输入框输入内容变化事件                     | App、H5                                                      | 1.6.0  |
  | onNavigationBarSearchInputConfirmed | 监听原生标题栏搜索输入框搜索事件，用户点击软键盘上的“搜索”按钮时触发。 | App、H5                                                      | 1.6.0  |
  | onNavigationBarSearchInputClicked   | 监听原生标题栏搜索输入框点击事件                             | App、H5                                                      | 1.6.0  |
  | onShareTimeline                     | 监听用户点击右上角转发到朋友圈                               | 微信小程序                                                   | 2.8.1+ |
  | onAddToFavorites                    | 监听用户点击右上角收藏                                       | 微信小程序                                                   | 2.8.1+ |

- **组件生命周期**

  `uni-app` 组件支持的生命周期，与vue标准组件的生命周期相同。这里没有页面级的onLoad等生命周期：

  | 函数名        | 说明                                                         | 平台差异说明 | 最低版本 |
  | :------------ | :----------------------------------------------------------- | :----------- | :------- |
  | beforeCreate  | 在实例初始化之后被调用。[详见](https://cn.vuejs.org/v2/api/#beforeCreate) |              |          |
  | created       | 在实例创建完成后被立即调用。[详见](https://cn.vuejs.org/v2/api/#created) |              |          |
  | beforeMount   | 在挂载开始之前被调用。[详见](https://cn.vuejs.org/v2/api/#beforeMount) |              |          |
  | mounted       | 挂载到实例上去之后调用。[详见](https://cn.vuejs.org/v2/api/#mounted) 注意：此处并不能确定子组件被全部挂载，如果需要子组件完全挂载之后在执行操作可以使用`$nextTick`[Vue官方文档](https://cn.vuejs.org/v2/api/#Vue-nextTick) |              |          |
  | beforeUpdate  | 数据更新时调用，发生在虚拟 DOM 打补丁之前。[详见](https://cn.vuejs.org/v2/api/#beforeUpdate) | 仅H5平台支持 |          |
  | updated       | 由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。[详见](https://cn.vuejs.org/v2/api/#updated) | 仅H5平台支持 |          |
  | beforeDestroy | 实例销毁之前调用。在这一步，实例仍然完全可用。[详见](https://cn.vuejs.org/v2/api/#beforeDestroy) |              |          |
  | destroyed     | Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。[详见](https://cn.vuejs.org/v2/api/#destroyed) |              |          |

## 路由

### API

框架以栈的形式管理当前所有页面， 当发生路由切换的时候，页面栈的表现如下：

| 路由方式   | 页面栈表现                        | 触发时机                                                     |
| ---------- | --------------------------------- | ------------------------------------------------------------ |
| 初始化     | 新页面入栈                        | uni-app 打开的第一个页面                                     |
| 打开新页面 | 新页面入栈                        | 调用 API  [uni.navigateTo](https://uniapp.dcloud.io/api/router?id=navigateto) 、使用组件  [](https://uniapp.dcloud.io/component/navigator?id=navigator) |
| 页面重定向 | 当前页面出栈，新页面入栈          | 调用 API  [uni.redirectTo](https://uniapp.dcloud.io/api/router?id=redirectto) 、使用组件 [](https://uniapp.dcloud.io/component/navigator?id=navigator) |
| 页面返回   | 页面不断出栈，直到目标返回页      | 调用 API  [uni.navigateBack](https://uniapp.dcloud.io/api/router?id=navigateback)  、使用组件 [](https://uniapp.dcloud.io/component/navigator?id=navigator) 、用户按左上角返回按钮、安卓用户点击物理back按键 |
| Tab 切换   | 页面全部出栈，只留下新的 Tab 页面 | 调用 API  [uni.switchTab](https://uniapp.dcloud.io/api/router?id=switchtab) 、使用组件 [](https://uniapp.dcloud.io/component/navigator?id=navigator) 、用户切换 Tab |
| 重加载     | 页面全部出栈，只留下新的页面      | 调用 API  [uni.reLaunch](https://uniapp.dcloud.io/api/router?id=relaunch) 、使用组件  [](https://uniapp.dcloud.io/component/navigator?id=navigator) |

### 传参

#### navigateBack

```
//page-A
let pages = getCurrentPages();
let currPage = pages[pages.length - 1]; // 当前页的实例
this.cityCul = currPage.$vm.cityCul
		       
//page-B
let pages = getCurrentPages()
let nowPage = pages[pages.length - 1]; //当前页页面实例
let prevPage = pages[pages.length - 2]; //上一页页面实例
prevPage.$vm.cityCul = this.CityCul
uni.navigateBack({
		url: "../index/index?city=" + this.CityCul

});
```

### Tips:

- **目前页面路径最多只能十层。**

## 运行环境判断

[开发环境和生产环境](https://uniapp.dcloud.io/frame?id=开发环境和生产环境)

`uni-app` 可通过 `process.env.NODE_ENV` 判断当前环境是开发环境还是生产环境。一般用于连接测试服务器或生产服务器的动态切换。

- 在HBuilderX 中，点击“运行”编译出来的代码是开发环境，点击“发行”编译出来的代码是生产环境
- cli模式下，是通行的编译环境处理方式。

```javascript
if(process.env.NODE_ENV === 'development'){
    console.log('开发环境')
}else{
    console.log('生产环境')
}
```

**[判断平台](https://uniapp.dcloud.io/frame?id=判断平台)**

平台判断有2种场景，一种是在编译期判断，一种是在运行期判断。

- 编译期判断 编译期判断，即条件编译，不同平台在编译出包后已经是不同的代码。详见：[条件编译](https://uniapp.dcloud.io/platform)

```javascript
// #ifdef H5
    alert("只有h5平台才有alert方法")
// #endif
```
- 运行期判断 运行期判断是指代码已经打入包中，仍然需要在运行期判断平台，此时可使用 `uni.getSystemInfoSync().platform` 判断客户端环境是 Android、iOS 还是小程序开发工具（在百度小程序开发工具、微信小程序开发工具、支付宝小程序开发工具中使用 `uni.getSystemInfoSync().platform` 返回值均为 devtools）。

  ```js
  switch(uni.getSystemInfoSync().platform){
      case 'android':
         console.log('运行Android上')
         break;
      case 'ios':
         console.log('运行iOS上')
         break;
      default:
         console.log('运行在开发者工具上')
         break;
  }
  ```

  

## 页面样式与尺寸

[尺寸单位](https://uniapp.dcloud.io/frame?id=尺寸单位)

`uni-app` 支持的通用 css 单位包括 px、rpx

- px 即屏幕像素

- rpx 即响应式px，一种根据屏幕宽度自适应的动态单位。

  rpx 是相对于基准宽度的单位，可以根据屏幕宽度进行自适应。

  ```
  开发者可以通过设计稿基准宽度计算页面元素 rpx 值，设计稿 1px 与框架样式 1rpx 转换公式如下：
  设计稿 1px / 设计稿基准宽度 = 框架样式 1rpx / 750rpx
  750 * 元素在设计稿中的宽度 / 设计稿基准宽度
  
  举例说明：
  若设计稿宽度为 750px，元素 A 在设计稿上的宽度为 100px，那么元素 A 在 uni-app 里面的宽度应该设为：750 * 100 / 750，结果为：100rpx。
  若设计稿宽度为 640px，元素 A 在设计稿上的宽度为 100px，那么元素 A 在 uni-app 里面的宽度应该设为：750 * 100 / 640，结果为：117rpx。
  ```

- ### 固定值

`uni-app` 中以下组件的高度是固定的，不可修改：

| 组件          | 描述       | App                                                          | H5   |
| :------------ | :--------- | :----------------------------------------------------------- | :--- |
| NavigationBar | 导航栏     | 44px                                                         | 44px |
| TabBar        | 底部选项卡 | HBuilderX 2.3.4之前为56px，2.3.4起和H5调为一致，统一为 50px。但可以自主更改高度） |      |

## renderjs

`renderjs`是一个运行在视图层的js。它比[WXS](https://uniapp.dcloud.io/?id=wxs)更加强大。它只支持app-vue和h5。

`renderjs`的主要作用有2个：

- 大幅降低逻辑层和视图层的通讯损耗，提供高性能视图交互能力
- 在视图层操作dom，运行for web的js库

**使用方式**

设置 script 节点的 lang 为 renderjs

```html
<script module="test" lang="renderjs">
    export default {
        mounted() {
            // ...
        },
        methods: {
            // ...
        }
    }
</script>
```

## 使用vue注意事项

### uniapp中nvue与vue的区别

uni-app是逻辑和渲染分离的，渲染层在**app端**提供了两套排版引擎。
小程序方式的webview渲染，和weex方式的原生渲染，两种渲染引擎可以自己根据需要选。
 **vue文件走的webview渲染**
 **nvue走weex方式的原生渲染**

组件和js写法是一样的，css不一样，原生排版的能用的css必须是flex布局

虽然nvue也可以多端编译，输出H5和小程序，但nvue的css写法受限，所以如果你不开发App，那么不需要使用nvue。

一个**App**中可以同时使用两种页面，比如首页使用nvue，二级页使用vue页面，hello uni-app示例就是如此。

### nvue适用场景

在App端某些vue页面表现不佳的场景下使用 nvue 作为强化补充。这些场景如下：

1. 需要高性能的区域长列表或瀑布流滚动。webview的页面级长列表滚动时没有性能问题的（就是滚动条覆盖webview整体高度），但页面中某个区域做长列表滚动，则需要使用nvue的`list`、`recycle-list`、`waterfall`等组件([详见](https://uniapp.dcloud.io/component/list))。这些组件的性能要高于vue页面里的区域滚动组件`scroll-view`。
2. 复杂高性能的自定义下拉刷新。uni-app的pages.json里可以配置原生下拉刷新，但引擎内置的下拉刷新样式只有雪花和circle圈2种样式。如果你需要自己做复杂的下拉刷新，推荐使用nvue的refresh组件。当然[插件市场](https://ext.dcloud.net.cn/search?q=下拉刷新)里也有很多vue下的自定义下拉刷新插件，只要是基于renderjs或wxs的，性能也可以商用，只是没有nvue的`refresh`组件更极致。
3. 左右拖动的长列表。在webview里，通过`swiper`+`scroll-view`实现左右拖动的长列表，前端模拟下拉刷新，这套方案的性能不好。此时推荐使用nvue，比如新建uni-app项目时的[新闻示例模板](https://ext.dcloud.net.cn/plugin?id=103)，就采用了nvue，切换很流畅。
4. 实现区域滚动长列表+左右拖动列表+吸顶的复杂排版效果，效果可参考hello uni-app模板里的`swiper-list`。[详见](https://ext.dcloud.net.cn/plugin?id=2128)
5. 如需要将软键盘右下角按钮文字改为“发送”，则需要使用nvue。比如聊天场景，除了软键盘右下角的按钮文字处理外，还涉及聊天记录区域长列表滚动，适合nvue来做。
6. 解决前端控件无法覆盖原生控件的层级问题。当你使用`map`、`video`、`live-pusher`等原生组件时，会发现前端写的`view`等组件无法覆盖原生组件，层级问题处理比较麻烦，此时使用nvue更好。或者在vue页面上也可以覆盖一个subnvue（一种非全屏的nvue页面覆盖在webview上），以解决App上的原生控件层级问题。[详见](https://uniapp.dcloud.io/component/native-component)
7. 如深度使用`map`组件，建议使用nvue。除了层级问题，App端nvue文件的map功能更完善，和小程序拉齐度更高，多端一致性更好。
8. 如深度使用`video`，建议使用nvue。比如如下2个场景：video内嵌到swiper中，以实现抖音式视频滑动切换，例子见[插件市场](https://ext.dcloud.net.cn/search?q=仿抖音)；nvue的视频全屏后，通过`cover-view`实现内容覆盖，比如增加文字标题、分享按钮。
9. 直播推流：nvue下有`live-pusher`组件，和小程序对齐，功能更完善，也没有层级问题。
10. 对App启动速度要求极致化。App端v3编译器模式下，如果首页使用nvue且在manifest里配置fast模式，那么App的启动速度可以控制在1秒左右。而使用vue页面的话，App的启动速度一般是3秒起，取决于你的代码性能和体积。



### [nvue开发与vue开发的常见区别](https://uniapp.dcloud.io/use-weex?id=nvue开发与vue开发的常见区别)

基于原生引擎的渲染，虽然还是前端技术栈，但和web开发肯定是有区别的。

1. nvue 页面控制显隐只可以使用`v-if`不可以使用`v-show`
2. nvue 页面只能使用`flex`布局，不支持其他布局方式。页面开发前，首先想清楚这个页面的纵向内容有什么，哪些是要滚动的，然后每个纵向内容的横轴排布有什么，按 flex 布局设计好界面。
3. nvue 页面的布局排列方向默认为竖排（`column`），如需改变布局方向，可以在 `manifest.json` -> `app-plus` -> `nvue` -> `flex-direction` 节点下修改，仅在 uni-app 模式下生效。[详情](https://uniapp.dcloud.io/collocation/manifest?id=nvue)。
4. nvue页面编译为H5、小程序时，会做一件css默认值对齐的工作。因为weex渲染引擎只支持flex，并且默认flex方向是垂直。而H5和小程序端，使用web渲染，默认不是flex，并且设置`display:flex`后，它的flex方向默认是水平而不是垂直的。所以nvue编译为H5、小程序时，会自动把页面默认布局设为flex、方向为垂直。当然开发者手动设置后会覆盖默认设置。
5. 文字内容，必须、只能在`<text>`组件下。不能在`<div>`、`<view>`的`text`区域里直接写文字。否则即使渲染了，也无法绑定js里的变量。
6. 只有`text`标签可以设置字体大小，字体颜色。
7. 布局不能使用百分比、没有媒体查询。
8. nvue 切换横竖屏时可能导致样式出现问题，建议有 nvue 的页面锁定手机方向。
9. 支持的css有限，不过并不影响布局出你需要的界面，`flex`还是非常强大的。详见
10. 不支持背景图。但可以使用`image`组件和层级来实现类似web中的背景效果。因为原生开发本身也没有web这种背景图概念
11. css选择器支持的比较少，只能使用 class 选择器。[详见](https://uniapp.dcloud.io/use-weex?id=样式)
12. nvue 的各组件在安卓端默认是透明的，如果不设置`background-color`，可能会导致出现重影的问题。
13. `class` 进行绑定时只支持数组语法。
14. Android端在一个页面内使用大量圆角边框会造成性能问题，尤其是多个角的样式还不一样的话更耗费性能。应避免这类使用。
15. nvue页面没有`bounce`回弹效果，只有几个列表组件有`bounce`效果，包括 `list`、`recycle-list`、`waterfall`。
16. 原生开发没有页面滚动的概念，页面内容高过屏幕高度并不会自动滚动，只有部分组件可滚动（`list`、`waterfall`、`scroll-view/scroller`），要滚得内容需要套在可滚动组件下。这不符合前端开发的习惯，所以在 nvue 编译为 uni-app模式时，给页面外层自动套了一个 `scroller`，页面内容过高会自动滚动。（组件不会套，页面有`recycle-list`时也不会套）。后续会提供配置，可以设置不自动套。
17. 在 App.vue 中定义的全局js变量不会在 nvue 页面生效。`globalData`和`vuex`是生效的。
18. App.vue 中定义的全局css，对nvue和vue页面同时生效。如果全局css中有些css在nvue下不支持，编译时控制台会报警，建议把这些不支持的css包裹在[条件编译](https://uniapp.dcloud.io/platform)里，`APP-PLUS-NVUE`
19. 不能在 `style` 中引入字体文件，nvue 中字体图标的使用参考：[加载自定义字体](https://uniapp.dcloud.io/use-weex?id=addrule)。如果是本地字体，可以用`plus.io`的API转换路径。
20. 目前不支持在 nvue 页面使用 `typescript/ts`。
21. nvue 页面关闭原生导航栏时，想要模拟状态栏，可以[参考文章](https://ask.dcloud.net.cn/article/35111)。但是，仍然强烈建议在nvue页面使用原生导航栏。nvue的渲染速度再快，也没有原生导航栏快。原生排版引擎解析`json`绘制原生导航栏耗时很少，而解析nvue的js绘制整个页面的耗时要大的多，尤其在新页面进入动画期间，对于复杂页面，没有原生导航栏会在动画期间产生整个屏幕的白屏或闪屏。

### Tips

- `uni-app` 完整支持 `Vue` 实例的生命周期，同时还新增 [应用生命周期](https://uniapp.dcloud.io/frame?id=应用生命周期) 及 [页面生命周期](https://uniapp.dcloud.io/frame?id=页面生命周期)。

- `uni-app` 完整支持 `Vue` 模板语法

- `data` 必须声明为返回一个初始数据对象的函数（注意函数内返回的数据对象不要直接引用函数外的对象）；否则页面关闭时，数据不会自动销毁，再次打开该页面时，会显示上次数据。

  ```
  data() {
      return {
          title: 'Hello'
      }
  }
  ```

## 条件编译

条件编译是用特殊的注释作为标记，在编译时根据这些特殊的注释，将注释里面的代码编译到不同平台。

**写法：**以 #ifdef 或 #ifndef 加 **%PLATFORM%** 开头，以 #endif 结尾。

- \#ifdef：if defined 仅在某平台存在
- \#ifndef：if not defined 除了某平台均存在
- **%PLATFORM%**：平台名称

| #ifdef **APP-PLUS** 需条件编译的代码 #endif              | 仅出现在 App 平台下的代码                                    |
| -------------------------------------------------------- | ------------------------------------------------------------ |
| #ifndef **H5** 需条件编译的代码 #endif                   | 除了 H5 平台，其它平台均存在的代码                           |
| #ifdef **H5** \|\| **MP-WEIXIN** 需条件编译的代码 #endif | 在 H5 平台或微信小程序平台存在的代码（这里只有\|\|，不可能出现&&，因为没有交集） |

**%PLATFORM%** **可取值如下：**

| 值            | 平台           |
| :------------ | :------------- |
| APP-PLUS      | App            |
| APP-PLUS-NVUE | App nvue       |
| H5            | H5             |
| MP-WEIXIN     | 微信小程序     |
| MP-ALIPAY     | 支付宝小程序   |
| MP-BAIDU      | 百度小程序     |
| MP-TOUTIAO    | 字节跳动小程序 |

### pages.json 的条件编译

下面的页面，只有运行至 App 时才会编译进去。

![img](https://img-cdn-qiniu.dcloud.net.cn/uniapp/doc/img/platform-4.png)



# API

## 页面实例

### getApp

`getApp()` 函数用于获取当前应用实例，一般用于获取globalData 。

**实例**

```javascript
const app = getApp()
console.log(app.globalData)
```

**注意：**

- 不要在定义于 `App()` 内的函数中，或调用 `App` 前调用 `getApp()` ，可以通过 `this.$scope` 获取对应的app实例
- 通过 `getApp()` 获取实例之后，不要私自调用生命周期函数。
- v3模式加速了首页`nvue`的启动速度，当在首页`nvue`中使用`getApp()`不一定可以获取真正的`App`对象。对此v3版本提供了`const app = getApp({allowDefault: true})`用来获取原始的`App`对象，可以用来在首页对`globalData`等初始化

### [getCurrentPages()](https://uniapp.dcloud.io/collocation/frame/window?id=getcurrentpages)

`getCurrentPages()` 函数用于获取当前**页面栈的实例**，以数组形式按栈的顺序给出，第一个元素为首页，最后一个元素为当前页面。

**注意：** `getCurrentPages()`仅用于展示页面栈的情况，请勿修改页面栈，以免造成页面状态错误。

每个页面实例的方法属性列表：

| 方法                  | 描述                          | 平台说明 |
| --------------------- | ----------------------------- | -------- |
| page.$getAppWebview() | 获取当前页面的webview对象实例 | App      |
| page.route            | 获取当前页面的路由            |          |

```js
let pages = getCurrentPages()
let nowPage = pages[pages.length - 1]; //当前页页面实例
let prevPage = pages[pages.length - 2]; //上一页页面实例
console.log(prevPage)
prevPage.$vm.cityCul = this.CityCul //给上一个页面实例添加一个data
```

Tips：

- `navigateTo`, `redirectTo` 只能打开非 tabBar 页面。
- `switchTab` 只能打开 `tabBar` 页面。
- `reLaunch` 可以打开任意页面。
- 页面底部的 `tabBar` 由页面决定，即只要是定义为 `tabBar` 的页面，底部都有 `tabBar`。
- 不能在 `App.vue` 里面进行页面跳转。

### [$getAppWebview()](https://uniapp.dcloud.io/collocation/frame/window?id=getappwebview)

`uni-app` 在 `getCurrentPages()`获得的页面里内置了一个方法 `$getAppWebview()` 可以得到当前webview的对象实例，从而实现对 webview 更强大的控制。在 html5Plus 中，plus.webview具有强大的控制能力，可参考：[WebviewObject](http://www.html5plus.org/doc/zh_cn/webview.html#plus.webview.WebviewObject)。

但`uni-app`框架有自己的窗口管理机制，请不要自己创建和销毁webview，如有需求覆盖子窗体上去，请使用[原生子窗体subNvue](https://uniapp.dcloud.io/api/window/subNVues)。

**注意：此方法仅 App 支持**

**示例：**

获取当前页面 webview 的对象实例

```javascript
export default {
  data() {
    return {
      title: 'Hello'
    }
  },
  onLoad() {
    // #ifdef APP-PLUS
    const currentWebview = this.$scope.$getAppWebview(); //此对象相当于html5plus里的plus.webview.currentWebview()。在uni-app里vue页面直接使用plus.webview.currentWebview()无效，非v3编译模式使用this.$mp.page.$getAppWebview()
    currentWebview.setBounce({position:{top:'100px'},changeoffset:{top:'0px'}}); //动态重设bounce效果
    // #endif
  }
}
```

获取指定页面 webview 的对象实例

`getCurrentPages()`可以得到所有页面对象，然后根据数组，可以取指定的页面webview对象

```javascript
var pages = getCurrentPages();
var page = pages[pages.length - 1];
// #ifdef APP-PLUS
var currentWebview = page.$getAppWebview();
console.log(currentWebview.id);//获得当前webview的id
console.log(currentWebview.isVisible());//查询当前webview是否可见
);
// #endif
```
