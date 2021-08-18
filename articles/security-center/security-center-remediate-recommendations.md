---
title: Azure 安全中心的修正建议 | Microsoft Docs
description: 本文介绍如何在 Azure 安全中心响应建议，以保护资源并符合安全策略。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: f0caaea2296a8981df4717497cb641676b43dea1
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237068"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>在 Azure 安全中心修正建议

建议提供了有关如何更好地保护资源的意见。 可以按照建议中提供的修正步骤来实施建议。

## <a name="remediation-steps"></a>修正步骤<a name="remediation-steps"></a>

在查看完所有建议后，决定先修正哪一建议。 我们建议你优先考虑最有可能增加安全功能分数的安全控制措施。

1. 从列表中选择一条建议。

1. 按照“修正步骤”部分中的说明进行操作。 每个建议都有其自己的一组指令。 以下屏幕截图显示了一些修正步骤，这些步骤用于将应用程序配置为仅允许通过 HTTPS 传输的流量。

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="建议的手动修正步骤。" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. 完成后，将显示一条通知，告知你问题是否已解决。

## <a name="fix-button"></a>修复按钮

为简化修正并提高环境的安全性（以及提高安全分数），许多建议中都包含“修复”选项。

修复可帮助你针对多个资源快速修正某个建议。

> [!TIP]
> “修复”功能仅可用于特定建议。 若要查找含可修复选项的建议，请对建议列表使用“响应操作”筛选器：
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="使用建议列表上方的筛选器查找含修复选项的建议。":::

若要实现“修复”：

1. 可从含“修复”操作图标 :::image type="icon" source="media/security-center-remediate-recommendations/fix-icon.png" border="false"::: 的建议列表中，选择一个建议。

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-recommendations-fix-action.png" alt-text="建议列表突出显示含修复操作的建议" lightbox="./media/security-center-remediate-recommendations/security-center-recommendations-fix-action.png#lightbox":::

1. 从“不正常的资源”选项卡上，选择要对其实施建议的资源，然后选择“修正”。

    > [!NOTE]
    > 列出的某些资源可能已禁用，因为你没有相应的权限，无法修改它们。

1. 在确认框中，阅读修正详细信息和影响。

    ![快速修复。](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > 影响在单击“修正”后打开的“修正资源”窗口的灰色框中列出。 其中列出了在继续执行“修复”时会发生的更改。

1. 请插入相关参数（如有必要），并批准修正。

    > [!NOTE]
    > 修正完成后可能需要几分钟时间，才能在“正常的资源”选项卡中看到资源。若要查看修正操作，请查阅[活动日志](#activity-log)。

1. 完成后，将显示一条通知，告知你修正是否成功。

## <a name="fix-actions-logged-to-the-activity-log"></a>修复操作将会记录到活动日志 <a name="activity-log"></a>

修正操作使用模板部署或 REST PATCH API 调用，将配置应用于资源。 这些操作记录在 [Azure 活动日志](../azure-resource-manager/management/view-activity-logs.md)中。


## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍了如何在安全中心修正建议。 若要了解有关安全中心的详细信息，请参阅以下页面：

* [在 Azure 安全中心内设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略
* [什么是安全策略、计划和建议？](security-policy-concept.md)