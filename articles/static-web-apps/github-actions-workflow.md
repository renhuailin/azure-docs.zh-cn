---
title: Azure 静态 Web 应用的 GitHub Actions 工作流
description: 了解如何使用 GitHub 存储库来设置对 Azure 静态 Web 应用的持续部署。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9dc0c8a83279e3a70bceebb316536485e337c873
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729727"
---
# <a name="github-actions-workflows-for-azure-static-web-apps"></a>Azure 静态 Web 应用的 GitHub Actions 工作流

使用 GitHub 部署时，YAML 文件会控制站点的生成工作流。 本文介绍文件的结构和选项。

部署从[触发器](#triggers)启动，该触发器运行由单个[步骤](#steps)组成的[作业](#jobs)。

> [!NOTE]
> Azure Static Web Apps 还支持 Azure DevOps 工作流。 请参阅[使用 Azure DevOps 发布](publish-devops.md)，了解如何设置管道。

## <a name="file-name-and-location"></a>文件名和位置

链接存储库时，Azure Static Web Apps 生成一个控制工作流的文件。

按照以下步骤查看工作流文件。

1. 在 GitHub 上打开应用存储库。
1. 选择“代码”选项卡。
1. 选择“.github/workflows”文件夹。
1. 选择名称类似于 azure-static-web-apps-<RANDOM_NAME>.yml 的文件。

## <a name="triggers"></a>触发器

GitHub Actions [触发器](https://help.github.com/actions/reference/events-that-trigger-workflows)通知 GitHub Actions 工作流运行一个基于特定事件的作业。 使用工作流文件中的 `on` 属性列出触发器。

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

在本例中，当主分支上的拉取请求为如下状态时，将启动工作流：

- 由客户
- 已同步
- 已重新打开
- 已关闭

可以自定义工作流的此部分，以针对不同的分支或不同的事件。

## <a name="jobs"></a>作业

每个触发器定义一系列要运行的[作业](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs)以响应事件。

| 名称 | 说明 |
| --- | --- |
| `build_and_deploy_job` | 当对 `on` 属性中列出的分支推送提交或打开拉取请求时执行。          |
| `close_pull_request_job` | 仅在关闭拉取请求时执行，这会删除从拉取请求创建的过渡环境。 |

## <a name="steps"></a>步骤

步骤是作业的顺序任务。 一个步骤会执行一些操作，例如，安装依赖项、运行测试，以及将应用程序部署到生产环境。

| 作业 | 步骤 |
| --- | --- |
| `build_and_deploy_job` | <li>签出 GitHub Action 环境中的存储库。<li>生成存储库并将其部署到 Azure 静态 Web 应用。 |
| `close_pull_request_job` | <li>通知 Azure 静态 Web 应用拉取请求已关闭。 |

## <a name="build-and-deploy"></a>生成并部署

名为 `build_and_deploy_job` 的步骤生成并部署到 Azure Static Web Apps 站点。 在 `with` 部分下，可以为部署自定义以下值。

下面的示例展示这些值在工作流文件中的显示方式。

```yml
...
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

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

请勿更改 `repo_token`、`action` 和 `azure_static_web_apps_api_token` 的值，因为它们是 Azure Static Web Apps 为你设置的。

## <a name="custom-build-commands"></a>自定义生成命令

可以精细控制在应用或 API 生成过程中运行的命令。 以下命令显示在作业的 `with` 部分下。

| Command | 说明 |
| --- |--- |
| `app_build_command` | 定义用于生成静态内容应用程序的自定义命令。<br><br>例如，若要为 Angular 应用程序配置生产版本，请创建一个名为 `build-prod` 的 npm 脚本，以运行 `ng build --prod` 并输入 `npm run build-prod` 作为自定义命令。 如果留空，工作流将尝试运行 `npm run build` 或 `npm run build:azure` 命令。 |
| `api_build_command` | 定义用于生成 Azure Functions API 应用程序的自定义命令。 |

下面的示例演示如何在作业的 `with` 部分中定义自定义生成命令。

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  ###### End of Repository/Build Configurations ######
```

> [!NOTE]
> 目前，只能为 Node.js 生成定义自定义生成命令。 生成过程在任何自定义命令之前始终调用 `npm install`。

## <a name="skip-building-front-end-app"></a>跳过前端应用的生成

如果需要完全控制前端应用的生成，可以将自定义步骤添加到工作流。 例如，可以选择绕过自动生成并部署在上一步中生成的应用。

若要跳过前端应用的生成，请将 `skip_app_build` 设置为 `true`，并将 `app_location` 设置为要部署的文件夹的位置。

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| 属性         | 说明                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | 将值设置为 `true` 以跳过生成前端应用。 |

> [!NOTE]
> 只能跳过前端应用的生成。 API 始终生成（如果存在）。

## <a name="environment-variables"></a>环境变量

可以通过某个作业的配置的 `env` 节为生成设置环境变量。

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
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>单存储库支持

单存储库是包含多个应用程序的代码的存储库。 默认情况下，工作流会跟踪存储库中的所有文件，但你可以调整配置以针对单个应用。

设置单存储库时，每个静态应用都有自己的配置文件，其范围仅限于单个应用中的文件。 不同工作流文件并排放置在存储库的“.github/workflows”文件夹中。

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

若要将工作流文件定位到单个应用，请在 `push` 和 `pull_request` 节中指定路径。

以下示例演示如何将 `paths` 节点添加到 azure-static-web-apps-purple-pond.yml 文件的 `push` 和 `pull_request` 节中。

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

在这种情况下，只有对以下文件进行更改才会触发新生成：

- app1 文件夹中的任何文件
- api1 文件夹中的任何文件
- 更改应用的 azure-static-web-apps-purple-pond.yml 工作流文件

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看预生产环境中的拉取请求](review-publish-pull-requests.md)
