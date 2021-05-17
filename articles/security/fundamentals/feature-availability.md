---
title: 适用于美国政府客户的 Azure 服务云功能可用性
description: 列出适用于美国政府客户的 Azure 安全服务（如 Azure Sentinel）的功能可用性
author: batami
ms.author: bagol
ms.service: security
ms.topic: reference
ms.date: 04/29/2021
ms.openlocfilehash: 6cead1e9dfb8cf1d12ef09079e97779c08523e36
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108290571"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>适用于美国政府客户的云功能可用性

本文介绍了 Microsoft Azure 和 Azure 政府版中以下安全服务的云功能可用性：

- Azure Sentinel

> [!NOTE]
> 本文中即将添加其他安全服务。
> 

## <a name="azure-government"></a>Azure Government

Azure 政府版使用与 Azure（有时称为 Azure 商业版或 Azure 公共版）相同的基础技术，其中包括基础结构即服务 (IaaS)、平台即服务 (PaaS) 和软件即服务 (SaaS) 的核心组件。 Azure 和 Azure 政府版均实施全面的安全控制以及 Microsoft 对保护客户数据的承诺。

Azure 政府版拥有物理隔离的云环境，专用于美国联邦、州、地方、部落政府及其合作伙伴。 尽管 Azure 和 Azure 政府版云环境均在 FedRAMP High 影响级别进行评估和授权，但 Azure 政府版通过与在美国存储客户数据有关的合同承诺，并仅限经过筛选的美国人员才能访问处理客户数据的系统，为客户提供额外一层保护。 使用云来存储或处理受美国出口管制法规（如 EAR、ITAR 和 DoE 10 CFR 第 810 部分）限制的数据的客户可能会对这些承诺感兴趣。

有关 Azure 政府版的详细信息，请参阅[什么是 Azure 政府版？](/azure/azure-government/documentation-government-welcome)

## <a name="microsoft-365-integration"></a>Microsoft 365 集成

产品之间的集成依赖于 Azure 和 Office 平台之间的互操作性。 可从 Microsoft 365 企业版和 Microsoft 365 政府版平台访问 Azure 环境中托管的产品/服务。 Office 365 和 Office 365 GCC 与 Azure 中的 Azure Active Directory (Azure AD) 配对。 Office 365 GCC High 和 Office 365 DoD 与 Azure 政府版中的 Azure AD 配对。

Microsoft 云的层次结构以及它们之间的相互关系如下图所示。

:::image type="content" source="media/microsoft-365-cloud-integration.png" alt-text="Microsoft 365 云集成。":::

Office 365 GCC 环境可帮助客户遵守 FedRAMP High、CJIS 和 IRS 1075 等美国政府要求。 Office 365 GCC High 和 DoD 环境可为需要遵守 DoD IL4/5、DFARS 7012、NIST 800-171 和 ITAR 的客户提供支持。

有关 Office 365 美国政府版环境的详细信息，请参阅：

- [Office 365 GCC](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc)
- [Office 365 GCC High 和 DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)


以下各节标识了服务与 Microsoft 365 集成的时间以及 Office 365 GCC、Office 365 High 和 Office 365 DoD 的功能可用性。
## <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel 是可缩放的云原生安全信息事件管理 (SIEM) 和安全业务流程自动响应 (SOAR) 解决方案。 Azure Sentinel 在整个企业范围内提供智能安全分析和威胁智能，为警报检测、威胁可见性、主动搜寻和威胁响应提供单一解决方案。

有关详细信息，请参阅 [Azure Sentinel 产品文档](/azure/sentinel/overview)。

Azure 和 Azure 政府版中 Azure Sentinel 的当前功能可用性如下表所示。


