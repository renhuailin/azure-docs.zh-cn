---
title: 在 Azure 门户中恢复删除的仪表板
description: 如果在 Azure 门户中删除了已发布的仪表板，可以恢复该仪表板。
ms.date: 03/25/2021
ms.topic: troubleshooting
ms.openlocfilehash: 96d330872327f8719e7cc4287415d86fd0ae5fd4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559701"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>在 Azure 门户中恢复删除的仪表板

如果你在全球 Azure 云中，并在 Azure 门户中删除了已发布的仪表板，则可以在删除后的 14 天内恢复此仪表板。 如果你在 Azure 政府云中或仪表板未发布，则无法恢复它，必须重新生成它。 有关发布仪表板的详细信息，请参阅[发布仪表板](azure-portal-dashboard-share-access.md#publish-a-dashboard)。 请按照以下步骤恢复已发布的仪表板：

1. 从 Azure 门户菜单中，选择“资源组”，然后选择你在其中发布了仪表板的资源组（默认情况下，此资源组名为“仪表板”）。

1. 在“活动日志”下，展开“删除仪表板”操作。 选择“更改历史记录”选项卡，然后选择“\<deleted resource\>”。

    ![“更改历史记录”选项卡的屏幕截图](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. 选择并复制左窗格中的内容，然后将其保存到文件扩展名为 _.json_ 的一个文本文件中。 门户使用此 JSON 文件重新创建仪表板。

    ![更改历史记录差异的屏幕截图](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. 在 Azure 门户菜单上，选择“仪表板”，然后选择“上传”。

    ![仪表板上传屏幕截图](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. 选择你保存的 JSON 文件。 门户将使用与已删除仪表板相同的名称和元素重新创建仪表板。

1. 选择“共享”以发布仪表板并重新建立相应的访问控制。

    ![仪表板共享屏幕截图](media/recover-shared-deleted-dashboard/dashboard-share.png)
