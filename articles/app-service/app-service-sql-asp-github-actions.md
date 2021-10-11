---
title: 教程：使用 GitHub Actions 部署到应用服务并连接到数据库
description: 使用 GitHub Actions 将支持数据库的 ASP.NET Core 应用部署到 Azure
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: 01d4293b33eb0756dec8bc9ae870a972959ced89
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701507"
---
# <a name="tutorial-use-github-actions-to-deploy-to-app-service-and-connect-to-a-database"></a>教程：使用 GitHub Actions 部署到应用服务并连接到数据库

了解如何设置 GitHub Actions 工作流来使用 [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)后端部署 ASP.NET Core 应用程序。 完成后，你便拥有了一个在 Azure 中运行并连接到 SQL 数据库的 ASP.NET 应用。 首先使用 [ARM 模板](/azure/azure-resource-manager/templates/overview)创建资源。

本教程不使用容器。 如果要部署到容器化的 ASP.NET Core 应用程序，请参阅[使用 GitHub Actions 部署到用于容器的应用服务并连接到数据库](app-service-sql-github-actions.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> - 使用 GitHub Actions 工作流通过 Azure 资源管理器模板（ARM 模板）将资源添加到 Azure
> - 使用 GitHub Actions 工作流生成 ASP.NET Core 应用程序

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个 GitHub 帐户。 如果没有该帐户，请注册[免费版](https://github.com/join)。
  - GitHub 存储库，用于存储资源管理器模板和工作流文件。 若要创建一个存储库，请参阅[创建新存储库](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)。

## <a name="download-the-sample"></a>下载示例

在 Azure 示例存储库中[创建示例项目的分支](https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions)。

```
https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions
```

## <a name="create-the-resource-group"></a>创建资源组

在 https://shell.azure.com 上打开 Azure Cloud Shell。 或者，可以使用 Azure CLI（如果已在本地安装）。 （有关 Cloud Shell 的详细信息，请参阅 [Cloud Shell 概述](../cloud-shell/overview.md)。）

```azurecli-interactive
az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>生成部署凭据

需使用服务主体进行身份验证才能正常运行资源部署脚本。 可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 请使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}
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

## <a name="configure-the-github-secret-for-authentication"></a>配置 GitHub 机密以进行身份验证

在 [GitHub](https://github.com/) 中，浏览存储库，选择“设置”>“机密”>“添加新机密”。

若要使用[用户级凭据](#generate-deployment-credentials)，请将 Azure CLI 命令的整个 JSON 输出粘贴到机密的“值”字段中。 将机密命名为 `AZURE_CREDENTIALS`。

## <a name="add-github-secrets-for-your-build"></a>为生成项添加 Github 机密

1. 在 GitHub 存储库中为 `SQLADMIN_PASS` 和 `SQLADMIN_LOGIN` 创建[两个新机密](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository)。 确保选择复杂的密码，否则 SQL 数据库服务器的创建步骤将失败。 以后你不再可以访问此密码，因此请将其另行保存。

2. 为你的 Azure 订阅 ID 创建 `AZURE_SUBSCRIPTION_ID` 机密。 如果不知道订阅 ID，请使用 Azure Shell 中的此命令进行查找。 复制 `SubscriptionId` 列中的值。
    ```azurecli
    az account list -o table
    ```
 
## <a name="create-azure-resources"></a>创建 Azure 资源

“创建 Azure 资源”工作流运行一个 [ARM 模板](/azure/azure-resource-manager/templates/overview)以将资源部署到 Azure。 该工作流：

- 通过[签出操作](https://github.com/marketplace/actions/checkout)签出源代码。
- 通过 [Azure 登录操作](https://github.com/marketplace/actions/azure-login)登录到 Azure，并收集环境和 Azure 资源的信息。
- 通过 [Azure 资源管理器部署操作](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)部署资源。

若要运行“创建 Azure 资源”工作流，请执行以下操作：

1. 打开存储库内 `.github/workflows` 中的 `infraworkflow.yml` 文件。

1. 将 `AZURE_RESOURCE_GROUP` 的值更新为你的资源组名称。

1. 将 ARM 部署操作中 `region` 的输入设置为你所在的区域。 
    1. 打开 `templates/azuredeploy.resourcegroup.parameters.json` 并将 `rgLocation` 属性更新为你所在的区域。
 
1. 转到“操作”并选择“运行工作流” 。

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="运行 GitHub Actions 工作流以添加资源。":::

1. 通过检查“操作”页上是否显示了绿色打勾标记来确认操作是否成功运行。

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="成功运行创建资源的操作。":::

1. 创建资源后，转到“操作”，选择“创建 Azure 资源”，然后禁用该工作流。 
 
    :::image type="content" source="media/github-actions-workflows/disable-workflow-github-actions.png" alt-text="禁用“创建 Azure 资源”工作流。":::

## <a name="create-a-publish-profile-secret"></a>创建发布配置文件机密

1. 在 Azure 门户中，打开使用 `Create Azure Resources` 工作流创建的新暂存应用服务（槽）。

1. 选择“获取发布配置文件”。

1. 在文本编辑器中打开发布配置文件并复制其内容。 

1. 为 `AZURE_WEBAPP_PUBLISH_PROFILE` 创建新的 GitHub 机密。 

## <a name="build-and-deploy-your-app"></a>生成并部署你的应用

若要运行生成和部署工作流：

1. 打开存储库内 `.github/workflows` 中的 `workflow.yaml` 文件。

1. 确认 `AZURE_RESOURCE_GROUP`、`AZURE_WEBAPP_NAME`、`SQLSERVER_NAME` 和 `DATABASE_NAME` 的环境变量是否与 `infraworkflow.yml` 中的环境变量相匹配。

1. 访问“交换到生产槽”输出中的 URL 来验证应用是否已部署。 应会看到名为“我的 TodoList 应用”的示例应用。 
 
## <a name="clean-up-resources"></a>清理资源

如果不再需要示例项目，请删除 Azure 门户中的资源组，并删除 GitHub 中的存储库。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure 和 GitHub 集成](/azure/developer/github/)
