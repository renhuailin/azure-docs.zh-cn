---
title: 使用 Azure Cosmos DB 中的 Azure 门户配置连续备份和时间点还原。
description: 了解如何识别还原点并使用 Azure 门户配置连续备份。 其中显示了如何还原实时和已删除的帐户。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ee6eedbc078e1b9c07ed00922ce1c37b38410128
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381862"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>配置和管理连续备份和时间点还原 (预览) -使用 Azure 门户
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> 时间点还原功能 (持续备份模式) 用于 Azure Cosmos DB 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 的时间点还原功能 (预览版) 可帮助你从容器内的意外更改中恢复，还原已删除的帐户、数据库或容器，或还原到任何备份存在) 的区域 (。 连续备份模式允许您在过去30天内还原到任何时间点。

本文介绍如何使用 Azure 门户来识别还原点并配置连续备份。

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>预配具有连续备份的帐户

创建新的 Azure Cosmos DB 帐户时，为 " **备份策略** " 选项选择 " **连续** 模式" 以启用新帐户的时间点还原功能。 为帐户启用此功能后，所有数据库和容器均可用于连续备份。 使用时间点还原时，数据始终还原到新帐户，当前无法还原到现有帐户。

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="预配具有连续备份配置的 Azure Cosmos DB 帐户。" border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>从意外修改中还原 live 帐户

你可以使用 Azure 门户来还原 live 帐户或所选的数据库和容器。 使用以下步骤来还原数据：

1. 登录到 [Azure 门户](https://portal.azure.com/)
1. 导航到 Azure Cosmos DB 帐户并打开 " **时间点还原** " 窗格。

   > [!NOTE]
   > 仅当你有权限时，才会填充 Azure 门户中的 "还原" 窗格 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 。 若要了解有关如何设置此权限的详细信息，请参阅 [备份和还原权限](continuous-backup-restore-permissions.md) 一文。

1. 填写以下详细信息以进行还原：

   * **还原点 (UTC)** –在过去30天内的时间戳。 此时间戳应存在该帐户。 可以指定 UTC 格式的还原点。 当你想要还原它时，它可以接近于第二个。 选择 " **单击此处** " 链接，获取有关 [标识还原点的](#event-feed)帮助。

   * **位置** –还原帐户的目标区域。 此帐户应位于给定时间戳的此区域 (例如。 "美国西部" 或 "美国东部") 。 帐户只能还原到源帐户所在的区域。

   * **还原资源** –可以选择 **整个帐户** ，也可以选择要还原的 **所选数据库/容器** 。 数据库和容器应以给定的时间戳存在。 根据所选的还原点和位置，将填充恢复资源，以便用户可以选择需要还原的特定数据库或容器。

   * **资源组** -将在其下创建并还原目标帐户的资源组。 资源组必须已存在。

   * **还原目标帐户** –目标帐户名称。 目标帐户名称需要遵循与创建新帐户时相同的准则。 此帐户将由源帐户所在的同一区域中的还原过程创建。
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="从意外修改 Azure 门户还原 live 帐户。" border="true":::

1. 选择以上参数后，选择 " **提交** " 按钮以开始还原。 还原成本只是一次收费，这取决于给定区域中存储的数据量和费用。 若要了解详细信息，请参阅 [定价](continuous-backup-restore-introduction.md#continuous-backup-pricing) 部分。

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>使用事件源标识还原时间

在 Azure 门户中填写还原点时间时，如果需要帮助识别还原点，请选择 " **单击此处** " 链接，转到 "事件源" 边栏选项卡。 事件源提供创建、替换、删除数据库和源帐户的容器上的事件的完整保真列表。 

例如，如果你想要在删除或更新某个容器之前还原到该点，请检查此事件源。 事件发生时按时间降序显示，最新事件显示在顶部。 可以浏览结果，并选择事件前后的时间进一步缩小时间范围。

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="使用事件源标识还原点时间。" border="true":::

> [!NOTE]
> 事件源不显示对项资源所做的更改。 在过去的30天内，你始终可以手动指定任何时间戳， (在该时间) 进行还原的情况。

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>还原已删除的帐户

你可以使用 Azure 门户在删除帐户的30天内完全还原该帐户。 使用以下步骤还原已删除的帐户：

1. 登录到 [Azure 门户](https://portal.azure.com/)
1. 在全局搜索栏中搜索 *Azure Cosmos DB* 的资源。 其中列出了所有现有帐户。
1. 接下来，选择 " **还原** " 按钮。 "还原" 窗格显示可在保持期内还原的已删除帐户的列表，该列表是从删除时间开始的30天。
1. 选择要还原的帐户。

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="从 Azure 门户还原已删除的帐户。" border="true":::

   > [!NOTE]
   > 注意：只有在具有相应权限的情况下，才会填充 Azure 门户中的 "还原" 窗格 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 。 若要了解有关如何设置此权限的详细信息，请参阅 [备份和还原权限](continuous-backup-restore-permissions.md) 一文。

1. 选择要还原的帐户，并输入以下详细信息来还原已删除的帐户：

   * **还原点 (UTC)** –在过去30天内的时间戳。 此帐户应已存在于该时间戳。 指定 UTC 格式的还原点。 当你想要还原它时，它可以接近于第二个。

   * **Location** -需要还原帐户的目标区域。 源帐户应在给定时间戳的此区域中存在。 例如 "美国西部" 或 "美国东部"。  

   * **资源组** -将在其下创建并还原目标帐户的资源组。 资源组必须已存在。

   * **还原目标帐户** –目标帐户名称需要遵循与创建新帐户时相同的准则。 此帐户将由源帐户所在的同一区域中的还原过程创建。

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>跟踪还原操作的状态

启动还原操作后，选择门户右上角的 " **通知** " 图标。 它提供显示正在还原的帐户状态的链接。 当还原正在进行时，将 *创建* 帐户的状态，还原操作完成后，帐户状态将更改为 " *联机*"。

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="当操作完成时，已还原帐户的状态从 &quot;正在创建&quot; 更改为 &quot;联机&quot;。" border="true":::

## <a name="next-steps"></a>后续步骤

* 使用 [Azure CLI](continuous-backup-restore-command-line.md)、 [PowerShell](continuous-backup-restore-powershell.md)或 [Azure 资源管理器](continuous-backup-restore-template.md)配置和管理连续备份。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
* [管理](continuous-backup-restore-permissions.md) 以连续备份模式还原数据所需的权限。
