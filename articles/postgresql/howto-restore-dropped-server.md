---
title: 还原已删除的 Azure Database for PostgreSQL 服务器
description: 本文介绍如何使用 Azure 门户在 Azure Database for PostgreSQL 中还原已删除的服务器。
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652919"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>还原已删除的 Azure Database for PostgreSQL 服务器

删除服务器后，可以在服务中将数据库服务器备份最多保留五天。 只能从服务器最初驻留的 Azure 订阅访问和还原数据库备份。 以下建议步骤可用于在服务器删除后的5天内恢复已删除的 PostgreSQL 服务器资源。 仅当服务器的备份仍可用且未从系统中删除时，建议的步骤才适用。 

## <a name="pre-requisites"></a>先决条件
若要还原已删除的 Azure Database for PostgreSQL 服务器，需要执行以下操作：
- 托管原始服务器的 Azure 订阅名称
- 创建服务器的位置

## <a name="steps-to-restore"></a>要还原的步骤

1. 浏览到 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)。 选择 **Azure Monitor** 服务，然后选择 " **活动日志**"。

2. 在活动日志中，单击 " **添加筛选器** " （如下所示），并设置以下筛选器：

    - **订阅** = 托管已删除服务器的订阅
    - **资源类型** = DBforPostgreSQL/服务器 (Azure Database for PostgreSQL 服务器) 
    - **Operation** = 删除 PostgreSQL Server (DBforPostgreSQL/servers/delete) 
 
    ![已筛选用于删除 PostgreSQL 服务器操作的活动日志](./media/howto-restore-dropped-server/activity-log-azure.png)

3. 选择 " **删除 PostgreSQL 服务器** " 事件，然后选择 " **JSON" 选项卡**。 `resourceId` `submissionTimestamp` 在 JSON 输出中复制和特性。 ResourceId 的格式如下： `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` 。


 4. 浏览到 "PostgreSQL [创建服务器 REST API" 页](/rest/api/PostgreSQL/servers/create) ，然后选择 " **试用** " 选项卡以绿色突出显示。 使用 Azure 帐户登录。

 5. 基于前面步骤3中捕获的 resourceId 属性 JSON 值，提供 **resourceGroupName**， **serverName** (删除的服务器名称) ， **subscriptionId** 属性。 Api 版本属性是预先填充的，可以按原样保留，如下图所示。

    ![使用 REST API 创建服务器](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. 滚动到 "请求正文" 部分，并粘贴以下内容替换 "删除的服务器位置"、"submissionTimestamp" 和 "resourceId"。 对于 "restorePointInTime"，请将 "submissionTimestamp" 值指定为减 **15 分钟** ，以确保命令不会出错。
    
    ```json
    {
      "location": "Dropped Server Location",  
      "properties": 
      {
        "restorePointInTime": "submissionTimestamp - 15 minutes",
        "createMode": "PointInTimeRestore",
        "sourceServerId": "resourceId"
      }
    }
    ```

    例如，如果当前时间为 2020-11-02T23：59： 59.0000000 Z，则建议在最早15分钟的时间为 2020-11-02T23：44： 59.0000000 Z 的还原点之前。

    > [!Important]
    > 删除服务器后5天的时间限制。 5天后，会出现错误，因为找不到备份文件。
    
7. 如果看到响应代码201或202，则已成功提交还原请求。 

    服务器创建可能需要一些时间，具体取决于原始服务器上预配的数据库大小和计算资源。 可以通过筛选来监视活动日志中的还原状态 
   - **订阅** = 你的订阅
   - **资源类型** = DBforPostgreSQL/服务器 (Azure Database for PostgreSQL 服务器)  
   - **Operation** = Update PostgreSQL Server Create

## <a name="next-steps"></a>后续步骤
- 如果尝试在5天内还原服务器，并且在准确遵循前面所述的步骤后仍然收到错误，请打开支持事件以获得帮助。 如果在5天后尝试还原已删除的服务器，则会出现错误，因为找不到该备份文件。 在这种情况下，请勿打开支持票证。 如果从系统中删除备份，则支持团队无法提供任何帮助。 
- 为了防止意外删除服务器，我们强烈建议使用 [资源锁](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)。
