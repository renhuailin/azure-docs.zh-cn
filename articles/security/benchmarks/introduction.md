---
title: Azure 安全基准简介
description: 安全基准简介
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369185"
---
# <a name="azure-security-benchmark-introduction"></a>Azure 安全基准简介

Azure 中每天都会发布新的服务和功能，开发人员很快就会发布在这些服务上构建的新云应用程序，而攻击者也总是会寻找新方法来攻击配置有误的资源。 云在快速发展，开发人员在快速进步，而攻击者始终在“推陈出新”。 你如何跟上形势并确保云部署的安全？ 云系统的安全做法与本地系统的安全做法有何不同？ 如何监视多个独立开发团队之间的一致性？

Microsoft 已经发现，使用安全基准可以帮助你快速保护云部署。 来自云服务提供商的基准建议提供了一个在你的环境中选择特定安全配置设置的起点，并使你能够快速降低组织面临的风险。

Azure 安全基准包含一系列具有重要影响的安全建议，可用于帮助保护在 Azure 中使用的服务：

- 安全控制：一般而言，在你的 Azure 租户和 Azure 服务中，这些建议都是适用的。 每个建议都会标识出利益干系人的列表，这些利益干系人通常会涉及到基准的规划、审批或实现。 
- **服务基线**：这些基线将控制应用于单个 Azure 服务，以提供有关该服务的安全配置的建议。

## <a name="implement-the-azure-security-benchmark"></a>实现 Azure 安全基准
- 通过查看有关企业控制机制的[文档](overview.md)和特定于服务的基线来规划你的 Azure 安全基准实现，以规划控制框架，以及该框架映射到 CIS (Controls v7.1) 和 NIST (SP 800-53) 框架等指南的方式。
- 使用 Azure 安全中心[合规性仪表板](../../security-center/security-center-compliance-dashboard.md)来监视符合 Azure 安全基准状态（和其他控制集）的程度。
- 建立规范措施，以通过 Azure 蓝图和 Azure Policy 自动执行安全配置，并强制符合 Azure 安全基准（以及组织中的其他要求）。
 
请注意，Azure 安全基准 v2 与 [Microsoft 安全最佳做法](/security/compass/microsoft-security-compass-introduction)（以前称为 Azure 安全罗盘）是一致的，因此，通过 Azure 安全基准就能全面了解 Microsoft 的 Azure 安全建议。

## <a name="common-use-cases"></a>常见用例

Azure 安全基准经常用于为以下类型的客户或服务合作伙伴解决这些常见难题：
- Azure 的新用户，正在寻找安全最佳做法来确保安全部署。
- 需要改善现有 Azure 部署的安全状况，以优先处理级别最高的风险并采取缓解措施。
- 需要审批要用于技术和业务的 Azure 服务是否符合特定安全准则。
- 需要符合来自政府或高度管控行业（如金融和医疗保健）的客户（或需要为这些客户构建系统的服务供应商）的法规要求。 这些客户需要确保其 Azure 配置符合行业框架（如 CIS、NIST 或 PCI）中指定的安全功能。 Azure 安全基准提供了一种高效的方法，其控制要求已预先映射到了这些行业基准。

## <a name="terminology"></a>术语

Azure 安全基准文档中经常使用术语“控制”、“基准”和“基线”，因此了解 Azure 如何使用这些术语很重要。


| 术语 | 说明 | 示例 |
|--|--|--|
| 控制 | “控制”是对需要实现的功能或活动的概要说明，并非特定于某种技术或实现。 | 数据保护是一项安全控制。 这项控制包含为了帮助确保数据受到保护而必须执行的特定操作。 |
| 基准 | “基准”包含针对特定技术（如 Azure）的安全建议。 这些建议按其所属的控制进行分类。 | Azure 安全基准包含特定于 Azure 平台的安全建议 |
| 基线 | 基线是有关单个 Azure 服务的基准的实现。 每个组织都会决定 Azure 实现范围内需要的基准建议和相应的配置。 | Contoso 公司希望通过遵循 Azure SQL 安全基线中建议的配置来启用 Azure SQL 安全功能。

欢迎你提供有关 Azure 安全基准的反馈！ 建议你在以下反馈区域中提供评论。 如果你希望以更私密的方式将你的建议告知 Azure 安全基准检验团队，欢迎你在 https://aka.ms/AzSecBenchmark 中填写表单
