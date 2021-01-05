---
title: Azure 安全中心中的补救建议 |Microsoft Docs
description: 本文介绍如何在 Azure 安全中心中响应建议，以保护资源并满足安全策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: dabd7e9e2c3c74225efc4611c7ad3523a6c76ba5
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807984"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>在 Azure 安全中心修正建议

建议提供了有关如何更好地保护资源的意见。 可以按照建议中提供的修正步骤来实施建议。

## <a name="remediation-steps"></a>修正步骤<a name="remediation-steps"></a>

在查看完所有建议后，决定先修正哪一建议。 建议将安全控制的优先级设置为可能的最高级别，以提高安全分数。

1. 从列表中，选择 "建议"。

1. 按照“修正步骤”部分中的说明进行操作。 每个建议都有其自己的一组指令。 以下屏幕截图显示了一些修正步骤，这些步骤用于将应用程序配置为仅允许通过 HTTPS 传输的流量。

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="建议手动修正步骤" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. 完成后，将显示一条通知，告知你问题是否已解决。

## <a name="quick-fix-remediation"></a>“快速修复”修正

为了简化修补并提高环境的安全性 (并增加安全分数) ，许多建议都包含一个快速修复选项。

快速修复可帮助您快速修正对多个资源的建议。

> [!TIP]
> 快速修复解决方案仅适用于特定的建议。 若要查找具有可用快速修补程序的建议，请使用 " **响应操作** " 筛选器获取建议列表：
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="使用建议列表上方的筛选器查找具有快速修复选项的建议":::

实现快速修复解决方案：

1. 在带有“快速修复!”标签的建议的列表中， 标签，选择 "建议"。

    [![选择“快速修复!”](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. 从 "不 **正常资源** " 选项卡中，选择要对其实施建议的资源，然后选择 " **修正**"。

    > [!NOTE]
    > 列出的某些资源可能已禁用，因为你没有相应的权限，无法修改它们。

1. 在确认框中，阅读修正详细信息和影响。

    ![快速修复](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > 影响在单击“修正”后打开的“修正资源”窗口的灰色框中列出。 其中列出了在继续进行“快速修复”修正时会发生哪些更改。

1. 请插入相关参数（如有必要），并批准修正。

    > [!NOTE]
    > 修正完成后可能需要几分钟时间，才能在“正常的资源”选项卡中看到资源。若要查看修正操作，请查阅[活动日志](#activity-log)。

1. 完成后，将显示一条通知，告知你修正是否成功。

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>活动日志中的“快速修复”修正日志记录 <a name="activity-log"></a>

修正操作使用模板部署或 REST PATCH API 调用，将配置应用于资源。 这些操作记录在 [Azure 活动日志](../azure-resource-manager/management/view-activity-logs.md)中。


## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍了如何在安全中心修正建议。 若要了解有关安全中心的详细信息，请参阅以下页面：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
