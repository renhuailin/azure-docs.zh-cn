---
title: 使用 Azure CLI 创建工作区
titleSuffix: Azure Machine Learning
description: 了解如何使用适用于机器学习的 Azure CLI 扩展创建新的 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2021
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 65445d67f87e5c10aef6f7e8b7e20e0908271a21
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123034595"
---
# <a name="manage-azure-machine-learning-workspaces-using-azure-cli"></a>使用 Azure CLI 管理 Azure 机器学习工作区

本文介绍如何使用 Azure CLI 创建和管理 Azure 机器学习工作区。 Azure CLI 提供了一些命令来管理 Azure 资源，这些命令旨在让用户快速使用 Azure（侧重于自动化）。 CLI 的机器学习扩展提供用于处理 Azure 机器学习资源的命令。

> [!NOTE]
> 本文中的示例引用 1.0 CLI 和 CLI (v2) 版本。 机器学习 CLI (v2) 目前提供公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://azure.microsoft.com/free/)。

* 若要从本地环境使用本文档中的 CLI 命令，需要使用 [Azure CLI](/cli/azure/install-azure-cli)。

    如果使用的是 [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，则可通过浏览器访问 CLI（它位于云端）。

## <a name="limitations"></a>限制

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>将 CLI 连接到 Azure 订阅

> [!IMPORTANT]
> 如果使用的是 Azure Cloud Shell，可跳过此部分。 Cloud Shell 会使用你登录 Azure 订阅所用的帐户自动对你进行身份验证。

可使用多种方法通过 CLI 对 Azure 订阅进行身份验证。 最简单的方法是使用浏览器进行交互式身份验证。 若要以交互方式进行身份验证，请打开命令行或终端，然后使用以下命令：

```azurecli-interactive
az login
```

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，需要打开浏览器并按照命令行中的说明操作。 按说明操作时，需要浏览到 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 并输入授权代码。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

有关其他身份验证方法，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

必须在资源组中创建 Azure 机器学习工作区。 可以使用现有资源组，也可以创建新组。 若要创建新的资源组，请使用以下命令。 将 `<resource-group-name>` 替换为此资源组使用的名称。 将 `<location>` 替换为此资源组使用的 Azure 区域：

> [!NOTE]
> 应选择可使用 Azure 机器学习的区域。 有关详细信息，请参阅 [各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令的响应类似于以下 JSON。 可以使用输出值来查找创建的资源，或将其解析为后续 CLI 步骤的输入以实现自动化。

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

要详细了解如何使用资源组，请参阅 [az group](/cli/azure/group)。

## <a name="create-a-workspace"></a>创建工作区

部署 Azure 机器学习工作区时，[需要各种其他服务作为依赖的相关资源](./concept-workspace.md#resources)。 使用 CLI 创建工作区时，CLI 可以代表你创建新的关联资源，你也可以附加现有资源。

> [!IMPORTANT]
> 附加自己的存储帐户时，请确保它满足以下条件：
>
> * 存储帐户不是高级帐户（Premium_LRS 和 Premium_GRS）
> * Azure Blob 和 Azure 文件功能同时启用
> * 分层命名空间 (ADLS Gen 2) 已禁用。这些要求仅适用于工作区使用的默认存储帐户。
>
> 附加 Azure 容器注册表时，必须先为容器注册表启用[管理员帐户](../container-registry/container-registry-authentication.md#admin-account)，然后才能将其用于 Azure 机器学习工作区。

# <a name="create-with-new-resources"></a>[使用新资源创建](#tab/createnewresources)

若要创建将在其中自动创建服务的新工作区，请使用以下命令：

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

# <a name="bring-existing-resources-10-cli"></a>[引入现有资源 (1.0 CLI)](#tab/bringexistingresources1)
若要创建使用现有资源的工作区，必须为每个资源提供资源 ID。 要获取此 ID，可以在 Azure 门户使用每个资源上的“属性”选项卡，或使用 Azure CLI 运行以下命令。

  * **Azure 存储账户**：     `az storage account show --name <storage-account-name> --query "id"`
  * **Azure Application Insights**：     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
  * **Azure 密钥保管库**：     `az keyvault show --name <key-vault-name> --query "ID"`
  * **Azure 容器注册表**：     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

  返回的资源 ID 具有以下格式：`"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"`。

获取要用于工作区的资源的 ID 后，请使用基础 `az workspace create -w <workspace-name> -g <resource-group-name>` 命令，并添加现有资源的参数和 ID。 例如，以下命令创建一个使用现有容器注册表的工作区：

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

# <a name="bring-existing-resources-cli-v2---preview"></a>[引入现有资源（CLI (v2) - 预览版）](#tab/bringexistingresources2)

若要在使用 CLI 引入现有关联资源的同时创建新工作区，首先必须定义如何在配置文件中配置工作区。

```yaml workspace.yml
name: azureml888
location: EastUS
description: Description of my workspace
storage_account: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>
container_registry: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.ContainerRegistry/registries/<registry-name>
key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.KeyVault/vaults/<vault-name>
application_insights: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/components/<application-insights-name>
```

然后，可以在工作区创建 CLI 命令中引用此配置文件。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --file workspace.yml
```

如果是附加现有资源，必须提供这些资源的 ID。 要获取此 ID，可以在 Azure 门户使用每个资源上的“属性”选项卡，或使用 Azure CLI 运行以下命令。

* **Azure 存储账户**：     `az storage account show --name <storage-account-name> --query "id"`
* **Azure Application Insights**：     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
* **Azure 密钥保管库**：     `az keyvault show --name <key-vault-name> --query "ID"`
* **Azure 容器注册表**：     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

资源 ID 值类似于以下内容：`"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"`。

---

> [!IMPORTANT]
> 附加现有资源时，不必指定所有资源。 可以指定一个或多个资源。 例如，可以指定某个现有存储帐户，工作区会创建其他资源。

工作区创建命令的输出类似于以下 JSON。 可以使用输出值来查找创建的资源，或将其解析为后续 CLI 步骤的输入。

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}

```

## <a name="advanced-configurations"></a>高级配置
### <a name="configure-workspace-for-private-network-connectivity"></a>为专用网络连接配置工作区

根据用例和组织要求，可以选择使用专用网络连接来配置 Azure 机器学习。 可以使用 Azure CLI 为工作区资源部署工作区和专用链接终结点。 有关将专用终结点和虚拟网络与工作区结合使用的详细信息，请参阅[虚拟网络隔离和隐私概述](how-to-network-security-overview.md)。 有关复杂的资源配置，另请参阅基于模板的部署选项，包括 [Azure 资源管理器](how-to-create-workspace-template.md)。

# <a name="10-cli"></a>[1.0 CLI](#tab/vnetpleconfigurationsv1cli)

如果希望仅允许虚拟网络访问工作区，可以在 `az ml workspace create` 命令中使用以下参数，或者使用 `az ml workspace private-endpoint` 命令。

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --pe-name "<pe name>"
                       --pe-auto-approval "<pe-autoapproval>"
                       --pe-resource-group "<pe name>"
                       --pe-vnet-name "<pe name>"
                       --pe-subnet-name "<pe name>"
```

* `--pe-name`：创建的专用终结点的名称。
* `--pe-auto-approval`：是否应自动批准专用终结点与工作区的连接。
* `--pe-resource-group`：用于创建专用终结点的资源组。 必须是包含虚拟网络的同一个组。
* `--pe-vnet-name`：用于创建专用终结点的现有虚拟网络。
* `--pe-subnet-name`：用于创建专用终结点的子网的名称。 默认值为 `default`。

有关如何使用这些命令的更多详细信息，请参阅 [CLI 参考页](/cli/azure/ml(v1)/workspace)。

# <a name="cli-v2---preview"></a>[CLI (v2) - 预览版](#tab/vnetpleconfigurationsv2cli)

若要使用 CLI (v2) 为工作区设置专用网络连接，请扩展工作区配置文件以包含专用链接终结点资源详细信息。

```yaml workspace.yml
name: azureml888
location: EastUS
description: Description of my workspace
storage_account: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>
container_registry: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.ContainerRegistry/registries/<registry-name>
key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.KeyVault/vaults/<vault-name>
application_insights: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/components/<application-insights-name>

private_endpoints:
  approval_type: AutoApproval
  connections:
    my-endpt1:
      subscription_id: <subscription-id>
      resource_group: <resourcegroup>
      location: <location>
      vnet_name: <vnet-name>
      subnet_name: <subnet-name>
```

然后，可以在工作区创建 CLI 命令中引用此配置文件。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --file workspace.yml
```

---

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>客户管理的密钥和极大影响业务的工作区

默认情况下，工作区的元数据存储在 Microsoft 维护的 Azure Cosmos DB 实例中。 该数据是使用 Microsoft 管理的密钥加密的。 还可以提供自己的密钥，而不使用 Microsoft 管理的密钥。 这样可以在 Azure 订阅中创建一组额外的资源来存储数据。

若要详细了解在引入自己的密钥进行加密时创建的资源，请参阅[使用 Azure 机器学习进行数据加密](./concept-data-encryption.md#azure-cosmos-db)。

以下 CLI 命令提供的示例分别使用 1.0 CLI 和 CLI (v2) 版本创建使用客户管理的密钥进行加密的工作区。

# <a name="10-cli"></a>[1.0 CLI](#tab/vnetpleconfigurationsv1cli)

使用 `--cmk-keyvault` 参数指定包含密钥的 Azure Key Vault，并使用 `--resource-cmk-uri` 指定保管库中密钥的资源 id 和 uri。

若要在工作区中[限制 Microsoft 收集的数据](./concept-data-encryption.md#encryption-at-rest)，可以另外指定 `--hbi-workspace` 参数。 

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --cmk-keyvault "<cmk keyvault name>"
                       --resource-cmk-uri "<resource cmk uri>"
                       --hbi-workspace
```

# <a name="cli-v2---preview"></a>[CLI (v2) - 预览版](#tab/vnetpleconfigurationsv2cli)

使用 `customer_managed_key` 参数并包含 `key_vault` 和 `key_uri` 参数来指定保管库中密钥的资源 id 和 uri。

若要在工作区中[限制 Microsoft 收集的数据](./concept-data-encryption.md#encryption-at-rest)，可以另外指定 `hbi_workspace` 属性。 

```yaml workspace.yml
name: azureml888
location: EastUS
description: Description of my workspace
storage_account: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>
container_registry: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.ContainerRegistry/registries/<registry-name>
key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.KeyVault/vaults/<vault-name>
application_insights: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/components/<application-insights-name>

hbi_workspace: true
customer_managed_key:
  key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers//Microsoft.KeyVault/<vaulttype>/<vaultname>
  key_uri: https://<keyvaultid>.vault.azure.net/keys/<keyname>/<keyversion>

```

然后，可以在工作区创建 CLI 命令中引用此配置文件。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --file workspace.yml
```
---

> [!NOTE]
> 授予 __机器学习应用__（在标识和访问管理中）对订阅的参与者权限，以管理数据加密和其他资源。

> [!NOTE]
> Azure Cosmos DB 不用于存储信息，例如模型性能、试验记录的信息或模型部署中记录的信息。 有关监视这些项的详细信息，请参阅体系结构和概念一文的[监视和日志记录](concept-azure-machine-learning-architecture.md)部分。

> [!IMPORTANT]
> 只能在创建工作区时选择高业务影响。 在创建工作区后，不能更改此设置。

有关客户管理的密钥和极大影响业务的工作区的详细信息，请参阅 [Azure 机器学习的企业安全性](concept-data-encryption.md#encryption-at-rest)。

## <a name="using-the-cli-to-manage-workspaces"></a>使用 CLI 管理工作区

### <a name="list-workspaces"></a>列出工作区

若要列出 Azure 订阅的所有工作区，请使用以下命令：

```azurecli-interactive
az ml workspace list
```

有关详细信息，请参阅 [az ml workspace list](/cli/azure/ml/workspace#az_ml_workspace_list) 文档。

### <a name="get-workspace-information"></a>获取工作区信息

若要获取有关工作区的信息，请使用以下命令：

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

有关详细信息，请参阅 [az ml workspace show](/cli/azure/ml/workspace#az_ml_workspace_show) 文档。

### <a name="update-a-workspace"></a>更新工作区

若要更新工作区，请使用以下命令：

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

有关详细信息，请参阅 [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update) 文档。

### <a name="sync-keys-for-dependent-resources"></a>同步依赖资源的密钥

如果更改工作区使用的资源之一的访问密钥，则工作区需要大约一个小时才能与新密钥同步。 若要强制工作区立即同步新密钥，请使用以下命令：

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

有关更改密钥的详细信息，请参阅[重新生成存储访问密钥](how-to-change-storage-access-key.md)。

有关 sync-keys 命令详细信息，请参阅 [az ml workspace sync-keys](/cli/azure/ml/workspace#az_ml_workspace_sync-keys)。

### <a name="delete-a-workspace"></a>创建工作区

若要删除不再需要的工作区，请使用以下命令：

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> 删除某个工作区不会删除该工作区使用的 Application Insights、存储帐户、密钥保管库或容器注册表。

也可以删除资源组，这会删除该资源组中的工作区和所有其他 Azure 资源。 若要删除资源组，请使用以下命令：

```azurecli-interactive
az group delete -g <resource-group-name>
```

有关详细信息，请参阅 [az ml workspace delete](/cli/azure/ml/workspace#az_ml_workspace_delete) 文档。

## <a name="troubleshooting"></a>疑难解答

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移动工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到另一个订阅，或将拥有的订阅移到新租户。 这样做可能会导致错误。

### <a name="deleting-the-azure-container-registry"></a>删除 Azure 容器注册表

Azure 机器学习工作区使用 Azure 容器注册表 (ACR) 执行某些操作。 首次需要 ACR 实例时，它会自动创建一个。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>后续步骤

有关适用于机器学习的 Azure CLI 扩展的详细信息，请参阅 [az ml](/cli/azure/ml) 文档。