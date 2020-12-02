---
title: 对“Azure 门户中没有符合条件的订阅”进行故障排除
description: 本文有助于对在尝试购买预留时，Azure 门户中出现的“没有符合条件的订阅”错误消息进行故障排除。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.openlocfilehash: 1b36577c3c0940687f98394f8ea4faae83f371be
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887189"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>对“没有符合条件的订阅”进行故障排除

本文有助于对在尝试购买预留时，Azure 门户中出现的“没有符合条件的订阅”错误消息进行故障排除。

## <a name="symptoms"></a>症状

1. 登录 [Azure 门户](https://portal.azure.com)并导航到“预留”。
1. 选择“添加”，然后选择一个服务。
1. 出现以下错误消息时：
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. 在“选择要购买的产品”区域中，展开“计费订阅”列表，查看特定订阅没有资格购买预留实例的原因 。 下图显示了无法购买预留的示例原因。  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="显示为何无法购买预留的示例" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>原因

要购买 Azure 预留实例，必须至少有一个满足以下要求的订阅：

- 订阅必须是受支持的套餐类型。 支持的套餐类型为：即用即付、云解决方案提供商 (CSP)、Microsoft Azure Enterprise 或 Microsoft 客户协议。
- 你必须是订阅的所有者或预留购买者。

如果没有满足要求的订阅，则会出现 `No eligible subscriptions` 错误。

### <a name="cause-1"></a>原因 1

订阅必须是受支持的套餐类型。 支持的套餐类型为：即用即付、CSP、Microsoft Azure Enterprise 或 Microsoft 客户协议。 你的订阅类型不受支持。 如果所选订阅的套餐类型不支持预留，则会出现以下错误。

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="显示“订阅不符合购买条件”错误消息的示例" :::

### <a name="cause-2"></a>原因 2

你必须是订阅的所有者或预留购买者。 如果权限不足，你会看到以下错误。

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>解决方案

- 如果当前套餐不支持预留，则需要创建新的 Azure 订阅。
- 如果你无权访问现有预留，则可以从当前所有者处获取相关访问权限。

### <a name="solution-1"></a>解决方案 1

要购买预留，需要创建支持预留的新 Azure 订阅。

- 如果有 Azure 免费试用版，则可以[升级订阅](../manage/upgrade-azure-subscription.md)。
- 可以使用[即用即付费率](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)来创建新的 Azure 订阅。
- 注册 [Microsoft 客户协议](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)，并创建新的订阅。
- 使用 [CSP](https://www.microsoft.com/solution-providers/home) 购买新订阅，并创建新的订阅。

### <a name="solution-2"></a>解决方案 2

要获取对预留的所有者访问权限，必须获取对以下任一项的访问权限：

- 购买预留的预留订单
- 预留本身

当前预留订单所有者或预留所有者可按照以下步骤向你委托访问权限。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务” > “预订”以列出自己有权访问的预订。
1. 选择要将访问权限委托给其他用户的预订。
1. 选择“访问控制 (IAM)”。
1. 选择“添加角色分配” > “角色” > “所有者”。 或者，如果希望提供有限的访问权限，请选择其他角色。
1. 键入要添加为“所有者”的用户的电子邮件地址。
1. 选择用户，再选择“保存”。

有关详细信息，请参阅[添加或更改可以管理预留的用户](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。

## <a name="next-steps"></a>后续步骤

- 如果需要预留所有者向你授予访问权限，请查看[添加或更改可以管理预留的用户](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
