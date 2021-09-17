---
title: 使用 Azure Functions 将 API 添加到 Azure 静态 Web 应用
description: 通过使用 Azure Functions 将无服务器 API 添加到静态 Web 应用，开始使用 Azure 静态 Web 应用。
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/14/2021
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 56c8044f1ab36015accb3f951469ee0a0579ca23
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608638"
---
# <a name="add-an-api-to-azure-static-web-apps-with-azure-functions"></a>使用 Azure Functions 将 API 添加到 Azure 静态 Web 应用

可以将无服务器 API 添加到由 Azure Functions 提供支持的 Azure Static Web Apps。 本文演示如何将 API 添加和部署到 Azure 静态 Web 应用站点。

> [!NOTE]
> 默认情况下，Static Web Apps 中提供的函数是预先配置的，以提供安全 API 终结点，并且仅支持 HTTP 触发的函数。 请参阅[通过 Azure Functions 提供 API 支持](apis.md)，了解它们与独立 Azure Functions 应用的不同之处。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。
  - 如果没有帐户，可以[免费创建一个](https://azure.microsoft.com/free)。
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code 的 [Azure Static Web Apps 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- 用于运行前端应用程序和 API 的 [Node.js](https://nodejs.org/download/)

## <a name="create-the-static-web-app"></a>创建静态 Web 应用

添加 API 之前，请创建前端应用程序并将其部署到 Azure Static Web Apps。 使用已部署的现有应用，或遵照[使用 Azure Static Web Apps 构建第一个静态站点](getting-started.md)快速入门来创建一个。

在 Visual Studio Code 中，打开应用存储库的根目录。 文件夹结构包含前端应用程序的源和“.github/workflows”文件夹中的 Static Web Apps GitHub 工作流。

```Files
├── .github
│   └── workflows
│       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
│
└── (folders and files from your static web app)
```

## <a name="create-the-api"></a>创建 API

为静态 Web 应用的 API 创建 Azure Functions 项目。 默认情况下，Static Web Apps Visual Studio Code 扩展会在存储库根目录中名为“api”的文件夹中创建项目。

1. 按 <kbd>F1</kbd> 打开命令面板。

1. 选择“Azure Static Web Apps：创建 HTTP 函数...”。如果系统提示安装 Azure Functions 扩展，请安装并重新运行此命令。

1. 出现提示时，输入以下值：

    | Prompt | 值 |
    | --- | --- |
    | 选择一种语言 | **JavaScript** |
    | 提供函数名称 | **message** |

    使用 HTTP 触发的函数生成一个 Azure Functions 项目。 应用的项目结构现在类似于以下示例。

    ```Files
    ├── .github
    │   └── workflows
    │       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
    │
    ├── api
    │   ├── message
    │   │   ├── function.json
    │   │   └── index.js
    │   ├── host.json
    │   ├── local.settings.json
    │   └── package.json
    │
    └── (folders and files from your static web app)
    ```

1. 接下来，更改 `message` 函数，以将消息返回到前端。 使用以下代码更新“api/message/index.js”中的函数。

    ```javascript
    module.exports = async function (context, req) {
        context.res.json({
            text: "Hello from the API"
        });
    };
    ```

> [!TIP]
> 可以通过再次运行“Azure Static Web Apps：创建 HTTP 函数...”命令来添加更多 API 函数。

## <a name="update-the-frontend-app-to-call-the-api"></a>更新前端应用程序以调用 API

更新前端应用程序以调用 `/api/message` 下的 API，并显示响应消息。

如果使用快速入门创建了应用，请使用以下说明来应用更新。

# <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

使用以下代码更新 src/index.html 文件的内容，以便从 API 函数中提取文本并将其显示在屏幕上。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Vanilla JavaScript App</title>
</head>

<body>
    <main>
    <h1>Vanilla JavaScript App</h1>
    <p>Loading content from the API: <b id="name">...</b></p>
    </main>

    <script>
    (async function() {
        const { text } = await( await fetch(`/api/message`)).json();
        document.querySelector('#name').textContent = text;
    }())
    </script>
</body>

</html>
```

# <a name="angular"></a>[Angular](#tab/angular)

1. 使用以下代码更新“src/app/app.module.ts”的内容，以便在应用中启用 `HttpClient`。

    ```typescript
    import { BrowserModule } from "@angular/platform-browser";
    import { NgModule } from "@angular/core";
    import { HttpClientModule } from '@angular/common/http';
    
    import { AppComponent } from "./app.component";
    
    @NgModule({
      declarations: [AppComponent],
      imports: [BrowserModule, HttpClientModule],
      bootstrap: [AppComponent]
    })
    export class AppModule {}
    ```

1. 使用以下代码更新“src/app/app.component.ts”的内容，以便从 API 函数中提取文本并将其显示在屏幕上。

    ```typescript
    import { HttpClient } from '@angular/common/http';
    import { Component } from '@angular/core';
    
    @Component({
      selector: 'app-root',
      template: `<div>{{message}}</div>`,
    })
    export class AppComponent {
      message = '';
    
      constructor(private http: HttpClient) {
        this.http.get('/api/message')
          .subscribe((resp: any) => this.message = resp.text);
      }
    }
    ```

# <a name="react"></a>[React](#tab/react)

使用以下代码更新“src/App.js”的内容，以便从 API 函数中提取文本并将其显示在屏幕上。

```javascript
import React, { useState, useEffect } from 'react';

function App() {
  const [data, setData] = useState('');

  useEffect(() => {
    (async function () {
      const { text } = await( await fetch(`/api/message`)).json();
      setData(text);
    })();
  });

  return <div>{data}</div>;
}

export default App;
```

# <a name="vue"></a>[Vue](#tab/vue)

使用以下代码更新“src/App.vue”的内容，以便从 API 函数中提取文本并将其显示在屏幕上。

```javascript
<template>
  <div>{{ message }}</div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      message: ""
    };
  },
  async mounted() {
    const { text } = await (await fetch("/api/message")).json();
    this.message = text;
  }
};
</script>
```

---

## <a name="run-the-frontend-and-api-locally"></a>在本地运行前端和 API

若要在本地同时运行前端应用程序和 API，Azure Static Web Apps提供了模拟云环境的 CLI。 CLI 利用 Azure Functions Core Tools 来运行 API。

### <a name="install-command-line-tools"></a>安装命令行工具

确保已安装了必要的命令行工具。

1. 安装 Azure Static Web Apps CLI。
    ```bash
    npm install -g @azure/static-web-apps-cli
    ```

1. 安装 Azure Functions Core Tools V3。
    ```bash
    npm install -g azure-functions-core-tools@3
    ```

### <a name="build-frontend-app"></a>构建前端应用程序

如果应用使用框架，请构建应用以在运行 Static Web Apps CLI 之前生成输出。

# <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

无需构建应用。

# <a name="angular"></a>[Angular](#tab/angular)

将应用构建到“dist/angular-basic”文件夹中。

```bash
npm run build --prod
```

# <a name="react"></a>[React](#tab/react)

将应用构建到“build”文件夹中。

```bash
npm run build
```

# <a name="vue"></a>[Vue](#tab/vue)

将应用构建到“dist”文件夹中。

```bash
npm run build
```

---

### <a name="start-the-cli"></a>启动 CLI

通过使用 Static Web Apps CLI 启动应用来同时运行前端应用程序和 API。 通过这种方式运行应用程序的两个部分，CLI 可从文件夹提供前端的构建输出，使正在运行的应用可以访问 API。

1. 在存储库的根目录下，使用 `start` 命令来启动 Static Web Apps CLI。 如果应用具有不同的文件夹结构，请调整参数。

    # <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

    将当前文件夹 (`.`) 和 API 文件夹 (`api`) 传递到 CLI。
     
    ```bash
    swa start . --api api
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    将构建输出文件夹 (`dist/angular-basic`) 和 API 文件夹 (`api`) 传递到 CLI。

    ```bash
    swa start dist/angular-basic --api api
    ```

    # <a name="react"></a>[React](#tab/react)

    将构建输出文件夹 (`build`) 和 API 文件夹 (`api`) 传递到 CLI。

    ```bash
    swa start build --api api
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    将构建输出文件夹 (`dist`) 和 API 文件夹 (`api`) 传递到 CLI。

    ```bash
    swa start dist --api api
    ```

    ---

1. 当 CLI 进程启动时，在 `http://localhost:4280/` 上访问应用。 请注意页面如何调用 API 并显示其输出，`Hello from the API`。

