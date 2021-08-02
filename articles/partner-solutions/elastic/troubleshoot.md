---
title: 弹性故障排除 - Azure 合作伙伴解决方案
description: 本文提供有关对与 Azure 的弹性集成进行故障排除的信息
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/20/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4bc4bc9d9bb5890ed95bbb446f570d25e43d813
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952566"
---
# <a name="troubleshooting-elastic-integration-with-azure"></a>Azure 弹性集成故障排除

本文档包含有关对使用弹性的解决方案进行故障排除的信息。

## <a name="unable-to-create-an-elastic-resource"></a>无法创建弹性资源

与 Azure 的弹性集成只能由对 Azure 订阅拥有“所有者”访问权限的用户设置。 [确认你具有适当的访问权限](../../role-based-access-control/check-access.md)。

## <a name="logs-not-being-emitted-to-elastic"></a>日志未发出到弹性

只有在 [Azure Monitor 资源日志类别](../../azure-monitor/essentials/resource-logs-categories.md)中列出的资源才会向弹性发出日志。 若要验证资源是否正在将日志发送到弹性，请导航到资源的 [Azure 诊断设置](../../azure-monitor/essentials/diagnostic-settings.md)。 验证是否有可用的诊断设置选项。

:::image type="content" source="media/troubleshoot/check-diagnostic-setting.png" alt-text="验证诊断设置":::

## <a name="purchase-errors"></a>购买错误

* 购买失败，因为该 Azure 订阅未连接有效信用卡，或该订阅未关联付款方式。

  使用其他 Azure 订阅。 或者，为订阅添加或更新信用卡或付款方式。 有关详细信息，请参阅[更新信用卡和付款方式](../../cost-management-billing/manage/change-credit-card.md)。

* EA 订阅不允许在市场中购买。

  使用其他订阅。 或者，检查你的 EA 订阅是否启用了市场购买。 有关详细信息，请参阅[启用市场购买](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)。

## <a name="get-support"></a>获取支持

若要联系与 Azure 弹性集成有关的支持人员，请在左窗格中选择“新建支持请求”。 选择“打开弹性支持票证”。

:::image type="content" source="media/troubleshoot/open-ticket.png" alt-text="打开支持票证":::

在弹性站点中，打开支持请求。

:::image type="content" source="media/troubleshoot/elastic-support.png" alt-text="打开弹性支持":::

## <a name="next-steps"></a>后续步骤

了解如何[管理弹性实例](manage.md)。