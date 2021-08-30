---
title: Azure 的端到端安全服务 | Microsoft Docs
description: 本文提供了一个图表，介绍有助于保护云资源以及检测和调查威胁的各项 Azure 服务。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: 9e68df70afdc0f8e35ec862d9f9570dd13306191
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114466048"
---
# <a name="end-to-end-security-in-azure"></a>Azure 的端到端安全服务
将 Azure 用于应用程序和服务的最合理原因之一是可以利用其各种安全工具和功能。 这些工具和功能可帮助在安全的 Azure 平台上创建安全的解决方案。 Microsoft Azure 提供具备保密性、完整性和可用性的客户数据，同时还能实现透明的问责制。

下面的图表和文档介绍了 Azure 中的安全服务。 这些安全服务有助于你满足业务的安全需求，并在云中保护用户、设备、资源、数据和应用程序。

## <a name="microsoft-security-services-map"></a>Microsoft 安全服务图

此安全服务图按服务所保护的资源（列）组织服务。 此图表还将服务分为以下类别（行）：

- 保护 - 支持在标识、主机、网络和数据层面实现分层深度防护策略的服务。 此安全服务和功能集合提供了一种了解和改善整个 Azure 环境安全状况的方法。
- 检测威胁 - 用于识别可疑活动并帮助缓解威胁的服务。
- 调查和响应 - 用于拉取日志记录数据，以便你评估可疑活动并做出响应的服务。

此图表包含 Azure 安全基准计划，其中包括一系列具有高影响力的安全建议，你可以使用它们保护在 Azure 中使用的服务。

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="显示 Azure 端到端安全服务的图表。" border="false":::

## <a name="security-controls-and-baselines"></a>安全控制和基线
[Azure 安全基准](../benchmarks/introduction.md)计划包括一系列具有高影响力的安全建议，你可以使用它们保护在 Azure 中使用的服务：

- 安全控制 - 一般而言，在你的 Azure 租户和 Azure 服务中，这些建议都是适用的。 每个建议都会标识出利益干系人的列表，这些利益干系人通常会涉及到基准的规划、审批或实现。
- 服务基线 - 这些基线将控制应用于单个 Azure 服务，以提供有关该服务的安全配置的建议。

## <a name="secure-and-protect"></a>保护

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="显示有助于保护云资源的 Azure 服务的图表。" border="false":::

