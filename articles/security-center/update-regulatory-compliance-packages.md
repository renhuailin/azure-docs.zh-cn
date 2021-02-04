---
title: 使用 Azure 安全中心中的规章相容性仪表板
description: 了解如何在安全中心的法规符合性仪表板中添加和删除法规标准
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 3fca96c6cf11e0b35cd830fe4c0d8ccac8e66bd2
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548858"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>自定义合规性仪表板中的标准集

Azure 安全中心会不断地将资源的配置与行业标准、法规和基准中的要求进行比较。 **规章相容性仪表板** 根据你满足特定合规性要求的方式，深入了解你的符合性状态。


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>安全中心中的规章遵从标准是如何表示的？

行业标准、法规标准和基准在安全中心的符合性仪表板中表示。 每个标准都是在 Azure 策略中定义的一个计划。

若要查看在仪表板中映射为评估的符合性数据，请在 " **安全策略** " 页中向管理组或订阅添加符合性标准。 若要了解有关 Azure 策略和计划的详细信息，请参阅使用 [安全策略](tutorial-security-policy.md)。

将标准或基准分配给所选作用域后，标准将显示在你的符合性仪表板中，并将所有关联的符合性数据映射为评估。 你还可以针对已分配的任何标准下载汇总报表。

Microsoft 会自行跟踪法规标准，并随着时间的推移，自动改进其在某些包中的覆盖范围。 当 Microsoft 发布新的计划内容时，它会自动显示在仪表板中，作为新策略映射到标准中的控件。


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>安全中心提供了哪些规章遵从标准？

默认情况下，将为每个订阅分配 **Azure 安全基准** 。 这是基于 Azure 的 Microsoft 创作准则，适用于基于常见符合性框架的安全性和符合性最佳实践。 [详细了解 Azure 安全基准](../security/benchmarks/introduction.md)。

你还可以添加如下标准：

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- 英国官方和英国 NHS
- 加拿大联邦 PBMM
- Azure CIS 1.1.0

标准将在仪表板可用时添加到仪表板。


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>向仪表板添加法规标准

以下步骤说明如何添加包以监视是否符合与一种支持的监管标准。

> [!NOTE]
> 若要将标准添加到仪表板，订阅必须启用 Azure Defender。 此外，只有作为所有者或策略参与者的用户具有添加符合性标准所必需的权限。 

1. 从安全中心的边栏中，选择“监管合规”以打开监管合规仪表板。 可在此处查看当前分配给当前所选订阅的合规标准。   

1. 在页面顶部，选择“管理合规策略”。 “策略管理”页将出现。

1. 选择要为其管理监管合规态势的订阅或管理组。 

    > [!TIP]
    > 建议选择标准所适用的最高范围，以便为所有嵌套资源聚合和跟踪合规数据。 

1. 若要添加与组织相关的标准，请单击“添加更多标准”。 

1. 从 " **添加合规性标准** " 页，可以搜索任何可用的标准，包括：

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - UKO and UK NHS
    - **加拿大联邦 PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![将法规标准添加到 Azure 安全中心的符合性仪表板](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. 选择 " **添加** "，然后输入特定计划的所有必要详细信息，如范围、参数和修正。

1. 从安全中心的边栏中，再次选择“监管合规”以返回到监管合规仪表板。

    新标准会出现在行业和监管标准的列表中。 

    > [!NOTE]
    > 新添加的标准可能需要几个小时才能出现在合规仪表板中。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="法规符合性仪表板" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="removing-a-standard-from-your-dashboard"></a>从仪表板中删除标准

如果任何提供的法规标准与您的组织不相关，则将其从 UI 中删除是一个简单的过程。 这样，你便可以进一步自定义合规性仪表板，仅关注适用于你的标准。

删除标准：

1. 从安全中心的菜单中，选择 " **安全策略**"。

1. 选择要从中删除标准的相关订阅。

    > [!NOTE]
    > 你可以从订阅中删除标准，但不能从管理组中删除。 

    此时会打开 "安全策略" 页。 对于所选订阅，它会显示默认策略、行业和法规标准以及您创建的任何自定义计划。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="从 Azure 安全中心的规章相容性仪表板中删除法规标准":::

1. 对于要删除的标准，选择 " **禁用**"。 此时将显示一个确认窗口。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="确认确实要删除所选的规章标准":::

1. 请选择“是”。 将删除此标准。 


## <a name="next-steps"></a>后续步骤

本文介绍了如何 **添加符合性标准** 来监视遵从法规和行业标准的符合性。

有关相关材料，请参阅以下页面：

- [Azure 安全基准](../security/benchmarks/introduction.md)
- [安全中心规章相容性仪表板](security-center-compliance-dashboard.md) -了解如何通过安全中心和外部工具跟踪和导出你的法规遵从性数据
- [使用安全策略](tutorial-security-policy.md)