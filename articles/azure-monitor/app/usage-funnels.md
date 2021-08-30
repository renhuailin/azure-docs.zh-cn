---
title: Application Insights 漏斗图
description: 了解如何使用漏斗图来发现客户与你的应用程序的交互方式。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: 0c0ab7da554b85f9adcde4d4991e1271f2acb433
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732006"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>使用 Application Insights 漏斗图了解客户如何使用你的应用程序

了解客户体验对你的业务而言至关重要。 如果你的应用程序涉及多个阶段，那么需要知道是否大多数客户正在完成整个进程，或者他们是否即将在某个时间点结束进程。 Web 应用程序中通过一系列步骤完成的进度被称为“漏斗图”。 可使用 Application Insights 漏斗图深入了解你的用户，并监视分步转换率。 

## <a name="create-your-funnel"></a>创建漏斗图
在创建漏斗图之前，需要决定想要回答的问题。 例如，你可能想要知道多少个用户正在查看主页、查看客户配置文件和创建票证。 

创建漏斗图：

1. 在“漏斗图”选项卡中选择“编辑” 。
1. 选择你的“第一步”。

     :::image type="content" source="./media/usage-funnels/funnel.png" alt-text="“漏斗”选项卡的屏幕截图，其中选择了“编辑”选项卡上的步骤。" lightbox="./media/usage-funnels/funnel.png":::

1. 要将筛选器应用到步骤，请选择“添加筛选器”，它会在为第一步选择项目后出现。
1. 然后选择“第二步”，依此类推。
1. 选择“视图”选项卡，以查看漏斗图结果

      :::image type="content" source="./media/usage-funnels/funnel-2.png" alt-text="“视图”选项卡上“漏斗图”选项卡的屏幕截图，其中显示第一步和第二步的结果。" lightbox="./media/usage-funnels/funnel-2.png":::

1. 要保存漏斗图以便下次查看，请选择顶部“保存”。 可使用“打开”以打开保存的漏斗图。

### <a name="funnels-features"></a>漏斗图功能

- 如果对应用进行采样，你会看到采样横幅。 选择标题栏打开上下文窗格，其中说明了如何关闭采样。 
- 选择一个步骤以在右侧查看更多详细信息。 
- 历史转换图显示过去 90 天内的兑换率。 
- 通过访问用户工具更好地了解用户。 可在每个步骤中使用筛选器。 

## <a name="next-steps"></a>后续步骤
  * [使用情况概述](usage-overview.md)
  * [用户、会话和事件](usage-segmentation.md)
  * [保留](usage-retention.md)
  * [工作簿](../visualize/workbooks-overview.md)
  * [添加用户上下文](./usage-overview.md)
  * [导出到 Power BI](./export-power-bi.md)