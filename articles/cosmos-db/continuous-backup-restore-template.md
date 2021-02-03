---
title: 使用 ARM 模板在 Azure Cosmos DB 中配置连续备份和时间点还原。
description: 了解如何使用 Azure 资源管理器模板为帐户预配连续备份和还原数据。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6c388a08a589cc89d83b7178e31e3f4497b924bb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527305"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-resource-manager-templates"></a>配置和管理连续备份和时间点还原-使用 Azure 资源管理器模板
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB 的时间点还原功能可帮助你从容器内的意外更改中恢复，还原已删除的帐户、数据库或容器，或还原到) 存在备份的任何区域 (。 连续备份模式允许您在过去30天内还原到任何时间点。

本文介绍如何使用 Azure 资源管理器模板为帐户预配连续备份和还原数据。

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>预配具有连续备份的帐户

可以使用 Azure 资源管理器模板来部署具有连续模式的 Azure Cosmos DB 帐户。 定义用于预配帐户的模板时，请包括 "backupPolicy" 参数，如以下示例中所示：

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

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>使用资源管理器模板进行还原

你还可以使用资源管理器模板还原帐户。 定义模板时，请提供以下参数：

* 将 "createMode" 参数设置为 "Restore"
* 定义 "restoreParameters"，请注意，"restoreSource" 值是从源帐户的命令输出中提取的 `az cosmosdb restorable-database-account list` 。 帐户名称的实例 ID 属性用于执行还原。
* 将 "restoreMode" 参数设置为 "PointInTime" 并配置 "restoreTimestampInUtc" 值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

接下来，使用 Azure PowerShell 或 CLI 部署模板。 下面的示例演示如何使用 CLI 命令部署模板：  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>后续步骤

* 使用 [Azure CLI](continuous-backup-restore-command-line.md)、 [PowerShell](continuous-backup-restore-command-line.md)或 [Azure 门户](continuous-backup-restore-portal.md)配置和管理连续备份。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
* [管理](continuous-backup-restore-permissions.md) 以连续备份模式还原数据所需的权限。