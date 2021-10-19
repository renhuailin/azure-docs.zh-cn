---
title: 使用已启用 Azure Arc 的逻辑应用创建和部署工作流
description: 创建和部署基于单租户的逻辑应用工作流，这些工作流在 Kubernetes 可以运行的任何位置运行。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, ladolan, reylons, archidda, sopai, azla
ms.topic: how-to
ms.date: 06/03/2021
ms.openlocfilehash: 17c9eb020d62207910008fb032872bd609df553f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712305"
---
# <a name="create-and-deploy-single-tenant-based-logic-app-workflows-with-azure-arc-enabled-logic-apps-preview"></a>使用已启用 Azure Arc 的逻辑应用（预览版）创建和部署基于单租户的逻辑应用工作流

> [!NOTE]
> 此功能为预览版，受 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)限制。

通过已启用 Azure Arc 的逻辑应用和 Azure 门户，可创建基于单租户的逻辑应用工作流，并将其部署到你运行和管理的 Kubernetes 基础结构。 逻辑应用在自定义位置运行，此位置映射到已启用 Azure Arc 的 Kubernetes 群集，其中已安装并启用了 Azure 应用服务平台扩展捆绑包。

例如，此群集可以是 Azure Kubernetes 服务、裸机 Kubernetes 或其他设置。 通过扩展捆绑包，可在 Kubernetes 群集上运行 Azure 逻辑应用、Azure 应用服务和 Azure Functions 等平台服务。 

有关详细信息，请查看以下文档：

