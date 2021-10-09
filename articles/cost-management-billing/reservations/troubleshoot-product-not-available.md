---
title: 排查 Azure 预留类型不可用问题
description: 本文有助于你了解和排查 Azure 门户中显示为不可用的预留实例的问题。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: primittal
ms.topic: troubleshooting
ms.date: 09/20/2021
ms.openlocfilehash: 51d30767c3e084ade041d22bac8a246aa5d1f502
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631127"
---
# <a name="troubleshoot-reservation-type-not-available"></a>排查预留类型不可用的问题

本文有助于你了解和排查 Azure 门户中显示为不可用的预留实例的问题。

## <a name="symptoms"></a>症状

1. 登录 [Azure 门户](https://portal.azure.com/)并导航到“预留”。
2. 选择“+ 添加”，然后选择产品。
3. 选择“所有产品”选项卡。
4. 在产品列表中选择一个产品。 可能会显示以下消息之一：
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="示例消息：所选订阅或区域的产品不可用" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="示例消息：核心配额不足" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>原因

有些预留无法购买，原因如下：

- 并非每个预留实例产品都对所有区域开放
- 你的订阅存在配额限制

### <a name="cause-1"></a>原因 1

并非所有预留实例产品都可供购买。 Azure 根据向客户提供货币折扣的相关成本决定开放或不开放某些产品的购买。 在其他情况下，Azure 不提供某些产品是因为受到特定数据中心内的容量条件限制。 此外，某些 Azure 产品开发小组现在可能会允许某些产品，因为他们要停用较旧的产品。

在其他情况下，Azure 会根据对某些区域中这些资源的需求对各种产品施加容量限制。 例如，当在数据中心中对特定 VM 大小的需求用完时，可能会做出这样的限制。 在此示例中，Azure 不能保证在该区域购买了该大小的预留的客户能获得容量。 最后，出于各种原因，某些产品是唯一的。 此类产品仅向一小部分选定的客户提供。

既然某些预留是无法购买的，为什么它们还显示在 Azure 门户中？ 这是因为用户创建了支持请求，指出他们找不到所需的产品。 Azure 预留开发团队认为，与不显示这些预留相比，显示所有带有 `Product unavailable` 消息的产品可以减少支持请求。

### <a name="cause-2"></a>原因 2

你的订阅存在配额限制。 订阅限制了可使用的 CPU 内核数。 此限制应用于一些预留实例产品，尤其是虚拟机。 Azure 需要确保购买预留实例的人可以使用它。 Azure 在 Azure 门户中执行粗略检查，以确保订阅中有足够的可用内核来部署 VM 并能通过购买预留获益。

这项检查非常简单，使你能够将特定产品添加到购物车并购买预留。 Azure 会评估订阅可用的 CPU 内核总数，并检查该数量是否大于所选项目的核心数。

Azure 不会检查“共享”范围或“管理组”范围预留实例的配额。 共享范围的预留实例权益适用于注册中的所有订阅。 管理组范围的预留实例权益适用于同时属于管理组和计费范围的所有订阅。 Azure 无法确定所有订阅中是否有足够的可用内核来部署资源。 无论配额是多少，当所选范围设置为“共享”或“管理组”时，Azure 始终允许你选择 VM 大小。

此外，Azure 不会对系统建议的购买进行配额检查。 这些建议基于有效的使用情况。 Azure 假设你有足够的内核来运行特定的 VM 大小，因为你已经产生了创建建议所需的使用情况。

## <a name="solution"></a>解决方案

根据你收到的错误消息，请使用以下方案之一解决自己的问题。

### <a name="solution-1"></a>解决方案 1

如果收到“产品不可用”消息，请选择错误消息中的“联系支持人员”链接，请求为自己的订阅添加例外。 并不是每个请求都能被授予例外。

### <a name="solution-2"></a>解决方案 2

如果收到“核心配额不足”消息，可以将范围更改为“共享”。 购买预留后，可以将预留范围从“共享”改为“单一” 。

或者选择错误消息中的“请求增加配额”链接，为自己的订阅请求更多 CPU 核心配额。

## <a name="next-steps"></a>后续步骤

- 若要详细了解预留范围选项，请参阅[范围预留](prepare-buy-reservation.md#scope-reservations)。