---
title: 为 Azure Static Web Apps 生成配置
description: 了解如何控制 Azure Static Web Apps 的生成过程。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 54b6231c98ce244913062010782591493ea45a5e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837730"
---
# <a name="build-configuration-for-azure-static-web-apps"></a>为 Azure Static Web Apps 生成配置

Azure Static Web Apps 生成配置由 Azure Pipelines 或 GitHub Actions 提供支持。  每个站点都有一个 YAML 配置文件，用于控制如何生成和部署站点。 本文介绍文件的结构和选项。

下表列出了可用配置设置。

| 属性 | 说明 | 必需 |
|---|---|---|
| `app_location` | 此文件夹包含前端应用程序的源代码。 此值是相对于 GitHub 中的存储库根目录和 Azure DevOps 中的当前工作文件夹的值。 | 是 |
| `api_location` | 此文件夹包含 API 应用程序的源代码。 此值是相对于 GitHub 中的存储库根目录和 Azure DevOps 中的当前工作文件夹的值。 | 否 |
| `output_location` | 如果 Web 应用运行生成步骤，则输出位置是生成公共文件的文件夹。 对于大多数项目，`output_location` 是相对于 `app_location` 的。 但是，对于 .NET 项目，该位置是相对于发布输出文件夹的。 | 否 |
| `app_build_command` | 对于 Node.js 应用程序，可以定义用于生成静态内容应用程序的自定义命令。<br><br>例如，若要为 Angular 应用程序配置生产版本，请创建一个名为 `build-prod` 的 npm 脚本，以运行 `ng build --prod` 并输入 `npm run build-prod` 作为自定义命令。 如果留空，工作流将尝试运行 `npm run build` 或 `npm run build:azure` 命令。 | 否 |
| `api_build_command` | 对于 Node.js 应用程序，可以定义用于生成 Azure Functions API 应用程序的自定义命令。 | 否 |
| `skip_app_build` | 将值设置为 `true` 以跳过生成前端应用。 | 否 |

使用这些设置，可以设置 GitHub Actions 或 [Azure Pipelines](publish-devops.md)，为静态 Web 应用运行持续集成/持续交付 (CI/CD)。

> [!NOTE]
> 目前，只能为 Node.js 生成定义 `app_build_command` 和 `api_build_command`。

## <a name="file-name-and-location"></a>文件名和位置

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

配置文件由 GitHub 生成，存储在 .github/workflows 文件夹中，采用以下格式命名：`azure-static-web-apps-<RANDOM_NAME>.yml`。

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

默认情况下，配置文件存储在存储库的根目录下，名称为 `azure-pipelines.yml`。

---

## <a name="build-configuration"></a>生成配置

以下示例配置监视存储库中的更改。 将提交推送到 `main` 分支时，会从 `app_location` 文件夹生成应用程序，并会将 `output_location` 中的文件提供给公共 Web。 此外，api 文件夹中的应用程序在站点的 `api` 路径下可用。

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

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
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations ######
          app_location: "src" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          action: "close"
```

在此配置中：

- 将会监视 `main` 分支中的提交。
- 当 `main` 分支上的拉取请求处于“已打开”、“已同步”、“已重新打开”或“已关闭”状态时，将会[触发](https://help.github.com/actions/reference/events-that-trigger-workflows) GitHub Actions 工作流。
- 针对 `on` 属性中列出的分支推送提交或打开拉取请求时，`build_and_deploy_job` 会执行。
- `app_location` 指向包含 Web 应用源文件的 `src` 文件夹。
- `api_location` 指向包含站点 API 终结点的 Azure Functions 应用程序的 `api` 文件夹。
- `output_location` 指向包含应用源文件的最终版本的 `public` 文件夹。

请勿更改 `repo_token`、`action` 和 `azure_static_web_apps_api_token` 的值，因为它们是 Azure Static Web Apps 为你设置的。

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - checkout: self
    submodules: true
  - task: AzureStaticWebApp@0
    inputs:
      app_location: 'src' # App source code path
      api_location: 'api' # Api source code path - optional
      output_location: 'public' # Built app content directory - optional
      azure_static_web_apps_api_token: $(deployment_token)
```

在此配置中：

- 将会监视 `main` 分支中的提交。
- `app_location` 指向包含 Web 应用源文件的 `src` 文件夹。
- `api_location` 指向包含站点 API 终结点的 Azure Functions 应用程序的 `api` 文件夹。
- `output_location` 指向包含应用源文件的最终版本的 `public` 文件夹。
- `$(deployment_token)` 变量指向[生成的 Azure DevOps 部署令牌](./publish-devops.md)。

---

## <a name="custom-build-commands"></a>自定义生成命令

对于 Node.js 应用程序，可以精细控制在应用或 API 生成过程中运行的命令。 下面的示例演示如何使用 `app_build_command` 和 `api_build_command` 的值定义生成。

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: '/'
  api_location: 'api'
  output_location: 'dist'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
```
# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
...

inputs:
  app_location: 'src'
  api_location: 'api'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  output_location: 'public'
  azure_static_web_apps_api_token: $(deployment_token)
```

---

## <a name="skip-building-front-end-app"></a>跳过前端应用的生成

如果需要完全控制前端应用的生成，可以绕过自动生成，部署在前面的某个步骤生成的应用。

若要跳过生成前端应用，请执行以下操作：

- 将 `app_location` 设置为要部署的文件的位置。
- 将 `skip_app_build` 设置为 `true`。
- 将 `output_location` 设置为空字符串 (`''`)。

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src/dist'
  api_location: 'api'
  output_location: ''
  skip_app_build: true
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yml
...

inputs:
  app_location: 'src/dist'
  api_location: 'api'
  output_location: '' # Leave this empty
  skip_app_build: true
  azure_static_web_apps_api_token: $(deployment_token)
```

---

> [!NOTE]
> 只能跳过前端应用的生成。 API 始终生成（如果存在）。

## <a name="environment-variables"></a>环境变量

可以通过某个作业的配置的 `env` 节为生成设置环境变量。

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

```yaml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src'
  api_location: 'api'
  output_location: 'public'

env: # Add environment variables here
  HUGO_VERSION: 0.58.0
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Azure DevOps 目前不支持环境变量。

---

## <a name="monorepo-support"></a>单存储库支持

单存储库是包含多个应用程序的代码的存储库。 默认情况下，工作流会跟踪存储库中的所有文件，但你可以调整配置以针对单个应用。

若要将工作流文件定位到单个应用，请在 `push` 和 `pull_request` 节中指定路径。

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

设置单存储库时，每个静态应用配置的范围局限于单个应用的文件。 不同工作流文件并排放置在存储库的“.github/workflows”文件夹中。

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

在此示例中，只有对以下文件进行更改才会触发新生成：

- app1 文件夹中的任何文件
- api1 文件夹中的任何文件
- 更改应用的 azure-static-web-apps-purple-pond.yml 工作流文件

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

若要在单个存储库中支持多个应用程序，请创建一个单独的工作流文件，并将其与不同的 Azure Pipelines 相关联。

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看预生产环境中的拉取请求](review-publish-pull-requests.md)