| 功能 | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [自带 ML (BYO-ML)](/azure/sentinel/bring-your-own-ml) | 公共预览版 | 公共预览版 |
| - [跨租户/跨工作区事件视图](/azure/sentinel/multiple-workspace-view) |公共预览版 | 公共预览版 |
| - [实体见解](/azure/sentinel/enable-entity-behavior-analytics) | 公共预览版 | 不可用 |
| - [融合物](/azure/sentinel/fusion)<br>高级多阶段攻击检测<sup>[1](#footnote1)</sup> | GA | 不可用 |
|- [笔记本](/azure/sentinel/notebooks) | GA | GA |
|- [SOC 事件审核指标](/azure/sentinel/manage-soc-with-incident-metrics) | GA | GA |
|- [播放列表](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-watchlist-is-now-in-public-preview/ba-p/1765887) | 公共预览版 | 不可用 |
| 威胁情报支持 | | |
| - [威胁情报 - TAXII 数据连接器](/azure/sentinel/import-threat-intelligence)  | 公共预览版 | 不可用 |
| - [威胁情报平台数据连接器](/azure/sentinel/import-threat-intelligence)  | 公共预览版 | 不可用 |
| - [威胁情报研究边栏选项卡](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | 公共预览版 | 不可用 |
| - [URL 引爆](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | GA | 不可用 |
| - [威胁情报工作簿](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | 不可用 |
|检测支持 | | |
| - [异常 Windows 文件共享访问检测](/azure/sentinel/fusion)  | 公共预览版 | 不可用 |
| - [异常 RDP 登录检测](/azure/sentinel/connect-windows-security-events#configure-the-security-events-connector-for-anomalous-rdp-login-detection)<br>内置 ML 检测 | 公共预览版 | 不可用 |
| - [异常 SSH 登录检测](/azure/sentinel/connect-syslog#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>内置 ML 检测 | 公共预览版 | 不可用 |
| Azure 服务连接器 | | |
| - [Azure 活动日志](/azure/sentinel/connect-azure-activity)                                  |   GA           |    GA         |
| - [Azure Active Directory](/azure/sentinel/connect-azure-active-directory)                |      GA        |       GA        |
| - [Azure ADIP](/azure/sentinel/connect-azure-ad-identity-protection)                         |  GA            |        GA              |
| - [Azure DDoS 防护](/azure/sentinel/connect-azure-ddos-protection)                |     GA         |       GA               |
| - [Azure Defender](/azure/sentinel/connect-azure-security-center)                  |    GA          |        GA              |
| - [适用于 IoT 的 Azure Defender](/azure/sentinel/connect-asc-iot)           |       GA       |  不可用           |
| - [Azure 防火墙](/azure/sentinel/connect-azure-firewall)                        |   GA           |        GA              |
| - [Azure 信息保护](/azure/sentinel/connect-azure-information-protection)              |     公共预览版         |         不可用             |
| - [Azure 密钥保管库](/azure/sentinel/connect-azure-key-vault)                           |       公共预览版         |         不可用                       |
| - [Azure Kubernetes 服务 (AKS)](/azure/sentinel/connect-azure-kubernetes-service)           |       公共预览版         |         不可用                |
| - [Azure SQL 数据库](/azure/sentinel/connect-azure-sql-logs)                        |     GA         |         GA             |
| - [Azure WAF](/azure/sentinel/connect-azure-waf)                                  |      GA        |      GA                |
| Windows 连接器 | | |
| - [Windows 防火墙](/azure/sentinel/connect-windows-firewall)                                 |     GA         |   GA           |
| - [Windows 安全事件](/azure/sentinel/connect-windows-security-events)                                  |      GA        |         GA     |
| 外部连接器| | |
| - [Agari Phishing Defense 和 Agari Brand Protection](/azure/sentinel/connect-agari-phishing-defense)       | 公共预览版 | 公共预览版 |
| - [AI Analyst Darktrace](/azure/sentinel/connect-data-sources)                            | 公共预览版 | 公共预览版 |
| - [AI Vectra Detect](/azure/sentinel/connect-ai-vectra-detect)                                 | 公共预览版 | 公共预览版 |
| - [Akamai Security Events](/azure/sentinel/connect-akamai-security-events)                           | 公共预览版 | 公共预览版 |
| - [Alcide kAudit](/azure/sentinel/connect-alcide-kaudit)                                   | 公共预览版 | 不可用      |
| - [适用于 Active Directory 的 Alsid](/azure/sentinel/connect-alsid-active-directory)                      | 公共预览版 | 不可用      |
| - [Apache HTTP Server](/azure/sentinel/connect-apache-http-server)                               | 公共预览版 | 不可用      |
| - [Aruba ClearPass](/azure/sentinel/connect-aruba-clearpass)                                  | 公共预览版 | 公共预览版 |
| - [AWS](/azure/sentinel/connect-data-sources)                                             | GA             | GA             |
| - [Barracuda CloudGen 防火墙](/azure/sentinel/connect-barracuda-cloudgen-firewall)                      | GA             | GA             |
| - [Barracuda Web 应用防火墙](/azure/sentinel/connect-barracuda)                       | GA             | GA             |
| - [BETTER Mobile Threat Defense MTD](/azure/sentinel/connect-better-mtd)                 | 公共预览版 | 不可用      |
| - [Beyond Security beSECURE](/azure/sentinel/connect-besecure)                        | 公共预览版 | 不可用      |
| - [Blackberry CylancePROTECT](/azure/sentinel/connect-data-sources)                        | 公共预览版 | 公共预览版 |
| - [Broadcom Symantec DLP](/azure/sentinel/connect-broadcom-symantec-dlp)                            | 公共预览版 | 公共预览版 |
| - [Check Point](/azure/sentinel/connect-checkpoint)                                      | GA             | GA             |
| - [Cisco ASA](/azure/sentinel/connect-cisco)                                        | GA             | GA             |
| - [Cisco Meraki](/azure/sentinel/connect-cisco-meraki)                                     | 公共预览版 | 公共预览版 |
| - [Cisco Umbrella](/azure/sentinel/connect-cisco-umbrella)                                   | 公共预览版 | 公共预览版 |
| - [Cisco UCS](/azure/sentinel/connect-cisco-ucs)                                        | 公共预览版 | 公共预览版 |
| - [Cisco Firepower EStreamer](/azure/sentinel/connect-data-sources)                          | 公共预览版 | 公共预览版 |
| - [Citrix Analytics WAF](/azure/sentinel/connect-citrix-waf)                             | GA             | GA             |
| - [通用事件格式(CEF)](/azure/sentinel/connect-common-event-format)                        | GA             | GA             |
| - [CyberArk Enterprise Password Vault (EPV) Events](/azure/sentinel/connect-cyberark) | 公共预览版 | 公共预览版 |
| - [ESET Enterprise Inspector](/azure/sentinel/connect-data-sources)                       | 公共预览版 | 不可用      |
| - [Eset Security Management Center](/azure/sentinel/connect-data-sources)                  | 公共预览版 | 不可用      |
| - [ExtraHop Reveal(x)](/azure/sentinel/connect-extrahop)                               | GA             | GA             |
| - [F5 BIG-IP](/azure/sentinel/connect-f5-big-ip)                                       | GA             | GA             |
| - [F5 Networks](/azure/sentinel/connect-f5)                                     | GA             | GA             |
| - [Forcepoint NGFW](/azure/sentinel/connect-forcepoint-casb-ngfw)                                  | 公共预览版 | 公共预览版 |
| - [Forepoint CASB](/azure/sentinel/connect-forcepoint-casb-ngfw)                                  | 公共预览版 | 公共预览版 |
| - [Forepoint DLP](/azure/sentinel/connect-forcepoint-dlp)                                   | 公共预览版 | 不可用      |
| - [适用于 CEF 的 ForgeRock Common Audit](/azure/sentinel/connect-data-sources)                  | 公共预览版 | 公共预览版 |
| - [Fortinet](/azure/sentinel/connect-fortinet)                                         | GA             | GA             |
| - [Google Workspace (G Suite)](/azure/sentinel/connect-google-workspace)                      | 公共预览版 | 不可用      |
| - [Illusive Attack Management System](/azure/sentinel/connect-illusive-attack-management-system)                | 公共预览版 | 公共预览版 |
| - [Imperva WAF 网关](/azure/sentinel/connect-imperva-waf-gateway)                             | 公共预览版 | 公共预览版 |
| - [Infoblox NIOS](/azure/sentinel/connect-infoblox)                                    | 公共预览版 | 公共预览版 |
| - [Juniper SRX](/azure/sentinel/connect-juniper-srx)                                      | 公共预览版 | 公共预览版 |
| - [Morphisec UTPP](/azure/sentinel/connect-data-sources)                                   | 公共预览版 | 公共预览版 |
| - [Netskope](/azure/sentinel/connect-data-sources)                                         | 公共预览版 | 公共预览版 |
| - [NXLog Windows DNS](/azure/sentinel/connect-nxlog-dns)                                             | 公共预览版 | 不可用      |
| - [NXLog LinuxAudit](/azure/sentinel/connect-nxlog-linuxaudit)                                 | 公共预览版 | 不可用      |
| - [Okta 单一登录](/azure/sentinel/connect-okta-single-sign-on)                              | 公共预览版 | 公共预览版 |
| - [Onapsis 平台](/azure/sentinel/connect-data-sources)                                 | 公共预览版 | 公共预览版 |
| - [One Identity Safeguard](/azure/sentinel/connect-one-identity)                          | GA             | GA             |
| - [Orca Security Alerts](/azure/sentinel/connect-orca-security-alerts)                            | 公共预览版 | 不可用      |
| - [Palo Alto Networks](/azure/sentinel/connect-paloalto)                               | GA             | GA             |
| - [Perimeter 81 活动日志](/azure/sentinel/connect-perimeter-81-logs)                      | GA             | 不可用      |
| - [Proofpoint On Demand Email Security](/azure/sentinel/connect-proofpoint-pod)             | 公共预览版 | 不可用      |
| - [Proofpoint TAP](/azure/sentinel/connect-proofpoint-tap)                                   | 公共预览版 | 公共预览版 |
| - [Pulse Connect Secure](/azure/sentinel/connect-proofpoint-tap)                             | 公共预览版 | 公共预览版 |
| - [Qualys Vulnerability Management](/azure/sentinel/connect-qualys-vm)                  | 公共预览版 | 公共预览版 |
| - [Salesforce Service Cloud](/azure/sentinel/connect-salesforce-service-cloud)                         | 公共预览版 | 不可用      |
| - [SonicWall 防火墙](/azure/sentinel/connect-sophos-cloud-optix)                              | 公共预览版 | 公共预览版 |
| - [Sophos Cloud Optix](/azure/sentinel/connect-sophos-cloud-optix)                               | 公共预览版 | 不可用      |
| - [Sophos XG 防火墙](/azure/sentinel/connect-sophos-xg-firewall)                               | 公共预览版 | 公共预览版 |
| - [Squadra Technologies secRMM](/azure/sentinel/connect-squadra-secrmm)               | GA             | GA             |
| - [Squid Proxy](/azure/sentinel/connect-squid-proxy)                                      | 公共预览版 | 不可用      |
| - [Symantec Integrated Cyber Defense Exchange](/azure/sentinel/connect-symantec)       | GA             | GA             |
| - [Symantec ProxySG](/azure/sentinel/connect-symantec-proxy-sg)                                | 公共预览版 | 公共预览版 |
| - [Symantec VIP](/azure/sentinel/connect-symantec-vip)                                     | 公共预览版 | 公共预览版 |
| - [Syslog](/azure/sentinel/connect-syslog)                                           | GA             | GA             |
| - [威胁情报平台](/azure/sentinel/connect-threat-intelligence)                   | 公共预览版 | 不可用      |
| - [威胁情报 TAXII](/azure/sentinel/connect-threat-intelligence)                       | 公共预览版 | 不可用      |
| - [Thycotic Secret Server](/azure/sentinel/connect-thycotic-secret-server)                          | 公共预览版 | 公共预览版 |
| - [Trend Micro Deep Security](/azure/sentinel/connect-trend-micro)                       | GA             | GA             |
| - [Trend Micro TippingPoint](/azure/sentinel/connect-trend-micro-tippingpoint)                         | 公共预览版 | 公共预览版 |
| - [Trend Micro XDR](/azure/sentinel/connect-data-sources)                                  | 公共预览版 | 不可用      |
| - [VMware Carbon Black Endpoint Standard](/azure/sentinel/connect-vmware-carbon-black)           | 公共预览版 | 公共预览版 |
| - [VMware ESXi](/azure/sentinel/connect-vmware-esxi)                                      | 公共预览版 | 公共预览版 |
| - [WireX Network Forensics Platform](/azure/sentinel/connect-wirex-systems)                | 公共预览版 | 公共预览版 |
| - [Zimperium Mobile Threat Defense](/azure/sentinel/connect-zimperium-mtd)                  | 公共预览版 | 不可用      |
| - [Zscaler](/azure/sentinel/connect-zscaler)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> 主权云不支持 SSH 和 RDP 检测，因为 Databricks ML 平台不可用。

### <a name="microsoft-365-data-connectors"></a>Microsoft 365 数据连接器

Office 365 GCC 与 Azure 中的 Azure Active Directory (Azure AD) 配对。 Office 365 GCC High 和 Office 365 DoD 与 Azure 政府版中的 Azure AD 配对。

> [!TIP]
> 请务必注意 Azure 环境，以了解[可实现互操作性之处](#microsoft-365-integration)。 在下表中，无法实现的互操作性以破折号 (-) 进行标记，表示不相关的支持。
>

| 连接器 | Azure  | Azure Government  |
| ------------------------------------ | -------------- | ---------------------- |
| [Dynamics365](/azure/sentinel/connect-dynamics-365)                               |              |                      |
| - Office 365 GCC |公共预览版 | -|
| - Office 365 GCC High | -|不可用 |
| - Office 365 DoD |- | 不可用|
| **[Microsoft 365 Defender](/azure/sentinel/connect-microsoft-365-defender)**                             |              |                      |
| - Office 365 GCC | 公共预览版| -|
| - Office 365 GCC High |- |不可用 |
| - Office 365 DoD |- | 不可用|
| **[Microsoft Cloud App Security (MCAS)](/azure/sentinel/connect-cloud-app-security)**                                      |              |                      |
| - Office 365 GCC | GA| -|
| - Office 365 GCC High |-|GA |
| - Office 365 DoD |- |GA |
| **[Microsoft Cloud App Security (MCAS)](/azure/sentinel/connect-cloud-app-security)** <br>影子 IT 日志                                  |              |                      |
| - Office 365 GCC | 公共预览版| -|
| - Office 365 GCC High |-|公共预览版 |
| - Office 365 DoD |- |公共预览版 |
| **[Microsoft Cloud App Security (MCAS)](/azure/sentinel/connect-cloud-app-security)**                  <br>警报                    |              |                      |
| - Office 365 GCC | 公共预览版| -|
| - Office 365 GCC High |-|公共预览版 |
| - Office 365 DoD |- |公共预览版 |
| **[用于终结点的 Microsoft Defender](/azure/sentinel/connect-microsoft-defender-advanced-threat-protection)**                                       |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |不可用 |
| - Office 365 DoD |- | 不可用|
| **[Microsoft Defender for Identity](/azure/sentinel/connect-azure-atp)**                                        |              |                      |
| - Office 365 GCC |公共预览版 | -|
| - Office 365 GCC High |- | 不可用 |
| - Office 365 DoD |- |不可用 |
| **[Microsoft Defender for Office 365](/azure/sentinel/connect-office-365-advanced-threat-protection)**               |              |                      |
| - Office 365 GCC |公共预览版 |- |
| - Office 365 GCC High |- |不可用 |
| - Office 365 DoD | -|不可用 |
| **[Office 365](/azure/sentinel/connect-office-365)**                                      |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |GA |
| - Office 365 DoD |- |GA |
| | |


## <a name="next-steps"></a>后续步骤

- 了解[共担责任](https://docs.microsoft.com/azure/security/fundamentals/shared-responsibility)模型、由云服务提供商处理的安全任务，以及由你处理的任务。
- 了解 [Azure 政府版云](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)功能以及可靠的设计和安全性，其可满足美国联邦、州和地方政府组织及其合作伙伴的合规性要求。
- 了解 [Office 365 政府版计划](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments)。
- 了解 [Azure 是否符合法律法规标准](/azure/compliance/)。
