---
title: 设置警报
titleSuffix: Azure Digital Twins
description: 请了解如何在 Azure 数字孪生指标上启用警报。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 461d7a82854ce62ee99eef1227c13c7a8f0371e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594871"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>排查 Azure 数字孪生的问题：警报

Azure 数字孪生会收集服务实例的[指标](troubleshoot-metrics.md)，这些指标提供有关资源状态的信息。 可以使用这些指标来评估 Azure 数字孪生服务以及连接到它的资源的整体运行状况。

警报会在监视数据中出现重要状况时主动通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 可以在 [Microsoft Azure 中的警报概述](../azure-monitor/alerts/alerts-overview.md)中详细了解警报。

## <a name="turn-on-alerts"></a>启用警报

下面介绍如何为 Azure 数字孪生实例启用警报：

1. 登录到 [Azure 门户](https://portal.azure.com)并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来查找它。 

2. 从菜单中选择“警报”，然后选择“+ 新建警报规则” 。

3. 在接下来的“创建警报规则”页上，可以按照提示来定义条件、要触发的操作和警报详细信息。     
    * “范围”详细信息中应会自动填写实例的详细信息
    * 你将定义“条件”和“操作组”详细信息，以自定义警报触发器和响应 
    * 在“警报规则详细信息”部分，输入规则的名称和可选说明。 如果希望警报在创建后立即生效，可选中“在创建后启用警报规则”复选框。
        - 还可以在这里选择资源组和“严重性”级别 。

4. 选择“创建警报规则”按钮，以创建警报规则。

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="显示“创建警报规则”页的屏幕截图，其中包含范围、条件、操作组和警报规则详细信息等部分" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

有关填写这些字段的引导式演练，请参阅 [Microsoft Azure 中的警报概述](../azure-monitor/alerts/alerts-overview.md)。 下面是演示 Azure 数字孪生步骤的一些示例。

### <a name="select-conditions"></a>选择条件

下面是“选择条件”过程的摘录，说明了哪些警报信号类型可用于 Azure 数字孪生。 在此页上，可以筛选信号类型，并从列表中选择所需信号。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="显示第一个“配置信号逻辑”页的屏幕截图。“信号类型”框突出显示，用于选择指标或活动日志，其下有可供选择的指标列表":::

在选择信号后，系统会要求你配置警报的逻辑。 可以针对某个维度进行筛选，设置警报的阈值，并设置检查条件的频率。 下面是针对平均路由失败率指标超出 5% 的情况设置警报的示例。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="显示第二个“配置信号逻辑”页的屏幕截图。该页显示了指标历史记录，有一个针对某个维度（如事件网格操作）进行筛选的区域，还有一个用于定义警报逻辑（如“平均值大于 5”）的部分":::

### <a name="verify-success"></a>验证是否成功

在设置警报后，它们会显示在你的实例的“警报”页上。
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="屏幕截图，显示“警报”页和用于添加的按钮。有一条已配置的警报" lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>后续步骤

* 若要详细了解 Azure Monitor 中的警报，请参阅 [Microsoft Azure 中的警报概述](../azure-monitor/alerts/alerts-overview.md)。
* 若要了解 Azure 数字孪生指标，请参阅[故障排除：使用 Azure Monitor 查看指标](troubleshoot-metrics.md)。
* 若要了解如何启用指标的诊断日志记录，请参阅[故障排除：设置诊断](troubleshoot-diagnostics.md)。
