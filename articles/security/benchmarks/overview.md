---
title: Azure 安全基准 V2 概述
description: Azure 安全基准 V2 概述
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3bc97a932e622838315f717cc7b036a559787805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369151"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Azure 安全基准 (V2) 概述

Azure 安全基准 (ASB) 提供了说明性的最佳做法和建议，以帮助提高 Azure 上工作负载、数据和服务的安全性。

此基准属于一组全面的安全指南，这组指南还包括：

- 云采用框架 - 安全方面的指导，包括[策略](/azure/cloud-adoption-framework/strategy/define-security-strategy)、[角色和责任](/azure/cloud-adoption-framework/organize/cloud-security)、[Azure 十大安全最佳做法](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)和[参考实现](/azure/cloud-adoption-framework/ready/enterprise-scale/)。
- Azure 架构良好的框架 - 有关在 Azure 上[保护工作负荷](/assessments/?mode=pre-assessment&session=local)的指导。
- Microsoft 安全最佳做法 - [建议](/security/compass/microsoft-security-compass-introduction)以及 Azure 上的示例。

 Azure 安全基准侧重于以云为中心的控制领域。 这些控制与众所周知的安全基准（例如 Internet 安全中心 (CIS) Controls 版本 7.1 和美国国家标准与技术研究院 (NIST) SP 800-53 所述基准）一致。
Azure 安全基准包含以下控制：

| ASB 控制领域 | 说明 
|--|--|
| [网络安全 (NS)](security-controls-v2-network-security.md) | 网络安全包含用于保护 Azure 网络的控制措施，包括保护虚拟网络、建立专用连接、阻止和减少外部攻击以及保护 DNS。 |
| [标识管理 (IM)](security-controls-v2-identity-management.md) | 标识管理包括用于使用 Azure Active Directory 建立安全标识和访问控制的控制措施，其中包括使用单一登录、强身份验证、用于应用程序的托管标识（和服务主体）、条件访问和帐户异常监视。 |
| [特权访问 (PA)](security-controls-v2-privileged-access.md) | 特权访问包括用于保护对你的 Azure 租户和资源的特权访问的控制措施，包括一系列用于避免管理模型、管理帐户和特权访问工作站面临有意和无意的风险的控制措施。 |
| [数据保护 (DP)](security-controls-v2-data-protection.md) | 数据保护涵盖控制静态数据保护、传输中的数据保护以及通过授权访问机制实现的数据保护，包括使用 Azure 中的访问控制、加密和日志记录发现、分类、保护和监视敏感数据资产。 |
| [资产管理 (AM)](security-controls-v2-asset-management.md) | 资产管理包括用于确保 Azure 资源安全可见性和治理的控制措施，包括以下几方面的建议：安全人员的权限、对资产清单的安全访问，以及管理对服务和资源的审批（盘点、跟踪和更正）。 |
| [日志记录和威胁检测 (LT)](security-controls-v2-logging-threat-detection.md) | 日志记录和威胁检测涵盖了用于检测 Azure 上的威胁以及启用、收集和存储 Azure 服务的审核日志的控件，包括使用控件启用检测、调查和修正过程，以利用 Azure 服务中的本机威胁检测生成高质量的警报；它还包括使用 Azure Monitor 收集日志，使用 Azure Sentinel 集中进行安全分析、时间同步和日志保留。 |
| [事件响应 (IR)](security-controls-v2-incident-response.md) | 事件响应包括对事件响应生命周期（准备、检测、分析、包含和事后活动）的控制措施，包括使用 Azure 安全中心和 Sentinel 等 Azure 服务自动执行事件响应过程。 |
| [状态和漏洞管理 (PV)](security-controls-v2-posture-vulnerability-management.md) | 状况和漏洞管理侧重于评估和改进 Azure 安全状况的控制措施，包括漏洞扫描、渗透测试和修正，以及 Azure 资源中的安全配置跟踪、报告和更正。 |
| [终结点安全 (ES)](security-controls-v2-endpoint-security.md) | 终结点安全保护对终结点检测和响应的控制措施，包括在 Azure 环境中对终结点使用终结点检测和响应 (EDR) 和反恶意软件服务。 |
| [备份和恢复 (BR)](security-controls-v2-backup-recovery.md) | 备份和恢复包括用于确保在不同服务层执行、验证和保护数据和配置备份的控制措施。 |
| [治理和策略 (GS)](security-controls-v2-governance-strategy.md) | 治理和策略提供的指导可确保使用一致的安全策略和记录在案的治理方法来指导和维持安全保障，包括为不同的云安全功能、统一的技术策略以及支持策略和标准建立角色和责任。 |

## <a name="azure-security-benchmark-recommendations"></a>Azure 安全基准检验建议

每项建议都包含以下信息：

- **Azure ID**：与建议对应的 Azure 安全基准检验 ID。
- **CIS Controls v7.1 ID**：与此建议对应的 CIS Controls v7.1 控制措施。
- **NIST SP 800-53 r4 ID**：与此建议对应的 NIST SP 800-53 r4（中等）控制措施。
- **详细信息**：此建议的原理阐述，以及关于如何实现建议的指南的链接。 如果 Azure 安全中心支持此建议，这些信息也会列出。
- **责任**：是由客户还是服务提供商负责（或二者共同负责）实现此建议。 安全责任将在公有云中共同分担。 某些安全控制仅适用于云服务提供商，因此该提供商负责处理相关事项。 这些是通常的看法 - 某些单独服务的责任不同于 Azure 安全基准中列出的责任。 单个服务的基线建议中介绍了这些差异。
- **客户安全利益干系人**：可就相应控制措施进行追究、问责、或咨询的客户组织的 [安全功能](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)。 它在各组织中可能有所不同，具体取决于公司的安全组织结构，以及你设置的与 Azure 安全性相关的角色和职责。

> [!NOTE]
> ASB 和行业基准（如 NIST 和 CI）之间的控制映射仅指示特定的 Azure 功能可用于完全或部分解决 NIST 或 CIS 中定义的控制要求。 应注意，此类实现不一定意味着完全符合 CIS 或 NIST 中的相应控制措施。

我们欢迎你提供详细反馈并积极参与 Azure 安全基准检验工作。 若要向 Azure 安全基准检验团队提供直接意见，请在 https://aka.ms/AzSecBenchmark 填写表单

## <a name="download"></a>下载

可以下载[电子表格格式](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)的 Azure 安全基准。

## <a name="next-steps"></a>后续步骤 
- 查看第一项安全控制：[网络安全](security-control-network-security.md)
- 阅读 [Azure 安全基准检验简介](introduction.md)
- 了解 [Azure 安全基础知识](../fundamentals/index.yml)