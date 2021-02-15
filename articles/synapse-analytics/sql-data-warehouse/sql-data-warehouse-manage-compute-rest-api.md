---
title: 暂停、恢复、缩放用于专用 SQL 池 (以前的 SQL DW) 的 REST Api
description: 通过 REST API 在 Azure Synapse Analytics 中管理专用 SQL 池（以前称为 SQL DW）的计算能力。
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c04f61aaef5f5072ce0fb39ff111ba07ee151700
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375895"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中专用 SQL 池（以前称为 SQL DW）的 REST API

用于管理 Azure Synapse Analytics 中专用 SQL 池（以前称为 SQL DW）的计算的 REST API。

## <a name="scale-compute"></a>缩放计算

若要更改数据仓库单位，请使用[创建或更新数据库](/rest/api/sql/databases/createorupdate) REST API。 以下示例将托管在服务器 MyServer 上的数据库 MySQLDW 的数据仓库单位设置为 DW1000。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>暂停计算

若要暂停数据库，请使用[暂停数据库](/rest/api/sql/databases/pause) REST API。 以下示例将暂停 Server01 服务器上托管的 Database02 数据库。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>恢复计算

若要启动数据库，请使用[恢复数据库](/rest/api/sql/databases/resume) REST API。 以下示例将启动 Server01 服务器上托管的 Database02 数据库。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>检查数据库状态

> [!NOTE]
> 数据库完成联机数据流即检查数据库状态会返回 ONLINE，导致产生连接错误。 如果你正在通过该 API 调用来触发连接尝试，可能需要在应用程序代码中添加 2 到 3 分钟的延迟。

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>获取维护计划

检查已为专用 SQL 池（以前称为 SQL DW）设置的维护计划。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>设置维护计划

设置和更新现有专用 SQL 池（以前称为 SQL DW）的维护计划。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[管理计算](sql-data-warehouse-manage-compute-overview.md)。
