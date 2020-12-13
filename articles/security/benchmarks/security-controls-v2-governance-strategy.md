---
title: Azure 安全基准 V2 - 治理和策略
description: Azure 安全基准 V2 治理和策略
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 935a6b41152ff914889e299b6455fbb30b2f0447
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369049"
---
# <a name="security-control-v2-governance-and-strategy"></a>安全控制 V2：治理和策略

治理和策略提供的指导可确保使用一致的安全策略和记录在案的治理方法来指导和维持安全保障，包括为不同的云安全功能、统一的技术策略以及支持策略和标准建立角色和责任。

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定义资产管理和数据保护策略

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| GS-1 | 2、13 | SC、AC |

确保制定和传达明确的用于对系统和数据进行持续监视和保护的策略。 确定业务关键数据和系统的发现、评估、保护和监视优先级。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 与业务风险相符的数据分类标准

- 安全组织对风险和资产清单的洞察力 

- 安全组织对 Azure 服务使用的审批 

- 资产在其生命周期中的安全性

- 与组织数据分类相符的必需访问控制策略

- 使用 Azure 原生的和第三方的数据保护功能

- 传输中数据用例和静态数据用例的数据加密要求

- 合适的加密标准

有关详细信息，请参阅以下资源：
- [Azure 安全体系结构建议 - 存储、数据和加密](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure 安全基准 - 资产管理](security-controls-v2-asset-management.md)

- [Azure 安全基准 - 数据保护](security-controls-v2-data-protection.md)

**责任**：客户

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| GS-2 | 4、9、16 | AC、CA、SC |

建立企业级策略，以便使用标识、网络、应用程序、订阅、管理组和其他控件的组合对资产访问权限进行分段。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（视频）](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南（文档）](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**责任**：客户

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| GS-3 | 20、3、5 | RA、CM、SC |

持续衡量并缓解你的个人资产及其托管环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](security-controls-v2-posture-vulnerability-management.md)

**责任**：客户

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| GS-4 | 空值 | PL、PM |

确保为安全组织中的角色和职责制定并传达清晰的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1 - 人员：针对云安全历程培训团队](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全最佳做法 2 - 人员：针对云安全技术培训团队](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全最佳做法 3 - 流程：针对云安全决策分配责任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**责任**：客户

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5：定义网络安全策略

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| GS-5 | 9 | CA、SC |

制定 Azure 网络安全方法，作为组织的整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 集中化的网络管理和安全职责

- 符合企业分段策略的虚拟网络分段模型

- 各种威胁和攻击场景中的补救策略

- Internet 边缘及入口和出口策略

- 混合云和本地互连策略

- 最新的网络安全项目（例如网络关系图、参考网络体系结构）

有关详细信息，请参阅以下资源：

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全基准 - 网络安全](security-controls-v2-network-security.md)

- [Azure 网络安全概述](../fundamentals/network-overview.md)

- [企业网络体系结构策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**责任**：客户

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| GS-6 | 16、4 | AC、AU、SC |

制定 Azure 标识和特权访问方法，作为组织的整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 集中化的标识和身份验证系统及其与其他内部和外部标识系统的互连

- 各种用例和条件中的强身份验证方法

- 保护权限高的用户

- 异常用户活动监视和处理  

- 用户标识和访问评审及协调流程

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 标识管理](security-controls-v2-identity-management.md)

- [Azure 安全基准 - 特权访问](security-controls-v2-privileged-access.md)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../fundamentals/identity-management-overview.md)

**责任**：客户

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| GS-7 | 19 | IR、AU、RA、SC |

制定日志记录和威胁响应策略，以快速检测并缓解威胁，同时满足合规性要求。 优先为分析师提供高质量警报和无缝体验，以便他们能够专注于威胁，而不是执行集成和手动步骤。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 安全运营 (SecOps) 组织的角色和职责 

- 符合 NIST 或其他行业框架要求的明确定义的事件响应流程 

- 日志捕获和保留，用于支持威胁检测、事件响应和合规性需求

- 使用 SIEM、原生 Azure 功能和其他源，集中查看和关联有关威胁的信息 

- 与客户、供应商和公开的利益相关方之间的通信和通知计划

- 使用 Azure 原生的和第三方的平台进行事件处理，例如日志记录和威胁检测、取证以及攻击补救和根除

- 处理事件和事件后活动的流程，例如经验教训和证据保留

有关详细信息，请参阅以下资源：
- [Azure 安全基准 - 日志记录和威胁检测](security-controls-v2-logging-threat-detection.md)

- [Azure 安全基准 - 事件响应](security-controls-v2-incident-response.md)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**责任**：客户

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8：定义备份和恢复策略

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| GS-8 | 10 | CP |

为你的组织制定 Azure 备份和恢复策略。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 符合你的业务恢复目标的恢复时间目标 (RTO) 和恢复点目标 (RPO) 定义

- 应用程序和基础结构设置中的冗余设计

- 使用访问控制和数据加密来保护备份

有关详细信息，请参阅以下资源：
- [Azure 安全基准 - 备份和恢复](security-controls-v2-backup-recovery.md)

- [Azure 良好体系结构框架 - Azure 应用程序的备份和灾难恢复](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure 采用框架 - 业务连续性和灾难恢复](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**责任**：客户

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [所有利益干系人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)