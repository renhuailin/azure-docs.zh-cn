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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550894"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>教程：提高合规性

Azure 安全中心使用合规性仪表板，可以根据合规性要求简化相关过程  。 

安全中心持续评估混合云环境，以根据适用于你的订阅的标准中的控制措施和最佳做法来分析风险因素。 仪表板反映了符合这些标准的状态。 

在 Azure 订阅上启用安全中心后，系统会自动为其分配 [Azure 安全基准](../security/benchmarks/introduction.md)。 这一公认的基准建立在 [Internet 安全中心 (CIS)](https://www.cisecurity.org/benchmark/azure/) 和[国家标准与技术研究院 (NIST)](https://www.nist.gov/) 的控制基础上，重点关注以云为中心的安全性。

在法规合规性仪表板中，可以查看在所选标准和法规下，环境中所有评估的状态。 针对建议进行操作并减少环境中的风险因素以后，合规性情况得到了改善。

在本教程中，将了解如何：

> [!div class="checklist"]
> * 使用合规性仪表板评估合规性
> * 针对建议进行操作，改进符合性情况
> * 设置合规性状态更改时的警报
> * 将合规性数据导出为连续流和每周快照

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要逐步执行本教程中介绍的功能：

- 必须启用 [Azure Defender](azure-defender.md)。 可以免费试用 Azure Defender 30 天。
- 你需要使用对策略合规性数据具有读取器访问权限的帐户登录（安全读取器不足）。 订阅的全局读取器角色将起作用。 至少需要分配“资源策略参与者”和“安全管理员”角色 。

##  <a name="assess-your-regulatory-compliance"></a>评估合规性

法规合规性仪表板显示所选的合规性标准及其所有要求，其中受支持的要求映射到适用的安全评估。 这些评估的状态反映了标准的合规性。

使用法规合规性仪表板有助于重点了解你在符合所选标准和法规方面存在哪些差距。 有了这个专注的视图，你还可以持续监视动态云环境和混合环境中一段时间内的合规性。

1. 从安全中心的菜单中，选择“法规符合性”。

    屏幕顶部是一个仪表板，其中概述了你的合规性状态以及一组支持的合规性法规。 你将看到总体合规性分数，以及与每个标准相关联的已通过评估的数目和失败的评估的数目。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="法规符合性仪表板" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. 针对与自己相关的符合性标准，选择一个选项卡 (1)。 你可看到该标准应用于哪些订阅 (2)，以及该标准的所有控件列表 (3)。 对于适用的控件，可以查看与该控件相关联的已通过评估和失败的评估的详细信息 (4)，以及受影响资源的数量 (5)。 某些控件为灰显状态。这些控件没有任何与之关联的安全中心评估。 你需要自行检查这些控件的要求，并在自己的环境中对其进行评估。 其中一部分可能与进程相关，与技术无关。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="浏览特定标准的符合性详细信息":::

1. 要生成总结特定标准的当前合规性状态的 PDF 报告，请选择“下载报告”。

    该报告根据安全中心评估数据为所选标准提供符合性状态的高级别总结，并根据该特定标准的控制进行整理。 该报告可与相关利益干系人共享，并可能为内部和外部审计员提供证据。

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="下载符合性报告":::

## <a name="improve-your-compliance-posture"></a>改进符合性情况

使用法规合规性仪表板中的信息，可以直接在仪表板中采用相关建议，改进合规性情况。

1.  单击在仪表板中显示的失败评估即可查看该建议的详细信息。 每项建议都包含一组修正步骤，遵循这些步骤即可解决问题。

1.  选择特定的资源，查看更多详细信息，然后解决与该资源的建议相关的问题。 <br>例如，在“Azure CIS 1.1.0 标准”中，选择“应对虚拟机应用磁盘加密”建议。

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="从标准中选择一个建议会直接转到相关建议的详细信息页面":::

1. 在此示例中，当你在建议详细信息页面中选择“执行操作”时，将进入 Azure 门户的 Azure 虚拟机页，可在其中启用“安全”选项卡中的加密：

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="建议详细信息页上的“执行操作”按钮会转到修正选项":::

    有关如何应用建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。

1.  在采取行动解决与建议相关的问题以后，就会在合规性仪表板报告中看到相关影响，因为你的合规性分数提高了。

    > [!NOTE]
    > 评估大约每 12 小时运行一次，因此只有在下一次相关评估运行以后才能看到对符合性数据造成的影响。


## <a name="export-your-compliance-status-data"></a>导出合规性状态数据

如果要在环境中使用其他监视工具来跟踪合规性状态，则安全中心会提供一种导出机制来简化此操作。 配置“连续导出”将选择的数据发送到 Azure 事件中心或 Log Analytics 工作区。

使用“连续导出”将数据发送到 Azure 事件中心或 Log Analytics 工作区：

- 在“连续流”中导出所有法规合规性数据：

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="持续导出法规合规性数据流" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- 导出法规合规性数据的每周快照：

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="持续导出法规合规性数据的每周快照" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

还可以直接从法规合规性仪表板导出法规合规性数据的 PDF/CSV 报表：

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="将法规合规性数据导出为 PDF 或 CSV 报表" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

有关详细信息，请参阅[连续导出安全中心数据](continuous-export.md)。


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>当合规性发生变更时，运行工作流自动化

安全中心的工作流自动化功能可在任一法规合规性评估状态变更时触发逻辑应用。

例如，在合规性评估失败时，你可能希望安全中心向特定用户发送电子邮件。 首先需要创建逻辑应用（使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)），然后在新工作流自动化中设置触发器，如[自动响应安全中心触发器](workflow-automation.md)中所述。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="使用对监管合规性评估的更改来触发工作流自动化" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用安全中心的法规符合性仪表板执行以下操作：

- 查看和监视与重要的标准和法规相关的合规性情况。
- 改进符合性状态，方法是：解决相关的建议问题，观察符合性分数的改进情况。

法规合规性仪表板可以大大简化合规性过程，显著缩短为 Azure、混合环境和多云环境收集合规性证据所需的时间。

若要了解更多信息，请参阅以下相关页面：

- [自定义法规合规性仪表板中的标准集](update-regulatory-compliance-packages.md) - 了解如何选择在法规合规性仪表板中显示的标准。 
- [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
- [管理 Azure 安全中心安全建议](security-center-recommendations.md) - 了解如何使用 Azure 安全中心的建议来保护 Azure 资源。