---
title: 分摊 Azure 成本
description: 本文介绍如何创建成本分摊规则，以将订阅、资源组或标记的成本分摊给其他订阅、资源组或标记。
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 7696dc683d41080f5be2c97ebcb0cdf314e2098d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706259"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>创建和管理 Azure 成本分摊规则（预览版）

大型企业的 Azure 服务或资源通常是集中管理的，但由不同的内部部门或业务部分使用。 通常，集中管理团队想要将共享服务的成本重新分摊给频繁使用这些服务的内部部门或组织业务部门。 本文可帮助你了解和使用成本管理中的成本分摊。

利用成本分摊，你可以将共享服务的成本从订阅、资源组或标记重新分配或分发到组织中的其他订阅、资源组或标记。 成本分摊会将共享服务的成本转移到使用这些服务的内部部门或业务部门拥有的其他订阅、资源组或标记。 换句话说，成本分摊有助于管理和显示从某处到另一处的成本责任。

成本分摊不会影响计费发票， 也不会更改计费责任。 成本分摊的主要目的是帮助你向其他人收回成本。 所有退款过程均发生在 Azure 之外的组织中。 成本分摊可以通过在向他们重新分配或分发成本时显示成本，从而收回成本。

分摊的成本显示在成本分析中。 它们显示为与创建成本分摊规则时指定的目标订阅、资源组或标记相关联的附加项。

> [!NOTE]
> 成本管理的成本分摊功能目前提供公共预览版。 成本管理中的某些功能可能不受支持，或者其功能受限。

## <a name="prerequisites"></a>先决条件

