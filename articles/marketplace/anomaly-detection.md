---
title: 在合作伙伴中心管理按流量计费异常 | Azure Marketplace
description: 了解针对按流量计费的自动异常情况检测如何帮助确保根据商业市场产品/服务的定量使用量为客户正确计费。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: sayantanroy83
ms.author: sroy
ms.date: 9/27/2021
ms.openlocfilehash: 5d253ab72d2915145444485c98131027555efdbc
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081589"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>在合作伙伴中心管理按流量计费异常

自定义的按流量计费选项当前可用于[服务型软件](plan-saas-offer.md) (SaaS) 产品/服务和具有托管应用程序计划的 [Azure 应用程序](plan-azure-application-offer.md#types-of-plans)。

如果使用按流量计费选项在商业市场计划中创建产品/服务，使你可以基于非标准单位对使用量进行收费，则需要知道客户何时使用了比预期更多的服务。

## <a name="use-the-anomaly-detection-feature"></a>使用异常情况检测功能

Microsoft 依赖于你（即合作伙伴）来先向客户报告其 SaaS 或 Azure 托管应用程序产品/服务的超额使用量，然后 Microsoft 才向客户开具发票。 如果报告了错误的使用量，则客户可能会收到不正确的发票，从而损害 Microsoft 和合作伙伴的信誉。

为了帮助确保为客户正确计费，请针对 SaaS 应用和 Azure 应用程序托管应用程序计划使用异常情况检测功能。 此功能根据按流量计费监视使用量，并预测在预期范围内的预期使用量值。 如果使用量超出预期范围，则将其视为意外情况（异常），并且你将在合作伙伴中心商业市场计划的“产品/服务概述”页上收到警报通知。 你可以针对你设置的每个自定义计量维度，每天跟踪客户的使用量。

## <a name="view-and-manage-metered-usage-anomalies"></a>查看和管理定量使用量异常

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在“主页”上，选择“见解”磁贴。

    [ ![说明了合作伙伴中心主页上的“见解”磁贴。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 在左侧菜单中，选择“使用情况”。
1. 选择“定量使用量异常”选项卡。

    [![说明“使用情况”页上的“定量使用量异常”选项卡。](./media/anomaly-detection/metered-usage-anomalies-workspaces.png)](./media/anomaly-detection/metered-usage-anomalies-workspaces.png#lightbox)<br>
    图 1：“定量使用量异常”选项卡

1. 对于根据按流量计费检测到的任何使用量异常，将要求你（即发布者）调查并确认异常是否真实。 选择“标记为异常”以确认诊断。

     [![说明“标记为异常”对话框。](./media/anomaly-detection/mark-as-anomaly-workspaces.png)](./media/anomaly-detection/mark-as-anomaly-workspaces.png#lightbox)<br>
    图 2：“标记为异常”对话框

1. 如果你认为我们检测到的超额使用量异常不是真实的，则可以针对合作伙伴中心标记的特定超额使用量异常，选择“不是异常”来提供该反馈。

    [![说明“为什么它不是异常”对话框。](./media/anomaly-detection/why-is-it-not-an-anomaly-workspaces.png)](./media/anomaly-detection/why-is-it-not-an-anomaly-workspaces.png#lightbox)
    图 3：“为什么它不是异常?”对话框

1. 你可以向下滚动页面，查看未确认异常的清单列表。 该列表提供你尚未确认的异常清单。 可以选择将任何合作伙伴中心标记的异常标记为真实或虚假。

   [![说明“使用情况”页上的合作伙伴中心未确认异常的列表。](./media/anomaly-detection/unacknowledged-anomalies-workspaces.png)](./media/anomaly-detection/unacknowledged-anomalies-workspaces.png#lightbox)<br>
    图 4：合作伙伴中心未确认异常的列表

    默认情况下，合作伙伴中心中显示预计财务影响超过 100 美元的标记异常。 但是，可以从“异常的估计财务影响”列表中选择“全部”，以查看所有标记的异常。 

    [![所选产品/服务的所有计量用量异常的屏幕截图。](./media/anomaly-detection/all-anomalies.png)](./media/anomaly-detection/all-anomalies.png#lightbox)

1. 你还会看到一个异常操作日志，其中显示了你对超额使用量所采取的操作。 在该操作日志中，你将能够看到哪些超额使用量事件被标记为真实或虚假。

   [![“使用情况”页面上异常操作日志的图示。](./media/anomaly-detection/anomaly-action-log-workspaces.png)](./media/anomaly-detection/anomaly-action-log-workspaces.png#lightbox)<br>
   ***图 5：异常操作日志***

1. 合作伙伴中心分析将不支持重述导出报表中的超额使用量事件。 你可以通过合作伙伴中心输入针对某个异常的纠正后的超额使用量，并将详细信息传递给 Microsoft 团队进行调查。 根据调查结果，Microsoft 将酌情向多收费的客户发放信用退款。 选择任何标记的异常时，可以选择“标记为异常”以将超额使用量异常标记为真实。

   [![“标记为异常”对话框的图示。](./media/anomaly-detection/mark-as-anomaly-workspaces.png)](./media/anomaly-detection/mark-as-anomaly-workspaces.png#lightbox)<br>
   ***图 6：“标记为异常”对话框***

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航菜单中，选择“商业市场” > “分析” > “使用情况”  。
1. 选择“定量使用量异常”选项卡。

    [![说明“使用情况”页上的“定量使用量异常”选项卡。](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)<br>
    图 1：“定量使用量异常”选项卡

1. 对于根据按流量计费检测到的任何使用量异常，将要求你（即发布者）调查并确认异常是否真实。 选择“标记为异常”以确认诊断。

     [![说明“标记为异常”对话框。](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)<br>
    图 2：“标记为异常”对话框

1. 如果你认为我们检测到的超额使用量异常不是真实的，则可以针对合作伙伴中心标记的特定超额使用量异常，选择“不是异常”来提供该反馈。

    [![说明“为什么它不是异常”对话框。](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    图 3：“为什么它不是异常?”对话框

1. 你可以向下滚动页面，查看未确认异常的清单列表。 该列表提供你尚未确认的异常清单。 可以选择将任何合作伙伴中心标记的异常标记为真实或虚假。

   [![说明“使用情况”页上的合作伙伴中心未确认异常的列表。](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)<br>
    图 4：合作伙伴中心未确认异常的列表

    默认情况下，合作伙伴中心中显示预计财务影响超过 100 美元的标记异常。 但是，可以从“异常的估计财务影响”列表中选择“全部”，以查看所有标记的异常。 

    :::image type="content" source="./media/anomaly-detection/all-anomalies.png" alt-text="所选产品/服务的所有计量用量异常的屏幕截图。":::

1. 你还会看到一个异常操作日志，其中显示了你对超额使用量所采取的操作。 在该操作日志中，你将能够看到哪些超额使用量事件被标记为真实或虚假。

   [![“使用情况”页面上异常操作日志的图示。](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox)<br>
   ***图 5：异常操作日志***

1. 合作伙伴中心分析将不支持重述导出报表中的超额使用量事件。 你可以通过合作伙伴中心输入针对某个异常的纠正后的超额使用量，并将详细信息传递给 Microsoft 团队进行调查。 根据调查结果，Microsoft 将酌情向多收费的客户发放信用退款。 选择任何标记的异常时，可以选择“标记为异常”以将超额使用量异常标记为真实。

   [![“标记为异常”对话框的图示。](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox)<br>
   ***图 6：“标记为异常”对话框***

---

首次在合作伙伴中心内将超额使用量标记为不正常时，从该实例起，你将有 30 天的时间将该异常标记为真实或虚假。 30 天后，你（即发布者）将无法对该异常采取任何操作。

> [!IMPORTANT]
> 少报的超额使用量单位不符合重述和财务调整的条件。

你可以查看选定计算周期的所有异常（已确认或其他）。 各种计算周期包括：过去 30 天、过去 60 天和过去 90 天。

> [!IMPORTANT]
> 请你在合作伙伴中心内首次报告异常后 30 天的时间内对标记的异常采取操作。

通过小组件中的筛选器模式，你可以选择单个产品/服务和单个自定义计量器。

将超额使用量标记为异常或确认将异常标记为真实的模型后，便无法将选择更改为“不是异常”。

> [!IMPORTANT]
> 如果发生超额收费情况，则可以重新提交超额使用量。

---

## <a name="see-also"></a>请参阅
- [使用商业市场计量服务的 SaaS 按流量计费](./partner-center-portal/saas-metered-billing.md)
- [托管应用程序按流量计费](marketplace-metering-service-apis.md)
- [针对按流量计费的异常情况检测服务](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)
