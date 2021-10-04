---
title: 配置 Azure Active Directory 以满足 FedRAMP High Impact 级别
description: 概述如何通过使用 Azure Active Directory 来满足组织的 FedRAMP High 影响级别。
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20eafed03b9dd63bf6c59445d744d7a3c67c5614
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601741"
---
# <a name="configure-azure-active-directory-to-meet-fedramp-high-impact-level"></a>配置 Azure Active Directory 以满足 FedRAMP High Impact 级别

[联邦风险与授权管理计划](https://www.fedramp.gov/) (FedRAMP) 是针对云服务提供商 (CSP) 的评估和授权过程。 具体而言，此过程适用于那些创建在联邦机构中使用的云解决方案产品/服务 (CSO) 的 CSP。 Azure 和 Azure 政府已从联合授权委员会获得了[高影响级别的暂时运营授权 (P-ATO)](/compliance/regulatory/offering-fedramp)（FedRAMP 认证的最高标准）。

Azure 提供了满足所有控制要求的功能，可为你的 CSO 或作为联邦机构获得 FedRAMP High 评级。 你的组织负责按要求完成其他配置或流程。 这项责任既适用于寻求对其 CSO 进行 FedRAMP High 授权的 CSP，也适用于寻求运营授权 (ATO) 的联邦机构。 

## <a name="microsoft-and-fedramp"></a>Microsoft 和 FedRAMP 

与其他任何 CSP 相比，Microsoft Azure 在 [FedRAMP High Impact](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md) 级别支持更多的服务。 虽然 Azure 公有云中的这个级别符合多个美国政府客户的需要，但要求更严格的机构可能会依赖于 Azure 政府云。 Azure 政府提供额外的保护措施，例如加强人员筛选。 

Microsoft 需要每年重新认证其云服务以维护其授权。 为此，Microsoft 会持续监视和评估其安全控制，并证明其服务的安全性会保持合规。 有关详细信息，请参阅 [Microsoft 云服务 FedRAMP 授权](https://marketplace.fedramp.gov/)和 [Microsoft FedRAMP 审核报告](https://aka.ms/MicrosoftFedRAMPAuditDocuments)。 若要接收其他 FedRAMP 报表，请发送电子邮件至 [Azure 联邦文档](mailto:AzFedDoc@microsoft.com)。

FedRAMP 授权有多种途径。 可以重复使用 Azure 的现有授权包和此处的指南，以显著减少获取 ATO 或 P-ATO 所需的时间和精力。 

## <a name="scope-of-guidance"></a>指南范围

FedRAMP High 基线由 [NIST 800-53 安全控制目录修订版 4](https://csrc.nist.gov/publications/detail/sp/800-53/rev-4/final) 中的 421 个控制和控制增强功能组成。 在适用的情况下，我们包括了 [800-53 修订版 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) 中的澄清信息。 本系列文章涵盖了这些控制中的一部分，这部分控制与标识相关，并且是必须配置的。 

我们提供了规范性指南，该指南可帮助你实现你负责在 Azure Active Directory (Azure AD) 中配置的控制的符合性。 若要充分满足某些标识控制要求，可能需要使用其他系统。 其他系统可能包含安全信息和事件管理工具，例如 Azure Sentinel。 如果在 Azure Active Directory 之外使用 Azure 服务，则需要考虑其他控制，但你可以使用 Azure 已有的功能来满足这些控制。

下面是 FedRAMP 资源的列表：

* [联邦风险与授权管理计划](https://www.fedramp.gov/)

* [FedRAMP 安全评估框架](https://www.fedramp.gov/assets/resources/documents/FedRAMP_Security_Assessment_Framework.pdf)

* [FedRAMP 授权机构指南](https://www.fedramp.gov/assets/resources/documents/Agency_Guide_for_Reuse_of_FedRAMP_Authorizations.pdf)

* [在 Microsoft 的云中管理合规性](https://www.microsoft.com/trustcenter/common-controls-hub)

* [Microsoft 政府云](https://go.microsoft.com/fwlink/p/?linkid=2087246)

* [Azure 合规性产品/服务](https://aka.ms/azurecompliance)

* [FedRAMP High Azure Policy 内置计划定义](../../governance/policy/samples/fedramp-high.md)

* [Microsoft 365 合规中心](/microsoft-365/compliance/microsoft-365-compliance-center)

* [Microsoft 合规性管理器](/microsoft-365/compliance/compliance-manager)

## <a name="next-steps"></a>后续步骤

[配置访问控制](fedramp-access-controls.md)

[配置标识和身份验证控件](fedramp-identification-and-authentication-controls.md)

[配置其他控件](fedramp-other-controls.md)

