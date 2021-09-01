---
title: 设置面向单租户 Azure 逻辑应用的 DevOps
description: 如何设置面向单租户 Azure 逻辑应用中的工作流的 DevOps 部署。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: 010fbfc3b6a2df9c8cdca1221fb4f25a5d288d70
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742186"
---
# <a name="set-up-devops-deployment-for-single-tenant-azure-logic-apps"></a>设置面向单租户 Azure 逻辑应用的 DevOps 部署

本文介绍如何使用 DevOps 工具和进程，将基于单租户的逻辑应用项目从 Visual Studio Code 部署到基础结构当中。 根据你更愿意使用 GitHub 还是 Azure DevOps 进行部署，选择最适合你的情况的路径和工具。 可以使用含有示例逻辑应用项目的内含示例，以及使用 GitHub 或 Azure DevOps 部署 Azure 的示例。 有关面向单租户的 DevOps 的详细信息，请查看[面向单租户 Azure 逻辑应用的 DevOps 部署概述](devops-deployment-single-tenant-azure-logic-apps.md)。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 使用 [Visual Studio Code 和 Azure 逻辑应用（标准版）扩展](create-single-tenant-workflows-visual-studio-code.md#prerequisites)创建的基于单租户的逻辑应用项目。

  如果你尚未设置逻辑应用项目或基础结构，可以根据你想使用的源和部署选项，使用内含示例项目来部署示例应用和基础结构。 有关这些示例项目以及为运行示例逻辑应用而包含的资源的详细信息，请查看[部署基础结构](#deploy-infrastructure)。

- 如果要部署到 Azure，则需要已在 Azure 中创建的现有逻辑应用（标准版）资源。 若要快速创建空逻辑应用资源，请查看[创建基于单租户的逻辑应用工作流 - 门户](create-single-tenant-workflows-azure-portal.md)。

<a name="deploy-infrastructure"></a>

## <a name="deploy-infrastructure-resources"></a>部署基础结构资源

如果尚未设置逻辑应用项目或基础结构，可以根据你想使用的源和部署选项，使用以下示例项目来部署示例应用和基础结构：

- [适用于单租户 Azure 逻辑应用的 GitHub 示例](https://github.com/Azure/logicapps/tree/master/github-sample)

  此示例包含适用于单租户 Azure 逻辑应用的示例逻辑应用项目，以及适用于 Azure 部署和 GitHub Actions 的示例。

- [适用于单租户 Azure 逻辑应用的 Azure DevOps 示例](https://github.com/Azure/logicapps/tree/master/azure-devops-sample)

  此示例包含适用于单租户 Azure 逻辑应用的示例逻辑应用项目，以及适用于 Azure 部署和 Azure Pipelines 的示例。
  
这两个示例均包含逻辑应用用于运行的以下资源。

| 资源名称 | 必需 | 说明 |
|---------------|----------|-------------|
| 逻辑应用（标准版） | 是 | 此 Azure 资源包含在单租户 Azure 逻辑应用中运行的工作流。 |
| Functions 高级托管计划或应用服务托管计划 | 是 | 此 Azure 资源可指定用于运行逻辑应用的托管资源，如计算、处理、存储、网络等。 <p><p>重要提示：在当前体验中，逻辑应用（标准版）资源需要[工作流标准托管计划](logic-apps-pricing.md#standard-pricing)（基于 Functions 高级托管计划）。   |
| Azure 存储帐户 | 是，同时适用于监控状态和无状态的工作流 | 此 Azure 资源可存储有关工作流的元数据、访问控制密钥、状态、输入、输出、运行历史记录和其他信息。 |
| Application Insights | 可选 | 此 Azure 资源可为工作流提供监视功能。 |
| API 连接 | 可选（如果不存在任何连接） | 这些 Azure 资源可定义工作流用于运行托管连接器操作（如 Office 365、SharePoint 等）的任何托管 API 连接。 <p><p>重要提示：在逻辑应用项目中，connections.js 文件包含任何托管 API 连接的元数据、终结点和密钥，以及工作流使用的 Azure 函数。  若要在各种环境中使用不同的连接和函数，请确保实现 connections.js 文件参数化并更新终结点。 <p><p>有关详细信息，请查看 [API 连接资源和访问策略](#api-connection-resources)。 |
| Azure 资源管理器 (ARM) 模板 | 可选 | 此 Azure 资源定义可以重复使用或[导出](../azure-resource-manager/templates/template-tutorial-export-template.md)的基线基础结构部署。 该模板还包括必需的访问策略，如使用托管 API 连接的访问策略。 <p><p>重要提示：ARM 模板导出操作将不会导出工作流使用的任何 API 连接资源的所有相关参数。 有关详细信息，请查看[查找 API 连接参数](#find-api-connection-parameters)。 |
||||

<a name="api-connection-resources"></a>

## <a name="api-connection-resources-and-access-policies"></a>API 连接资源和访问策略

在单租户 Azure 逻辑应用中，工作流中的每个托管或 API 连接资源均需要一个关联的访问策略。 此策略需要逻辑应用的标识，以便提供正确的权限来访问托管连接器基础结构。 内含示例项目包含一个 ARM 模板，其中含有所有必要的基础结构资源（包括这些访问策略）。

下图显示了逻辑应用项目与基础结构资源之间的依赖关系：

![显示单租户 Azure 逻辑应用模型中某个逻辑应用项目的基础结构依赖关系的概念图。](./media/set-up-devops-deployment-single-tenant-azure-logic-apps/infrastructure-dependencies.png)

<a name="find-api-connection-parameters"></a>

### <a name="find-api-connection-parameters"></a>查找 API 连接参数

如果工作流使用托管 API 连接，则使用导出模板功能将不会包括所有相关参数。 在 ARM 模板中，每个 [API 连接资源定义](logic-apps-azure-resource-manager-templates-overview.md#connection-resource-definitions)都采用以下常规格式：

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {}
}
```

若要查找需要在 `properties` 对象中使用以便完成连接资源定义的值，可以针对特定连接器使用以下 API：

`GET https://management.azure.com/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/{connector-name}?api-version=2016-06-01`

在响应中查找 `connectionParameters` 对象，该对象包含完成该特定连接器的资源定义所需的所有信息。 以下示例演示了 SQL 托管连接的示例资源定义：

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {
      "displayName": "sqltestconnector",
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/sql"
      },
      "parameterValues": {
         "authType": "windows", 
         "database": "TestDB",
         "password": "TestPassword",
         "server": "TestServer",
         "username": "TestUserName"
      }
   }
}
```

或者可以在逻辑应用设计器中创建连接时查看网络跟踪。 按照前文所述，查找对连接器的托管 API 的 `PUT` 调用，并查看请求正文来了解所需的所有信息。

## <a name="deploy-logic-app-resources-zip-deploy"></a>部署逻辑应用（压缩部署）

将逻辑应用项目推送到源存储库后，可以设置生成和发布管道，以将逻辑应用部署到 Azure 内部或外部的基础结构。

### <a name="build-your-project"></a>生成项目

若要基于逻辑应用项目类型设置生成管道，请完成下表中列出的相应操作：

| 项目类型 | 说明和步骤 |
|--------------|-----------------------|
| 基于 Nuget | 基于 NuGet 的项目结构以 .NET Framework 为基础。 若要生成这些项目，请务必遵循 .NET Standard 的生成步骤。 有关详细信息，请查看[使用 MSBuild 创建 NuGet 包](/nuget/create-packages/creating-a-package-msbuild)文档。 |
| 基于捆绑包 | 基于扩展捆绑包的项目并非采用特定语言，且无需任何语言特定的生成步骤。 可以使用任何方法压缩项目文件。 <p><p>重要说明：请确保 .zip 文件包含实际的生成工件，其中包括所有工作流文件夹、配置文件（如 host.json、connections.json）以及任何其他相关文件。 |
|||

### <a name="release-to-azure"></a>发布到 Azure

若要设置部署到 Azure 的发布管道，请选择 GitHub、Azure DevOps 或 Azure CLI 的关联选项。

> [!NOTE]
> Azure 逻辑应用目前不支持 Azure 部署槽位。

#### <a name="github"></a>[GitHub](#tab/github)

对于 GitHub 部署，可以使用 [GitHub Actions](https://docs.github.com/actions) 部署逻辑应用，例如，Azure Functions 中的 GitHub Actions。 此操作要求传递以下信息：

- 用于部署的逻辑应用名称
- 包含实际生成工件的 zip 文件，其中包括所有工作流文件夹、配置文件（如 host.json、connections.json）以及任何其他相关文件。
- 用于进行身份验证的[发布配置文件](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials)

```yaml
- name: 'Run Azure Functions Action'
  uses: Azure/functions-action@v1
  id: fa
  with:
   app-name: 'MyLogicAppName'
   package: 'MyBuildArtifact.zip'
   publish-profile: 'MyLogicAppPublishProfile'
```

有关详细信息，请查看[使用 GitHub Action 持续交付](../azure-functions/functions-how-to-github-actions.md)文档。

#### <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

对于 Azure DevOps 部署，可以通过使用 Azure Pipelines 中的 [Azure Function 应用部署任务](/azure/devops/pipelines/tasks/deploy/azure-function-app?view=azure-devops&preserve-view=true)来部署逻辑应用。 此操作要求传递以下信息：

- 用于部署的逻辑应用名称
- 包含实际生成工件的 zip 文件，其中包括所有工作流文件夹、配置文件（如 host.json、connections.json）以及任何其他相关文件。
- 用于进行身份验证的[发布配置文件](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials)

```yaml
- task: AzureFunctionApp@1
  displayName: 'Deploy logic app workflows'
  inputs:
     azureSubscription: 'MyServiceConnection'
     appType: 'workflowapp'
     appName: 'MyLogicAppName'
     package: 'MyBuildArtifact.zip'
     deploymentMethod: 'zipDeploy'
```

有关详细信息，请查看[使用 Azure Pipelines 部署 Azure Function](/azure/devops/pipelines/targets/azure-functions-windows) 文档。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果使用其他部署工具，则可以使用 Azure CLI 部署基于单租户的逻辑应用。 在开始之前，需要具有以下项：

- 在本地计算机上安装了最新的 Azure CLI 扩展。

  - 如果没有此扩展，请参阅[操作系统或平台安装指南](/cli/azure/install-azure-cli)。

  - 如果不确定是否具有最新版本，请按照[检查环境和 CLI 版本的步骤](#check-environment-cli-version)操作。

- Azure CLI 的预览版单租户 Azure 逻辑应用（标准）扩展。

  如果没有此扩展，请按照[安装扩展的步骤](#install-logic-apps-cli-extension)操作。 虽然单租户 Azure 逻辑应用已正式发布，但 Azure CLI 的单租户 Azure 逻辑应用扩展仍处于预览阶段。

- 要用于部署逻辑应用的 Azure 资源组。

  如果没有此资源组，请按照[创建资源组的步骤](#create-resource-group)操作。

- Azure 存储帐户（用于与逻辑应用一起保留数据和运行历史记录）。

  如果没有此存储帐户，请按照[创建存储帐户的步骤](/cli/azure/storage/account#az_storage_account_create)操作。

<a name="check-environment-cli-version"></a>

##### <a name="check-environment-and-cli-version"></a>检查环境和 CLI 版本

1. 登录 [Azure 门户](https://portal.azure.com)。 在终端或命令窗口中，通过运行 [`az login`](/cli/azure/authenticate-azure-cli) 命令来确认订阅处于活动状态：

   ```azurecli-interactive
   az login
   ```

1. 在终端或命令窗口中，通过运行 `az` 命令，并使用以下必需参数来检查 Azure CLI 的版本：

   ```azurecli-interactive
   az --version
   ```

1. 如果没有最新版本的 Azure CLI，请按照[适用于操作系统或平台的安装指南](/cli/azure/install-azure-cli)来更新安装。

   有关最新版本的详细信息，请查看[最新发行说明](/cli/azure/release-notes-azure-cli?tabs=azure-cli)。

<a name="install-logic-apps-cli-extension"></a>

##### <a name="install-azure-logic-apps-standard-extension-for-azure-cli"></a>安装 Azure CLI Azure 逻辑应用（标准）扩展

目前，只有此扩展的预览版本可用。 如果以前尚未安装此扩展，请运行命令 `az extension add`，并带有以下必需参数：

```azurecli-interactive
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

若要获取版本 0.1.1 的最新扩展，请运行以下命令以删除现有扩展，然后从源安装最新版本：

```azurecli-interactive
az extension remove --name logicapp
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

<a name="create-resource-group"></a>

#### <a name="create-resource-group"></a>创建资源组

如果还没有为逻辑应用设置资源组，请运行 `az group create` 命令创建该组。 除非你已为 Azure 帐户设置了一个默认订阅，否则请确保将 `--subscription` 参数与订阅名称或标识符一起使用。 否则，无需使用 `--subscription` 参数。

> [!TIP]
> 若要设置默认订阅，请运行以下命令，将 `MySubscription` 替换为订阅名称或标识符。
>
> `az account set --subscription MySubscription`

例如，以下命令使用位置 `eastus` 中名为 `MySubscription` 的 Azure 订阅创建一个名为 `MyResourceGroupName` 的资源组：

```azurecli
az group create --name MyResourceGroupName 
   --subscription MySubscription 
   --location eastus
```

如果已成功创建资源组，输出会将 `provisioningState` 显示为 `Succeeded`：

```output
<...>
   "name": "testResourceGroup",
   "properties": {
      "provisioningState": "Succeeded"
    },
<...>
```

<a name="deploy-logic-app"></a>

##### <a name="deploy-logic-app"></a>部署逻辑应用

若要将压缩生成工件部署到 Azure 资源组，请运行 `az logicapp deployment` 命令，并使用以下必需参数：

> [!IMPORTANT]
> 请确保 zip 文件在根级别包含项目的生成工件。 这些生成工件包括所有工作流文件夹、配置文件（如 host.json、connections.json）和任何其他相关文件。 不要添加任何额外的文件夹，也不要将任何生成工件放入项目结构中不存在的文件夹。 例如，下面的列表显示示例 MyBuildArtifacts.zip 文件结构：
>
> ```output
> MyStatefulWorkflow1-Folder
> MyStatefulWorkflow2-Folder
> connections.json
> host.json
> ```

```azurecli-interactive
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --src MyBuildArtifact.zip
```

---

### <a name="release-to-containers"></a>发布到容器

如需实现逻辑应用容器化，部署的工作方式与你部署和管理的任何其他容器大致相同。

有关演示如何实施端到端容器生成和部署管道的示例，请查看[适用于容器的 CI/CD](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/)。

## <a name="next-steps"></a>后续步骤

- [面向单租户 Azure 逻辑应用的 DevOps 部署](devops-deployment-single-tenant-azure-logic-apps.md)

我们希望你就单租户 Azure 逻辑应用体验提供反馈！

- 如果发现 bug 或问题，请[在 GitHub 中创建问题](https://github.com/Azure/logicapps/issues)。
- 如有问题、请求、意见和其他反馈，请[使用此反馈表单](https://aka.ms/logicappsdevops)。
