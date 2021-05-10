---
title: 如何为 Azure 应用程序产品/服务创建计划
description: 了解如何在合作伙伴中心为 Azure 应用程序产品/服务创建计划。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370003"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>如何为 Azure 应用程序产品/服务创建计划

通过 Microsoft 商业市场销售的产品/服务必须至少有一种计划，才能在商业市场中列出你的产品/服务。 可以在同一个产品/服务内创建多种计划，每种计划包含不同的选项。 这些计划（有时称为 SKU）可能在计划类型（解决方案模板或托管应用程序）、盈利或受众方面有所不同。  有关计划的通用指南，请参阅[商业市场产品/服务的计划和定价](plans-pricing.md)。

## <a name="create-a-plan"></a>创建计划

1. 在“计划概述”选项卡的顶部附近，选择“+ 创建新计划”。 
1. 在出现的对话框的“计划 ID”框中，输入唯一的计划 ID。 客户可以在产品 URL 中看到此 ID。 最大长度为 50，可以使用小写字母数字字符、短划线或下划线。 选择“创建”后，不能修改计划 ID。
1. 在“计划名称”框中，输入此计划的唯一名称。 客户在决定要选择产品/服务中的哪个计划时会看到此名称。 最多可以使用 50 个字符。
1. 选择“创建”。

## <a name="define-the-plan-setup"></a>定义计划设置

使用“计划设置”选项卡可以设置计划类型，无论它是否重复使用其他计划中的技术配置以及计划应在哪些 Azure 区域中可用。 此选项卡上的答案会影响在其他选项卡上为此计划显示的字段。

### <a name="select-the-plan-type"></a>选择计划类型

- 从“计划类型”列表中，选择“解决方案模板”或“托管应用程序”。

“解决方案模板”计划完全由客户管理。 “托管应用程序”计划使发布者可以代表客户管理应用程序。 有关这两种计划类型的详细信息，请参阅[计划类型](plan-azure-application-offer.md#types-of-plans)。

### <a name="re-use-technical-configuration-optional"></a>重复使用技术配置（可选）

如果在产品/服务内创建了多个相同类型的计划，并且它们之间的技术配置相同，则可以重复使用其他计划中的技术配置。 此计划发布之后便无法更改此设置。

#### <a name="to-re-use-technical-configuration"></a>若要重复使用技术配置

1. 选中“此计划重复使用同种类型的其他计划中的技术配置”复选框。
1. 在出现的列表中，选择所需的基本计划。

> [!NOTE]
> 重复使用其他计划中的包时，整个“技术配置”选项卡会从此计划中消失。 其他计划中的技术配置详细信息（包括将来进行的任何更新）也会用于此计划。

### <a name="select-azure-regions-clouds"></a>选择 Azure 区域（云）

你的计划必须至少在一个 Azure 区域中提供。 在计划已发布并在特定 Azure 区域中可用后，无法从产品/服务中删除该区域。

#### <a name="azure-global-region"></a>Azure 全球区域

默认情况下，“Azure 全球区域”复选框为选中状态。 这样便可向具有商业市场集成的所有 Azure 全球区域中的客户提供你的计划。 对于托管应用程序计划，可以针对要在其中提供计划的市场进行选择。

若要从此区域删除你的产品/服务，请取消选中“Azure 全球”复选框。

#### <a name="azure-government-region"></a>Azure 政府区域

此区域为来自美国联邦、州、地方或部落实体以及有资格为其提供服务的合作伙伴的客户提供受控访问权限。 作为发布者，你负责任何符合性控制、安全措施和最佳做法。 Azure 政府使用物理隔离的数据中心和网络（仅位于美国）。

Azure 政府服务处理受特定政府法规和要求约束的数据。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要了解这些计划的认证，可以提供多达 100 个链接来描述它们。 这些链接可以直接链接到计划上的列表，也可以是你自己网站上关于这些认证的符合性说明的链接。 这些链接只对 Azure 政府客户可见。

##### <a name="to-select-the-azure-government-region"></a>选择 Azure 政府区域

1. 选中“Azure 政府”复选框。
1. 在“Azure 政府认证”下，选择“+ 添加认证(最多 100 个)”。
1. 在出现的框中，提供认证的名称和链接。
1. 若要添加其他认证，请重复执行步骤 2 和 3。

选择“保存草稿”，然后转到下一选项卡：“计划列表”。

## <a name="define-the-plan-listing"></a>定义计划列表

“计划列表”选项卡是配置计划的列表详细信息的位置。 此选项卡显示用于区分同一产品/服务中各计划的特定信息。 可以定义你希望显示在商业市场中的计划名称、摘要和说明。

1. “计划名称”框中显示你之前为此计划提供的名称。 可以随时更改该名称。 该名称在商业市场中显示为产品/服务的软件计划的标题，并且不得超过 100 个字符。
1. 在“计划摘要”框中，提供计划（而不是产品/服务）的简短摘要。 此摘要限制为 100 个字符。
1. 在“计划说明”框中，说明此软件计划的与众不同之处，以及与产品/服务中的其他计划的任何区别。 不要描述产品/服务，请只描述计划。 说明最多可包含 2000 个字符。
1. 选择“保存草稿”，然后继续操作。

## <a name="next-steps"></a>后续步骤

执行下列操作之一：

- 如果要配置解决方案模板计划，请转到[配置解决方案模板计划](create-new-azure-apps-offer-solution.md)。
- 如果要配置托管应用程序计划，请转到[配置托管应用程序计划](create-new-azure-apps-offer-managed.md)。
