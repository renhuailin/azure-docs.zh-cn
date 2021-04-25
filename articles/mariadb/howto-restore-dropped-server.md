---
title: 还原删除的 Azure Database for MariaDB 服务器
description: 本文介绍如何使用 Azure 门户在 Azure Database for MariaDB 中还原删除的服务器。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/12/2021
ms.openlocfilehash: 7cf2ce61239c7f2320f7b789acbb8f340e6de471
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315182"
---
# <a name="restore-a-deleted-azure-database-for-mariadb-server"></a>还原删除的 Azure Database for MariaDB 服务器

删除服务器后，数据库服务器备份最多可在服务中保留五天。 只能从服务器最初所在的 Azure 订阅访问和还原数据库备份。 按照以下建议步骤可在删除服务器后 5 天内恢复删除的 MariaDB 服务器资源。 仅当服务器的备份仍可用且未从系统中删除时，建议的步骤才适用。 

## <a name="pre-requisites"></a>先决条件
若要还原已删除的 Azure Database for MariaDB 服务器，需要以下信息：
- 托管原始服务器的 Azure 订阅名称
- 之前创建服务器的位置

## <a name="steps-to-restore"></a>还原步骤

1. 在 Azure 门户中，从“监视”页转到[活动日志](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)。 

2. 在活动日志中，单击“添加筛选器”（如图所示），并设置以下筛选器 

    - **订阅** = 托管已删除服务器的订阅
    - **资源类型** = Azure Database for MariaDB 服务器 (Microsoft.DBForMariaDB/servers) 
    - **操作** = 删除 MariaDB 服务器 (Microsoft.DBForMariaDB/servers/delete) 
 
     [![为删除 MySQL 服务器操作筛选出的活动日志](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. 双击“删除 MySQL 服务器”事件，然后单击“JSON”选项卡，并记下 JSON 输出中的“resourceId”和“submissionTimestamp”属性。 resourceId 的格式如下：/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBForMariaDB/servers/deletedserver。
 
 4. 转到[创建服务器 REST API](/rest/api/mariadb/servers/create) 页，并单击以绿色突出显示的“试用”选项卡，然后使用 Azure 帐户登录。
 
 5. 提供 resourceGroupName、serverName（已删除的服务器名称）、subscriptionId（基于步骤 3 中获取的 resourceId 属性派生），而 api-version 已预先填充，如图所示。
 
     [![使用 REST API 创建服务器](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. 滚动到“请求正文”部分，并粘贴以下内容：
     
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

7. 替换上述请求正文中的以下值：
   * “Dropped Server Location”，替换为最初创建已删除的服务器的 Azure 区域
   * “submissionTimestamp”和“resourceId”，替换为在步骤 3 中获取的值。 
   * 对于“restorePointInTime”，请指定将“submissionTimestamp”减去 15 分钟后得到的值，以确保命令不出错。

8. 如果看到响应代码 201 或 202，则表示已成功提交还原请求。 

9. 服务器创建可能需要一些时间，具体取决于原始服务器上预配的数据库大小和计算资源。 通过在活动日志中筛选以下信息可监视还原状态 
   - **订阅** = 你的订阅
   - **资源类型** = Azure Database for MariaDB 服务器 (Microsoft.DBForMariaDB/servers) 
   - **操作** = 更新 MariaDB 服务器创建

## <a name="next-steps"></a>后续步骤
- 如果在五天内尝试还原服务器，但在准确执行前述步骤后仍收到错误，请开启帮助支持事件。 如果在五天后尝试还原已删除的服务器，则会因为找不到备份文件而出错。 在这种情况下，请勿开启支持票证。 如果已从系统中删除备份，则支持团队也无法提供任何帮助。 
- 为防止意外删除服务器，强烈建议使用[资源锁](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)。
