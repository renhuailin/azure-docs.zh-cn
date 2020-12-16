---
title: GitHub 操作中的自定义容器 CI/CD
description: 了解如何使用 GitHub 操作通过 CI/CD 管道将自定义 Linux 容器部署到应用服务。
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 4f5deb33218c336da7a477b4f39cd45f7386debf
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97604968"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>使用 GitHub Actions 将自定义容器部署到应用服务

[GitHub 操作](https://docs.github.com/en/free-pro-team@latest/actions) 使你可以灵活地生成自动化软件开发工作流。 使用 [Azure Web 部署操作](https://github.com/Azure/webapps-deploy)，可以使用 GitHub 操作自动化工作流，将自定义容器部署到 [应用服务](overview.md) 。

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含工作流中的各个步骤和参数。

对于 Azure App Service 容器工作流，文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 检索服务主体或发布配置文件。 <br /> 2.创建 GitHub 机密。 |
|**生成** | 1. 创建环境。 <br /> 2. 构建容器映像。 |
|**部署** | 1. 部署容器映像。 |

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 一个 GitHub 帐户。 如果没有该帐户，请注册[免费版](https://github.com/join)。 需要将 GitHub 存储库中的代码部署到 Azure App Service。 
- 容器的工作容器注册表和 Azure App Service 应用。 此示例使用 Azure 容器注册表。 请确保完成对容器 Azure App Service 的完整部署。 与常规 web 应用不同，用于容器的 web 应用没有默认登录页。 发布容器以获得工作示例。
    - [了解如何使用 Docker 创建容器化的 Node.js 应用程序，将容器映像推送到注册表，然后将该映像部署到 Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>生成部署凭据

使用 GitHub 操作 Azure 应用服务进行身份验证的建议方法是使用发布配置文件。 也可以使用服务主体进行身份验证，但该过程需要更多步骤。 

将发布配置文件凭据或服务主体另存为 [GitHub 机密](https://docs.github.com/en/free-pro-team@latest/actions/reference/encrypted-secrets)，以便使用 Azure 进行身份验证。 你将在工作流中访问机密。 

# <a name="publish-profile"></a>[发布配置文件](#tab/publish-profile)

发布配置文件是应用级凭据。 将发布配置文件设置为 GitHub 机密。 

1. 在 Azure 门户中，请参阅应用服务。 

1. 在 " **概述** " 页上，选择 " **获取发布配置文件**"。

    > [!NOTE]
    > 从2020年10月起，Linux web 应用在 `WEBSITE_WEBDEPLOY_USE_SCM` `true` **下载文件之前** 需要将应用设置设置为。 此要求将在将来删除。 若要了解如何配置常见的 web 应用设置，请参阅 [在 Azure 门户中配置应用服务应用](/azure/app-service/configure-common)。  

1. 保存下载的文件。 你将使用该文件的内容来创建 GitHub 机密。

# <a name="service-principal"></a>[服务主体](#tab/service-principal)

可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 请使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

在此示例中，将占位符替换为你的订阅 ID、资源组名称和应用名称。 输出是一个 JSON 对象，其中包含用于提供对应用服务应用的访问权限的角色分配凭据。 复制此 JSON 对象供以后使用。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 始终应授予最小访问权限。 上一示例中的范围限制为特定的应用服务应用，而不是整个资源组。

---
## <a name="configure-the-github-secret-for-authentication"></a>配置用于身份验证的 GitHub 机密

# <a name="publish-profile"></a>[发布配置文件](#tab/publish-profile)

在 [GitHub](https://github.com/) 中，浏览存储库，选择“设置”>“机密”>“添加新机密”。

若要使用 [应用级凭据](#generate-deployment-credentials)，请将下载的发布配置文件的内容粘贴到机密的值字段中。 命名机密 `AZURE_WEBAPP_PUBLISH_PROFILE` 。

配置 GitHub 工作流时，请 `AZURE_WEBAPP_PUBLISH_PROFILE` 在部署 Azure Web 应用操作中使用。 例如：
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[服务主体](#tab/service-principal)

在 [GitHub](https://github.com/) 中，浏览存储库，选择“设置”>“机密”>“添加新机密”。

要使用 [用户级凭据](#generate-deployment-credentials)，请将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称，如 `AZURE_CREDENTIALS` 。

以后配置工作流文件时，请使用该机密作为 Azure 登录操作的输入 `creds`。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>为注册表配置 GitHub 机密

定义要用于 Docker 登录操作的机密。 本文档中的示例使用容器注册表的 Azure 容器注册表。 

1. 在 Azure 门户或 Docker 中转到容器，复制用户名和密码。 你可以在注册表的 "**设置**" "  >  **访问密钥**" 下的 Azure 门户中找到 Azure 容器注册表用户名和密码。 

2. 为名为的注册表用户名定义新机密 `REGISTRY_USERNAME` 。 

3. 为名为的注册表密码定义新的密码 `REGISTRY_PASSWORD` 。 

## <a name="build-the-container-image"></a>构建容器映像

下面的示例演示生成 Node.JS Docker 映像的工作流的一部分。 使用 [Docker 登录名](https://github.com/azure/docker-login) 登录到专用容器注册表。 此示例使用 Azure 容器注册表，但相同的操作适用于其他注册表。 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

你还可以使用 [Docker 登录名](https://github.com/azure/docker-login) 同时登录多个容器注册表。 此示例包括两个新的 GitHub 机密，用 docker.io 进行身份验证。 该示例假设注册表的根级别上存在 Dockerfile。 

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>部署到应用服务容器

若要将映像部署到应用服务中的自定义容器，请使用 `azure/webapps-deploy@v2` 操作。 此操作有七个参数：

| **参数**  | **解释**  |
|---------|---------|
| **app-name** | （必需）应用服务应用的名称 | 
| **publish-profile** |  (可选) 适用于 (Windows 和 Linux 的 Web 应用) 和 (Linux) 的 Web 应用容器。 不支持多容器方案。 发布配置文件 (\*) 带有 Web 部署机密的文件内容 | 
| **slot-name** | （可选）输入生产槽以外的现有槽 |
| **package** |  (可选) 仅适用于 Web 应用：包或文件夹的路径。 \*.zip、 \* war、 \* .jar 或要部署的文件夹 |
| **images** |  (必需) 仅适用于 Web 应用容器：指定完全限定的容器映像 () 名称。 例如，"myregistry.azurecr.io/nginx:latest" 或 "python： 3.7.2-alpine/"。 对于多容器应用程序，可以 (多行分隔) 提供多个容器映像名称 |
| **配置-文件** |  (可选) 仅适用于 Web 应用容器： Docker-Compose 文件的路径。 应为完全限定的路径或相对于默认工作目录。 对于多容器应用是必需的。 |
| **启动-命令** |  (可选) 输入启动命令。 例如， dotnet run 或 dotnet filename.dll |

# <a name="publish-profile"></a>[发布配置文件](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[服务主体](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>后续步骤

可在 GitHub 上找到分组到不同存储库中的操作集，其中每一个都包含文档和示例，帮助你将 GitHub 用于 CI/CD 并将应用部署到 Azure。

- [要部署到 Azure 的操作工作流](https://github.com/Azure/actions-workflow-samples)

- [Azure 登录](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker 登录/注销](https://github.com/Azure/docker-login)

- [触发工作流的事件](https://docs.github.com/en/free-pro-team@latest/actions/reference/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [初学者工作流](https://github.com/actions/starter-workflows)