- [什么是已启用 Azure Arc 的逻辑应用？](azure-arc-enabled-logic-apps-overview.md)
- [单租户与多租户和集成服务环境](../logic-apps/single-tenant-overview-compare.md)
- [Azure Arc 概述](../azure-arc/overview.md)
- [Azure Kubernetes 服务概述](../aks/intro-kubernetes.md)
- [什么是已启用 Azure Arc 的 Kubernetes？](../azure-arc/kubernetes/overview.md)
- [已启用 Azure Arc 的 Kubernetes 上的自定义位置](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [Azure Arc 上的应用服务、函数和逻辑应用（预览版）](../app-service/overview-arc-integration.md)
- [设置启用了 Azure Arc 的 Kubernetes 群集，以便运行应用服务、Functions 和逻辑应用（预览版）](../app-service/manage-create-arc-environment.md)

## <a name="prerequisites"></a>先决条件

本部分介绍所有可用于创建和部署逻辑应用工作流的方法和工具的常见先决条件。 特定于工具的先决条件及其相应的步骤一同显示。

- 具有活动订阅的 Azure 帐户。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 具有已启用 Azure Arc 的 Kubernetes 群集和自定义位置的 Kubernetes 环境，可在其中托管和运行 Azure 逻辑应用、Azure 应用服务和 Azure Functions。

  > [!IMPORTANT]
  > 对于 Kubernetes 环境、自定义位置和逻辑应用，请确保使用相同的资源位置。

  在 Kubernetes 群集上创建应用服务捆绑扩展时，可以[更改默认缩放行为](#change-scaling)用于运行逻辑应用工作流。 使用 Azure CLI 命令 [`az k8s-extension create`](/cli/azure/k8s-extension) 创建扩展时，请确保添加配置设置 `keda.enabled=true`：

  `az k8s-extension create {other-command-options} --configuration-settings "keda.enabled=true"`

  有关详细信息，请查看以下文档：

  - [Azure Arc 上的应用服务、函数和逻辑应用（预览版）](../app-service/overview-arc-integration.md)
  - [已启用 Azure Arc 的 Kubernetes 上的群集扩展](../azure-arc/kubernetes/conceptual-extensions.md)
  - [设置启用了 Azure Arc 的 Kubernetes 群集，以便运行应用服务、Functions 和逻辑应用（预览版）](../app-service/manage-create-arc-environment.md)
  - [更改默认缩放行为](#change-scaling)

- 自己的 Azure Active Directory (Azure AD) 标识

  如果工作流需要使用任何 Azure 托管的连接（如 Office 365 Outlook 或 Azure 存储），则逻辑应用必须使用 Azure AD 标识进行身份验证。 已启用 Azure Arc 的逻辑应用可以在任何基础结构上运行，但需要有权使用 Azure 托管连接的标识。 若要设置此标识，请在 Azure AD 中创建一个应用注册，逻辑应用将其用作所需的标识。

  > [!NOTE]
  > 对于已启用 Azure Arc 的逻辑应用，托管标识支持当前不可用。

  若要使用 Azure CLI 创建 Azure Active Directory (Azure AD) 应用注册，请遵循以下步骤：

  1. 使用 [`az ad sp create`](/cli/azure/ad/sp#az_ad_sp_create) 命令创建应用注册。

  1. 若要查看所有详细信息，请运行 [`az ad sp show`](/cli/azure/ad/sp#az_ad_sp_show) 命令。

  1. 从这两个命令的输出中，查找并保存需要保留供稍后使用的客户端 ID、对象 ID、租户 ID 和客户端密码值。

  若要使用 Azure 门户创建 Azure Active Directory (Azure AD) 应用注册，请遵循以下步骤：

  1. 使用 [Azure 门户](../active-directory/develop/quickstart-register-app.md)创建新的 Azure AD 应用注册。

  1. 创建完成后，在门户中找到新的应用注册。

  1. 在注册菜单中，选择“概述”，然后保存客户端 ID、租户 ID 和客户端密码值。

  1. 若要查找对象 ID，请在“本地目录中的托管应用程序”字段旁边选择自己应用注册的名称。 在属性视图中，复制对象 ID。

## <a name="create-and-deploy-logic-apps"></a>创建和部署逻辑应用

根据是要使用 Azure CLI、Visual Studio Code，还是 Azure 门户，选择匹配的选项卡来查看特定的先决条件和步骤。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在开始之前，需要具有以下项：

- 在本地计算机上安装了最新的 Azure CLI 扩展。

  - 如果没有此扩展，请参阅[操作系统或平台安装指南](/cli/azure/install-azure-cli)。

  - 如果不确定是否具有最新版本，请按照[检查环境和 CLI 版本的步骤](#check-environment-cli-version)操作。

- Azure CLI 的预览版 Azure 逻辑应用（标准）扩展。

  虽然单租户 Azure 逻辑应用已正式发布，但 Azure 逻辑应用扩展仍处于预览状态。

- 可用于在其中创建逻辑应用的 [Azure 资源组](#create-resource-group)。

  如果没有此资源组，请按照[创建资源组的步骤](#create-resource-group)操作。

- Azure 存储帐户（用于与逻辑应用一起保留数据和运行历史记录）。

  如果没有此存储帐户，可以在创建逻辑应用时创建此帐户，也可以按照[创建存储帐户的步骤](/cli/azure/storage/account#az_storage_account_create)操作。

<a name="check-environment-cli-version"></a>

#### <a name="check-environment-and-cli-version"></a>检查环境和 CLI 版本

1. 登录到 Azure 门户。 运行以下命令，检查订阅是否处于活动状态：

   ```azurecli-interactive
   az login
   ```

1. 运行以下命令，在终端或命令窗口中检查 Azure CLI 版本：

   ```azurecli-interactive
   az --version
   ```

   有关最新版本，请参阅[最新发行说明](/cli/azure/release-notes-azure-cli?tabs=azure-cli)。

1. 如果没有最新版本，请按照[适用于你操作系统或平台的安装指南](/cli/azure/install-azure-cli)来更新安装。

<a name="install-logic-apps-cli-extension"></a>

##### <a name="install-azure-logic-apps-standard-extension-for-azure-cli"></a>安装 Azure CLI Azure 逻辑应用（标准）扩展

通过运行以下命令，安装 Azure CLI 的预览版单租户 Azure 逻辑应用（标准）扩展：

```azurecli-interactive
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

<a name="create-resource-group"></a>

#### <a name="create-resource-group"></a>创建资源组

如果还没有用于逻辑应用的资源组，请运行命令 `az group create` 创建该组。 除非你已为 Azure 帐户设置了一个默认订阅，否则请确保将 `--subscription` 参数与订阅名称或标识符一起使用。 否则，无需使用 `--subscription` 参数。

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

#### <a name="create-logic-app"></a>创建逻辑应用

若要创建已启用 Azure Arc 的逻辑应用，请使用以下必需参数运行命令 `az logicapp create`。 逻辑应用资源位置、自定义位置和 Kubernetes 环境必须相同。

| parameters | 说明 |
|------------|-------------|
| `--name -n` | 逻辑应用的唯一名称 |
| `--resource-group -g` | 要在其中创建逻辑应用的[资源组](../azure-resource-manager/management/manage-resource-groups-cli.md)。 如果没有可用的资源组，请[创建一个资源组](#create-resource-group)。 |
| `--storage-account -s` | 要用于逻辑应用的[存储帐户](/cli/azure/storage/account)。 对于同一资源组中的存储帐户，请使用字符串值。 对于不同资源组中的存储帐户，请使用资源 ID。 |
|||

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation
```

若要使用专用 Azure 容器注册表映像创建已启用 Azure Arc 的逻辑应用，请使用以下必需参数运行命令 `az logicapp create`：

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   --deployment-container-image-name myacr.azurecr.io/myimage:tag
   --docker-registry-server-password MyPassword 
   --docker-registry-server-user MyUsername
```

#### <a name="show-logic-app-details"></a>显示逻辑应用详细信息

若要显示已启用 Azure Arc 的逻辑应用的详细信息，请使用以下必需参数运行命令 `az logicapp show`：

```azurecli
az logicapp show --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="deploy-logic-app"></a>部署逻辑应用

若要使用 [Azure 应用服务的 Kudu zip 部署](../app-service/resources-kudu.md)来部署已启用 Azure Arc 的逻辑应用，请使用以下必需参数运行命令 `az logicapp deployment source config-zip`：

> [!IMPORTANT]
> 请确保 zip 文件在根级别包含项目的生成工件。 这些生成工件包括所有工作流文件夹、配置文件（如 host.json、connections.json）和任何其他相关文件。 不要添加任何额外的文件夹，也不要将任何生成工件放入项目结构中不存在的文件夹。 例如，下面的列表显示示例 MyBuildArtifacts.zip 文件结构：
>
> ```output
> MyStatefulWorkflow1-Folder
> MyStatefulWorkflow2-Folder
> connections.json
> host.json
> ```

```azurecli
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --src MyBuildArtifact.zip
```

#### <a name="start-logic-app"></a>启动逻辑应用

若要启动已启用 Azure Arc 的逻辑应用，请使用以下必需参数运行命令 `az logicapp start`：

```azurecli
az logicapp start --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="stop-logic-app"></a>停止逻辑应用

若要停止已启用 Azure Arc 的逻辑应用，请使用以下必需参数运行命令 `az logicapp stop`：

```azurecli
az logicapp stop --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="restart-logic-app"></a>重启逻辑应用

若要重启已启用 Azure Arc 的逻辑应用，请使用以下必需参数运行命令 `az logicapp restart`：

```azurecli
az logicapp restart --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="delete-logic-app"></a>删除逻辑应用

若要删除已启用 Azure Arc 的逻辑应用，请使用以下必需参数运行命令 `az logicapp delete`：

```azurecli
az logicapp delete --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

可在 Visual Studio Code 中创建、部署和监视完整的逻辑应用工作流。 开发在单租户 Azure 逻辑应用中和已启用 Azure Arc 的逻辑应用中运行的逻辑应用工作流时，设计器体验不会有任何变化或不同。

1. 若要创建逻辑应用项目，请遵循[通过 Visual Studio Code 在单租户 Azure 逻辑应用中创建集成工作流](create-single-tenant-workflows-visual-studio-code.md)文档中的先决条件和步骤。

1. 准备好部署到 Azure 后，使用“部署到逻辑应用”体验在先前创建的自定义位置中创建逻辑应用资源，并将工作流部署到相同的位置。

   1. 在逻辑应用项目窗口的空白区域中，打开快捷菜单，然后选择“部署到逻辑应用”。

   1. 选择与自定义位置关联的 Azure 订阅。

   1. 若要创建新的已启用 Azure Arc 的逻辑应用资源，请选择“在 Azure 中创建新的逻辑应用(高级)”。 或者，可以从列表中选择现有的逻辑应用资源，并跳过后续步骤。

   1. 提供逻辑应用的全局唯一名称。

   1. 选择要在其中部署的已启用 Azure Arc 的 Kubernetes 环境的自定义位置。 如果改为选择通用 Azure 区域，请创建在单租户 Azure 逻辑应用中运行的未启用 Azure Arc 的逻辑应用资源。

   1. 选择或创建要在其中部署逻辑应用的新资源组。

   1. 选择或创建一个新的存储帐户，用于保存逻辑应用的状态和元数据。

   1. 选择或创建一个新的 Application Insights 资源，用于存储逻辑应用的应用程序日志。

   1. 如果尚未执行此操作，请设置 Azure Active Directory (Azure AD) 标识，以便逻辑应用可以对托管 API 连接进行身份验证。 有关详细信息，请参阅顶级[先决条件](#prerequisites)。

   1. 输入 Azure AD 标识的客户端 ID、租户 ID、对象 ID 和客户端密码。

      > [!NOTE]
      > 只需完成此步骤一次。 Visual Studio Code 会更新项目的 connections.json 文件和托管 API 连接的访问策略。

1. 完成后，逻辑应用将启动并在已启用 Azure Arc 的 Kubernetes 群集中运行，可供你测试。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

目前正在为已启用 Azure Arc 的逻辑应用开发基于门户的设计器的编辑功能。 可使用基于门户的设计器创建、部署和查看逻辑应用，但在部署后无法在门户中编辑。 目前，可以在 Visual Studio Code 本地创建和编辑逻辑应用项目，然后使用 Visual Studio Code、Azure CLI 或自动化部署进行部署。

1. 在 Azure 门户中，[创建一个逻辑应用（标准）资源](create-single-tenant-workflows-azure-portal.md)。 但对于“发布”目标，请选择“Docker 容器”。 在“区域”中，选择先前创建的自定义位置作为应用的位置。

   默认情况下，逻辑应用（标准）资源在单租户 Azure 逻辑应用中运行。 但是，对于已启用 Azure Arc 的逻辑应用，逻辑应用资源在为 Kubernetes 环境创建的自定义位置中运行。 此外，因为已为你创建应用服务计划，所以无需再创建该计划。

   > [!IMPORTANT]
   > 逻辑应用资源位置、自定义位置和 Kubernetes 环境必须相同。

1. [使用 Visual Studio Code 编辑和部署逻辑应用](create-single-tenant-workflows-visual-studio-code.md)。

1. 生成和部署逻辑应用后，可使用门户或 Application Insights 像往常一样监视和查看工作流。

   部署的逻辑应用的门户体验目前以只读模式提供，这意味着无法更改工作流或应用设置。 但是，仍可以查看运行历史记录、触发器历史记录以及有关应用的其他信息。 目前，可使用 Azure CLI、Visual Studio Code 或自动化部署来更新逻辑应用。

---

## <a name="set-up-connection-authentication"></a>设置连接身份验证

目前，已启用 Azure Arc 的 Kubernetes 群集不支持使用逻辑应用的托管标识对托管 API 连接进行身份验证。 在工作流中使用托管连接器时，你需要创建这些 Azure 托管和管理的连接。

应改为在 Azure Active Directory (Azure AD) 中创建自己的应用注册。 然后，可使用此应用注册作为在已启用 Azure Arc 的逻辑应用中部署和运行的逻辑应用的标识。 有关详细信息，请查看[顶级先决条件](#prerequisites)。

在应用注册中，需要客户端 ID、对象 ID、租户 ID 和客户端密码。 如果使用 Visual Studio Code 进行部署，可拥有使用 Azure AD 标识设置逻辑应用的内置体验。 有关详细信息，请查看[创建和部署逻辑应用工作流 - Visual Studio Code](#create-and-deploy-logic-apps)。

然而，如果使用 Visual Studio Code 进行开发，而使用 Azure CLI 或自动化管道进行部署，请按照这些步骤进行操作。

### <a name="configure-connection-and-app-settings-in-your-project"></a>在项目中配置连接和应用设置

1. 在逻辑应用项目的 connections.js 文件中，找到托管连接的 `authentication` 对象。 将此对象的内容替换为之前在[顶级先决条件](#prerequisites)中生成的应用注册信息：

   ```json
   "authentication": {
      "type": "ActiveDirectoryOAuth",
      "audience": "https://management.core.windows.net/",
      "credentialType": "Secret",
      "clientId": "@appsetting('WORKFLOWAPP_AAD_CLIENTID')",
      "tenant": "@appsetting('WORKFLOWAPP_AAD_TENANTID')",
      "secret": "@appsetting('WORKFLOWAPP_AAD_CLIENTSECRET')"
   } 
   ```

1. 在逻辑应用项目的 local.settings.js 文件中，添加客户端 ID、对象 ID、租户 ID 和客户端密码。 部署完成后，这些设置会成为逻辑应用设置。

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         <...>
         "WORKFLOWAPP_AAD_CLIENTID":"<my-client-ID>",
         "WORKFLOWAPP_AAD_OBJECTID":"<my-object-ID",
         "WORKFLOWAPP_AAD_TENANTID":"<my-tenant-ID>",
         "WORKFLOWAPP_AAD_CLIENTSECRET":"<my-client-secret>"
      }
   }
   ```

> [!IMPORTANT]
> 对于生产场景或生产环境，请确保可以保护此类机密和敏感信息的安全，例如使用[密钥保管库](../app-service/app-service-key-vault-references.md)提供保护。

### <a name="add-access-policies"></a>添加访问策略

在单租户 Azure 逻辑应用中，每个逻辑应用都具有一个由访问策略授权使用 Azure 托管和管理的连接的标识。 可使用 Azure 门户或基础结构部署设置这些访问策略。

#### <a name="arm-template"></a>ARM 模板

在 Azure 资源管理器模板（ARM 模板）中，为每个托管 API 连接添加以下资源定义并提供以下信息：

| 参数 | 说明 |
|-----------|-------------|
| <*connection-name*> | 托管 API 连接的名称，例如 `office365` |
| <object-ID> | Azure AD 标识的对象 ID（之前在应用注册中保存） |
| <*tenant-ID*> | Azure AD 标识的租户 ID（之前在应用注册中保存） |
|||

```json
{
   "type": "Microsoft.Web/connections/accessPolicies",
   "apiVersion": "2016-06-01",
   "name": "[concat('<connection-name>'),'/','<object-ID>')]",
   "location": "<location>",
   "dependsOn": [
      "[resourceId('Microsoft.Web/connections', parameters('connection_name'))]"
   ],
   "properties": {
      "principal": {
         "type": "ActiveDirectory",
         "identity": {
            "objectId": "<object-ID>",
            "tenantId": "<tenant-ID>"
         }
      }
   }
}
```

有关详细信息，请查看 [Microsoft.Web/connections/accesspolicies（ARM 模板）](/azure/templates/microsoft.web/connections?tabs=json)文档。 

#### <a name="azure-portal"></a>Azure 门户

对于此任务，请使用之前保存的客户端 ID 作为应用程序 ID。

1. 在 Azure 门户中，查找并打开逻辑应用。 在逻辑应用菜单的“工作流”下，选择“连接”，其中列出了逻辑应用工作流中的所有连接 。

1. 在“API 连接”下，选择一个连接，在本例中为 `office365`。

1. 在连接的菜单的“设置”下，选择“访问策略” > “添加”  。

1. 在“添加访问策略”窗格的搜索框中，找到并选择以前保存的客户端 ID。

1. 完成后，选择“添加”。

1. 为逻辑应用中每个 Azure 托管的连接重复这些步骤。

## <a name="automate-devops-deployment"></a>自动进行 DevOps 部署

若要生成和部署已启用 Azure Arc 的逻辑应用，可使用与[基于单租户的逻辑应用](single-tenant-overview-compare.md)相同的管道和过程。 若要使用 DevOps 的管道自动进行基础结构部署，请针对非容器部署和容器部署在基础结构级别进行以下更改。

### <a name="standard-deployment-non-container"></a>标准部署（非容器）

如果使用 zip 部署进行逻辑应用部署，则无需设置用于托管容器映像的 Docker 注册表。 尽管从技术上讲，Kubernetes 上的逻辑应用在容器中运行，但已启用 Azure Arc 的逻辑应用会为你管理这些容器。 针对这种场景，请在设置基础结构后完成以下任务：

- 通知资源提供程序，你正在 Kubernetes 上创建逻辑应用。
- 在部署中添加应用服务计划。 有关详细信息，请参阅[在部署中添加应用服务计划](#include-app-service-plan)。

在 [Azure 资源管理器模板（ARM 模板）](../azure-resource-manager/templates/overview.md)中，添加以下值：

| 项目 | JSON 属性 | 说明 |
|------|---------------|-------------|
| 位置 | `location` | 请确保使用与自定义位置和 Kubernetes 环境相同的资源位置（Azure 区域）。 逻辑应用资源位置、自定义位置和 Kubernetes 环境必须相同。 <p><p>请注意：此值与自定义位置的名称不同。 |
| 应用类型 | `kind` | 要部署的应用的类型，通过它 Azure 平台能够识别你的应用。 对于 Azure 逻辑应用，此信息类似于以下示例：`kubernetes,functionapp,workflowapp,linux` |
| 扩展位置 | `extendedLocation` | 此对象需要 Kubernetes 环境的自定义位置的 `"name"`，并且必须将 `"type"` 设置为 `"CustomLocation"`。 |
| 托管计划资源 ID | `serverFarmId` | 关联的应用服务计划的资源 ID，格式如下： <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| 存储连接字符串 | `AzureWebJobsStorage` | 存储帐户的连接字符串 <p><p>重要说明：需要在 ARM 模板中提供存储帐户的连接字符串。 对于生产场景或生产环境，请确保可以保护此类机密和敏感信息的安全，例如使用密钥保管库提供保护。 |
||||

#### <a name="arm-template"></a>ARM 模板

以下示例介绍了可在 ARM 模板中使用的已启用 Azure Arc 的逻辑应用资源定义示例。 有关详细信息，请查看 [Microsoft.Web/sites 模板格式 (JSON)](/azure/templates/microsoft.web/sites?tabs=json)文档。

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": "kubernetes,functionapp,workflowapp,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "clientAffinityEnabled": false,
      "name": "[parameters('name')]",
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "workflowapp"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "Node|12"
      }
   }
}
```

### <a name="container-deployment"></a>容器部署

如果希望使用容器工具和部署过程，可将逻辑应用容器化，并将其部署到已启用 Azure Arc 的逻辑应用。 针对这种场景，请在设置基础结构后完成以下高级任务：

- 设置用于托管容器映像的 Docker 注册表。
- 通知资源提供程序，你正在 Kubernetes 上创建逻辑应用。
- 在部署模板中，指向计划在其中进行部署的 Docker 注册表和容器映像。 单租户 Azure 逻辑应用使用此信息从 Docker 注册表获取容器映像。
- 在部署中添加应用服务计划。 有关详细信息，请参阅[在部署中添加应用服务计划](#include-app-service-plan)。

在 [Azure 资源管理器模板（ARM 模板）](../azure-resource-manager/templates/overview.md)中，添加以下值：

| 项目 | JSON 属性 | 说明 |
|------|---------------|-------------|
| 位置 | `location` | 请确保使用与自定义位置和 Kubernetes 环境相同的资源位置（Azure 区域）。 逻辑应用资源位置、自定义位置和 Kubernetes 环境必须相同。 <p><p>请注意：此值与自定义位置的名称不同 。 |
| 应用类型 | `kind` | 要部署的应用的类型，通过它 Azure 平台能够识别你的应用。 对于 Azure 逻辑应用，此信息类似于以下示例：`kubernetes,functionapp,workflowapp,container` |
| 扩展位置 | `extendedLocation` | 此对象需要 Kubernetes 环境的自定义位置的 `"name"`，并且必须将 `"type"` 设置为 `"CustomLocation"`。 |
| 容器名称 | `linuxFxVersion` | 容器的名称，格式如下：`DOCKER\|<container-name>` |
| 托管计划资源 ID | `serverFarmId` | 关联的应用服务计划的资源 ID，格式如下： <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| 存储连接字符串 | `AzureWebJobsStorage` | 存储帐户的连接字符串 <p><p>重要说明：部署到 Docker 容器时，需要在 ARM 模板中提供存储帐户的连接字符串。 对于生产场景或生产环境，请确保可以保护此类机密和敏感信息的安全，例如使用密钥保管库提供保护。 |
||||

若要引用 Docker 注册表和容器映像，请在模板中添加以下值：

| 项目 | JSON 属性 | 说明 |
|------|---------------|-------------|
| Docker 注册表服务器 URL | `DOCKER_REGISTRY_SERVER_URL` | Docker 注册表服务器的 URL |
| Docker 注册表服务器 | `DOCKER_REGISTRY_SERVER_USERNAME` | 用于访问 Docker 注册表服务器的用户名 |
| Docker 注册表服务器密码 | `DOCKER_REGISTRY_SERVER_PASSWORD` | 用于访问 Docker 注册表服务器的密码 |
||||

#### <a name="arm-template"></a>ARM 模板

以下示例介绍了可在 ARM 模板中使用的已启用 Azure Arc 的逻辑应用资源定义示例。 有关详细信息，请查看 [Microsoft.Web/sites 模板格式（ARM 模板）](/azure/templates/microsoft.web/sites?tabs=json)文档。

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": " kubernetes,functionapp,workflowapp,container",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "name": "[parameters('name')]",
      "clientAffinityEnabled": false,
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "workflowapp"
            }, 
            {
               "name": "DOCKER_REGISTRY_SERVER_URL",
               "value": "<docker-registry-server-URL>"
            },
            { 
               "name": "DOCKER_REGISTRY_SERVER_USERNAME",
               "value": "<docker-registry-server-username>"
            },
            {
               "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
               "value": "<docker-registry-server-password>"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "DOCKER|<container-name>"
      }
   }
}
```

<a name="include-app-service-plan"></a>

### <a name="include-app-service-plan-with-deployment"></a>在部署中添加应用服务计划

无论使用的是标准部署还是容器部署，都必须在部署中添加应用服务计划。 尽管此应用服务计划与 Kubernetes 环境不太相关，但标准部署和容器部署仍需要此计划。

虽然其他创建选项通常会为此计划处理 Azure 资源的预配，但如果部署使用“基础结构即代码”模板，则必须为该计划显式创建 Azure 资源。 托管计划资源不会更改，只有 `sku` 信息才会更改。

在 [Azure 资源管理器模板（ARM 模板）](../azure-resource-manager/templates/overview.md)中，添加以下值：

| 项目 | JSON 属性 | 说明 |
|------|---------------|-------------|
| 位置 | `location` | 请确保使用与自定义位置和 Kubernetes 环境相同的资源位置（Azure 区域）。 逻辑应用资源位置、自定义位置和 Kubernetes 环境必须相同。 <p><p>请注意：此值与自定义位置的名称不同。 |
| 种类 | `kind` | 要部署的此类应用服务计划需要为 `kubernetes,linux` |
| 扩展位置 | `extendedLocation` | 此对象需要 Kubernetes 环境的自定义位置的 `"name"`，并且必须将 `"type"` 设置为 `"CustomLocation"`。 |
| 托管计划名称 | `name` | 应用服务计划的名称 |
| 计划层 | `sku: tier` | 应用服务计划层，即 `K1` |
| 计划名称 | `sku: name` | 应用服务计划名称，即 `Kubernetes` |
||||

#### <a name="arm-template"></a>ARM 模板

以下示例介绍了可用于应用部署的应用服务计划资源定义示例。 有关详细信息，请查看 [Microsoft.Web/serverfarms 模板格式（ARM 模板）](/azure/templates/microsoft.web/serverfarms?tabs=json)文档。

```json
{
   "type": "Microsoft.Web/serverfarms",
   "apiVersion": "2020-12-01",
   "location": "<location>",
   "name": "<hosting-plan-name>",
   "kind": "kubernetes,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
   },
   "sku": {
      "tier": "K1",
      "name": "Kubernetes", 
      "capacity": 1
   },
   "properties": {
      "kubeEnvironmentProfile": {
         "id": "[parameters('kubeEnvironmentId')]"
      }
   }
}
```

<a name="change-scaling"></a>

## <a name="change-default-scaling-behavior"></a>更改默认缩放行为

已启用 Azure Arc 的逻辑应用根据后端存储队列中的作业数自动管理逻辑应用的缩放。 但是，你可以更改默认缩放行为。

在逻辑应用中，工作流定义指定操作运行的顺序。 每当触发工作流运行时，Azure 逻辑应用运行时都会为工作流定义中的每种操作类型创建一个作业。 然后，运行时将这些作业组织到作业排序器中。 此排序器协调运行工作流定义的作业，但基础 Azure 逻辑应用的作业编排引擎会运行每个作业。

对于有状态工作流，作业编排引擎使用存储队列消息在作业排序器中计划作业。 在后台，作业调度程序（或调度程序辅助角色实例）监视这些作业队列 。 编排引擎使用默认的最小和最大辅助角色实例数来监视作业队列。 对于无状态工作流，编排引擎完全将操作状态保留在内存中。

若要更改默认缩放行为，需指定不同的最小和最大辅助角色实例数来监视作业队列。

### <a name="prerequisites-to-change-scaling"></a>更改缩放的先决条件

在已启用 Azure Arc 的 Kubernetes 群集上，必须将以前创建的应用服务捆绑包扩展的 `keda.enabled` 属性设置为 `true`。 有关详细信息，请查看[顶级先决条件](#prerequisites)。

### <a name="change-scaling-threshold"></a>更改缩放阈值

在已启用 Azure Arc 的逻辑应用中，作业队列的长度会触发缩放事件，并为逻辑应用的缩放频率设置阈值。 可更改队列长度，它的默认值设置为 `20` 个作业。 若要降低缩放频率，请增加队列长度。 若要增加缩放频率，请减小队列长度。 此过程可能需要用到某种试错方法。

若要更改队列长度，在逻辑应用项目的根级别 host.json 文件中，设置 `Runtime.ScaleMonitor.KEDA.TargetQueueLength` 属性，例如：

```json
"extensions": {
   "workflow": {
      "settings": {
         "Runtime.ScaleMonitor.KEDA.TargetQueueLength": "10"
      }
   }
}
```

### <a name="change-throughput-maximum"></a>更改最大吞吐量

在现有的逻辑应用资源上可更改辅助角色实例的最大数（默认值设置为 `2`）。 该值控制可监视作业队列的辅助角色实例数的上限。

若要更改此最大值，请使用 Azure CLI (logic app create only) 和 Azure 门户。

#### <a name="azure-cli"></a>Azure CLI

若要创建新的逻辑应用，请使用以下参数运行命令 `az logicapp create`：

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 1] [--max-worker-count 4]
```

若要配置最大实例计数，请使用 `--settings` 参数：

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
   --settings "K8SE_APP_MAX_INSTANCE_COUNT=10"
```

#### <a name="azure-portal"></a>Azure 门户

在基于单租户的逻辑应用设置中，执行以下步骤以添加或编辑 `K8SE_APP_MAX_INSTANCE_COUNT` 设置的值：

1. 在 Azure 门户中，找到并打开基于单租户的逻辑应用。

1. 在逻辑应用菜单上的“设置”下，选择“配置” 。

1. 在“配置”窗格的“应用设置”下，可添加新的应用程序设置，或者编辑现有的值（如果已添加） 。

   1. 选择“新建应用程序设置”，添加采用所需最大值的 `K8SE_APP_MAX_INSTANCE_COUNT` 设置。

   1. 编辑 `K8SE_APP_MAX_INSTANCE_COUNT` 设置的当前值。

1. 完成后，保存所做的更改。

### <a name="change-throughput-minimum"></a>更改最小吞吐量

在现有的逻辑应用资源上，可更改辅助角色实例的最小数（默认值设置为 `1`）。 该值控制可监视作业队列的辅助角色实例数的下限。 为获得高可用性或高性能，请增大此值。

若要更改此最小值，请使用 Azure CLI 或 Azure 门户。

#### <a name="azure-cli"></a>Azure CLI

对于现有逻辑应用资源，请使用以下参数运行命令 `az logicapp scale`：

```azurecli
az logicapp scale --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --instance-count 5 
```

若要创建新的逻辑应用，请使用以下参数运行命令 `az logicapp create`：

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 2] [--max-worker-count 4]
```

#### <a name="azure-portal"></a>Azure 门户

在基于单租户的逻辑应用的设置中，按照以下步骤更改“横向扩展”属性值：

1. 在 Azure 门户中，找到并打开基于单租户的逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“横向扩展” 。

1. 在“横向扩展”窗格中，将最小实例数滑块拖动到所需的值。

1. 完成后，保存所做的更改。

## <a name="troubleshoot-problems"></a>排查问题

若要获取有关已部署的逻辑应用的详细信息，请尝试以下选项：

### <a name="access-app-settings-and-configuration"></a>访问应用设置和配置

若要访问应用设置，请使用以下参数运行命令 `az logicapp config appsettings`：

```azurecli
az logicapp config appsettings list --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

若要配置应用设置，请使用以下参数运行命令 `az logicapp config appsettings set`。 确保使用 `--settings` 参数替换设置的名称和值。

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --settings "MySetting=1"
```

若要删除应用设置，请使用以下参数运行命令 `az logicapp config appsettings delete`。 确保使用 `--setting-names` 参数替换要删除的设置的名称。

```azurecli
az logicapp config appsettings delete --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
   --setting-names MySetting
```

### <a name="view-logic-app-properties"></a>查看逻辑应用属性

若要查看应用的信息和属性，请使用以下参数运行命令 `az logicapp show`：

```azurecli
az logicapp show --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="monitor-workflow-activity"></a>监视工作流活动

若要查看逻辑应用中工作流的活动，请执行以下步骤： 

1. 在 Azure 门户中，找到并打开已部署的逻辑应用。

1. 在逻辑应用菜单上，选择“工作流”，然后选择自己的工作流 **** 。 

1. 在工作流菜单上，选择“监视” **** 。

### <a name="collect-logs"></a>收集日志

若要获取记录的逻辑应用相关数据，在逻辑应用上启用 Application Insights（如果尚未启用）。

## <a name="next-steps"></a>后续步骤

- [有关已启用 Azure Arc 的逻辑应用](azure-arc-enabled-logic-apps-overview.md)
