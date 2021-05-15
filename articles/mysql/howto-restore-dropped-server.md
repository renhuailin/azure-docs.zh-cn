---
title: 还原已删除的 Azure Database for MySQL 服务器
description: 本文介绍如何使用 Azure 门户还原 Azure Database for MySQL 中已删除的服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 34dddd8e5f3fb418fc7155630bf82a922e418402
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657084"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>还原已删除的 Azure Database for MySQL 服务器

删除服务器后，数据库服务器备份最多可在服务中保留五天。 只能从该服务器起初所在的 Azure 订阅访问和还原数据库备份。 可通过以下建议步骤在服务器删除后的 5 天内恢复已删除的 MySQL 服务器资源。 仅当服务器的备份仍可用且未从系统中删除时，建议的步骤才适用。 

## <a name="pre-requisites"></a>先决条件
要还原已删除的 Azure Database for MySQL 服务器，需要执行以下操作：
- 托管原始服务器的 Azure 订阅名称
- 之前创建服务器的位置

## <a name="steps-to-restore"></a>还原步骤

1. 在 Azure 门户，从“监视”页转到[活动日志](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)。 

2. 在活动日志中，单击“添加筛选器”（如图所示），并设置以下筛选器 

    - 订阅 = 托管已删除服务器的订阅
    - 资源类型 = Azure Database for MySQL 服务器 (Microsoft.DBforMySQL/servers) 
    - 操作 = 删除 MySQL 服务器 (Microsoft.DBforMySQL/servers/delete) 
 
     [![针对删除 MySQL 服务器操作筛选出的活动日志](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. 双击“删除 MySQL 服务器”事件，然后单击“JSON”选项卡，并记下 JSON 输出中的“resourceId”和“submissionTimestamp”属性。 resourceId 的格式如下：/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver。
 
 4. 转到[创建服务器 REST API 页](/rest/api/mysql/servers/create)，并单击以绿色突出显示的“试用”选项卡，然后用 Azure 帐户登录。
 
 5. 提供 resourceGroupName、serverName（已删除的服务器名称）、subscriptionId（基于步骤 3 中获取的 resourceId 属性派生），而 api-version 已预先填充，如图所示。
 
     [![使用 REST API 创建服务器](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. 向下滚动到“请求正文”部分，并粘贴以下内容来替换“已删除的服务器位置”、“submissionTimestamp”和“resourceId”。 对于“restorePointInTime”，指定一个值，使其等于“submissionTimestamp”减去 15 分钟，以确保命令不会出错。
 
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

7. 如果看到响应代码 201 或 202，则表示还原请求已成功提交。 

8. 服务器创建可能需要一些时间，具体取决于原始服务器上预配的数据库大小和计算资源。 通过在活动日志中进行筛选可监视还原状态 
   - 订阅 = 自己的订阅
   - 资源类型 = Azure Database for MySQL 服务器 (Microsoft.DBforMySQL/servers) 
   - 操作 = 更新 MySQL 服务器创建

## <a name="next-steps"></a>后续步骤
- 如果在五天内尝试还原服务器，但在准确执行前面所述的步骤后仍然收到错误，请打开支持事件以获取帮助。 如果在五天后尝试还原已删除的服务器，则会因为找不到备份文件而出现错误。 在这种情况下，请勿打开支持票证。 如果备份已从系统中删除，则支持团队无法提供任何帮助。 
- 为防止意外删除服务器，我们强烈建议使用[资源锁](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)。