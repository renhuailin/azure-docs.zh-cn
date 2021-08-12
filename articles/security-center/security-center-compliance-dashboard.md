---
title: 教程：合规性检查 - Azure 安全中心
description: 教程：了解如何使用 Azure 安全中心提高合规性。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 970329753d6dd6c6175c26da4288c304e27e3982
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237620"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>教程：提高合规性

Azure 安全中心使用合规性仪表板，可以根据合规性要求简化相关过程  。 

安全中心持续评估混合云环境，以根据适用于你的订阅的标准中的控制措施和最佳做法来分析风险因素。 仪表板反映了符合这些标准的状态。 

在 Azure 订阅上启用安全中心后，系统会自动为该订阅分配 [Azure 安全基准](/security/benchmark/azure/introduction)。 这一公认的基准建立在 [Internet 安全中心 (CIS)](https://www.cisecurity.org/benchmark/azure/) 和[国家标准与技术研究院 (NIST)](https://www.nist.gov/) 的控制基础上，重点关注以云为中心的安全性。

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
- 你必须使用对策略合规性数据具有读取者访问权限的帐户登录（安全读取者的权限不够）。 订阅的全局读取器角色将起作用。 至少需要分配“资源策略参与者”和“安全管理员”角色 。

##  <a name="assess-your-regulatory-compliance"></a>评估合规性

法规合规性仪表板显示所选的合规性标准及其所有要求，其中受支持的要求映射到适用的安全评估。 这些评估的状态反映了标准的合规性。

使用法规合规性仪表板有助于重点了解你在符合所选标准和法规方面存在哪些差距。 有了这个专注的视图，你还可以持续监视动态云环境和混合环境中一段时间内的合规性。

1. 从安全中心的菜单中，选择“法规符合性”。

    屏幕顶部是一个仪表板，其中概述了你的合规性状态以及一组支持的合规性法规。 你将看到总体合规性分数，以及与每个标准相关联的已通过评估的数目和失败的评估的数目。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="法规合规性仪表板。" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. 针对与自己相关的符合性标准，选择一个选项卡 (1)。 你可看到该标准应用于哪些订阅 (2)，以及该标准的所有控件列表 (3)。 对于适用控件，你可查看与控件关联的及格评估和未及格评估的详细信息 (4)，以及受影响的资源数量 (5)。 某些控件为灰显状态。这些控件没有任何与之关联的安全中心评估。 查看其要求并在环境中对其进行评估。 其中一部分可能与进程相关，与技术无关。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="浏览特定标准的合规性详细信息。":::

1. 要生成总结特定标准的当前合规性状态的 PDF 报告，请选择“下载报告”。

    该报告根据安全中心评估数据，就所选标准提供你的合规性状态的大致汇总。 该报告按照该特定标准的控件进行整理。 该报告可与相关利益干系人共享，并可能为内部和外部审计员提供证据。

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="下载合规性报告。":::

## <a name="improve-your-compliance-posture"></a>改进符合性情况

使用法规合规性仪表板中的信息，可以直接在仪表板中采用相关建议，改进合规性情况。

1.  选择仪表板中出现的未及格评估可查看该建议的详细信息。 每项建议都包含一组修正步骤，可用于解决问题。

1.  选择特定的资源，查看更多详细信息，然后解决与该资源的建议相关的问题。 <br>例如，在“Azure CIS 1.1.0 标准”中，选择“应对虚拟机应用磁盘加密”建议。

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="从标准中选择一个建议会直接转到相关建议的详细信息页面。":::

1. 在此示例中，当你在建议详细信息页面中选择“执行操作”时，将进入 Azure 门户的 Azure 虚拟机页，可在其中启用“安全”选项卡中的加密：

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="建议详细信息页上的“执行操作”按钮会转到修正选项。":::

    有关如何应用建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。

1.  在采取行动实施建议后，你将在合规性仪表板报表中看到相关影响，原因是你的合规性分数会增加。

    > [!NOTE]
    > 评估大约每 12 小时运行一次，因此只有在下一次相关评估运行以后才能看到对符合性数据造成的影响。


## <a name="export-your-compliance-status-data"></a>导出合规性状态数据

如果要在环境中使用其他监视工具来跟踪合规性状态，则安全中心会提供一种导出机制来简化此操作。 配置“连续导出”将选择的数据发送到 Azure 事件中心或 Log Analytics 工作区。

使用“连续导出”将数据发送到 Azure 事件中心或 Log Analytics 工作区：

- 在“连续流”中导出所有法规合规性数据：

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="持续导出法规合规性数据流。" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- 导出法规合规性数据的每周快照：

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="持续导出法规合规性数据的每周快照。" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

还可以直接从法规合规性仪表板导出法规合规性数据的 PDF/CSV 报表：

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="将法规合规性数据导出为 PDF 或 CSV 报表。" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

有关详细信息，请参阅[连续导出安全中心数据](continuous-export.md)。


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>当合规性发生变更时，运行工作流自动化

安全中心的工作流自动化功能可在任一法规合规性评估状态变更时触发逻辑应用。

例如，在合规性评估失败时，你可能希望安全中心向特定用户发送电子邮件。 首先需要创建逻辑应用（使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)），然后在新的工作流自动化中设置触发器，如[自动响应安全中心触发器](workflow-automation.md)中所述。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="使用对监管合规性评估的更改来触发工作流自动化。" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>常见问题解答 - 法规合规性仪表板

