---
title: 共同销售要求 |Azure Marketplace
description: 了解 Microsoft 商用 marketplace 中的产品/服务必须满足的要求，才能符合共同销售的 incentivized 或共同销售的状态。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 2/24/2021
ms.openlocfilehash: 9fb86b3d4a46a70d71aa0c3fe9fd1b4a1ea6f298
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120124"
---
# <a name="co-sell-requirements"></a>联合销售要求

本文提供了不同级别的共同销售状态的要求。 有关支持共同销售的产品/服务类型的最新列表，请参阅  [配置商业 marketplace 产品/服务的共同销售](commercial-marketplace-co-sell.md)。 有关共同销售的概述，请参阅 [与 Microsoft 销售团队和合作伙伴共同销售概述](marketplace-co-sell.md)。

下表显示了所有可能的共同销售状态：

| 状态 | 评论 |
| ------------ | ------------- |
| 不合作销售 | 尚未满足 [共同销售就绪状态](#requirements-for-co-sell-ready-status) 的最低要求。 |
| 合作销售就绪 | 已满足 [共同销售就绪状态的所有要求](#requirements-for-co-sell-ready-status) 。 |
| Azure IP 共同销售 incentivized | 除了 [这些附加要求](#requirements-for-ip-co-sell-incentivized-status)外，还满足共同销售的现成需求。 |
| Biz 应用 ISV 连接高级激励  | 此状态适用于 Dynamics 365 和 Power Apps 产品/服务，并指示已满足 [此状态](#requirements-for-biz-apps-isv-connect-premium-incentive-status) 的所有要求。 |
|||

## <a name="requirements-for-co-sell-ready-status"></a>共同销售就绪状态的要求

为实现共同销售就绪状态，必须满足以下要求：

**所有合作伙伴**：

- 合作伙伴中心中有一个 MPN ID 和一个有效的 [商用 marketplace 帐户](./partner-center-portal/create-account.md)。
- 请确保在合作伙伴中心拥有完整的 [业务配置文件](/partner-center/create-a-marketing-profile.md) 。 作为合格的 Microsoft 合作伙伴，你的业务配置文件可帮助你向寻找你的独特解决方案和专业知识的客户展示你的业务需求，从而实现了 [引用](/partner-center/referrals.md)。
- 完成 " **与 Microsoft 共同销售** " 选项卡，并将产品/服务发布到 "商业应用商店"。
- 为每个共同销售的符合条件的地理位置和所需物料清单提供销售联系人) 

**服务合作伙伴**：

- 对于 _服务解决方案_ 类型的产品/服务，您必须在任何能力领域具有活动金牌资格。
 
**Business Applications isv**：

- Dynamics 365 客户参与 & PowerApps 和 Dynamics 365 财务 & Ops (，Dynamics 365 business central) ，PowerApps 解决方案需要 ISV Connect 注册。

### <a name="complete-the-co-sell-with-microsoft-tab"></a>完成 "与 Microsoft 共同销售" 选项卡

发布或更新产品/服务时，请提供有关 " **与 Microsoft 共同销售** " 选项卡上的所有所需信息，如为 [商业 Marketplace 产品配置共同销售](commercial-marketplace-co-sell.md)中的详细信息。 这包括提供下列文档：

- 解决方案/产品/服务
- 解决方案/产品/服务

我们提供了可帮助您创建这些文档的模板。 有关 "与 Microsoft 共同销售" 选项卡的必需和可选信息的详细信息，请参阅 [配置商业 marketplace 产品/服务的共同销售](commercial-marketplace-co-sell.md)。

### <a name="publish-your-offer-live"></a>实时发布产品/服务

若要符合共同销售就绪状态的条件，你的产品/服务或解决方案必须至少发布到一个商业 marketplace 在线商店： Azure Marketplace 或 Microsoft AppSource。 有关将产品/服务发布到商业市场的信息，请参阅 [按产品/服务类型发布指南](publisher-guide-by-offer-type.md)。 如果之前未在商业应用商店中发布产品/服务，请确保拥有 [商业 marketplace 帐户](./partner-center-portal/create-account.md)。

## <a name="requirements-for-ip-co-sell-incentivized-status"></a>IP 共同销售 incentivized 状态的要求

Azure IP 共同销售 incentivized 状态适用于以下产品/服务类型：

- Azure 应用程序
- Azure 容器
- Azure 虚拟机
- IoT Edge 模块
- 软件即服务 (SaaS)

实现共同销售就绪状态后，有三个额外的要求来实现 Azure IP 共同销售 incentivized 状态：

要求 1-实现以下内容：

- 在 _组织级别_，在尾随12个月内生成至少 $100000 USD 的 Azure 消耗收入阈值。 这可通过 Azure 解决方案的组合获得。 如果产品/服务在商业应用商店中是事务，则可以满足此要求，只需满足计费的收入阈值 $100000 美元。

要求 2-通过 Microsoft 技术验证来处理基于 Azure 的解决方案：
- 技术验证必须确认超过50% 的产品/服务的基础结构使用 Azure 上的可重复的 IP 代码。 请注意，默认情况下，事务 Azure Vm 和商用 marketplace 上的 Azure 应用程序解决方案将满足此要求。

要求3–提供参考体系结构关系图：
- 上传参考体系结构图表，其中包含合作伙伴中心的共同销售文档以供查看。 有关创建此关系图的指南，请参阅 [参考体系结构关系图](reference-architecture-diagram.md)。 有关上传关系图的信息，请参阅 [配置商业 marketplace 产品/服务的共同销售](commercial-marketplace-co-sell.md)。

## <a name="requirements-for-biz-apps-isv-connect-premium-incentive-status"></a>Biz Apps 的要求 ISV 连接高级激励状态

此状态适用于基于 IP 的解决方案、应用和在 Dynamics 365 或 Power Apps 上构建的服务。

不需要 (以上列出) 的共同销售就绪状态即可实现 Biz Apps ISV Connect 标准激励。 但在应用程序实现共同销售就绪状态后，可将其视为 Biz 应用 ISV 连接高级激励状态，具体取决于过去12个月的收入份额和共同销售结果/阈值。

要求-你必须在 [ISV Connect](business-applications-isv-program.md) 高级层中具有活动注册。

## <a name="next-steps"></a>后续步骤

- [配置商业 marketplace 产品/服务的共同销售](commercial-marketplace-co-sell.md)
