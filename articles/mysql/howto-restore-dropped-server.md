---
title: 还原已删除的 Azure Database for MySQL 服务器
description: 本文介绍如何使用 Azure 门户还原 Azure Database for MySQL 中已删除的服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 9ddf3885931e527e4fd82037d9e82693fb6afa5f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653043"
---
# <a name="restore-a-deleted-azure-database-for-mysql-server"></a>还原已删除的 Azure Database for MySQL 服务器

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

删除服务器后，数据库服务器备份最多可在服务中保留五天。 只能从服务器最初所在的 Azure 订阅访问和还原数据库备份。 可通过以下建议步骤在服务器删除后的 5 天内恢复已删除的 MySQL 服务器资源。 仅当服务器的备份仍可用且未从系统中删除时，建议的步骤才适用。 

## <a name="pre-requisites"></a>先决条件
要还原已删除的 Azure Database for MySQL 服务器，需要执行以下操作：
- 托管原始服务器的 Azure 订阅名称
- 服务器创建位置

## <a name="steps-to-restore"></a>还原步骤

1. 在 Azure 门户中，从“监视”页转到[活动日志](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)。 

2. 在活动日志中，单击“添加筛选器”（如图所示），并设置以下筛选器 

    - **订阅** = 托管已删除服务器的订阅
    - 资源类型 = Azure Database for MySQL 服务器 (Microsoft.DBforMySQL/servers) 
    - 操作 = 删除 MySQL 服务器 (Microsoft.DBforMySQL/servers/delete) 
 
     [![针对删除 MySQL 服务器操作筛选出的活动日志](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. 双击“删除 MySQL 服务器”事件，然后单击“JSON”选项卡，并记下 JSON 输出中的“resourceId”和“submissionTimestamp”属性。 resourceId 的格式如下：/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver。
 
 4. 转到[创建服务器 REST API](/rest/api/mysql/singleserver/servers(2017-12-01)/create) 页，并单击以绿色突出显示的“试用”选项卡，然后使用 Azure 帐户登录。
 
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

9. 服务器创建可能需要一些时间，具体取决于原始服务器上预配的数据库大小和计算资源。 通过在活动日志中筛选以下信息，可以监视还原状态 
   - **订阅** = 你的订阅
   - 资源类型 = Azure Database for MySQL 服务器 (Microsoft.DBforMySQL/servers) 
   - 操作 = 更新 MySQL 服务器创建

## <a name="next-steps"></a>后续步骤
- 如果在五天内尝试还原服务器，但在准确执行前述步骤后仍收到错误，请开启帮助支持事件。 如果在五天后尝试还原已删除的服务器，则会因为找不到备份文件而出错。 在这种情况下，请勿开启支持票证。 如果备份已从系统中删除，则支持团队无法提供任何帮助。 
- 为了防止意外删除服务器，强烈建议使用[资源锁](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)。
