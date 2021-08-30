---
title: 使用 Azure 安全中心中的监管合规仪表板
description: 了解如何在安全中心的监管合规仪表板中添加和删除法规标准
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/05/2021
ms.author: memildin
ms.openlocfilehash: 523375ff69d6139a1e910b9253a6816235bfecc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741969"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>在监管合规仪表板中自定义标准集

Azure 安全中心会不断将资源的配置与行业标准、法规和基准中的要求进行比较。 监管合规仪表板可根据用户如何满足特定的合规性要求来深入了解用户的合规情况。

> [!TIP]
> 在[常见问题解答](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)中详细了解安全中心的监管合规性仪表板。

## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>安全中心中如何体现监管合规标准？

行业标准、监管标准和基准在安全中心的监管合规仪表板中均有体现。 每个标准都是 Azure 策略中定义的一项举措。

要在仪表板中查看作为评估结果的合规性数据，请从“安全策略”页面中向用户的管理组或订阅源添加合规性标准。 如需详细了解 Azure Policy 和计划，请参阅[使用全策略](tutorial-security-policy.md)。

为所选范围指定标准或基准后，该标准将出现在监管合规仪表板中，并将所有相关的合规性数据作为评估结果体现。 还可以为任何已指定的标准下载摘要报告。

Microsoft 会自行跟踪法规标准，一段时间后，自动提高这些标准在部分软件包的覆盖率。 当 Microsoft 发布该标准的新内容时，仪表板中将自动显示这些新内容，作为对应到标准中控制措施的新策略使用。


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>安全中心提供了哪些监管合规标准？

默认情况下，将为每个订阅分配“Azure 安全基准”。 Azure 安全基准是 Microsoft 制定的 Azure 专属准则，适合基于常见合规框架的安全性与合规性最佳做法采用。 [详细了解 Azure 安全基准](/security/benchmark/azure/introduction)。

现在，可添加如下标准：

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- UK Official 和 UK NHS
- 加拿大联邦 PBMM
- Azure CIS 1.3.0
- CMMC 级别 3
- 限制性的新西兰 ISM

标准将在仪表板可用时添加到仪表板。


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>向仪表板添加监管标准

以下步骤说明如何添加包以监视是否符合与一种支持的监管标准。

> [!NOTE]
> 若要将标准添加到仪表板，订阅必须启用 Azure Defender。 只有作为所有者或策略参与者的用户才有所需的权限来添加合规标准。 

1. 从安全中心的边栏中，选择“监管合规”以打开监管合规仪表板。 可在此处查看当前分配给当前所选订阅的合规标准。   

1. 在页面顶部，选择“管理合规策略”。 “策略管理”页将出现。

1. 选择要为其管理监管合规态势的订阅或管理组。 

    > [!TIP]
    > 建议选择标准所适用的最高范围，以便为所有嵌套资源聚合和跟踪合规数据。 

1. 若要添加与组织相关的标准，请扩展“行业和监管标准”部分，并选择“添加更多标准”。

1. 在“添加监管合规标准”页中，可以搜索任何可用标准，包括：

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - UKO and UK NHS
    - **加拿大联邦 PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC 级别 3**
    - **受限于新西兰 ISM**
    
    ![将监管标准添加到 Azure 安全中心的监管合规仪表板。](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. 选择“添加”，然后输入特定计划的所有必要详细信息，如范围、参数和修正。

1. 从安全中心的边栏中，再次选择“监管合规”以返回到监管合规仪表板。

    新标准会出现在行业和监管标准的列表中。 

    > [!NOTE]
    > 新添加的标准可能需要几个小时才能出现在合规仪表板中。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="法规合规性仪表板。" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>从仪表板中删除标准

如果提供的任何标准都不适用于用户的组织，那么可以直接从 UI 中删除这些标准。 这样，用户便可以进一步自定义监管合规仪表板，仅使用适用的标准。

请执行以下步骤删除标准：

1. 在“安全中心”的菜单中，选择“安全策略”。

1. 选择要从中删除标准的相关订阅。

    > [!NOTE]
    > 可以删除订阅中的标准，但不能删除管理组中的标准。 

    此时将打开“安全策略”页。 对于所选订阅，会显示默认策略、行业和监管标准以及用户创建的任何自定义措施。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="从 Azure 安全中心的监管合规仪表板中删除监管标准。":::

1. 对于要删除的标准，选择“禁用”。 确认窗口随即打开。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="确认确实要删除所选的监管标准。":::

1. 请选择“是”。 将删除此标准。 


## <a name="next-steps"></a>后续步骤

本文介绍了如何添加合规标准以监视是否符合监管标准和行业标准。

如需相关材料，请参阅以下页面内容：

- [Azure 安全基准](/security/benchmark/azure/introduction)
- [安全中心监管合规仪表板](security-center-compliance-dashboard.md) - 了解如何通过安全中心和外部工具跟踪和导出监管合规数据
- [使用安全策略](tutorial-security-policy.md)