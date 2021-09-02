---
title: 创建或管理 B2B 集成帐户
description: 创建、链接和管理 Azure 逻辑应用中用于企业集成的集成帐户
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 4df6a84cd3402a934dc64ca8b8ac1a79ef7f0c22
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113666134"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>创建和管理 Azure 逻辑应用中用于 B2B 企业集成的集成帐户

你需先创建一个集成帐户，然后才能使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)构建[企业集成和 B2B 解决方案](../logic-apps/logic-apps-enterprise-integration-overview.md)。该集成帐户是一个单独的 Azure 资源，该资源为你定义并用于逻辑应用工作流的集成项目提供安全、可缩放和可管理的容器。 例如，可以创建、存储和管理 B2B 项目，包括贸易合作伙伴、协议、映射、架构、证书和批配置。

下表描述了[定价各异](https://azure.microsoft.com/pricing/details/logic-apps/)的可用集成帐户级别或层：

| 层 | 说明 |
|------|-------------|
| **基本** | 适用于只需处理消息或充当与大型企业实体有贸易合作关系的小型企业合作伙伴的情况。 <p><p>受逻辑应用 SLA 支持。 |
| **标准** | 适用于 B2B 关系更复杂且需要管理的实体数增加的情况。 <p><p>受逻辑应用 SLA 支持。 |
| **免费** | 适用于探索场景，不适用于生产场景。 此层对区域可用性、吞吐量和使用情况有限制。 例如，此免费层仅适用于 Azure 中的公共区域（如“美国西部”或“东南亚”），但不适用于 [Azure 中国世纪互联](/azure/china/overview-operations)或 [Azure 政府](../azure-government/documentation-government-welcome.md)。 <p><p>注意：不受逻辑应用 SLA 支持。 |
|||

> [!IMPORTANT]
> 根据集成帐户的层级，创建集成帐户会产生成本。 有关更多信息，请参阅[逻辑应用定价和计费模型](logic-apps-pricing.md#integration-accounts)和[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 此外，必须先[将集成帐户关联到](#link-account)逻辑应用，逻辑应用工作流才可以使用集成帐户、B2B 项目及 B2B 连接器。 集成帐户和逻辑应用必须位于同一位置或区域。 

本主题演示如何完成以下任务：

* 创建集成帐户。

  > [!TIP]
  > 若要在[集成服务环境](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中创建集成帐户，请参阅[在 ISE 中创建集成帐户](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)。

* 将集成帐户链接到逻辑应用。
* 更改集成帐户的定价层。
* 从逻辑应用取消链接集成帐户。
* 将集成帐户移到另一个 Azure 资源组或订阅。
* 删除集成帐户。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="create-integration-account"></a>创建集成帐户

### <a name="portal"></a>[Portal](#tab/azure-portal)

对于此任务，可以使用 Azure 门户并执行本部分中所述的步骤，也可以使用 [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount) 或 [Azure CLI](/cli/azure/resource#az_resource_create)。

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 主菜单上选择“创建资源”  。 在搜索框中，输入“集成帐户”作为筛选器，然后选择“集成帐户”。 

   ![创建新的集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. 在“集成帐户”下选择“创建”。  

   ![选择“添加”以创建集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. 提供有关集成帐户的以下信息：

   ![提供集成帐户详细信息](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | <integration-account-name>  | 集成帐户的名称，只能包含字母、数字、连字符 (`-`)、下划线 (`_`)、括号（`(`、`)`）和句点 (`.`)。 本示例使用的名称是“Fabrikam-Integration”。 |
   | **订阅** | 是 | <*Azure-subscription-name*> | Azure 订阅的名称 |
   | **资源组** | 是 | <*Azure-resource-group-name*> | 用于组织相关资源的 [Azure 资源组](../azure-resource-manager/management/overview.md)的名称。 对于本示例，请创建名为“FabrikamIntegration-RG”的新资源组。 |
   | **定价层** | 是 | <*定价级别*> | 集成帐户的定价层，以后可以更改。 对于本示例，请选择“免费”。  有关详细信息，请参阅以下主题： <p>- [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **位置** | 是 | <*Azure-region*> | 用于存储集成帐户元数据的区域。 请选择逻辑应用所在的同一位置，或者在集成帐户所在的同一位置创建逻辑应用。 对于本示例，使用“美国西部”。 <p>注意：若要在 [integration service 环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中创建集成帐户，请选择 ISE 作为位置。 有关详细信息，请参阅[在 ISE 中创建集成帐户](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)。 |
   | **Log Analytics** | 否 | 关闭、打开 | 对于本示例，请保留“关闭”设置。  |
   |||||

1. 完成后，选择“创建”  。

   部署完成后，Azure 将打开你的集成帐户。

   ![Azure 打开集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. 在逻辑应用能够使用该集成帐户之前，请执行以下步骤，将集成帐户和逻辑应用链接到一起。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

你可以使用本部分中的 Azure CLI 命令创建集成帐户。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>创建集成帐户

使用这些命令创建集成帐户。

1. 若要添加 [az 逻辑集成帐户](/cli/azure/logic/integration-account)扩展，请使用 [az extension add](/cli/azure/extension#az_extension_add) 命令：

   ```azurecli
   az extension add –-name logic
   ```

1. 若要创建资源组或使用现有资源组，请运行 [az group create](/cli/azure/group#az_group_create) 命令：

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   若要列出资源组的集成帐户，请使用 [az log integration-account list](/cli/azure/logic/integration-account#az_logic_integration_account_list) 命令：

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. 若要创建集成帐户，请运行 [az logic integration-account create](/cli/azure/logic/integration-account#az_logic_integration_account_create) 命令：

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   集成帐户名称只能包含字母、数字、连字符 (-)、下划线 (_)、括号 ((,)) 和 (.)。

   > [!TIP]
   > 若要在[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中创建集成帐户，请选择 ISE 作为位置。 有关详细信息，请参阅[在 ISE 中创建集成帐户](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)。

   若要查看特定的集成帐户，请使用 [az logic integration-account show](/cli/azure/logic/integration-account#az_logic_integration_account_show) 命令：

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   你可以使用 [az logic integration-account update](/cli/azure/logic/integration-account#az_logic_integration_account_update) 命令更改你的 SKU 或定价层：

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   有关定价的详细信息，请参阅这些资源：

   * [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)

若要使用 JSON 文件导入集成帐户，请使用 [az logic integration-account import](/cli/azure/logic/integration-account#az_logic_integration_account_import) 命令：

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

你可以使用 [az logic integration-account delete](/cli/azure/logic/integration-account#az_logic_integration_account_delete) 命令：

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

在逻辑应用能够使用该集成帐户之前，请执行以下步骤，将集成帐户和逻辑应用链接到一起。 下一部分将介绍链接。

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>链接到逻辑应用

要使逻辑应用能够访问包含 B2B 项目的集成帐户，必须先将集成帐户链接到逻辑应用。 逻辑应用和集成帐户必须位于同一区域。 若要完成此任务，可以使用 Azure 门户。 如果使用 Visual Studio，而逻辑应用位于 [Azure 资源组项目](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)中，则你可以[使用 Visual Studio 将逻辑应用链接到集成帐户](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)。

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在 [Azure 门户](https://portal.azure.com)中，打开现有的逻辑应用或创建新的逻辑应用。

1. 在逻辑应用的菜单中，在“设置”  下，选择“工作流设置”  。 在“集成帐户”下，打开“选择集成帐户”列表。   选择要链接到逻辑应用的集成帐户。

   ![选择集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. 若要完成链接，请选择“保存”。 

   ![显示选择“保存”的位置以选择集成帐户的屏幕截图。](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   成功链接集成帐户后，Azure 会显示一条确认消息。

   ![Azure 确认成功链接](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

现在，逻辑应用可以使用集成帐户中的项目以及 B2B 连接器，例如 XML 验证和平面文件编码或解码。  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>更改定价层

若要提高集成帐户的[限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)，可以[升级到更高的定价层](#upgrade-pricing-tier)（如果适用）。 例如，可以从“免费”层升级到“基本”层或“标准”层。 还可以[降级到更低的层](#downgrade-pricing-tier)（如果适用）。 有关定价详细信息，请参阅以下主题：

* [逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)
* [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>升级定价层

可以使用 Azure 门户或 Azure CLI 进行更改。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 主要搜索框中，输入“集成帐户”作为筛选器，然后选择“集成帐户”。 

   ![查找集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 将显示你的 Azure 订阅中的所有集成帐户。

1. 在“集成帐户”下，选择要移动的集成帐户。  在集成帐户菜单中选择“概述”。 

   ![在集成帐户菜单中选择“概述”](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在“概述”窗格中选择“升级定价层”，随后会列出所有可用的更高层。  选择层时，更改会立即生效。

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 如果尚未[安装 Azure CLI 必备组件](/cli/azure/get-started-with-azure-cli)，现在请安装。

1. 在 Azure 门户中打开 [Azure Cloud Shell](../cloud-shell/overview.md) 环境。

   ![打开 Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 在命令提示符下输入 [**az resource** 命令](/cli/azure/resource#az_resource_update)，并将 `skuName` 设置为所需的更高层。

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   例如，如果目前使用的是“基本”层，可将 `skuName` 设置为 `Standard`：

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>降级定价层

若要进行此项更改，请使用 [Azure CLI](/cli/azure/get-started-with-azure-cli)。

1. 如果尚未[安装 Azure CLI 必备组件](/cli/azure/get-started-with-azure-cli)，现在请安装。

1. 在 Azure 门户中打开 [Azure Cloud Shell](../cloud-shell/overview.md) 环境。

   ![打开 Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 在命令提示符下输入 [**az resource** 命令](/cli/azure/resource#az_resource_update)，并将 `skuName` 设置为所需的更低层。

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   例如，如果目前使用的是“标准”层，可将 `skuName` 设置为 `Basic`：

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>从逻辑应用取消链接

如果要将逻辑应用链接到其他集成帐户，或不再将集成帐户用于逻辑应用，请使用 Azure 资源浏览器删除链接。

1. 在浏览器中，转到 [Azure 资源浏览器 (https://resources.azure.com)](https://resources.azure.com)。 使用相同的 Azure 帐户凭据登录。

   ![Azure 资源浏览器](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. 在搜索框中，输入逻辑应用的名称，以便找到并选择逻辑应用。

   ![查找并选择逻辑应用](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. 在浏览器标题栏上，选择“读/写”。

   ![启用“读/写”模式](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. 在“数据”选项卡上，选择“编辑”。 

   ![在“接收器”选项卡中选择“编辑”](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. 在编辑器中，找到 `integrationAccount` 对象，并删除该属性，其具有以下格式：

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   例如：

   ![查找“integrationAccount”对象](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. 在“数据”选项卡上，选择“放置”以保存所做的更改。 

   ![若要保存更改，请选择“放置”](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. 在 Azure 门户中，查找并选择“逻辑应用”。 在 Azure 门户中，在逻辑应用的“工作流设置”下，检查“集成帐户”属性现在是否显示为空。 

   ![检查集成帐户是否未链接](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>移动集成帐户

可将集成帐户移到另一个 Azure 资源组或 Azure 订阅。 移动资源时，Azure 会创建新的资源 ID，因此请确保改用新 ID，并更新与所移动的资源关联的任何脚本或工具。 若要更改订阅，还必须指定现有或新的资源组。

对于此任务，可以使用 Azure 门户并执行本部分中所述的步骤，或使用 [Azure CLI](/cli/azure/resource#az_resource_move)。

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 主要搜索框中，输入“集成帐户”作为筛选器，然后选择“集成帐户”。 

   ![查找集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 将显示你的 Azure 订阅中的所有集成帐户。

1. 在“集成帐户”下，选择要移动的集成帐户。  在集成帐户菜单中选择“概述”。 

   ![在集成帐户菜单中选择“概述”](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在“资源组”或“订阅名称”旁边选择“更改”。   

   ![更改资源组或订阅](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. 选择还要移动的任何相关资源。

1. 根据所做的选择，执行以下步骤来更改资源组或订阅：

   * 资源组：在“资源组”列表中选择目标资源组。  若要创建不同的资源组，请选择“创建新的资源组”。 

   * 订阅：在“订阅”列表中选择目标订阅。  在“资源组”列表中选择目标资源组。  若要创建不同的资源组，请选择“创建新的资源组”。 

1. 为了确认你已知道在使用新资源 ID 更新与所要移动的资源关联的任何脚本或工具之前，这些脚本或工具不会正常运行，请选中确认框，然后选择“确定”。 

1. 完成后，请确保使用已移动的资源的新资源 ID 更新所有脚本。  

## <a name="delete-integration-account"></a>删除集成帐户

对于此任务，可以使用 Azure 门户并执行本部分中所述的步骤，也可以使用 [Azure CLI](/cli/azure/resource#az_resource_delete) 或 [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount)。

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 主要搜索框中，输入“集成帐户”作为筛选器，然后选择“集成帐户”。 

   ![查找集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 将显示你的 Azure 订阅中的所有集成帐户。

1. 在“集成帐户”下，选择要删除的集成帐户。  在集成帐户菜单中选择“概述”。 

   ![在集成帐户菜单中选择“概述”](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在“概述”窗格中选择“删除”。 

   ![在“概述”窗格中选择“删除”](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. 若要确认删除集成帐户，请选择“是”。 

   ![若要确认删除，请选择“是”](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>后续步骤

* [在集成帐户中创建贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [在集成帐户中的合作伙伴之间创建协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)
