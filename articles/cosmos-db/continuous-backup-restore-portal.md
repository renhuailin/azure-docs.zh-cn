---
title: 使用 Azure 门户在 Azure Cosmos DB 中配置连续备份和时间点还原。
description: 了解如何识别还原点并使用 Azure 门户配置连续备份。 它介绍了如何还原已删除的实时帐户。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ee6eedbc078e1b9c07ed00922ce1c37b38410128
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381862"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>使用 Azure 门户配置和管理连续备份和时间点还原（预览版）
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB 的时间点还原功能（连续备份模式）目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 的时间点还原功能（预览版）可帮助你从容器内的意外更改中恢复，还原已删除的帐户、数据库或容器，或者还原到任何区域（需存在备份）。 通过连续备份模式，可还原到过去 30 天内的任何时间点。

本文介绍了如何识别还原点并使用 Azure 门户配置连续备份。

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>为帐户预配连续备份

创建新的 Azure Cosmos DB 帐户时，为“备份策略”选项选择“连续”模式，启用新帐户的时间点还原功能。 为帐户启用此功能后，所有数据库和容器均可用于连续备份。 使用时间点还原时，数据将始终还原到新帐户，当前还无法还原到现有帐户。

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="为 Azure Cosmos DB 帐户预配连续备份配置。" border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>将实时帐户从意外修改中还原

可以使用 Azure 门户来还原实时帐户或该帐户下所选的数据库和容器。 还原数据需要完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)
1. 导航到 Azure Cosmos DB 帐户，打开“时间点还原”窗格。

   > [!NOTE]
   > 只有在你拥有 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限的情况下，才会填充 Azure 门户中的“还原”窗格。 若要了解有关如何设置此权限的详细信息，请参阅[备份和还原权限](continuous-backup-restore-permissions.md)一文。

1. 填写以下详细信息以进行还原：

   * **还原点 (UTC)** – 在过去 30 天内的时间戳。 该帐户应存在于该时间戳下。 可以指定 UTC 格式的还原点。 如果你希望还原，可将该还原点设为与第二个还原点相接近。 选择“单击此处”，获取有关[标识还原点](#event-feed)的帮助。

   * **位置** – 还原帐户的目标区域。 此帐户应存在于规定时间戳以及此区域中（例如 美国西部或美国东部）。 帐户只能还原到源帐户所在的区域。

   * **还原资源** – 可选择“整个帐户”，也可选择要还原的“所选数据库/容器”。 数据库和容器应存在于规定时间戳下。 根据所选的还原点和位置，将填充恢复资源，以便用户可以选择需要还原的特定数据库或容器。

   * **资源组** - 将在该资源组下创建并还原目标帐户。 此资源组必须已存在。

   * **还原目标帐户** – 目标帐户名称。 目标帐户名称需要遵循与创建新帐户时相同的准则。 此帐户将通过源帐户所在的同一区域中的还原过程来创建。
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="使用 Azure 门户将实时帐户从意外修改中还原。" border="true":::

1. 选择以上参数后，选择“提交”按钮，开始还原。 还原费用是一次性收费，根据规定区域的数据量和存储收费情况而定。 如要了解详细信息，请参阅[定价](continuous-backup-restore-introduction.md#continuous-backup-pricing)部分。

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>使用事件源来标识还原时间

在 Azure 门户中填写还原点时间时，如果需要帮助识别还原点，请选择“单击此处”，转到“事件源”边栏选项卡。 事件源提供了创建、替换、删除数据库和源帐户容器上的事件的完整保真列表。 

例如，如果要在删除或更新某个容器之前还原到该点，请检查此事件源。 事件将按事件发生时的时间降序显示，最新事件显示在顶部。 用户可以浏览结果，并选择事件前后的时间以进一步缩小时间范围。

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="使用事件源来标识还原时间。" border="true":::

> [!NOTE]
> 事件源不会显示对项资源所做的更改。 你可以随时手动指定过去 30 天内的任何时间戳（只要在该时间戳有账户存在）进行还原。

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>还原已删除的帐户

可使用 Azure 门户在删除帐户的 30 天内完全还原该帐户。 完成以下步骤还原已删除的帐户：

1. 登录到 [Azure 门户](https://portal.azure.com/)
1. 在全局搜索栏中搜索“Azure Cosmos DB”资源。 随后将列出所有现有帐户。
1. 接下来，选择“还原”按钮。 “还原”窗格将显示可在保持期内还原的已删除帐户列表，保留期为自删除时间起 30 天。
1. 选择要还原的帐户。

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="从 Azure 门户还原已删除的帐户。" border="true":::

   > [!NOTE]
   > 注：只有在你拥有 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限的情况下，才会填充 Azure 门户中的“还原”窗格。 若要了解有关如何设置此权限的详细信息，请参阅[备份和还原权限](continuous-backup-restore-permissions.md)一文。

1. 选择要还原的帐户，并输入以下详细信息还原已删除的帐户：

   * **还原点 (UTC)** – 在过去 30 天内的时间戳。 该时间戳下应已存在该账户。 指定 UTC 格式的还原点。 如果你希望还原，可将该还原点设为与第二个还原点相接近。

   * **位置** – 还原帐户的目标区域。 源帐户应存在于规定时间戳下以及此区域中。 例如，美国西部或美国东部。  

   * **资源组** - 将在该资源组下创建并还原目标帐户。 此资源组必须已存在。

   * **还原目标帐户** - 目标帐户名称需要遵循与创建新帐户时相同的准则。 此帐户将通过源帐户所在的同一区域中的还原过程来创建。

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>跟踪还原操作的状态

启动还原操作后，选择门户右上角的“通知”钟形图标。 它将提供一个显示正在还原的帐户状态的链接。 在还原过程中，帐户状态将为“正在创建”，还原操作完成后，帐户状态将更改为“联机”。

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="当操作完成时，已还原帐户的状态从“正在创建”更改为“联机”。" border="true":::

## <a name="next-steps"></a>后续步骤

* 使用 [Azure CLI](continuous-backup-restore-command-line.md)、[PowerShell](continuous-backup-restore-powershell.md)、[Azure 资源管理器](continuous-backup-restore-template.md)配置和管理连续备份。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
* [管理](continuous-backup-restore-permissions.md)以连续备份模式还原数据所需的权限。
