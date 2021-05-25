---
title: 还原已删除的 Azure Database for PostgreSQL 服务器
description: 本文介绍了如何使用 Azure 门户还原 Azure Database for PostgreSQL 中已删除的服务器。
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652919"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>还原已删除的 Azure Database for PostgreSQL 服务器

在服务器被删除后，数据库服务器备份最多可在服务中保留五天。 只能从服务器最初所在的 Azure 订阅访问和还原数据库备份。 如果 PostgreSQL 服务器资源被删除，请在删除后的 5 天内按照以下建议步骤进行恢复。 仅当服务器的备份仍可用且未从系统中删除时，建议的步骤才适用。 

## <a name="pre-requisites"></a>先决条件
若要还原已删除的 Azure Database for PostgreSQL 服务器，你需要以下：
- 托管原始服务器的 Azure 订阅名称
- 服务器创建位置

## <a name="steps-to-restore"></a>还原步骤

1. 浏览到 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)。 依次选择“Azure Monitor”服务和“活动日志”。

2. 在“活动日志”中，单击“添加筛选器”（如图所示），并设置以下筛选器

    - 订阅 = 托管已删除的服务器的订阅
    - 资源类型 = Azure Database for PostgreSQL 服务器 (Microsoft.DBforPostgreSQL/servers)
    - 操作 = 删除 PostgreSQL 服务器 (Microsoft.DBforPostgreSQL/servers/delete)
 
    ![针对“删除 PostgreSQL 服务器”操作筛选出的活动日志](./media/howto-restore-dropped-server/activity-log-azure.png)

3. 依次选择“删除 PostgreSQL 服务器”事件和“JSON”选项卡。复制 JSON 输出中的 `resourceId` 和 `submissionTimestamp` 属性。 resourceId 格式如下：`/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver`。


 4. 转到 PostgreSQL [创建服务器 REST API](/rest/api/PostgreSQL/servers/create) 页，然后选择以绿色突出显示的“试调用”选项卡。 使用 Azure 帐户登录。

 5. 根据在上面第 3 步中捕获的 resourceId 属性 JSON 值，提供 resourceGroupName、serverName（已删除的服务器名称）、subscriptionId 属性。 api-version 属性是预先填充的，可以保持原样，如下图所示。

    ![使用 REST API 创建服务器](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. 滚动下面的“请求正文”部分，并粘贴以下替换“已删除的服务器位置”、“submissionTimestamp”和“resourceId”。 对于“restorePointInTime”，指定值为“submissionTimestamp”减去 15 分钟，以确保命令不会出错。
    
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

    例如，如果当前时间为 2020-11-02T23:59:59.0000000Z，建议还原点时间至少提前 15 分钟，即 2020-11-02T23:44:59.0000000Z。

    > [!Important]
    > 在服务器被删除后有五天的时间限制。 五天后，由于找不到备份文件，预计会出现错误。
    
7. 如果看到响应代码 201 或 202，则表示已成功提交还原请求。 

    服务器创建可能需要一些时间，具体取决于原始服务器上预配的数据库大小和计算资源。 通过在活动日志中筛选以下信息，可以监视还原状态 
   - 订阅 = 你的订阅
   - 资源类型 = Azure Database for PostgreSQL 服务器 (Microsoft.DBforPostgreSQL/servers) 
   - 操作 = 更新 PostgreSQL 服务器创建

## <a name="next-steps"></a>后续步骤
- 如果你尝试在五天内还原服务器，但在准确地执行了前面所述的步骤后仍看到错误，请开立支持事件来获取帮助。 如果你尝试在五天后还原已删除的服务器，则预计会出现错误，因为找不到备份文件。 在这种情况下，请勿开立支持票证。 如果备份已从系统中删除，则支持团队无法提供任何帮助。 
- 为了防止意外删除服务器，强烈建议使用[资源锁](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)。
