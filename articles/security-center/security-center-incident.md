---
title: 在 Azure 安全中心管理安全事件 | Microsoft Docs
description: 本文档可帮助你使用 Azure 安全中心管理安全事件。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: e385dec04db44e88ef5759cf8e2391b19b77dccc
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237523"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>在 Azure 安全中心管理安全事件

会审和调查安全警报可能会非常耗时，即使对于技术高超的安全分析员也是如此。 对于许多安全分析员而言，很难知道从何处着手。 

安全中心使用[分析](./security-center-alerts-overview.md)以在不同[安全警报](security-center-managing-and-responding-alerts.md)之间连接信息。 通过使用这些连接，安全中心可以提供包含攻击活动及其相关警报的单一视图，以帮助你了解攻击者的操作和受影响的资源。

本文概述了安全中心内的事件。

## <a name="what-is-a-security-incident"></a>什么是安全事件？

在安全中心，安全事件是对资源的所有警报汇总，与 [网络攻击链](alerts-reference.md#intentions) 模式保持一致。 事件显示在[安全警报](security-center-managing-and-responding-alerts.md)页中。 选择事件以查看相关警报并获取详细信息。

## <a name="managing-security-incidents"></a>管理安全事件

1. 在安全中心的警报页上，使用“添加筛选器”按钮按警报名称筛选到警报名称“在多个资源上检测到的安全事件” 。 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="在 Azure 安全中心的“警报”页上查找事件。":::

    现在，此列表已经过筛选，只显示事件。 请注意，安全事件与安全警报的图标不同。

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Azure 安全中心的“警报”页上的事件列表。":::

1. 若要查看事件的详细信息，请从列表中选择一个事件。 此时会显示一个侧窗格，其中包含有关事件的更多详细信息。

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="显示事件详细信息的侧窗格。":::

1. 若要查看更多详细信息，请选择“查看完整详细信息”。

    [![在 Azure 安全中心响应安全事件。](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    “安全事件”页的左窗格显示有关安全事件的大致信息：标题、严重性、状态、活动时间、说明以及受影响的资源。 在受影响的资源旁边，可以看到相关的 Azure 标记。 在调查警报时，可以使用这些标记来推断资源的组织环境。

    右窗格包含“警报”选项卡，其中包含与此事件相关联的安全警报。 

    >[!TIP]
    > 有关特定警报的详细信息，请选择该警报。 

    [![事件的“执行操作”选项卡。](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    若要切换到“执行操作”选项卡，请选择该选项卡或右窗格底部的按钮。 使用此选项卡执行更多操作，例如：
    - 缓解威胁 - 为此安全事件提供手动修正步骤
    - 防范未来的攻击 - 提供安全建议，帮助减少攻击面、提高安全状况和防范未来的攻击
    - 触发自动响应 - 提供触发逻辑应用以响应此安全事件的选项
    - 禁止显示类似的警报 - 如果警报与组织无关，还可禁止显示具有类似特征的未来警报 

   > [!NOTE]
   > 同一个警报可以作为事件的一部分存在，也可以作为独立警报显示。

1. 若要修正事件中的威胁，请按照每个警报提供的修正步骤操作。


## <a name="next-steps"></a>后续步骤

本页介绍了安全中心的安全事件功能。 如需相关信息，请参阅以下页面：

- [安全中心的“安全警报”](security-center-alerts-overview.md)
- [管理和响应安全警报](security-center-managing-and-responding-alerts.md)