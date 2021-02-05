---
title: Azure 静态 Web 应用的 GitHub Actions 工作流
description: 了解如何使用 GitHub 存储库来设置对 Azure 静态 Web 应用的持续部署。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: cshoe
ms.openlocfilehash: 785fd535c46b67cfd631cd18560f396a6901e5c0
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593938"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Azure 静态 Web 应用的 GitHub Actions 工作流预览版

当你创建新的 Azure 静态 Web 应用资源时，Azure 会生成一个 GitHub Actions 工作流来控制应用的持续部署。 该工作流由 YAML 文件驱动。 本文详细介绍该工作流文件的结构和选项。

部署由[触发器](#triggers)启动，该触发器运行由单个[步骤](#steps)定义的[作业](#jobs)。

## <a name="file-location"></a>文件位置

将 GitHub 存储库链接到 Azure 静态 Web 应用时，会将工作流文件添加到存储库。

按照以下步骤查看生成的工作流文件。

1. 在 GitHub 上打开应用存储库。
1. 在“代码”选项卡上，单击 `.github/workflows` 文件夹。
1. 单击名称类似于 `azure-static-web-apps-<RANDOM_NAME>.yml` 的文件。

存储库中的 YAML 文件将类似于以下示例：

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v2
      with:
        submodules: true
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        output_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>触发器

GitHub Actions [触发器](https://help.github.com/actions/reference/events-that-trigger-workflows)通知 GitHub Actions 工作流运行一个基于事件触发器的作业。 使用工作流文件中的 `on` 属性列出触发器。

```yml
on:
  push:
    branches:
    - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - main
```

通过与 `on` 属性关联的设置，可以定义触发作业的分支，并为不同的拉取请求状态设置触发器。

在此示例中，工作流在 _主_ 分支发生更改的情况下启动。 启动工作流的更改包括对所选的分支推送提交和打开拉取请求。

## <a name="jobs"></a>作业

每个事件触发器都需要一个事件处理程序。 [作业](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs)定义触发事件时会发生的情况。

在静态 Web 应用工作流文件中，有两个可用的作业。

| 名称  | 说明 |
|---------|---------|
|`build_and_deploy_job` | 当对 `on` 属性中列出的分支推送提交或打开拉取请求时执行。 |
|`close_pull_request_job` | 仅在关闭拉取请求时执行，此请求将删除从拉取请求创建的过渡环境。 |

## <a name="steps"></a>步骤

步骤是作业的顺序任务。 一个步骤执行一些操作，如安装依赖项、运行测试，以及将应用程序部署到生产环境。

工作流文件定义以下步骤。

| 作业  | 步骤  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>签出操作环境中的存储库。<li>生成存储库并将其部署到 Azure 静态 Web 应用。</ol>|
| `close_pull_request_job` | <ol><li>通知 Azure 静态 Web 应用拉取请求已关闭。</ol>|

## <a name="build-and-deploy"></a>生成并部署

生成名为 `Build and Deploy` 的步骤并部署到 Azure 静态 Web 应用实例。 在 `with` 部分下，可以为部署自定义以下值。

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    output_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| properties | 说明 | 必选 |
|---|---|---|
| `app_location` | 应用程序代码的位置。<br><br>例如，如果应用程序源代码位于存储库的根目录中，输入 `/`；如果应用程序代码位于名为 `app` 的目录中，则输入 `/app`。 | 是 |
| `api_location` | Azure Functions 代码的位置。<br><br>例如，如果应用代码位于名为 `api` 的文件夹中，输入 `/api`。 如果未在文件夹中检测到 Azure Functions 的应用，则生成不会失败，工作流假定你不需要 API。 | 否 |
| `output_location` | 与 `app_location` 相对应的生成输出目录的位置。<br><br>例如，如果应用程序源代码位于 `/app` 中，并且生成脚本将文件输出到 `/app/build` 文件夹，则将 `build` 设置为 `output_location` 值。 | 否 |

Azure 静态 Web 应用为你设置的 `repo_token`、`action` 和 `azure_static_web_apps_api_token` 值不应手动更改。

## <a name="custom-build-commands"></a>自定义生成命令

你可以对部署过程中运行的命令进行精细控制。 可以在作业的 `with` 部分下定义以下命令。

部署在任何自定义命令之前始终调用 `npm install`。

| Command            | 说明 |
|---------------------|-------------|
| `app_build_command` | 定义要在静态内容应用程序部署过程中运行的自定义命令。<br><br>例如，若要为角度应用程序配置生产版本，请创建一个名为的 npm 脚本 `build-prod` 以运行 `ng build --prod` ，并输入 `npm run build-prod` 作为自定义命令。 如果留空，工作流将尝试运行 `npm run build` 或 `npm run build:Azure` 命令。  |
| `api_build_command` | 定义要在 Azure Functions API 应用程序部署过程中运行的自定义命令。 |

## <a name="route-file-location"></a>路由文件位置

你可以自定义工作流，以在存储库的任何文件夹中查找 [routes.json](routes.md)。 可以在作业的 `with` 部分下定义以下属性。

| properties            | 说明 |
|---------------------|-------------|
| `routes_location` | 定义找到 routes.json 文件的目录位置。 此位置相对于存储库的根目录。 |

 如果你的前端框架生成步骤不会在默认情况下将此文件移到 `output_location`，则显式了解 routes.json 文件的位置尤为重要。

## <a name="environment-variables"></a>环境变量

可以通过作业配置的部分来设置生成的环境变量 `env` 。

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: "upload"
          ###### Repository/Build Configurations
          app_location: "/"
          api_location: "api"
          output_location: "public"
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Monorepo 支持

Monorepo 是包含多个应用程序的代码的存储库。 默认情况下，静态 Web 应用工作流文件跟踪存储库中的所有文件，但你可以将其调整为面向单个应用。 因此，对于 monorepos，每个静态应用程序都有自己的配置文件，该文件在存储库的 *github/工作流* 文件夹中并行存在。

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

若要将工作流文件定位于单个应用，请在和部分中指定路径 `push` `pull_request` 。

下面的示例演示如何将节点添加 `paths` 到 `push` `pull_request` 名为 _azure-static-web-apps-purple-pond_ 的文件的和部分。

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

在此实例中，只有对以下文件所做的更改才会触发新生成：

- *App1* 文件夹中的所有文件
- *Api1* 文件夹中的所有文件
- 对应用程序的 *azure-static-web-apps-purple-pond. docker-compose.override.yml* 工作流文件的更改

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看预生产环境中的拉取请求](review-publish-pull-requests.md)
