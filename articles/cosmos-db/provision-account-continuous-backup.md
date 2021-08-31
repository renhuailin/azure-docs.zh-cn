---
title: 在 Azure Cosmos DB 中为帐户预配连续备份和时间点还原
description: 了解如何使用 Azure 门户、PowerShell、CLI 和资源管理器模板为帐户预配连续备份和时间点还原。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78996e58111d380778e8d02673f518720250e6fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779754"
---
# <a name="provision-an-azure-cosmos-db-account-with-continuous-backup-and-point-in-time-restore"></a>为 Azure Cosmos DB 帐户预配连续备份和时间点还原 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB 的时间点还原功能可帮助你从容器内的意外更改中恢复，还原已删除的帐户、数据库或容器，或者还原到任何区域（其中已存在备份）。 通过连续备份模式，可还原到过去 30 天内的任何时间点。

本文介绍如何使用 [Azure 门户](#provision-portal)、[PowerShell](#provision-powershell)、[CLI](#provision-cli) 和[资源管理器模板](#provision-arm-template)为帐户预配连续备份和时间点还原。

> [!NOTE]
> 只有在满足以下条件时，才能预配连续备份模式帐户：
>
> * 如果帐户的类型为 SQL API 或是 API for MongoDB。
> * 如果帐户有单个写入区域。
> * 如果帐户未启用客户管理的密钥 (CMK)。

## <a name="provision-using-azure-portal"></a><a id="provision-portal"></a>使用 Azure 门户进行预配

创建新的 Azure Cosmos DB 帐户时，在“备份策略”选项卡中选择“连续”模式，启用新帐户的时间点还原功能。 使用时间点还原时，数据将还原到新帐户，目前还无法还原到现有帐户。

:::image type="content" source="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png" alt-text="为 Azure Cosmos DB 帐户预配连续备份配置。" border="true" lightbox="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png":::

## <a name="provision-using-azure-powershell"></a><a id="provision-powershell"></a>使用 Azure PowerShell 进行预配

在预配帐户之前，请安装[最新版 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) 或高于 6.2.0 的版本。 接下来，请使用以下命令连接到 Azure 帐户并选择所需的订阅：

1. 使用以下命令登录到 Azure：

   ```azurepowershell
   Connect-AzAccount
   ```

1. 使用以下命令选择特定订阅：

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

#### <a name="sql-api-account"></a><a id="provision-powershell-sql-api"></a>SQL API 帐户

要为帐户预配连续备份，请添加参数 `-BackupPolicyType Continuous` 以及常规预配命令。

以下 cmdlet 是单个区域写入帐户 Pitracct 的示例，其连续备份策略是在“美国西部”区域中的“MyRG”资源组下创建的：

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct" `
  -ApiKind "Sql"
      
```

#### <a name="api-for-mongodb"></a><a id="provision-powershell-mongodb-api"></a>适用于 MongoDB 的 API

以下 cmdlet 是在“美国西部”区域的“MyRG”资源组下创建的连续备份帐户“Pitracct”的示例：

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "Pitracct" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="provision-using-azure-cli"></a><a id="provision-cli"></a>使用 Azure CLI 进行预配

在预配帐户之前，请通过以下步骤安装 Azure CLI：

1. 安装最新版本的 Azure CLI

   * 安装最新版 [Azure CLI](/cli/azure/install-azure-cli) 或高于 2.26.0 的版本
   * 如果已经安装了 CLI，请运行 `az upgrade` 命令以更新到最新版本。 此命令仅适用于高于 2.11 的 CLI 版本。 如果有较早的版本，请使用上面的链接安装最新版本。

1. 登录并选择你的订阅。

   * 使用 `az login` 来命令登录 Azure 账户。
   * 使用 `az account set -s <subscriptionguid>` 命令选择所需订阅。

### <a name="sql-api-account"></a><a id="provision-cli-sql-api"></a>SQL API 帐户

若要为 SQL API 帐户预配连续备份，应将额外的参数 `--backup-policy-type Continuous` 与常规预配命令一起传递。 以下命令是一个名为 Pitracct 的单个区域写入帐户的示例，其连续备份策略是在“美国西部”区域中的“MyRG”资源组下创建的：

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --resource-group MyRG \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

### <a name="api-for-mongodb"></a><a id="provision-cli-mongo-api"></a>适用于 MongoDB 的 API

以下命令显示一个名为 Pitracct 的单个区域写入帐户的示例，其连续备份策略是在“美国西部”区域中的“MyRG”资源组下创建的：

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --kind MongoDB \
  --resource-group MyRG \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-using-resource-manager-template"></a><a id="provision-arm-template"></a>使用资源管理器模板进行预配

可以使用 Azure 资源管理器模板来部署具有连续模式的 Azure Cosmos DB 帐户。 定义用于预配帐户的模板时，请包括 `backupPolicy` 参数，如以下示例中所示：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

接下来，使用 Azure PowerShell 或 CLI 部署模板。 下面的示例演示如何使用 CLI 命令部署模板：

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="next-steps"></a>后续步骤

* [还原活动的或已删除的 Azure Cosmos DB 帐户](restore-account-continuous-backup.md)
* [如何将帐户从定期备份迁移到连续备份](migrate-continuous-backup.md)。
* [连续备份模式资源模型](continuous-backup-restore-resource-model.md)。
* [管理](continuous-backup-restore-permissions.md)以连续备份模式还原数据所需的权限。