| 服务 | 说明 |
|------|--------|
| [Azure 安全中心](../../security-center/security-center-introduction.md)| 一个统一的基础结构安全管理系统，可以增强数据中心的安全状况，以及为云中（无论是否在 Azure 中）和本地的混合工作负荷提供高级威胁防护。 |
| **标识&nbsp;&&nbsp;访问&nbsp;管理** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| Microsoft 基于云的标识和访问管理服务。  |
|  | [条件访问](../../active-directory/conditional-access/overview.md)是 Azure AD 用来统合信号、做出决策以及强制执行组织策略的工具。 |
|  | [域服务](../../active-directory-domain-services/overview.md)是 Azure AD 用来提供托管域服务（例如域加入、组策略、轻型目录访问协议 (LDAP) 和 Kerberos/NTLM 身份验证）的工具。 |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) 是一种 Azure AD 服务，你可以通过该服务管理、控制和监视对组织中重要资源的访问。 |
|  | [多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)是 Azure AD 用来帮助保护对数据和应用程序的访问（通过要求完成第二种身份验证方法）的工具。 |
| [Azure AD 标识保护](../../active-directory/identity-protection/overview-identity-protection.md) | 借助该工具，组织可以自动检测和修复基于标识的风险，使用门户中的数据调查风险，以及将风险检测数据导出到第三方实用工具来进行进一步分析。 |
| **基础结构&nbsp;&&nbsp;网络** |  |
| [VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | 这是一种虚拟网络网关，用于通过公共 Internet 发送 Azure 虚拟网络和本地位置之间的加密流量，以及通过 Microsoft 网络发送 Azure 虚拟网络之间的加密流量。 |
| [Azure DDoS 保护标准](../../ddos-protection/ddos-protection-overview.md) | 提供增强的 DDoS 缓解功能来抵御 DDoS 攻击。 这种防护自动经过优化，可帮助保护虚拟网络中的特定 Azure 资源。 |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | 可缩放的全局入口点，它使用 Microsoft 全球边缘网络来创建快速、安全且可大规模缩放的 Web 应用程序。 |
| [Azure 防火墙](../../firewall/overview.md) | 是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 |
| [Azure Key Vault](../../key-vault/general/overview.md) | 一种安全的机密存储，适用于令牌、密码、证书、API 密钥以及其他机密。 通过 Key Vault，还可以创建和控制用于加密数据的加密密钥。 |
| [Key Vault 托管 HSM](../../key-vault/managed-hsm/overview.md) | 一项完全托管、高度可用、单租户、符合标准的云服务，通过该服务，你可以使用 FIPS 140-2 级别 3 验证的 HSM 保护云应用程序的加密密钥。 |
| [Azure 专用链接](../../private-link/private-link-overview.md) | 借助该服务，可以通过虚拟网络中的专用终结点访问 Azure PaaS 服务（例如，Azure 存储和 SQL 数据库）和 Azure 托管的客户拥有的服务/合作伙伴服务。 |
| [Azure 应用程序网关](../../application-gateway/overview.md) | 一种高级 Web 流量负载均衡器，可用于管理 Web 应用程序的流量。 应用程序网关可以根据 HTTP 请求的其他属性（例如 URI 路径或主机头）进行路由决策。 |
| [Azure 服务总线](../../service-bus-messaging/service-bus-messaging-overview.md) | 一个完全托管的企业消息中转站，其中包含消息队列和发布订阅主题。 可使用服务总线将应用程序和服务相互分离。 |
| [Web 应用程序防火墙](../../web-application-firewall/overview.md) | 可在出现常见攻击和漏洞时为 Web 应用程序提供集中保护。 WAF 可以通过 Azure 应用程序网关和 Azure Front Door 部署。 |
| **数据和应用程序** |  |
| [Azure 备份](../../backup/backup-overview.md) | 提供简单、安全且经济高效的解决方案来备份数据，并从 Microsoft Azure 云恢复数据。 |
| [Azure 存储服务加密](../../storage/common/storage-service-encryption.md) | 在存储数据前自动加密数据，并在你检索数据时自动解密数据。 |
| [Azure 信息保护](/azure/information-protection/what-is-information-protection) | 一种基于云的解决方案，使组织能够通过将标签应用于内容来发现文档及电子邮件并进行分类和保护。 |
| [API 管理](../../api-management/api-management-key-concepts.md) | 一种为现有后端服务创建一致且现代化的 API 网关的方法。 |
| [Azure 机密计算](../../confidential-computing/overview.md) | 通过该服务，可在云中处理敏感数据时将这些数据隔离。 |
| [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops) | 当开发项目存储在 Azure DevOps 中时，它们会从多个层面的安全和管理技术、操作做法和合规策略中受益。 |
| **客户访问** |  |
| [Azure AD 外部标识](../../active-directory/external-identities/compare-with-b2c.md) | 借助 Azure AD 中的外部标识，可以允许组织外部人员访问应用和资源，而让他们使用所需的任何标识进行登录。 |
|  | 可以通过 [Azure AD B2B](../../active-directory/external-identities/what-is-b2b.md) 协作与外部用户共享应用和资源。 |
|  | 使用 [Azure AD B2C](../../active-directory-b2c/overview.md) 时，每天能够支持数百万个用户以及几十亿次身份验证，并监视和自动处理拒绝服务、密码喷射或暴力攻击之类的威胁。 |

## <a name="detect-threats"></a>检测威胁

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="显示用于检测威胁的 Azure 服务的图表。" border="false":::

| 服务 | 说明 |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | 为 Azure 和混合资源及工作负载提供高级智能保护。 可在你的环境中使用安全中心的 Azure Defender 仪表板显示和控制云工作负载保护功能。 |
| [Azure Sentinel](../../sentinel/overview.md) | 可缩放的云原生安全信息事件管理 (SIEM) 和安全业务流程自动响应 (SOAR) 解决方案。 Sentinel 在整个企业范围内提供智能安全分析和威胁智能，为警报检测、威胁可见性、主动搜寻和威胁响应提供单一解决方案。 |
| **标识&nbsp;&&nbsp;访问&nbsp;管理** |  |
| [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender) | 一款统一的破坏前/后企业防御套件，它以本机方式协调各终结点、标识、电子邮件和应用程序中的威胁检测、阻止、调查和响应，以提供针对复杂攻击的综合保护。 |
|  | [Microsoft Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) 是一个企业终结点安全平台，专门用于帮助企业网络防御、检测、调查和响应高级威胁。 |
|  | [Microsoft Defender for Identity](/defender-for-identity/what-is) 是一个基于云的安全解决方案，可利用本地 Active Directory 信号识别、检测并调查针对组织的高级威胁、身份盗用和恶意内部操作。 |
| [Azure AD 标识保护](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | 可发送两种类型的自动通知电子邮件，来帮助你管理用户风险和风险检测：“检测到有风险的用户”电子邮件以及“每周摘要”电子邮件。 |
| **基础结构和网络** |  |
| [适用于 IoT 的 Azure Defender](../../defender-for-iot/overview.md) | 一种统一的安全解决方案，用于识别 IoT/OT 设备、漏洞和威胁。 无论你是需要保护现有的 IoT/OT 设备还是为新的 IoT 创新构建安全性，它都使你能够保护整个 IoT/OT 环境。 |
| [Azure 网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md) | 提供所需的工具用于监视、诊断 Azure 虚拟网络中的资源并查看其指标，以及为其启用或禁用日志。 网络观察程序用于监视和修复 IaaS 产品的网络运行状况，其中包括虚拟机、虚拟网络、应用程序网关和负载均衡器。 |
| [Azure Policy 审核日志记录](../../governance/policy/overview.md) | 可帮助强制执行组织标准并大规模评估合规性。 Azure Policy 使用自动启用的活动日志来包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用的元素。 |
| **数据和应用程序** |  |
| [适用于容器注册表的 Azure Defender](../../security-center/defender-for-container-registries-introduction.md) | 包含一个漏洞扫描程序，可扫描基于 Azure 资源管理器的 Azure 容器注册表中的映像，使你能够更深入地了解映像的漏洞。 |
| [适用于 Kubernetes 的 Azure Defender](../../security-center/defender-for-kubernetes-introduction.md) | 通过 Azure Kubernetes 服务 (AKS) 检索到的日志来监视 AKS 托管服务，从而提供群集级别的威胁防护。 |
| [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) | 可在多个云中运行的云访问安全代理 (CASB)。 它提供了丰富的显示效果、数据旅程控制和成熟分析服务，用于跨所有云服务发现和防范网络威胁。 |

## <a name="investigate-and-respond"></a>调查和响应

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="显示有助于调查和响应威胁的 Azure 服务的图表。" border="false":::

| 服务 | 说明 |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | 强大的搜索和查询工具，可用于在整个组织的数据源中搜寻安全威胁。 |
| [Azure&nbsp;Monitor&nbsp;日志和指标](../../azure-monitor/overview.md)&nbsp;&nbsp; | 提供了一个全面的解决方案，用于从云和本地环境收集、分析和处理遥测数据。 Azure Monitor 会[将各种源中的数据收集并聚合](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor)到一个通用数据平台，在该平台中，可以使用这些数据进行分析、实现可视化和发出警报。 |
| **标识&nbsp;&&nbsp;访问&nbsp;管理** |  |
| [Azure&nbsp;AD&nbsp;报表和监视](../../active-directory/reports-monitoring/index.yml)&nbsp;&nbsp; | 可以通过 [Azure AD 报表](../../active-directory/reports-monitoring/overview-reports.md)全面了解环境中的活动。 |
|  | 可以通过 [Azure AD 监视](../../active-directory/reports-monitoring/overview-monitoring.md)将 Azure AD 活动日志路由到其他终结点。|
| [Azure AD PIM 审核历史记录](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | 显示过去 30 天内对所有特权角色的所有角色分配和激活情况。 |
| **数据和应用程序** |  |
| [Microsoft Cloud App Security](/cloud-app-security/investigate) | 提供可让你深入了解云环境中活动的工具。 |

## <a name="next-steps"></a>后续步骤

- 了解[云中责任分担](shared-responsibility.md)。

- 了解针对恶意用户和非恶意用户的 [Azure 云隔离选项](isolation-choices.md)。