1. 若要停止 CLI，请按 <kbd>Ctrl-C</kbd>。

## <a name="add-api-location-to-workflow"></a>将 API 位置添加到工作流

在将应用部署到 Azure 之前，请用 API 文件夹的正确位置更新存储库的 GitHub Actions 工作流。

1. 在“.github/workflows/azure-static-web-apps-\<DEFAULT-HOSTNAME>.yml”中打开工作流。

1. 搜索属性 `api_location`，并将值设置为 `api`。
1. 保存该文件。

## <a name="deploy-changes"></a>部署更改

若要将更改发布到 Azure 中的静态 Web 应用，请提交代码并将其推送到远程 GitHub 存储库。

1. 按 <kbd>F1</kbd> 打开命令面板。

1. 选择“Git: Commit All”命令。

1. 当系统提示提交消息时，输入“添加 API”，并将所有更改提交到本地 git 存储库。

1. 按 <kbd>F1</kbd> 打开命令面板。

1. 选择“Git: push”命令。

    更改推送到 GitHub 中的远程存储库，触发 Static Web Apps GitHub 操作工作流构建和部署应用。

1. 打开 GitHub 中的存储库，以监视工作流运行的状态。

1. 工作流运行完成后，访问静态 Web 应用以查看更改。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置应用设置](./application-settings.md)
