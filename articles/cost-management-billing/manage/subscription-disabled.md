---
title: 重新激活已禁用的 Azure 订阅
description: 说明何时会禁用用户的 Azure 订阅，以及如何重新激活它。
keywords: azure 订阅已禁用
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/15/2021
ms.author: banders
ms.openlocfilehash: 1e669a794abe848d326a6d08156621dcf3883ec5
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292235"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>重新激活已禁用的 Azure 订阅

Azure 订阅被禁用可能是因为额度已过期、达到了支出限制、账单过期未付、达到了信用卡限制，或者订阅已被帐户管理员取消。 了解自己的情况属于什么样的问题，按照本文所述步骤重新激活订阅。

## <a name="your-credit-is-expired"></a>信用已过期

注册 Azure 免费帐户时，将获得免费试用版订阅，它提供为期 30 天、价值 200 美元的 Azure 额度（以计费货币表示）以及 12 个月的免费服务。 30 天结束后，Azure 会禁用订阅。 禁用订阅是为了防止你在意外情况下因使用量超出订阅的信用额度和免费服务而产生相应的费用。 若要继续使用 Azure 服务，则必须[升级订阅](upgrade-azure-subscription.md)。 升级之后，你的订阅仍可使用 12 个月的免费服务。 只需对超出免费服务用量限制的使用量付费即可。

## <a name="you-reached-your-spending-limit"></a>已达到支出限制

具有用于免费试用和 Visual Studio Enterprise 等功能的信用额度的 Azure 订阅对这些服务具有支出限制。 只能在包含的额度内使用这些服务。 当使用量达到支出限制后，Azure 将在此计费周期的剩余时间内禁用订阅。 禁用订阅是为了防止你在意外情况下因使用量超出订阅的信用额度而产生相应的费用。 若要取消支出限制，请参阅[在 Azure 门户中取消支出限制](spending-limit.md#remove)。

> [!NOTE]
> 如果使用的是免费试用版订阅且移除了支出限制，则在免费试用结束时会将订阅转换为采用即用即付费率的个人订阅。 创建订阅后，可保留剩余信用额度整整 30 天。 仍可使用 12 个月的免费服务。

要监视和管理 Azure 的计费活动，请参阅[计划管理 Azure 成本](../understand/plan-manage-costs.md)。

## <a name="your-bill-is-past-due"></a>帐单已过期

若要处理逾期未付余额，请参阅以下文章之一：

- 对于包含即用即付的 Microsoft 在线订阅计划订阅，请参阅[从 Azure 收到电子邮件后处理 Azure 订阅的逾期未付余额问题](resolve-past-due-balance.md)。
- 有关 Microsoft 客户协议订阅，请参阅[如何支付 Microsoft Azure 帐单](../understand/pay-bill.md)。

## <a name="the-bill-exceeds-your-credit-card-limit"></a>帐单金额超出了信用卡限制

为了解决此问题，请[改用其他信用卡](change-credit-card.md)。 或者，如果代表的是公司，则可[改用发票付款](pay-by-invoice.md)。

## <a name="the-subscription-was-accidentally-canceled"></a>订阅被意外取消

如果帐户管理员意外取消了即用即付订阅，可在 Azure 门户中重新激活它。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 转到“订阅”，然后选择已取消的订阅。
1. 选择“重新激活”。
1. 选择“确定”来确认重新激活。  
    :::image type="content" source="./media/subscription-disabled/reactivate-sub.png" alt-text="显示“确认重新激活”的屏幕截图" :::

对于其他订阅类型，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以重新激活订阅。

## <a name="after-reactivation"></a>重新激活后

重新激活订阅后，创建或管理资源可能存在延迟。 如果延迟超过 30 分钟，请联系 [Azure 计费支持人员](https://go.microsoft.com/fwlink/?linkid=2083458)来获取帮助。 大多数 Azure 资源可自动恢复，无需任何操作。 但建议检查 Azure 服务资源，并重启所有无法自动恢复的资源。

## <a name="upgrade-a-disabled-free-account"></a>升级禁用的免费帐户

如果你使用的不是免费的资源，且由于额度用完订阅被禁用，那么升级订阅后，这些资源将被重新启用。 这种情况将导致你需要为使用的资源付费。 有关升级免费帐户的详细信息，请参阅[升级 Azure 帐户](upgrade-azure-subscription.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 了解如何[计划管理 Azure 成本](../understand/plan-manage-costs.md)。
