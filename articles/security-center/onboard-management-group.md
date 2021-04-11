---
title: 将管理组加入 Azure 安全中心
description: 了解如何使用提供的 Azure Policy 定义来为管理组中的所有订阅启用 Azure 安全中心。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 01036343e2585adf7c09ad3f0d236948a537fc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016553"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>对管理组中的所有订阅启用安全中心

你可以使用 Azure Policy 对同一管理组 (MG) 中的所有 Azure 订阅启用 Azure 安全中心。 这比通过门户单独访问它们更为方便，即使订阅属于不同的所有者，此方法也有效。 

加入管理组及其所有订阅：

1. 以具有“安全管理员”权限的用户身份，打开 Azure Policy，然后搜索“在订阅上启用 Azure 安全中心”定义 。

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="Azure Policy 定义“对订阅启用 Azure 安全中心”":::

1. 选择“分配”，并确保将范围设置为 MG 级别。

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="分配定义“对订阅启用 Azure 安全中心”":::

    > [!TIP]
    > 除范围外，没有其他必需的参数。

1. 选择“创建修正任务”，确保将加入所有未启用安全中心的现有订阅。

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="为 Azure Policy 定义“对订阅启用 Azure 安全中心”创建修正任务":::

1. 分配定义后，修正任务将执行以下操作：

    1. 检测 MG 中所有尚未在安全中心注册的订阅。
    1. 将这些订阅标记为“不合规”。
    1. 将所有已注册的订阅标记为“合规”（无论它们是否启用了 Azure Defender）。

    然后，修正任务将免费对不合规的订阅启用安全中心。

> [!IMPORTANT]
> 策略定义将仅对现有订阅启用安全中心。 若要注册新创建的订阅，请打开“合规性”选项卡，选择相关的不合规订阅，然后创建修正任务。如果要使用安全中心监视一个或多个新订阅，请重复此步骤。

## <a name="optional-modifications"></a>可选修改

有多种用于修改 Azure Policy 定义的方法可供选择： 

- **以不同的方式定义合规性性** - 提供的策略将 MG 中尚未在安全中心注册的所有订阅分类为“不合规”。 你可以选择将它设置为未启用 Azure Defender 的所有订阅。

    提供的定义将下面任一“定价”设置定义为合规。 也就是说设置为“标准”或“免费”的订阅均合规。

    > [!TIP]
    > 启用 Azure Defender 计划后，将在策略定义中根据“标准”设置对其进行描述。 禁用时，它是“免费”设置。 若要了解这些计划之间的差异，请参阅[安全中心免费版与已启用 Azure Defender](security-center-pricing.md)。 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    如果将其更改为以下内容，则仅设置为“标准”的订阅会被分类为合规：

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **定义一些要在启用安全中心时应用的 Azure Defender 计划** - 提供的策略启用没有任何可选 Azure Defender 计划的安全中心。 你可以选择启用一个或多个计划。

    提供的定义的 `deployment` 部分具有参数 `pricingTier`。 默认情况下，它设置为 `free`，但可以对其进行修改。 


## <a name="next-steps"></a>后续步骤：

现已加入了整个管理组，请启用 Azure Defender 的高级保护。 

> [!div class="nextstepaction"]
> [启用 Azure Defender](enable-azure-defender.md)