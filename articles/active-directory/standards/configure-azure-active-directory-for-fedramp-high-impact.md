---
title: 配置 Azure Active Directory 以满足 FedRAMP High Impact 级别
description: 概述如何通过使用 Azure Active Directory 来满足组织的 FedRAMP High Impact 级别。
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
ms.openlocfilehash: 965fbe7ec1f46e006e30d721bf306a0debaf8473
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293934"
---
# <a name="configure-azure-active-directory-to-meet-fedramp-high-impact-level"></a>配置 Azure Active Directory 以满足 FedRAMP High Impact 级别

[联邦风险与授权管理计划](https://www.fedramp.gov/) (FedRAMP) 是针对云服务提供商 (CSP) 创建用于联邦机构的云解决方案产品/服务 (CSO) 的评估和授权过程。 Azure 和 Azure 政府已从联合授权委员会（FedRAMP 认证的最高门槛）获得了 [ 级别的暂时运营授权 (P-ATO)](https://docs.microsoft.com/compliance/regulatory/offering-fedramp)。

Azure 提供了满足所有控制要求的功能，可为你的 CSO 或作为联邦机构获得 FedRAMP High 评级。 你的组织负责完成其他配置或流程以符合要求。 这项责任既适用于寻求对其 CSO 进行 FedRAMP High 授权的 CSP，也适用于寻求运营授权 (ATO) 的联邦机构。 

## <a name="microsoft-and-fedramp"></a>Microsoft 和 FedRAMP 

与其他任何 CSP 相比，Microsoft Azure 在 [FedRAMP High Impact](https://docs.microsoft.com/azure/azure-government/compliance/azure-services-in-fedramp-auditscope) 级别支持更多的服务。 尽管 Azure 公有云中的 FedRAMP High 可满足多个美国政府客户的需求，但要求更严格的机构可能会依赖于 Azure 政府云。 Azure 政府云提供额外的保护措施，例如加强人员筛选。 

Microsoft 需要每年重新认证其云服务以维护其授权。 为此，Microsoft 将持续监视和评估其安全控制，并演示其服务的安全性是否始终合规。

* [Microsoft 云服务 FedRAMP 授权](https://marketplace.fedramp.gov/)

* [Microsoft FedRAMP 审核报表](https://aka.ms/MicrosoftFedRAMPAuditDocuments)

若要接收其他 FedRAMP 报表，请发送电子邮件至 [Azure 联邦文档](mailto:AzFedDoc@microsoft.com)。

FedRAMP 授权有多种途径。 你可以重复使用 Microsoft Azure 现有的授权包和此处的指南，以显著减少获取 ATO 或 P-ATO 所需的时间和精力。 有关 FedRAMP 的详细信息，请访问[FedRAMP 网站](https://www.fedramp.gov/)。

 ## <a name="scope-of-guidance"></a>指南范围

FedRAMP High 基线由 [NIST 800-53 安全控件目录修订版 4](https://csrc.nist.gov/publications/detail/sp/800-53/rev-4/final) 中的 421 个控件和控件增强组成。 在适用的情况下，我们包括了 [800-53 修订版 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) 中的澄清信息。 本系列文章涵盖了与标识相关的这些控件的子集，必须配置这些控件。 我们提供了规范性指南，以帮助实现你负责在 Azure Active Directory (Azure AD) 中配置的控件的合规性。 为了完全满足某些标识控件要求，你可能需要使用其他系统。 其他系统可能包含安全信息和事件管理 (SIEM) 工具，如 Azure Sentinel。 如果在 Azure Active Directory 之外使用 Azure 服务，则需要考虑其他一些控件，你可以使用 Azure 已有的功能来满足这些控件。

FedRAMP 资源

* [联邦风险与授权管理计划](https://www.fedramp.gov/)

* [FedRAMP 安全评估框架](https://www.fedramp.gov/assets/resources/documents/FedRAMP_Security_Assessment_Framework.pdf)

* [FedRAMP 授权机构指南](https://www.fedramp.gov/assets/resources/documents/Agency_Guide_for_Reuse_of_FedRAMP_Authorizations.pdf)

* [在 Microsoft 的云中管理合规性](https://www.microsoft.com/trustcenter/common-controls-hub)

* [Microsoft 政府云](https://go.microsoft.com/fwlink/p/?linkid=2087246)

* [Azure 合规性产品/服务](https://aka.ms/azurecompliance)

* [FedRAMP High 蓝图示例概述](https://docs.microsoft.com/azure/governance/blueprints/samples/fedramp-h/)

* [Microsoft 365 合规中心](https://docs.microsoft.com///microsoft-365/compliance/microsoft-365-compliance-center)

* [Microsoft 合规性管理员](https://docs.microsoft.com///microsoft-365/compliance/compliance-manager)

 

## <a name="next-steps"></a>后续步骤

[配置访问控制](fedramp-access-controls.md)

[配置标识和身份验证控件](fedramp-identification-and-authentication-controls.md)

[配置其他控件](fedramp-other-controls.md)

 
