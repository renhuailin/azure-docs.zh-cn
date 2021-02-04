---
title: 有关 Azure Cosmos DB 时间点还原功能的常见问题。
description: 本文列出了有关使用连续备份模式实现的 Azure Cosmos DB 时间点还原功能的常见问题。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c0af1db12f3ade2945524f48e4539d2d2e9aa6b9
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539173"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Azure Cosmos DB 时间点还原功能的常见问题解答 (预览版) 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> 时间点还原功能 (持续备份模式) 用于 Azure Cosmos DB 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文列出了有关使用连续备份模式实现 (预览版) Azure Cosmos DB 时间点还原功能的常见问题。

## <a name="how-much-time-does-it-takes-to-restore"></a>还原需要多长时间？
还原持续时间依赖于你的数据的大小。

### <a name="can-i-submit-the-restore-time-in-local-time"></a>是否可以在本地时间提交还原时间？
根据数据库或容器等关键资源是否存在，还原可能不会发生。 你可以通过输入时间并在给定的时间内查看所选数据库或容器来进行验证。 如果看不到要还原的资源，还原过程将不起作用。

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>如何跟踪帐户是否正在还原？
提交还原命令并等待同一页后，操作完成后，状态栏将显示 "已成功还原还原的帐户" 消息。 你还可以搜索已还原的帐户，并 [跟踪正在还原的帐户的状态](continuous-backup-restore-portal.md#track-restore-status)。 当还原正在进行时，帐户的状态将为 "正在创建"，还原操作完成后，帐户状态将更改为 "联机"。

同样，对于 PowerShell 和 CLI，可以通过执行命令来跟踪还原操作的进度，如下所示 `az cosmosdb show` ：

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

当帐户处于联机状态时，provisioningState 将显示 "成功"。

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>如何确定帐户是否已从另一个帐户还原？
运行 `az cosmosdb show` 命令，在输出中可以看到属性的值 `createMode` 。 如果值设置为 **Restore**，则为。 它表示该帐户已从另一个帐户还原。 `restoreParameters`属性有更多详细信息 `restoreSource` ，例如，具有源帐户 ID。 参数中的最后一个 GUID `restoreSource` 是源帐户的 instanceId。

例如，在下面的输出中，源帐户的实例 ID 为 "7b4bb-f6a0-430e-ade1-638d781830cc"

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>在共享的吞吐量数据库中还原共享吞吐量数据库或容器时会发生什么情况？
整个共享吞吐量数据库都会还原。 你无法在共享吞吐量数据库中选择用于还原的容器的子集。

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>在帐户定义中使用 InstanceID 的情况如何？
在任意给定时间点，Azure Cosmos DB 帐户的 "accountName" 属性在活动状态下是全局唯一的。 但是，在删除帐户后，可以创建同名的另一个帐户，因此 "accountName" 不再足以标识帐户的实例。 

ID 或 "instanceId" 是帐户实例的一个属性，它用于在多个帐户之间消除歧义， (live 和 deleted) 如果它们具有相同的还原名称。 可以通过运行或命令获取实例 ID `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` 。 Name 属性值表示 "InstanceID"。

## <a name="next-steps"></a>后续步骤

* 什么是 [连续备份](continuous-backup-restore-introduction.md) 模式？
* 使用 [Azure 门户](continuous-backup-restore-portal.md)、 [PowerShell](continuous-backup-restore-powershell.md)、 [CLI](continuous-backup-restore-command-line.md)或 [Azure 资源管理器](continuous-backup-restore-template.md)配置和管理连续备份。
* [管理](continuous-backup-restore-permissions.md) 以连续备份模式还原数据所需的权限。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)