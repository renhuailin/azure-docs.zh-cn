---
title: Azure 成本管理中的分组和筛选选项
description: 本文介绍如何使用成本管理中的分组和筛选选项。
author: bandersmsft
ms.author: banders
ms.date: 10/11/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 8cc4a0bfd7503a48b3df6887c05f268fcfae5733
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856327"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>成本分析中的分组和筛选选项

成本分析有很多分组和筛选选项。 本文有助于了解何时使用它们。

若要观看有关分组和筛选选项的视频，请观看 [Cost Management reporting by dimensions and tags](https://www.youtube.com/watch?v=2Vx7V17zbmk)（按维度和标记生成的成本管理报表）视频。 若要观看其他视频，请访问[成本管理 YouTube 频道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>分组和筛选属性

下表列出了一些成本分析中可用的最常用分组和筛选选项以及使用它们的时机。

| properties | 何时使用 | 说明 |
| --- | --- | --- |
| **可用性区域** | 按可用性区域细分 AWS 成本。 | 仅适用于 AWS 范围和管理组。 Azure 数据不包含可用性区域，会显示为“无可用性区域”。 |
| **计费周期** | 将 PAYG 成本按已开票或将开票的月份细分。 | 使用“计费周期”可以准确表示已开票的 PAYG 费用。 如果筛选到自定义的日期范围，请在计费周期之前和之后额外包括 2 天。 仅限确切的计费周期日期会与发票不匹配。 会显示计费周期内所有发票的成本。 使用 **发票 ID** 筛选特定发票。 仅适用于 PAYG 订阅，因为 EA 和 MCA 按日历月计费。 EA/MCA 帐户可以使用日期选取器中的日历月份或每月粒度来完成同一目标。 |
| **费用类型** | 细分使用量、购买、退款和未使用预留成本。 | 只有在使用实际成本而不使用摊销成本时，预留购买和退款才适用。 只有在查看摊销成本时，未使用预留成本才适用。 |
| **部门** | 按 EA 部门细分成本。 | 仅适用于 EA 和管理组。 PAYG 订阅没有部门，会显示为“无部门”或“未分配” 。 |
| **注册帐户** | 按 EA 帐户所有者细分成本。 | 仅适用于 EA 计费帐户、部门和管理组。 PAYG 订阅没有 EA 注册帐户，会显示为“无注册帐户”或“未分配” 。 |
| **频率** | 细分基于使用情况的一次性定期成本。 | |
| **发票 ID** | 按计费发票细分成本。 | 未开票费用尚无发票 ID，并且 EA 成本不包含发票详细信息，会显示为“无发票 ID”。  |
| **位置** | 按资源位置或区域细分成本。 | 购买项和市场使用情况可能显示为“未分配”或“无资源位置” 。 |
| **计量** | 按使用计量细分成本。 | 购买项和市场使用情况会显示为“无计量”。 请参阅“收费类型”以确定购买情况，参阅“发布者类型”以确定市场费用。 |
| **操作** | 按运营细分 AWS 成本。 | 仅适用于 AWS 范围和管理组。 Azure 数据不包括运营，会显示为“无运营” - 改用“计量” 。 |
| **定价模型** | 按需求、预留或点使用细分成本。 | 购买项显示为 **OnDemand**。 如果看到“不适用”，请按“预留”分组来确定使用是预留还是按需使用，并按“费用类型”分组来确定购买情况。
| **提供程序** | 按提供程序类型进行成本细分：Azure、Microsoft 365、Dynamics 365、AWS 等。 | 产品和业务线的标识符。 |
| **发布者类型** | Microsoft、Azure、AWS 和 Azure 市场成本细分。 | 对于 MCA 帐户，值为“Microsoft”，对于 EA 和即用即付帐户，值为“Azure”。  |
| **预留** | 按预留细分成本。 | 不与预留关联的任何使用情况或购买项会显示为“无预留”。 按“发布者类型”分组，以确定其他 Azure、AWS 或市场购买。 |
| **资源** | 按资源细分成本。 | 市场购买项显示为“其他市场购买项”，Azure 购买项（如预留和支持费用）显示为“其他 Azure 购买项” 。 按“发布者类型”分组或筛选，以识别其他 Azure、AWS 或市场购买项。 |
| **资源组** | 按资源组细分成本。 | 购买项、不与订阅关联的租户资源、未部署到资源组的订阅资源以及经典资源没有资源组，会显示为“其他市场购买项”、“其他 Azure 购买项”、“其他租户资源”、“其他订阅资源”、“$system”或“其他费用”     。 |
| **资源类型** | 按资源类型细分成本。 | 购买项和经典服务没有 Azure 资源管理器资源类型，会显示为“其他”、“经典服务”或“无资源类型”  。 |
| **服务名称** 或 **计量类别** | 按 Azure 服务细分成本。 | 购买项和市场使用情况会显示为“无服务名称”或“未分配”。 |
| **服务层级** 或 **计量子类别** | 按 Azure 使用情况计量子分类细分成本。 | 购买项和市场使用情况将为空，或显示为“未分配”。 |
| **订阅** | 按 Azure 订阅和 AWS 关联帐户细分成本。 | 购买项和租户资源可能显示为“无订阅”。 |
| **标记** | 按特定标记键的标记值细分成本。 | 购买项、不与订阅关联的租户资源、未部署到资源组的订阅资源以及经典资源不可标记，会显示为“标记不可用”。 在使用情况数据中不包括标记的服务会显示为“不支持标记”。 未在资源上指定标记的任何其他情况都将显示为“未标记”。 详细了解[每种资源类型的标记支持](../../azure-resource-manager/management/tag-support.md)。 |

有关术语的详细信息，请参阅[了解在 Azure 使用情况和费用文件中使用的术语](../understand/understand-usage.md)。

## <a name="changes-to-publisher-type-values"></a>“发布者类型”值的更改

对于具有 MCA 协议的客户，`Publisher type` 值“Azure”将替换为值“Microsoft”，以反映它适用于在“成本管理 + 计费”中跟踪的 Microsoft 的所有费用。  此更改于 2021 年 10 月 14 日生效。

注意以下事项：

- 成本分析中的已保存视图 - 任何使用筛选器`PublisherType` = “Azure”的已保存视图都将更新，以反映新值“Microsoft”。 
    - 所需操作：无。
- 具有 `PublisherType` 筛选器的预算 - 任何使用`PublisherType` = “Azure”筛选器创建的预算都将更新，以反映新值“Microsoft”。
    - 所需操作：无。
- 导出 - 2021 年 10 月 14 日之前生成的任何导出文件的 `PublisherType field` 的值为“Azure”，在 2021 年 10 月 14 日之后，值将为“Microsoft”。 
    - 所需操作：在将新文件与 2021 年 10 月 14 日之前下载的文件合并时，务必要考虑变化的值。
- 下载的数据 - 在从“成本分析”下载的任何文件中，2021 年 10 月 14 日之前下载的使用情况详细信息文件的 `PublisherType` 字段仍为旧值“Azure”。
    - 所需操作：在将新文件与 2021 年 10 月 14 日之前下载的文件合并时，务必要考虑变化的值。
- REST API 调用 - 如果使用“成本管理 + 计费”REST API 调用（按照值“Azure”筛选“发布者类型”字段），请在 2021 年 10 月 14 日之后更新调用以按照“Microsoft”进行筛选。 
    - 所需操作：在 REST API 调用中使用`Publisher type` = “Microsoft”来获取所有第一方费用。使用`Publisher type` = “Azure”的调用将不会返回任何数据。**

> [!NOTE]
> 如果用户有企业协议 (EA) 帐户或即用即付 (PAYG) 帐户，此更改不会造成影响。


## <a name="next-steps"></a>后续步骤

- [开始分析成本](./quick-acm-cost-analysis.md)。
