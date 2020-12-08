---
title: 教程：合规性检查 - Azure 安全中心
description: 教程：了解如何使用 Azure 安全中心提高合规性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2020
ms.author: memildin
ms.openlocfilehash: bbc36dbb2a17d379d31a9a235898500aea36247d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533904"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>教程：提高合规性

Azure 安全中心使用合规性仪表板，可以根据合规性要求简化相关过程  。 在仪表板中，安全中心会对你的 Azure 环境进行持续的评估，以便了解你的符合性情况。 安全中心根据安全最佳做法分析混合云环境中的风险因素。 这些评估会从支持的一组标准映射到符合性控件。 在合规性仪表板中，可以查看在特定的法规标准下，环境中所有评估的状态。 针对建议进行操作并减少环境中的风险因素以后，合规性情况得到了改善。

在本教程中，将了解如何：

> [!div class="checklist"]
> * 使用合规性仪表板评估合规性
> * 针对建议进行操作，改进符合性情况

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要逐步执行本教程中介绍的功能：

- 必须启用 [Azure Defender](azure-defender.md)。 可以免费试用 Azure Defender 30 天。
- 你需要使用对策略合规性数据具有读取器访问权限的帐户登录（安全读取器不足）。 订阅的全局读取器角色将起作用。 至少需要分配“资源策略参与者”和“安全管理员”角色 。

##  <a name="assess-your-regulatory-compliance"></a>评估合规性

安全中心会持续评估资源的配置以识别安全问题和漏洞。 这些评估以建议的形式提供，着重于改进安全机制。 在合规性仪表板中，可以查看一组符合性标准及其所有要求，其中的受支持的要求会映射到适用的安全评估。 这样你就可以根据这些评估的状态和标准来查看自己的符合性情况。

可以通过合规性仪表板视图重点了解你在符合某个重要的标准或规范方面存在哪些差距。 有了这个专注的视图，你还可以持续监视动态云环境和混合环境中一段时间内的符合性分数。

>[!NOTE]
> 默认情况下，安全中心支持以下法规标准：Azure CIS、PCI DSS 3.2、ISO 27001 和 SOC TSP。 
>
> 通过[动态合规性包（预览版）](update-regulatory-compliance-packages.md)功能，你可以将你的规章合规性仪表板中显示的标准升级到新的动态包  。 你还可以使用相同的预览功能来添加新的合规性包，并监视与其他标准的合规性。 

1. 从安全中心的菜单中，选择“法规符合性”。 <br>
在屏幕顶部会显示一个仪表板，其中概述了你的符合性状态以及一组支持的符合性法规。 可以查看总体符合性分数，以及与每个标准相关联的已通过评估和失败的评估的数目。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="法规符合性仪表板":::

1. 针对与自己相关的符合性标准，选择一个选项卡 (1)。 你可看到该标准应用于哪些订阅 (2)，以及该标准的所有控件列表 (3)。 对于适用的控件，可以查看与该控件相关联的已通过评估和失败的评估的详细信息 (4)，以及受影响资源的数量 (5)。 某些控件为灰显状态。这些控件没有任何与之关联的安全中心评估。 你需要自行检查这些控件的要求，并在自己的环境中对其进行评估。 其中一部分可能与进程相关，与技术无关。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="浏览特定标准的符合性详细信息":::

1. 若要生成并下载总结当前特定标准的符合性状态的 PDF 报告，请单击“下载报告”  。

    该报告根据安全中心评估数据为所选标准提供符合性状态的高级别总结，并根据该特定标准的控制进行整理。 该报告可与相关利益干系人共享，并可能为内部和外部审计员提供证据。

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="下载符合性报告":::

## <a name="improve-your-compliance-posture"></a>改进符合性情况

根据法规符合性仪表板中的信息，可以直接在仪表板中采用相关建议，改进符合性情况。

1.  单击在仪表板中显示的失败评估即可查看该建议的详细信息。 每项建议都包含一组修正步骤，遵循这些步骤即可解决问题。

1.  可以选择特定的资源来查看更多的详细信息，然后解决与该资源的建议相关的问题。 <br>例如，在“Azure CIS 1.1.0 (新)标准”中，可以选择建议“应对虚拟机应用磁盘加密” 。

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="从标准中选择一个建议会直接转到相关建议的详细信息页面":::

1. 在此示例中，当你在建议详细信息页面中选择“执行操作”时，你将进入 Azure 门户的 Azure 虚拟机页，可在其中打开“安全”选项卡并启用加密 ：

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="建议详细信息页上的“执行操作”按钮会转到修正选项":::

    有关如何应用建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。

1.  在采取行动解决与建议相关的问题以后，就会在合规性仪表板报告中看到相关影响，因为你的符合性分数提高了。

    > [!NOTE]
    > 评估大约每 12 小时运行一次，因此只有在下一次相关评估运行以后才能看到对符合性数据造成的影响。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用安全中心的法规符合性仪表板执行以下操作：

-   查看和监视与重要的标准和法规相对应的符合性情况。
-   改进符合性状态，方法是：解决相关的建议问题，观察符合性分数的改进情况。

法规符合性仪表板可以大大简化符合性过程，显著缩短为 Azure 和混合环境收集符合性证据所需的时间。

若要了解更多信息，请参阅下列相关文章：

-   [在法规符合性仪表板中的动态合规性包更新（预览版）](update-regulatory-compliance-packages.md) - 了解此预览功能，该功能允许你将法规符合性仪表板中显示的标准更新为新的动态包。 你还可以使用相同的预览功能来添加新的合规性包，并监视与其他标准的合规性。 
-   [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
-   [管理 Azure 安全中心安全建议](security-center-recommendations.md) - 了解如何使用 Azure 安全中心的建议来保护 Azure 资源。
-   [提高 Azure 安全中心的安全分数](secure-score-security-controls.md) - 了解如何确定漏洞和安全建议的优先级，以便最大程度地改善安全状况。
