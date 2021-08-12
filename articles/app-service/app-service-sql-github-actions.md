---
title: 教程：使用 GitHub Actions 部署到用于容器的应用服务并连接到数据库
description: 了解如何使用 GitHub Actions 将 ASP.NET Core 应用部署到 Azure 和 Azure SQL 数据库
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/22/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: f8048dab26451fea85b52caa3ffdc27d7e0a677a
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111894139"
---
# <a name="tutorial-use-github-actions-to-deploy-to-app-service-for-containers-and-connect-to-a-database"></a>教程：使用 GitHub Actions 部署到用于容器的应用服务并连接到数据库

本教程逐步介绍如何设置一个 GitHub Actions 工作流，以使用 [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)后端部署容器化的 ASP.NET Core 应用程序。 完成后，你便拥有了一个在 Azure 中运行并连接到 SQL 数据库的 ASP.NET 应用。 首先，你将使用 [ARM 模板](../azure-resource-manager/templates/overview.md) GitHub Actions 工作流创建 Azure资源。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> - 使用 GitHub Actions 工作流通过 Azure 资源管理器模板（ARM 模板）将资源添加到 Azure
> - 使用 GitHub Actions 工作流生成包含最新 Web 应用更改的容器

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个 GitHub 帐户。 如果没有该帐户，请注册[免费版](https://github.com/join)。
  - GitHub 存储库，用于存储资源管理器模板和工作流文件。 若要创建一个存储库，请参阅[创建新存储库](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)。

## <a name="download-the-sample"></a>下载示例

在 Azure 示例存储库中[创建示例项目的分支](https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/)。

```
https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/
```

## <a name="create-the-resource-group"></a>创建资源组

在 https://shell.azure.com 上打开 Azure Cloud Shell。 或者，可以使用 Azure CLI（如果已在本地安装）。 （有关 Cloud Shell 的详细信息，请参阅“Cloud Shell 概述”。）

```azurecli-interactive
    az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>生成部署凭据

需使用服务主体进行身份验证才能正常运行资源部署脚本。 可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 请使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

在该示例中，请将占位符替换为你的订阅 ID、资源组名称和服务主体名称。 输出是一个 JSON 对象，包含的角色分配凭据可提供对应用服务应用的访问权限。 复制此 JSON 对象供以后使用。 如需帮助，请参阅[配置部署凭据](https://github.com/Azure/login#configure-deployment-credentials)。

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
> 始终应授予最小访问权限。 上一个示例中的范围仅限于特定的应用服务应用而不是整个资源组。

## <a name="configure-the-github-secret-for-authentication"></a>配置 GitHub 机密以进行身份验证

在 [GitHub](https://github.com/) 中，浏览存储库，选择“设置”>“机密”>“添加新机密”。

若要使用[用户级凭据](#generate-deployment-credentials)，请将 Azure CLI 命令的整个 JSON 输出粘贴到机密的“值”字段中。 为机密指定名称 `AZURE_CREDENTIALS`。

## <a name="add-a-sql-server-secret"></a>添加 SQL Server 机密

在存储库中为 `SQL_SERVER_ADMIN_PASSWORD` 创建新机密。 此机密可以是符合 Azure 密码安全标准的任何密码。 以后你不再可以访问此密码，因此请将其另行保存。

## <a name="create-azure-resources"></a>创建 Azure 资源

“创建 Azure 资源”工作流运行一个 [ARM 模板](../azure-resource-manager/templates/overview.md)以将资源部署到 Azure。 该工作流：

- 通过[签出操作](https://github.com/marketplace/actions/checkout)签出源代码。
- 通过 [Azure 登录操作](https://github.com/marketplace/actions/azure-login)登录到 Azure，并收集环境和 Azure 资源的信息。
- 通过 [Azure 资源管理器部署操作](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)部署资源。

若要运行“创建 Azure 资源”工作流，请执行以下操作：

1. 打开存储库内 `.github/workflows` 中的 `azuredeploy.yaml` 文件。

1. 将 `AZURE_RESOURCE_GROUP` 的值更新为你的资源组名称。

1. 转到“操作”并选择“运行工作流” 。

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="运行 GitHub Actions 工作流以添加资源。":::

1. 通过检查“操作”页上是否显示了绿色打勾标记来确认操作是否成功运行。

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="成功运行创建资源的操作。":::

## <a name="add-container-registry-and-sql-secrets"></a>添加容器注册表和 SQL 机密

1. 在 Azure 门户中，打开资源组中新建的 Azure 容器注册表。

1. 转到“访问密钥”，然后复制用户名和密码值。

1. 在存储库中为 `ACR_USERNAME` 和 `ACR_PASSWORD` 密码创建新的 GitHub 机密。

1. 在 Azure 门户中，打开你的 SQL 数据库。 打开“连接字符串”并复制值。

1. 为 `SQL_CONNECTION_STRING` 创建新机密。 将 `{your_password}` 替换为你的 `SQL_SERVER_ADMIN_PASSWORD`。

## <a name="build-push-and-deploy-your-image"></a>生成、推送和部署映像

生成、推送和部署工作流生成一个包含最新应用更改的容器，将该容器推送到 [Azure 容器注册表](../container-registry/index.yml)，并将 Web 应用程序过渡槽更新为指向所推送的最新容器。 该工作流包含生成和部署作业：

- 生成作业通过[签出操作](https://github.com/marketplace/actions/checkout)签出源代码。 然后，该作业使用 [Docker 登录操作](https://github.com/marketplace/actions/docker-login)和自定义脚本向 Azure 容器注册表进行身份验证，生成容器映像，并将其部署到 Azure 容器注册表。
- 部署作业通过 [Azure 登录操作](https://github.com/marketplace/actions/azure-login)登录到 Azure，并收集环境和 Azure 资源的信息。 然后，该作业通过 [Azure 应用服务设置操作](https://github.com/marketplace/actions/azure-app-service-settings)更新 Web 应用设置，并通过 [Azure Web 部署操作](https://github.com/marketplace/actions/azure-webapp)部署到应用服务过渡槽。 最后，该作业运行自定义脚本来更新 SQL 数据库，并从过渡槽交换到生产槽。

若要运行生成、推送和部署工作流，请执行以下操作：

1. 打开存储库内 `.github/workflows` 中的 `build-deploy.yaml` 文件。

1. 确认 `AZURE_RESOURCE_GROUP` 和 `WEB_APP_NAME` 的环境变量是否与 `azuredeploy.yaml` 中的环境变量相匹配。

1. 将 `ACR_LOGIN_SERVER` 值更新为你的 Azure 容器注册表登录服务器。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure 和 GitHub 集成](/azure/developer/github/)