- [合规性仪表板支持哪些标准？](#what-standards-are-supported-in-the-compliance-dashboard)
- [一些控件为何灰显？](#why-do-some-controls-appear-grayed-out)
- [可如何从仪表板中删除 PCI-DSS、ISO 27001 或 SOC2 TSP 等内置标准？](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [我根据建议执行了推荐的更改，但它没有在仪表板中反映出来](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [我需要具备哪些权限才能访问仪表板？](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [没有为我加载法规合规性仪表板](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [可如何在仪表板中按照每种标准查看有关及格和不及格控件的报表？](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [可如何以非 PDF 的格式下载带有合规性数据的报表？](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [可如何在法规合规性仪表板中为某些策略创建例外？](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [需要具有哪些 Azure Defender 计划或许可证才能使用法规合规性仪表板？](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)
- [如何知道要使用哪个基准或标准？](#how-do-i-know-which-benchmark-or-standard-to-use)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>合规性仪表板支持哪些标准？
默认情况下，法规合规性仪表板显示的是 Azure 安全基准。 Azure 安全基准是 Microsoft 制定的 Azure 专属准则，适合基于常见合规框架的安全性与合规性最佳做法。 有关详细信息，请查看 [Azure 安全基准简介](../security/benchmarks/introduction.md)。

若要按任何其他标准跟踪合规性，需要将这些标准显式添加到仪表板中。
 
可添加其他标准，例如 Azure CIS 1.3.0、NIST SP 800-53、NIST SP 800-171、SWIFT CSP CSCF-v2020、UK Official 和 UK NHS、HIPAA、Canada Federal PBMM、ISO 27001、SOC2-TSP、PCI-DSS 3.2.1。  

更多标准将被添加仪表板中，可在[在法规合规性仪表板中自定义标准集](update-regulatory-compliance-packages.md)中查看详细信息。

### <a name="why-do-some-controls-appear-grayed-out"></a>一些控件为何灰显？
对于仪表板中的每一项合规性标准，都有一个列表列出该标准的控件。 对于适用控件，可查看及格评估和未及格评估的详细信息。

某些控件为灰显状态。这些控件没有任何与之关联的安全中心评估。 某些控件与过程或进程相关，因此无法通过安全中心进行验证。 有些控件尚未实现任何自动化策略或评估，但未来将实现这些内容。 而有些控件由平台负责，具体可查看[云中的共同责任](../security/fundamentals/shared-responsibility.md)。 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>可如何从仪表板中删除 PCI-DSS、ISO 27001 或 SOC2 TSP 等内置标准？ 
若要自定义法规合规性仪表板，且仅关注适用于你的标准，你可删除所显示的与你的组织无关的所有法规标准。 若要删除标准，请按照[从仪表板中删除标准](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)中的说明进行操作。

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>我根据建议执行了推荐的更改，但它没有在仪表板中反映出来
在采取行动实施建议后，要等 12 个小时才会看到合规性数据的变化。 评估大约每 12 小时运行一次，因此只有在评估运行后才会看到对合规性数据造成的影响。
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>我需要具备哪些权限才能访问仪表板？
若要查看合规性数据，你至少需要还对策略合规性数据具有读取者访问权限，也就是说只有安全读取者权限是不够的。 如果你是订阅的全局读取者，那么这也足够了。

要访问仪表板和管理标准，至少必须具备“资源策略参与者”和“安全管理员”角色 。


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>没有为我加载法规合规性仪表板
若要使用法规合规性仪表板，Azure 安全中心必须已在订阅级别启用 Azure Defender。 如果仪表板没有正确加载，请尝试以下步骤：

1. 清除浏览器缓存。
1. 尝试使用其他浏览器。
1. 尝试从其他网络位置打开仪表板。


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>可如何在仪表板中按照每种标准查看有关及格和不及格控件的报表？
在主仪表板上，可查看仪表板中有关 (1) 和“排名前 4”的最低合规性标准的及格和不及格控件的报表。 若要查看各项及格/不及格控件状态，请选择 (2)“显示所有 x”（其中，x 是你正在跟踪的标准数目）。 上下文平面会显示你跟踪的各项标准的合规性状态。

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="法规合规性仪表板的摘要部分。":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>可如何以非 PDF 的格式下载带有合规性数据的报表？
选择“下载报表”时，可选择标准和格式（PDF 或 CSV）。 生成的报表将反映你在门户筛选器中选择的当前订阅集。

- PDF 报表会显示你选择的标准的汇总状态
- CSV 报表会提供每项资源的详细结果，原因是它与每项控件的关联策略相关

目前不支持下载自定义策略的报表，仅支持下载所提供的法规标准的相关报表。


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>可如何在法规合规性仪表板中为某些策略创建例外？
对于安全中心内置的且包含在安全功能分数中的策略，可直接在门户中为一项或多项策略创建例外，如[从安全功能分数中排除资源和建议](exempt-resource.md)中所述。

对于其他策略，可按照 [Azure Policy 例外结构](../governance/policy/concepts/exemption-structure.md)在策略本身直接创建例外。


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>需要具有哪些 Azure Defender 计划或许可证才能使用法规合规性仪表板？
如果你在你的任何 Azure 资源类型上启用了任何 Azure Defender 包，那么你有权在安全中心访问法规合规性仪表板及其所有数据。


### <a name="how-do-i-know-which-benchmark-or-standard-to-use"></a>如何知道要使用哪个基准或标准？
[Azure 安全基准](/security/benchmark/azure/introduction) (ASB) 是 Microsoft 定义的一组规范安全建议和最佳做法，与常见的合规性控制框架（包括 [CIS Microsoft Azure 基础基准](https://www.cisecurity.org/benchmark/azure/)和 [NIST SP 800-53](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final)）一致。 ASB 是一种非常全面的基准，旨在建议各种 Azure 服务的最新安全功能。 我们建议希望最大限度改进其安全态势，并且能够将其合规性状态与行业标准保持一致的客户使用 ASB。

[CIS 基准](https://www.cisecurity.org/benchmark/azure/)由独立实体 Internet 安全中心 (CIS) 编写，其中包含有关部分核心 Azure 服务的建议。 我们使用 CIS 来尝试确保其建议与 Azure 中的最新增强功能保持同步，但是有时它们确实落伍了并且过时了。 尽管如此，一些客户还是喜欢将 CIS 的这一客观第三方评估用作其初始和主要安全基线。

自我们发布 Azure 安全基准以来，许多客户选择改用该基准来替代 CIS 基准。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用安全中心的法规符合性仪表板执行以下操作：

> [!div class="checklist"]
> * 查看和监视与重要的标准和法规相关的合规性情况。
> * 改进符合性状态，方法是：解决相关的建议问题，观察符合性分数的改进情况。

法规合规性仪表板可以大大简化合规性过程，显著缩短为 Azure、混合环境和多云环境收集合规性证据所需的时间。

若要了解更多信息，请参阅以下相关页面：

- [自定义法规合规性仪表板中的标准集](update-regulatory-compliance-packages.md) - 了解如何选择在法规合规性仪表板中显示的标准。 
- [管理 Azure 安全中心安全建议](security-center-recommendations.md) - 了解如何使用安全中心的建议来保护 Azure 资源。