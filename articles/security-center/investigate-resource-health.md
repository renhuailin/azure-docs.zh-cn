---
title: 教程：调查资源的运行状况 - Azure 安全中心
description: 教程：了解如何使用 Azure 安全中心调查资源的运行状况。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: memildin
ms.openlocfilehash: cb6f81fc02245e7932222d8555774e398944f1f4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751825"
---
# <a name="tutorial-investigate-the-health-of-your-resources"></a>教程：调查资源的运行状况

> [!NOTE]
> 本教程中所述的资源运行状况页以预览版提供。
> 
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] | |定价：|Azure Defender for DNS 按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中显示的定价计费

资源运行状况页提供单个资源总体运行状况的快照视图。 可以查看有关该资源的详细信息以及适用于该资源的所有建议。 如果使用 [Azure Defender](azure-defender.md)，还可查看该特定资源的未解决的安全警报。

此单页（目前以预览版提供）在安全中心的门户页显示：

1. **资源信息**：资源组及其附加到的订阅，以及地理位置等。
1. **应用的安全功能**：是否为资源启用 Azure Defender。
1. **未完成的建议和警报数**：未完成的安全建议和 Azure Defender 警报的数量。
1. **可操作的建议和警报**：两个选项卡列出适用于资源的建议和警报。

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="显示虚拟机运行状况信息的 Azure 安全中心的资源运行状况页":::

在本教程中，将了解如何：

> [!div class="checklist"]
> * 访问所有资源类型的资源运行状况页
> * 计算资源未解决的安全问题数
> * 改善资源的安全状况

## <a name="prerequisites"></a>先决条件

若要逐步执行本教程中介绍的功能：

- Azure 订阅：如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- 要应用安全建议，必须使用具有相关权限（资源组参与者、资源组所有者、订阅参与者或订阅所有者）的帐户进行登录
- 要消除警报，必须使用具有相关权限（安全管理员、订阅参阅这或订阅所有者）的帐户进行登录

##  <a name="access-the-health-information-for-a-resource"></a>访问资源的运行状况信息

> [!TIP]
> 在以下屏幕截图中，我们将打开一个虚拟机，但资源运行状况页可以显示所有资源类型的详细信息。 

打开资源的资源运行状况页：

1. 从[资产清单页](asset-inventory.md)中选择任何资源。

    :::image type="content" source="media/investigate-resource-health/inventory-select-resource.png" alt-text="从资产清单中选择一个资源，以查看资源运行状况页。" lightbox="./media/investigate-resource-health/inventory-select-resource.png":::

1. 使用资源运行状况页的左窗格，获取有关资源的订阅、状态和监视信息的概述。 还可以查看是否为资源启用 Azure Defender：

    :::image type="content" source="media/investigate-resource-health/resource-health-left-pane.png" alt-text="Azure 安全中心资源运行状况页的左窗格显示有关资源的订阅、状态和监视信息。它还包括未完成的安全建议和 Azure Defender 警报的总数。":::

1. 使用右窗格中的两个选项卡查看适用于此资源的安全建议和 Azure Defender 警报列表：

    :::image type="content" source="media/investigate-resource-health/resource-health-right-pane.png" alt-text="Azure 安全中心资源运行状况页的右窗格有两个选项卡：建议和警报。" lightbox="./media/investigate-resource-health/resource-health-right-pane.png":::

    > [!NOTE]
    > Azure 安全中心使用词语“正常”和“不正常”来描述资源的安全状态。 这些词语与资源是否符合特定[安全建议](security-policy-concept.md#what-is-a-security-recommendation)相关。
    >
    > 在以上屏幕截图中，可以看到即使此资源为“正常”，也会列出建议。 资源运行状况页的一个优点在于列出了所有建议，以便你可以全面了解资源的运行状况。 


## <a name="evaluate-the-outstanding-security-issues-for-a-resource"></a>计算资源未解决的安全问题数

资源运行状况页列出使资源处于“不正常”状态的建议以及处于活动状态的警报。 

- 要确保根据应用于订阅的策略对资源进行强化，请修复建议中所述的问题：
    1. 在右窗格中，选择一条建议。
    1. 按照屏幕上的指示继续操作。

        > [!TIP]
        > 修复由安全建议提出的问题的说明因安全中心的每条建议而异。
        >
        > 要确定首先解决的建议，请查看每条建议的严重级别及其[对安全分数的潜在影响](secure-score-security-controls.md#security-controls-and-their-recommendations)。

- 调查 Azure Defender 警报：
    1. 在右窗格中，选择一个警报。
    1. 按照[响应安全警报](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)中的说明进行操作。


## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何使用安全中心的资源运行状况页。

若要了解更多信息，请参阅以下相关页面：

- [响应安全警报](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)
- [查看安全建议](security-center-recommendations.md)