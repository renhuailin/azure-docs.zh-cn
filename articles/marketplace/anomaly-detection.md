---
title: 管理合作伙伴中心的计费异常 |Azure Marketplace
description: 了解计费计费的自动异常情况检测如何帮助确保客户正确计费，以按流量计费方式使用商业 marketplace 产品。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 2/18/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 1ff6262a75261c575082f3fc48d588c868ad0b51
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092527"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>管理合作伙伴中心的计费异常

自定义的按流量计费选项目前可用于 [软件即服务](plan-saas-offer.md) (SaaS) 产品和使用托管应用程序计划的 [Azure 应用程序](plan-azure-application-offer.md#types-of-plans) 。

如果你使用 "按流量计费" 选项在 "商业 marketplace 计划" 中创建产品/服务，以便根据非标准单位对使用情况收费，则需要了解客户在使用超过预期的服务时的情况。

## <a name="use-the-anomaly-detection-feature"></a>使用异常检测功能

Microsoft 在向客户开具发票之前，会向你提供合作伙伴，以报告客户对其 SaaS 或 Azure 托管应用程序的使用情况。 如果报告了错误的使用情况，则客户可能会收到错误的发票，从而损坏 Microsoft 和合作伙伴的信誉。

为了帮助确保客户的计费正确，请将 **异常检测** 功能用于 SaaS 应用和 Azure 应用程序托管应用程序计划。 此功能监视按流量计费的使用情况，并预测预期范围内的预期使用值。 如果使用情况不在预期范围内，则将其视为意外， (异常情况 ) ，你将在合作伙伴中心的商业市场计划中的产品/服务概述页上收到警报通知。 对于已设置的每个自定义计量维度，你都可以跟踪客户的每日使用情况。

## <a name="view-and-manage-metered-usage-anomalies"></a>查看和管理按流量计费的异常

1. 登录到 [合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航菜单中，选择 "**商业市场**  >  **分析**"。
1. 选择 " **按流量计费的异常** " 选项卡。

    [![说明 "使用情况" 页上的按流量计费的异常选项卡。](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***图1：计量使用情况异常选项卡***

1. 对于针对按流量计费而检测到的任何使用情况异常，作为发行者，系统会要求调查并确认异常是否为 true。 选择 " **标记为异常** " 以确认诊断。

     [![说明 "标记为异常" 对话框。](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***图2：标记为异常对话框***

1. 如果你认为我们检测到的超额使用异常不是正版，则可以通过选择不是特定超额使用的合作伙伴中心标记异常的 **异常** 来提供此反馈。

    [![说明为什么它不是异常对话框。](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***图3：为何不是异常？对话框***

1. 可以向下滚动页面以查看未确认异常的清单列表。 此列表提供了尚未确认的异常清单。 可以选择将任何合作伙伴中心标记为的异常标记为正版或 false。

   [![说明 "使用情况" 页上的 "合作伙伴中心未确认的异常" 列表。](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***图4：合作伙伴中心未确认的异常列表***

1. 你还会看到异常操作日志，其中显示了你对超额使用情况所采取的操作。 在操作日志中，你将能够看到哪些超额使用事件被标记为正版或 false。

   [ ![ 说明 "使用情况" 页上的异常操作日志。](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
   ***图5：异常操作日志***

1. 合作伙伴中心分析将不支持重述在导出报告中使用超额使用情况事件。 通过合作伙伴中心，你可以为异常输入纠正的超额使用情况，并将详细信息传递给 Microsoft 团队进行调查。 根据调查，Microsoft 会根据需要向额外收取客户颁发信用退款。 选择任何已标记的异常时，可以选择 " **标记为异常** "，将使用超额异常标记为正版。

   [ ![ 说明标记为异常对话框。](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
   ***图：6：标记为异常对话框***

第一次将超额使用情况标记为 "合作伙伴中心" 中的 "异常" 时，将从该实例获得30天的窗口，将异常标记为正版或 false。 在30天内，因为发布者将无法处理异常情况。

> [!IMPORTANT]
> 未报告的超额使用单位不符合重述和财务调整条件。

您可以查看所选计算期间 (确认或) 的所有异常情况。 各种计算周期为过去30天、过去60天和最后90天。

> [!IMPORTANT]
> 在合作伙伴中心第一次报告异常时，系统会要求你在30天内对标记的异常进行操作。

通过此小组件中的筛选模式，你可以选择单个产品/服务和单个自定义计量。

将超额使用情况标记为异常或确认将异常标记为正版的模型后，不能将所选内容更改为 "不是异常情况"。

> [!IMPORTANT]
> 在出现 overcharge 情况时，可以重新提交超额使用情况。

## <a name="see-also"></a>请参阅
- [使用商业性 marketplace 计量服务的 SaaS 计费计费](./partner-center-portal/saas-metered-billing.md)
- [托管应用程序计费计费](./partner-center-portal/azure-app-metered-billing.md)
- [针对按流量计费的异常情况检测服务](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)