- 成本分摊目前仅支持签署了 [Microsoft 客户协议](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)或[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 的客户。
- 要创建或管理成本分摊规则，你必须针对[企业协议](../manage/understand-ea-roles.md)使用企业管理员帐户。 或者，必须是 Microsoft 客户协议的[计费帐户](../manage/understand-mca-roles.md)所有者。

## <a name="create-a-cost-allocation-rule"></a>创建成本分摊规则

1. 在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。
2. 导航到“成本管理 + 计费” > “成本管理”。 
3. 在“设置” > “配置”中 ，选择“成本分摊(预览)”。
4. 确保选择了正确的 EA 注册或计费帐户。
5. 选择“+添加”  。
6. 输入成本分摊规则名称的描述性文本。

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="演示如何创建规则名称的示例" lightbox="./media/allocate-costs/rule-name.png" :::

规则的评估开始日期用于生成预填充的成本分摊百分比。

1. 选择“添加源”，然后选择订阅、资源组或标记以选择要分配的成本。
2. 选择“添加目标”，然后选择要接受分摊的成本的订阅、资源组或标记。
3. 如果需要创建其他成本分摊规则，请重复此过程。

## <a name="configure-the-allocation-percentage"></a>配置分摊百分比

配置分摊百分比以定义如何在指定目标间按比例划分成本。 你可以手动定义整数百分比来创建分摊规则。 也可以根据指定目标中计算、存储或网络的当前使用量，按比例拆分成本。

按计算成本、存储成本或网络成本分配成本时，比例百分比是通过评估所选目标的成本得出的。 成本与当前计费月份的资源类型相关联。

当分配与总成本成正比的成本时，按当前计费月份所选目标的总和或总成本分配比例百分比。

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="显示分摊百分比的示例" lightbox="./media/allocate-costs/cost-distribution.png" :::

设置后，将修复定义的预填充百分比。 它们用于所有正在进行的分摊。 只有手动更新规则时，该百分比才会更改。

1. 在“预填充百分比”列表中选择以下选项之一。
    - **均匀分摊** - 每个目标接受的总成本的百分比比例相等。
    - **总成本** - 根据目标的总成本创建与目标成正比的比率。 该比率用于分摊来自选定源的成本。
    - **计算成本** - 根据目标的 Azure 计算成本（[Microsoft.Compute](/azure/templates/microsoft.compute/allversions) 命名空间中的资源类型）创建与目标成正比的比率。该比率用于分摊来自选定源的成本。
    - **存储成本** - 根据目标的 Azure 存储成本（[Microsoft.Storage](/azure/templates/microsoft.storage/allversions) 命名空间中的资源类型）创建与目标成正比的比率。 该比率用于分摊来自选定源的成本。
    - **网络成本** - 根据目标的 Azure 网络成本（[Microsoft.Network](/azure/templates/microsoft.network/allversions) 命名空间中的资源类型）创建与目标成正比的比率。 该比率用于分摊来自选定源的成本。
    - **自定义** - 允许手动指定整数百分比。 指定的总比例必须等于 100%。
1. 配置规则后，选择“创建”。

分摊规则将开始处理。 当规则处于活动状态时，所选源的所有成本将分摊给指定的目标。

> [!NOTE] 
> 新规则可能需要长达两个小时才能处理完成并处于活动状态。

下面的视频演示了如何创建成本分摊规则。

>[!VIDEO https://www.youtube.com/embed/nYzIIs2mx9Q]

## <a name="verify-the-cost-allocation-rule"></a>验证成本分摊规则

当成本分摊规则处于活动状态时，所选源的成本将分摊给指定的分摊目标。 使用以下信息来验证成本是否已正确分摊到目标。

### <a name="view-cost-allocation-for-a-subscription"></a>查看订阅的成本分摊

在成本分析中查看分摊规则的影响。 在 Azure 门户中，转到 [订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 在活动成本分摊规则的目标列表中选择一个订阅。 然后在菜单中选择“成本分析”。 在“成本分析”中，选择“分组条件”，然后选择“成本分摊”。 生成的视图显示订阅生成的快速成本细分。 此外，还会显示分摊给订阅的成本，如下图所示。

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="显示成本细分的示例" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>查看资源组的成本分摊

使用类似的过程查看成本分摊规则对资源组的影响。 在 Azure 门户中，转到[资源组](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)。 在活动成本分摊规则的目标列表中选择一个资源组。 然后在菜单中选择“成本分析”。 在“成本分析”中，选择“分组条件”，然后选择“成本分摊”。 此视图显示资源组生成的快速成本细分。 此外，还显示分摊给资源组的成本。

### <a name="view-cost-allocation-for-tags"></a>查看标记的成本分摊

在 Azure 门户中，导航到“成本管理 + 计费” > “成本管理” > “成本分析”  。 在“成本分析”中，选择“添加筛选器”。 选择“标记”，然后选择标记键和已向其分摊成本的标记值。

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="显示标记项成本的示例" lightbox="./media/allocate-costs/tagged-costs.png" :::

### <a name="view-cost-allocation-in-the-downloaded-usage-details-and-in-exports-csv-files"></a>在已下载的使用情况详细信息和导出 CSV 文件中查看成本分摊

已下载的使用情况详细信息文件和导出的数据中也提供了成本分摊规则。 数据文件具有列名称 `costAllocationRuleName`。 如果成本分摊规则适用于使用情况详细信息或导出文件中的条目，则使用成本分摊规则名称填充该行。 下面的示例图像通过源订阅的一个条目显示负费用。 这就是从中获取分摊成本的费用。 成本分摊规则的目标还有正费用。

:::image type="content" source="./media/allocate-costs/rule-costs-allocated.png" alt-text="屏幕截图显示使用情况详细信息文件中的分摊成本。" lightbox="./media/allocate-costs/rule-costs-allocated.png" :::

#### <a name="azure-invoice-reconciliation"></a>Azure 发票对帐 

使用情况详细信息文件还用于 Azure 发票对帐。 在对帐期间显示任何内部分摊成本可能会造成混淆。 若要减少任何潜在的混淆情况并与发票上显示的数据保持一致，可以筛选掉任何成本分摊规则。 删除成本分摊规则后，使用情况详细信息文件应与计费订阅发票显示的成本匹配。

:::image type="content" source="./media/allocate-costs/rule-name-filtered.png" alt-text="屏幕截图显示已筛选掉规则名称的分摊成本" lightbox="./media/allocate-costs/rule-name-filtered.png" :::

## <a name="edit-an-existing-cost-allocation-rule"></a>编辑现有的成本分摊规则

你可以编辑成本分摊规则以更改源或目标，或者是否要更新计算、存储或网络选项的预填充百分比。 按照创建规则的相同方式编辑规则。 修改现有规则最多可能需要两个小时才能重新处理。

## <a name="current-limitations"></a>当前限制

目前，成本管理中的成本分析、预算和预测视图支持成本分摊。 分摊的成本还显示在订阅列表和“订阅”概述页中。

成本分摊公共预览版当前不支持以下项：

- [使用情况详细信息](/rest/api/consumption/usagedetails/list) API 公开的数据
- 计费订阅区域
- [成本管理 Power BI 应用](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Power BI Desktop 连接器](/power-bi/connect-data/desktop-connect-azure-cost-management)


## <a name="next-steps"></a>后续步骤

- 阅读[成本管理 + 计费常见问题解答](../cost-management-billing-faq.yml)，了解关于成本分摊的问题及解答。
- 使用[成本分摊 Rest API](/rest/api/cost-management/costallocationrules) 创建或更新分摊规则
- 详细了解[如何通过成本管理优化云投资](cost-mgt-best-practices.md)