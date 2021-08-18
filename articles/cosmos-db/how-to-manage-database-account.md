---
title: 了解如何在 Azure Cosmos DB 中管理数据库帐户
description: 了解如何使用 Azure 门户、PowerShell、CLI 和 Azure 资源管理器模板管理 Azure Cosmos DB 资源
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/13/2021
ms.author: mjbrown
ms.openlocfilehash: 06d72e0a8953e6f22faebb6da2bc607fe5ebfad0
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112239537"
---
# <a name="manage-an-azure-cosmos-account"></a>管理 Azure Cosmos 帐户
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文介绍如何使用 Azure 门户、Azure PowerShell、Azure CLI 和 Azure 资源管理器模板管理 Azure Cosmos 帐户中的各种任务。

## <a name="create-an-account"></a>创建帐户

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure 门户

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

请参阅[使用 Azure CLI 创建 Azure Cosmos DB 帐户](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

请参阅[使用 PowerShell 创建 Azure Cosmos DB 帐户](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Azure Resource Manager 模板

请参阅[使用 Azure 资源管理器模板创建 Azure Cosmos DB 帐户](./manage-with-templates.md)

## <a name="addremove-regions-from-your-database-account"></a>在数据库帐户中添加/删除区域

> [!TIP]
> 添加新区域时，必须将所有数据完全复制并提交到新区域，然后才能将该区域标记为可用。 此操作所需的时间取决于帐户中存储的数据量。

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到 Azure Cosmos 帐户，打开“全局复制数据”菜单。

1. 要添加区域，请在地图上选择包含与所需区域对应的 + 标签的六边形。 另外，若要添加某个区域，请选择“+ 添加区域”选项，然后从下拉菜单中选择一个区域。

1. 若要删除区域，请选择带对号的蓝色六边形以从地图中清除一个或多个区域。 或者选择右侧位于区域旁边的“废纸篓”(🗑) 图标。

1. 若要保存更改，请选择“确定”。

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="添加或删除区域菜单":::

在单区域写入模式下，不能删除写入区域。 必须先故障转移到另一区域，然后才能删除当前的写入区域。

在多区域写入模式下，如果你至少具有一个区域，则可以添加或删除任何区域。

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

请参阅[使用 Azure CLI 添加或删除区域](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

请参阅[使用 PowerShell 添加或删除区域](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>配置多个写入区域

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure 门户

打开“全局复制数据”选项卡，选择“启用”以启用多区域写入 。 启用多区域写入后，你的帐户当前拥有的所有读取区域将变为读取和写入区域。

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Azure Cosmos 帐户配置多区域写入屏幕截图":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

请参阅[使用 Azure CLI 启用多写入区域](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

请参阅[使用 PowerShell 启用多写入区域](manage-with-powershell.md#multi-region-writes)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Resource Manager 模板

可部署资源管理器模板（用于创建帐户）和 `enableMultipleWriteLocations: true` 设置，将该帐户从单个写入区域迁移到多个写入区域。 以下 Azure 资源管理器模板是一个极简模板，它将为 SQL API 部署 Azure Cosmos 帐户，并启用两个区域和多个写入位置。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2021-03-15",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>为 Azure Cosmos 帐户启用自动故障转移

借助自动故障转移选项，在某个区域不可用时，Azure Cosmos DB 可以故障转移到具有最高故障转移优先级的区域，无需用户操作。 如果启用自动故障转移，则可修改区域优先级。 帐户必须具有两个或更多区域以启用自动故障转移。

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure 门户

1. 在 Azure Cosmos 帐户中，打开“全局复制数据”窗格。

2. 在窗格顶部选择“自动故障转移”。

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="“全局复制数据”菜单":::

3. 在“自动故障转移”窗格中，确保将“启用自动故障转移”设置为“开”。   

4. 选择“保存” 。

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="自动故障转移门户菜单":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

请参阅[使用 Azure CLI 启用自动故障转移](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

请参阅[使用 PowerShell 启用自动故障转移](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>为 Azure Cosmos 帐户设置故障转移优先级

Cosmos 帐户配置为自动故障转移后，可以更改区域的故障转移优先级。

> [!IMPORTANT]
> 帐户配置为自动故障转移后，不能修改写入区域（故障转移优先级为零）。 要更改写入区域，必须禁用自动故障转移并执行手动故障转移。

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure 门户

1. 在 Azure Cosmos 帐户中，打开“全局复制数据”窗格。

2. 在窗格顶部选择“自动故障转移”。

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="“全局复制数据”菜单":::

3. 在“自动故障转移”窗格中，确保将“启用自动故障转移”设置为“开”。  

4. 若要修改故障转移优先级，请将鼠标指针悬停在读取区域上，并通过在行左侧出现的三个点拖动读取区域。

5. 选择“保存” 。

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="自动故障转移门户菜单":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

请参阅[使用 Azure CLI 设置故障转移优先级](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

请参阅[使用 PowerShell 设置故障转移优先级](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>在 Azure Cosmos 帐户上执行手动故障转移

> [!IMPORTANT]
> Azure Cosmos 帐户必须配置为手动故障转移，才能成功执行此操作。

执行手动故障转移的过程涉及将帐户的写入区域（故障转移优先级 = 0）更改为已为该帐户配置的其他区域。

> [!NOTE]
> 具有多个写入区域的帐户无法进行手动故障转移。 对于使用 Azure Cosmos SDK 的应用程序，SDK 会检测某个区域何时变为不可用，然后自动重定向到下一个最近的区域。

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure 门户

1. 导航到 Azure Cosmos 帐户，打开“全局复制数据”菜单。

2. 在菜单顶部，选择“手动故障转移”。

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="“全局复制数据”菜单":::

3. 在“手动故障转移”菜单上，选择你的新写入区域。 选中相应的复选框，以指示你了解此选项会更改你的写入区域。

4. 若要触发故障转移，请选择“确定”。

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="手动故障转移门户菜单":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

请参阅[使用 Azure CLI 触发手动故障转移](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

请参阅[使用 PowerShell 触发手动故障转移](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>后续步骤

有关如何管理 Azure Cosmos 帐户以及数据库和容器的详细信息和示例，请阅读以下文章：

* [使用 Azure PowerShell 管理 Azure Cosmos DB](manage-with-powershell.md)
* [使用 Azure CLI 管理 Azure Cosmos DB](manage-with-cli.md)