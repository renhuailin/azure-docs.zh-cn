---
title: 跨区域移动仪表板
description: 使用“跨区域移动仪表板”监视跨区域移动的资源。
author: Aarthi-Vijayaraghavan
manager: sutalasi
ms.service: resource-move
ms.topic: how-to
ms.date: 08/30/2021
ms.author: AarthiV
ms.openlocfilehash: 9339b61135d4b8b21f88b35a842a080dac9f8e69
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620861"
---
# <a name="move-across-region-dashboard"></a>跨区域移动仪表板
本文介绍如何通过 Azure 资源转移器中的“跨区域移动仪表板”来监视跨区域移动的资源。 
## <a name="monitor-via-the-dashboard"></a>通过仪表板监视
1. 在“Azure 资源转移器”中，选择左侧导航窗格中的“概览”。  可以在两个页面“入门指南”和“跨区域移动仪表板”之间切换。  “入门指南”页面提供了跨订阅、跨资源组和跨区域移动资源的选项。
“跨区域移动仪表板”页面将跨区域移动的所有监视信息集中在一个地方。
    [![“跨区域移动仪表板”选项卡](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)
2. 仪表板将列出用户创建的所有移动组合。 以下两部分用于捕获跨区域移动的状态。
    在“按移动状态列出的资源”中，监视每个状态的资源百分比和数量。
    在“错误摘要”中，监视活跃错误，需要首先解决这些错误，然后才能成功移动到目标区域。
    [![状态和问题部分](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)
> [!NOTE]
> 仪表板中仅会列出在所选订阅中已创建的源/目标组合。

3. 使用筛选器来选择偏好的“订阅”、“源区域”和“目标区域”。  
    [![筛选器](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)
4. 通过选择源/目标旁边的“查看所有资源”导航到详细信息页面。
    [![详细信息](media\move-across-region-dashboard\move-across-region-dashboard-details.png)](media\move-across-region-dashboard\move-across-region-dashboard-details.png)
## <a name="next-steps"></a>后续步骤
[了解](about-move-process.md)移动过程。
