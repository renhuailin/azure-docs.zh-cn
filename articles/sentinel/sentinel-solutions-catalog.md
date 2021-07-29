---
title: Azure Sentinel 解决方案目录 | Microsoft Docs
description: 本文显示并详细说明目前可用的 Sentinel 解决方案包。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: yelevin
ms.openlocfilehash: c5e3a5938af46b89c3da64bb4782cff27793a0b4
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077889"
---
# <a name="azure-sentinel-solutions-catalog"></a>Azure Sentinel 解决方案目录

> [!IMPORTANT]
>
> Azure Sentinel 解决方案体验目前以“预览版”提供，所有单独解决方案包也以预览版提供。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[Azure Sentinel 解决方案](sentinel-solutions.md)提供一种统一的方式，可通过单个部署步骤在工作区中获取 Azure Sentinel 内容（如数据连接器、工作簿、分析和自动化）。 

##  <a name="currently-available-solutions"></a>当前可用的解决方案

现在 Azure Sentinel 中提供了以下解决方案。 它们都是免费安装的，但要使用数据引入、逻辑应用等功能，通常会收取费用。

| 解决方案 | 说明 |
| - | - |
| **用于 Sentinel 的 Azure 防火墙解决方案** | Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。  Sentinel 中的这个 Azure 防火墙解决方案在 Azure Sentinel 的基础上提供了内置的可定制威胁检测功能。 此解决方案包含工作簿、检测、搜寻查询和演练手册。 |
| **Azure Sentinel 4 Dynamics 365** | 适用于 Dynamics 365 的持续威胁监视解决方案使你能够收集 Dynamics 365 日志，获取 Dynamics 365 内活动的可见性，并对其进行分析以检测威胁和恶意活动。 该解决方案包括数据连接器、工作簿、分析规则和搜寻查询。 |
| **适用于 Teams 的 Azure Sentinel** | Teams 在 Microsoft 365 云中的通信和数据共享中充当一个中心角色。 由于 Teams 服务涉及到云中的许多底层技术，因此不仅是在日志搜索方面，而且在会议实时监控方面，人工分析和自动化分析都会带来很大优势。 Azure Sentinel 为管理员提供了这些解决方案。 该解决方案包括分析规则、搜寻查询和演练手册。 |
| Box 解决方案 | [Box](https://www.box.com/overview) 是为整个内容生命周期构建的一个安全、易用的平台，功能包括从文件创建和共享到共同编辑、签名、分类和保留。 |
| Cisco ISE 解决方案 | [CISCO 标识服务引擎 (ISE)](https://www.cisco.com/c/en/us/products/collateral/security/identity-services-engine/data_sheet_c78-656174.html) 是一站式解决方案，可简化安全策略管理并降低运营成本。 使用 ISE，可以看到用户和设备通过有线、无线和 VPN 连接控制对公司网络的访问。 |
| Cisco Umbrella 解决方案 | [Cisco Umbrella](https://umbrella.cisco.com/) 可在你需要的时间，按照你需要的方式提供灵活的云交付安全性。 它将多个安全功能合组合为一个解决方案，因此你可以将保护扩展到任何位置的设备、远程用户和分布式位置。 要在几分钟内保护你分散在各地的用户，最简单的方法就是采用 Umbrella。 |
| Cloudflare 解决方案 | [Cloudflare](https://www.cloudflare.com/) 可保障面向外部的资源（如网站、API 和应用程序）的可靠性。 Cloudflare 可保护内部资源，例如防火墙后面的应用程序、团队和设备。 它是您开发全局可伸缩应用程序的平台。 |
| 适用于 SAP 的持续威胁监视 | 借助[适用于 SAP 的持续威胁监视](sap-deploy-solution.md)，可以监视 SAP 系统，以应对业务层和应用程序层中的复杂威胁。 SAP 数据连接器从整个 SAP 系统环境中流式传输大量（14 个）应用程序日志，通过 NetWeaver RFC 调用从高级业务应用程序编程 (ABAP) 收集日志，并通过 OSSAP 控制接口收集文件存储数据。 |
| Contrast Protect Azure Sentinel 解决方案 | [Contrast Protect](https://www.contrastsecurity.com/runtime-application-self-protection-rasp) 通过在应用程序中嵌入自动和精确的运行时保护功能来持续监视和阻止攻击，使团队能够在运行应用程序的任何位置保护应用程序。 通过专注于可操作且及时的应用层威胁情报，安全和操作团队可以更轻松地了解和管理威胁和攻击的严重性。 Contrast Protect 与 Azure Sentinel 无缝集成，因此你可以更深入地了解应用层的安全风险。 |
| 适用于 Azure Sentinel 的 Corelight | 适用于 Azure Sentinel 的 [Corelight](https://corelight.com/) 让使用 Azure Sentinel 的事件响应者和威胁搜寻者能够提高工作效率和工作速度。 Corelight 基于最佳开源技术 Zeek 和 Suricata (NDR) 解决方案提供网络检测和响应，使网络防御者能够全面洞察其环境。<br>数据连接器支持通过 Corelight 传感器将事件从 Zeek 和 Suricata 引入 Azure Sentinel。 适用于 Azure Sentinel 的 Corelight 还包括工作簿和仪表板、搜寻查询和分析规则，可帮助组织通过 Corelight 和 Azure Sentinel 组合来提高调查和事件响应的效率。 |
| CrowdStrike Falcon Endpoint Protection 解决方案 | [CrowdStrike Falcon Endpoint Protection](https://www.crowdstrike.com/resources/data-sheets/falcon-endpoint-protection-pro/) 将最有效的防护技术和全面的攻击可见性与内置的威胁情报和响应组合在一起，从而提供理想的防病毒替代解决方案。 |
| 适用于 Azure Sentinel 解决方案库的 HYAS Insight | [HYAS Insight](https://www.hyas.com/hyas-insight) 是一种威胁调查和归因解决方案，它使用独家的数据源和非传统机制来提高分析师、研究人员和调查人员的洞察力和工作效率，同时提高发现的准确性。 HYAS Insight 将攻击实例和活动连接到数十亿个的入侵指标，以提供洞察力和见解。 借助易于使用的用户界面、转换和 API 访问，HYAS Insight 将丰富的威胁数据整合到强大的研究和归因解决方案中。 HYAS Insight 由 HYAS Intelligence 团队进行了补充，可帮助组织更好地了解他们每天面对的威胁的性质。 |
| Infoblox 云数据连接器解决方案 | [BloxOne DDI](https://www.infoblox.com/products/bloxone-ddi/) 是业界第一个 DDI (DNS/DHCP/IPAM) 解决方案，使你能够集中管理和自动执行从云到任何位置的 DDI，且实现了前所未有的成本效益。 BloxOne DDI 在构建时采用了原生原则并作为 SaaS 服务提供，通过消除传统 DDI 实现的复杂性、瓶颈和可伸缩性限制，极大简化了网络管理。<br>BloxOne 威胁防御通过与现有防御措施协作来保护网络，自动将安全性扩展到必不可少的数字产品（包括 SD-WAN、IoT 和云），从而最大程度地保护品牌。 它能够支持安全业务流程、自动化和响应 (SOAR) 解决方案，缩短调查和修正网络威胁的时间，优化整个安全生态系统的性能，并降低企业威胁防御的总成本。 |
| McAfee ePolicy Orchestrator 解决方案 | 利用 [McAfee ePO](https://www.mcafee.com/enterprise/en-in/products/epolicy-orchestrator.html)，可以对终结点和企业安全产品进行集中式策略管理和强制执行。 McAfee ePO 监视和管理网络，检测威胁并保护终结点免受这些威胁。 |
| Oracle Database Audit 解决方案 | [Oracle Database](https://www.oracle.com/database/technologies/security/db-auditing.html) 在 Enterprise 和 Standard 版的数据库中提供强大的审核支持。 Oracle Database Unified Auditing 使用策略和条件在 Oracle 数据库中实现有选择性且有效的审核。 |
| Palo Alto Prisma 解决方案 | [Prisma Cloud](https://www.paloaltonetworks.com/prisma/cloud) 是行业领先的综合性云原生安全平台，为多云和混合云环境提供完整的生命周期安全性和全栈保护。 |
| PingFederate 解决方案 | [PingFederate®](https://www.pingidentity.com/en/resources/client-library/data-sheets/pingfederate-data-sheet.html) 是领先的企业联合服务器，可以提供面向员工、合作伙伴和客户标识类型的用户身份验证和基于标准的单一登录 (SSO)。 PingFederate 让组织摆脱了陈旧、昂贵又不灵活的 IAM 解决方案，而是应用旨在满足复杂企业需求的新式标识和访问管理解决方案。 |
| Proofpoint POD 解决方案 | [Proofpoint on Demand Email Security](https://www.proofpoint.com/us/products/email-security-and-protection/email-protection) 可准确地对各种类型的电子邮件进行分类，同时检测和阻止不涉及到恶意有效负载的威胁。 你可以自动标记可疑电子邮件以提高最终用户的认知度，并在几秒钟内跟踪所有电子邮件。 |
| Proofpoint TAP 解决方案 | [Proofpoint Targeted Attack Protection (TAP)](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) 可帮助检测、缓解和阻止通过电子邮件攻击用户的高级威胁，其中包括使用恶意附件和 URL 来安装恶意软件或诱骗用户分享密码和敏感信息的攻击手段。 TAP 还会检测云应用中的威胁和风险，并关联与凭据盗窃或其他攻击相关的电子邮件攻击。 |
| Qualys VM 解决方案 | [Qualys 漏洞管理 (VM)](https://www.qualys.com/apps/vulnerability-management/) 提供全局可见性，可让你了解你的 IT 资产以及如何加以保护。 随着企业采用云计算、移动性和其他颠覆性的技术来实现数字化转型，Qualys VM 为这些传统边界已经已经逐渐模糊的混合 IT 环境提供了新一代的漏洞管理功能。 这是通过轻量级 Qualys 云代理中的基于代理的检测而完成的，这会将网络覆盖范围扩展到无法扫描的资产。 |
| RiskIQ 安全情报演练手册 | [RiskIQ](https://www.riskiq.com/) 创建了多个 Azure Sentinel 演练手册，其中预先打包功能，以便在 Azure Sentinel 平台中扩充事件并向事件添加上下文。 这些演练手册可以单独运行，也可以配置为在 Azure Sentinel 门户中自动运行。 当某个事件包含已知的指标（如域或 IP 地址）时，RiskIQ 将使用它在 Internet 上连接的其他内容来扩充该值，并检测是否可能会造成威胁。 将向事件中添加注释，事件链接到 RiskIQ 调查平台中的更多详细信息。 |
| 适用于 Azure Sentinel 的 Senserva 产品/服务 | [Senserva](https://www.senserva.com/product/) 是适用于 Azure Sentinel 的云安全态势管理 (CSPM)，通过持续生成基于优先级的风险评估，简化 Azure Active Directory 安全风险的管理，从而防患于未然。 Senserva 信息包括 Senserva 管理的所有 Azure 对象的详细安全排名，通过首先修复影响最大的最严重问题，使客户能够执行最佳发现和修正。  Senserva 的所有扩充信息都发送到 Azure Sentinel，以便通过 Log Analytics 工作区由查询、工作簿和演练手册进行处理。<br>Senserva 为 Microsoft 云环境中的安全用户帐户、Azure Active Directory 配置（包括条件访问）、应用程序和事件提供了深度分析。 Senserva 的专利技术为 Microsoft 365 和 Microsoft Azure 管理员和安全团队节省了无数的时间。 Senserva 的创建组合运用了行业标准 (NIST800-53、MITRE Att&CK)、行业建议、供应商建议和 Senserva 自己的专家，正在申请专利。 |
| Slack Audit 解决方案 | 使用 [Slack Audit](https://slack.com/) 数据连接器，可以通过 REST API 将 [Slack Audit 记录](https://api.slack.com/admins/audit-logs)事件引入到 Azure Sentinel 中。 详细信息请参阅 [API 文档](https://api.slack.com/admins/audit-logs#the_audit_event)。 这些事件的可见性可帮助你检查潜在的安全风险、分析团队的协作运用情况、诊断配置问题等。 |
| Sophos XG 防火墙解决方案 | [Sophos XG 防火墙](https://www.sophos.com/products/next-gen-firewall.aspx)通过可见性、同步安全性和自动响应为企业提供一体式保护。 包括揭示隐藏的风险，阻止未知的威胁，隔离受感染的系统。 借助 XG 防火墙，还可以轻松地通过其 VPN 客户端和硬件将安全网络扩展到在任何地点工作的员工。 |
| Symantec Endpoint Protection 解决方案 | [Symantec Endpoint Protection](https://www.broadcom.com/products/cyber-security/endpoint) 是一种安全软件套件，其中包含针对服务器和台式计算机的反恶意软件、入侵防护和防火墙功能。 还具有数据丢失防护软件的某些典型功能。 此解决方案用于阻止未批准的程序运行，并应用防火墙策略来阻止或允许网络流量。 它会尝试标识和阻止企业网络内部或来自 Web 浏览器的恶意流量。 |
| Symantec ProxySG 解决方案 | Symantec 利用可缩放的高性能 Web 代理设备来保护组织，这些设备可以确保在通信中排除以 Web 活动为目标的高级威胁。 [Symantec 安全 Web 网关](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway)解决方案独特的代理服务器体系结构，使组织能够有效监视、控制和保护流量，确保安全的 Web 和云体验。 |
| TitaniumCloud 文件扩充解决方案 | [ReversingLabs TitaniumCloud](https://www.reversinglabs.com/products/file-reputation-service) 是一个威胁情报解决方案，提供最新的文件信誉服务、威胁分类和超过 100 亿个“无恶意软件”和恶意软件文件的丰富背景。 将使用 ReversingLabs 的文件分解技术对文件进行处理。 一组功能强大的 REST API 查询和馈送函数为威胁识别、分析、情报开发和威胁搜寻服务提供目标文件和恶意软件情报。 |
| Ubiquiti UniFi 解决方案 | [Ubiquiti Inc.](https://www.ui.com/) 是一家美国技术公司，面向企业和家庭制造和销售多个品牌的无线数据通信产品和有线产品。 |
| vArmour 应用程序控制器和 Azure Sentinel 解决方案 | [vArmour 应用程序控制器](https://www.varmour.com/)是一种行业领先的应用程序关系管理解决方案：一种实现企业的可视化和安全性的转型方法。 与 Azure Sentinel 结合时，这两者可无缝集成，提供增强的可见性和自动化安全操作。 数字优先的企业是基于用户与用程序之间的数百万动态互连而构建，而这些用户和应用程序往往是跨环境的。 目前，大部分此类互联都是不可见的。 随着环境复杂性的增加，组织面临的风险也不断增加。 应用程序控制器是一种易于部署的解决方案，可提供对应用程序关系和依赖项的全面实时可见性和控制，因此可以改进操作决策、增强安全态势，并降低跨多云部署的业务风险，所有这些都无需添加昂贵的新代理或基础结构。 因此，应用程序的复原能力和安全性都将提升。 |
| VMware Carbon Black 解决方案 | [VMware Carbon Black](https://www.carbonblack.com/products/vmware-carbon-black-cloud-endpoint/) 通过能够适应你的需求的云原生终结点保护功能实现安全性转型。 VMware Carbon Black 提供了一个终结点平台，可帮助你发现能够隐藏恶意攻击的细微波动并调整预防措施来响应。 |
| 

## <a name="next-steps"></a>后续步骤

本文档介绍了 Azure Sentinel 解决方案以及如何查找和部署这些解决方案。

- 详细了解 [Azure Sentinel 解决方案](sentinel-solutions.md)。
- [查找和部署 Azure Sentinel 解决方案](sentinel-solutions-deploy.md)。
