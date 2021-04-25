---
title: Azure 静态 Web 应用的 GitHub Actions 工作流
description: 了解如何使用 GitHub 存储库来设置对 Azure 静态 Web 应用的持续部署。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 4f1f432da33bded4fc0f04170673e5943dec5fb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311322"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Azure 静态 Web 应用的 GitHub Actions 工作流预览版

当你创建新的 Azure 静态 Web 应用资源时，Azure 会生成一个 GitHub Actions 工作流来控制应用的持续部署。 该工作流由 YAML 文件驱动。 本文详细介绍该工作流文件的结构和选项。

部署由[触发器](#triggers)启动，该触发器运行由单个[步骤](#steps)定义的[作业](#jobs)。

## <a name="file-location"></a>文件位置

将 GitHub 存储库链接到 Azure Static Web Apps 时，会将工作流文件添加到存储库。

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

在本例中，当主分支发生更改时，将启动工作流。 启动工作流的更改包括对所选的分支推送提交和打开拉取请求。

## <a name="jobs"></a>作业

每个事件触发器都需要一个事件处理程序。 [作业](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs)定义触发事件时会发生的情况。

在静态 Web 应用工作流文件中，有两个可用的作业。

| 名称                     | 说明                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | 当对 `on` 属性中列出的分支推送提交或打开拉取请求时执行。          |
| `close_pull_request_job` | 仅在关闭拉取请求时执行，这会删除从拉取请求创建的过渡环境。 |

## <a name="steps"></a>步骤

步骤是作业的顺序任务。 一个步骤会执行一些操作，例如，安装依赖项、运行测试，以及将应用程序部署到生产环境。

工作流文件定义以下步骤。

| 作业                      | 步骤                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>签出操作环境中的存储库。<li>生成存储库并将其部署到 Azure 静态 Web 应用。</ol> |
| `close_pull_request_job` | <ol><li>通知 Azure 静态 Web 应用拉取请求已关闭。</ol>                                                        |

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

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

Azure 静态 Web 应用为你设置的 `repo_token`、`action` 和 `azure_static_web_apps_api_token` 值不应手动更改。

## <a name="custom-build-commands"></a>自定义生成命令

你可以对部署过程中运行的命令进行精细控制。 可以在作业的 `with` 部分下定义以下命令。

部署在任何自定义命令之前始终调用 `npm install`。

| Command             | 说明                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | 定义要在静态内容应用程序部署过程中运行的自定义命令。<br><br>例如，若要为 Angular 应用程序配置生产版本，请创建一个名为 `build-prod` 的 npm 脚本，以运行 `ng build --prod` 并输入 `npm run build-prod` 作为自定义命令。 如果留空，工作流将尝试运行 `npm run build` 或 `npm run build:azure` 命令。 |
| `api_build_command` | 定义要在 Azure Functions API 应用程序部署过程中运行的自定义命令。                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>跳过应用生成

如果需要完全控制前端应用程序的生成方式，可以在工作流中添加自定义生成步骤。 然后，你可以将 Static Web Apps 操作配置为绕过自动生成过程，并且仅部署在上一步生成的应用。

若要跳过生成应用，请将 `skip_app_build` 设置为 `true`，并将 `app_location` 设置为要部署的文件夹的位置。

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
> 只能跳过前端应用的生成。 如果应用具有 API，则仍会由 Static Web Apps GitHub 操作生成。

## <a name="route-file-location"></a>路由文件位置

你可以自定义工作流，以在存储库的任何文件夹中查找 [staticwebapp.config.json](routes.md)。 可以在作业的 `with` 部分下定义以下属性。

| properties          | 说明                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | 定义找到 _staticwebapp.config.json_ 文件的目录位置。 此位置相对于存储库的根目录。 |

默认情况下，如果前端框架生成步骤不会将此文件移到 `output_location`，则显式定义 _staticwebapp.config.json_ 文件的位置尤为重要。

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
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
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

单存储库是包含多个应用程序的代码的存储库。 默认情况下，Static Web Apps 工作流文件会跟踪存储库中的所有文件，但你可以将其调整为针对单个应用。 因此，对于单存储库，每个静态应用都有其自己的配置文件，这些文件并排放置在存储库的“.github/workflows”文件夹中。

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
