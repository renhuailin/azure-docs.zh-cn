---
title: 适用于美国政府客户的 Azure 服务云功能可用性
description: 列出适用于美国政府客户的 Azure 安全服务（如 Azure Sentinel）的功能可用性
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 05/23/2021
ms.openlocfilehash: 817e23bfe21aeabde51064cd2606fa447ee6de22
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060005"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>适用于美国政府客户的云功能可用性


本文介绍了 Microsoft Azure 和 Azure 政府版中以下安全服务的云功能可用性：

- [Azure Sentinel](#azure-sentinel)

> [!NOTE]
> 本文中即将添加其他安全服务。
> 

## <a name="azure-government"></a>Azure Government

Azure 政府版使用与 Azure（有时称为 Azure 商业版或 Azure 公共版）相同的基础技术，其中包括基础结构即服务 (IaaS)、平台即服务 (PaaS) 和软件即服务 (SaaS) 的核心组件。 Azure 和 Azure 政府版均实施全面的安全控制以及 Microsoft 对保护客户数据的承诺。

Azure 政府版拥有物理隔离的云环境，专用于美国联邦、州、地方、部落政府及其合作伙伴。 尽管 Azure 和 Azure 政府版云环境均在 FedRAMP High 影响级别进行评估和授权，但 Azure 政府版通过与在美国存储客户数据有关的合同承诺，并仅限经过筛选的美国人员才能访问处理客户数据的系统，为客户提供额外一层保护。 使用云来存储或处理受美国出口管制法规（如 EAR、ITAR 和 DoE 10 CFR 第 810 部分）限制的数据的客户可能会对这些承诺感兴趣。

有关 Azure 政府版的详细信息，请参阅[什么是 Azure 政府版？](../../azure-government/documentation-government-welcome.md)

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

有关详细信息，请参阅 [Azure Sentinel 产品文档](../../sentinel/overview.md)。

Azure 和 Azure 政府版中 Azure Sentinel 的当前功能可用性如下表所示。


| 功能 | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [自带 ML (BYO-ML)](../../sentinel/bring-your-own-ml.md) | 公共预览版 | 公共预览版 |
| - [跨租户/跨工作区事件视图](../../sentinel/multiple-workspace-view.md) |公共预览版 | 公共预览版 |
| - [实体见解](../../sentinel/enable-entity-behavior-analytics.md) | GA | 公共预览版 |
| - [融合物](../../sentinel/fusion.md)<br>高级多阶段攻击检测<sup>[1](#footnote1)</sup> | GA | GA |
| - [搜寻](../../sentinel/hunting.md) | GA | GA |
|- [笔记本](../../sentinel/notebooks.md) | GA | GA |
|- [SOC 事件审核指标](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [播放列表](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-watchlist-is-now-in-public-preview/ba-p/1765887) | 公共预览版 | 不可用 |
| 威胁情报支持 | | |
| - [威胁情报 - TAXII 数据连接器](../../sentinel/import-threat-intelligence.md)  | 公共预览版 | 不可用 |
| - [威胁情报平台数据连接器](../../sentinel/import-threat-intelligence.md)  | 公共预览版 | 不可用 |
| - [威胁情报研究边栏选项卡](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | 公共预览版 | 不可用 |
| - [URL 引爆](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | GA | 不可用 |
| - [威胁情报工作簿](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | 不可用 |
|检测支持 | | |
| - [异常 Windows 文件共享访问检测](../../sentinel/fusion.md)  | 公共预览版 | 不可用 |
| - [异常 RDP 登录检测](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>内置 ML 检测 | 公共预览版 | 不可用 |
| - [异常 SSH 登录检测](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>内置 ML 检测 | 公共预览版 | 不可用 |
| Azure 服务连接器 | | |
| - [Azure 活动日志](../../sentinel/connect-azure-activity.md)                                  |   GA           |    GA         |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md)                |      GA        |       GA        |
| - [Azure ADIP](../../sentinel/connect-azure-ad-identity-protection.md)                         |  GA            |        GA              |
| - [Azure DDoS 防护](../../sentinel/connect-azure-ddos-protection.md)                |     GA         |       GA               |
| - [Azure Defender](../../sentinel/connect-azure-security-center.md)                  |    GA          |        GA              |
| - [适用于 IoT 的 Azure Defender](../../sentinel/connect-asc-iot.md)           |       GA       |  不可用           |
| - [Azure 防火墙](../../sentinel/connect-azure-firewall.md)                        |   GA           |        GA              |
| - [Azure 信息保护](../../sentinel/connect-azure-information-protection.md)              |     公共预览版         |         不可用             |
| - [Azure 密钥保管库](../../sentinel/connect-azure-key-vault.md)                           |       公共预览版         |         不可用                       |
| - [Azure Kubernetes 服务 (AKS)](../../sentinel/connect-azure-kubernetes-service.md)           |       公共预览版         |         不可用                |
| - [Azure SQL 数据库](../../sentinel/connect-azure-sql-logs.md)                        |     GA         |         GA             |
| - [Azure WAF](../../sentinel/connect-azure-waf.md)                                  |      GA        |      GA                |
| Windows 连接器 | | |
| - [Windows 防火墙](../../sentinel/connect-windows-firewall.md)                                 |     GA         |   GA           |
| - [Windows 安全事件](../../sentinel/connect-windows-security-events.md)                                  |      GA        |         GA     |
| 外部连接器| | |
| - [Agari Phishing Defense 和 Agari Brand Protection](../../sentinel/connect-agari-phishing-defense.md)       | 公共预览版 | 公共预览版 |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md)                            | 公共预览版 | 公共预览版 |
| - [AI Vectra Detect](../../sentinel/connect-ai-vectra-detect.md)                                 | 公共预览版 | 公共预览版 |
| - [Akamai Security Events](../../sentinel/connect-akamai-security-events.md)                           | 公共预览版 | 公共预览版 |
| - [Alcide kAudit](../../sentinel/connect-alcide-kaudit.md)                                   | 公共预览版 | 不可用      |
| - [适用于 Active Directory 的 Alsid](../../sentinel/connect-alsid-active-directory.md)                      | 公共预览版 | 不可用      |
| - [Apache HTTP Server](../../sentinel/connect-apache-http-server.md)                               | 公共预览版 | 不可用      |
| - [Aruba ClearPass](../../sentinel/connect-aruba-clearpass.md)                                  | 公共预览版 | 公共预览版 |
| - [AWS](../../sentinel/connect-data-sources.md)                                             | GA             | GA             |
| - [Barracuda CloudGen 防火墙](../../sentinel/connect-barracuda-cloudgen-firewall.md)                      | GA             | GA             |
| - [Barracuda Web 应用防火墙](../../sentinel/connect-barracuda.md)                       | GA             | GA             |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/connect-better-mtd.md)                 | 公共预览版 | 不可用      |
| - [Beyond Security beSECURE](../../sentinel/connect-besecure.md)                        | 公共预览版 | 不可用      |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md)                        | 公共预览版 | 公共预览版 |
| - [Broadcom Symantec DLP](../../sentinel/connect-broadcom-symantec-dlp.md)                            | 公共预览版 | 公共预览版 |
| - [Check Point](../../sentinel/connect-checkpoint.md)                                      | GA             | GA             |
| - [Cisco ASA](../../sentinel/connect-cisco.md)                                        | GA             | GA             |
| - [Cisco Meraki](../../sentinel/connect-cisco-meraki.md)                                     | 公共预览版 | 公共预览版 |
| - [Cisco Umbrella](../../sentinel/connect-cisco-umbrella.md)                                   | 公共预览版 | 公共预览版 |
| - [Cisco UCS](../../sentinel/connect-cisco-ucs.md)                                        | 公共预览版 | 公共预览版 |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md)                          | 公共预览版 | 公共预览版 |
| - [Citrix Analytics WAF](../../sentinel/connect-citrix-waf.md)                             | GA             | GA             |
| - [通用事件格式(CEF)](../../sentinel/connect-common-event-format.md)                        | GA             | GA             |
| - [CyberArk Enterprise Password Vault (EPV) Events](../../sentinel/connect-cyberark.md) | 公共预览版 | 公共预览版 |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | 公共预览版 | 不可用      |
| - [Eset Security Management Center](../../sentinel/connect-data-sources.md)                  | 公共预览版 | 不可用      |
| - [ExtraHop Reveal(x)](../../sentinel/connect-extrahop.md)                               | GA             | GA             |
| - [F5 BIG-IP](../../sentinel/connect-f5-big-ip.md)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/connect-f5.md)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | 公共预览版 | 公共预览版 |
| - [Forepoint CASB](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | 公共预览版 | 公共预览版 |
| - [Forepoint DLP](../../sentinel/connect-forcepoint-dlp.md)                                   | 公共预览版 | 不可用      |
| - [适用于 CEF 的 ForgeRock Common Audit](../../sentinel/connect-data-sources.md)                  | 公共预览版 | 公共预览版 |
| - [Fortinet](../../sentinel/connect-fortinet.md)                                         | GA             | GA             |
| - [Google Workspace (G Suite)](../../sentinel/connect-google-workspace.md)                      | 公共预览版 | 不可用      |
| - [Illusive Attack Management System](../../sentinel/connect-illusive-attack-management-system.md)                | 公共预览版 | 公共预览版 |
| - [Imperva WAF 网关](../../sentinel/connect-imperva-waf-gateway.md)                             | 公共预览版 | 公共预览版 |
| - [Infoblox NIOS](../../sentinel/connect-infoblox.md)                                    | 公共预览版 | 公共预览版 |
| - [Juniper SRX](../../sentinel/connect-juniper-srx.md)                                      | 公共预览版 | 公共预览版 |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | 公共预览版 | 公共预览版 |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | 公共预览版 | 公共预览版 |
| - [NXLog Windows DNS](../../sentinel/connect-nxlog-dns.md)                                             | 公共预览版 | 不可用      |
| - [NXLog LinuxAudit](../../sentinel/connect-nxlog-linuxaudit.md)                                 | 公共预览版 | 不可用      |
| - [Okta 单一登录](../../sentinel/connect-okta-single-sign-on.md)                              | 公共预览版 | 公共预览版 |
| - [Onapsis 平台](../../sentinel/connect-data-sources.md)                                 | 公共预览版 | 公共预览版 |
| - [One Identity Safeguard](../../sentinel/connect-one-identity.md)                          | GA             | GA             |
| - [Orca Security Alerts](../../sentinel/connect-orca-security-alerts.md)                            | 公共预览版 | 不可用      |
| - [Palo Alto Networks](../../sentinel/connect-paloalto.md)                               | GA             | GA             |
| - [Perimeter 81 活动日志](../../sentinel/connect-perimeter-81-logs.md)                      | GA             | 不可用      |
| - [Proofpoint On Demand Email Security](../../sentinel/connect-proofpoint-pod.md)             | 公共预览版 | 不可用      |
| - [Proofpoint TAP](../../sentinel/connect-proofpoint-tap.md)                                   | 公共预览版 | 公共预览版 |
| - [Pulse Connect Secure](../../sentinel/connect-proofpoint-tap.md)                             | 公共预览版 | 公共预览版 |
| - [Qualys Vulnerability Management](../../sentinel/connect-qualys-vm.md)                  | 公共预览版 | 公共预览版 |
| - [Salesforce Service Cloud](../../sentinel/connect-salesforce-service-cloud.md)                         | 公共预览版 | 不可用      |
| - [SonicWall 防火墙](../../sentinel/connect-sophos-cloud-optix.md)                              | 公共预览版 | 公共预览版 |
| - [Sophos Cloud Optix](../../sentinel/connect-sophos-cloud-optix.md)                               | 公共预览版 | 不可用      |
| - [Sophos XG 防火墙](../../sentinel/connect-sophos-xg-firewall.md)                               | 公共预览版 | 公共预览版 |
| - [Squadra Technologies secRMM](../../sentinel/connect-squadra-secrmm.md)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/connect-squid-proxy.md)                                      | 公共预览版 | 不可用      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/connect-symantec.md)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/connect-symantec-proxy-sg.md)                                | 公共预览版 | 公共预览版 |
| - [Symantec VIP](../../sentinel/connect-symantec-vip.md)                                     | 公共预览版 | 公共预览版 |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [威胁情报平台](../../sentinel/connect-threat-intelligence.md)                   | 公共预览版 | 不可用      |
| - [威胁情报 TAXII](../../sentinel/connect-threat-intelligence.md)                       | 公共预览版 | 不可用      |
| - [Thycotic Secret Server](../../sentinel/connect-thycotic-secret-server.md)                          | 公共预览版 | 公共预览版 |
| - [Trend Micro Deep Security](../../sentinel/connect-trend-micro.md)                       | GA             | GA             |
| - [Trend Micro TippingPoint](../../sentinel/connect-trend-micro-tippingpoint.md)                         | 公共预览版 | 公共预览版 |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | 公共预览版 | 不可用      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/connect-vmware-carbon-black.md)           | 公共预览版 | 公共预览版 |
| - [VMware ESXi](../../sentinel/connect-vmware-esxi.md)                                      | 公共预览版 | 公共预览版 |
| - [WireX Network Forensics Platform](../../sentinel/connect-wirex-systems.md)                | 公共预览版 | 公共预览版 |
| - [Zimperium Mobile Threat Defense](../../sentinel/connect-zimperium-mtd.md)                  | 公共预览版 | 不可用      |
| - [Zscaler](../../sentinel/connect-zscaler.md)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> 主权云不支持 SSH 和 RDP 检测，因为 Databricks ML 平台不可用。

### <a name="microsoft-365-data-connectors"></a>Microsoft 365 数据连接器

Office 365 GCC 与 Azure 中的 Azure Active Directory (Azure AD) 配对。 Office 365 GCC High 和 Office 365 DoD 与 Azure 政府版中的 Azure AD 配对。

> [!TIP]
> 请务必注意 Azure 环境，以了解[可实现互操作性之处](#microsoft-365-integration)。 在下表中，无法实现的互操作性以破折号 (-) 进行标记，表示不相关的支持。
>

| 连接器 | Azure  | Azure Government  |
| ------------------------------------ | -------------- | ---------------------- |
| [Dynamics365](../../sentinel/connect-dynamics-365.md)                               |              |                      |
| - Office 365 GCC |公共预览版 | -|
| - Office 365 GCC High | -|不可用 |
| - Office 365 DoD |- | 不可用|
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)**                             |              |                      |
| - Office 365 GCC | 公共预览版| -|
| - Office 365 GCC High |- |不可用 |
| - Office 365 DoD |- | 不可用|
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)**                                      |              |                      |
| - Office 365 GCC | GA| -|
| - Office 365 GCC High |-|GA |
| - Office 365 DoD |- |GA |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)** <br>影子 IT 日志                                  |              |                      |
| - Office 365 GCC | 公共预览版| -|
| - Office 365 GCC High |-|公共预览版 |
| - Office 365 DoD |- |公共预览版 |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)**                  <br>警报                    |              |                      |
| - Office 365 GCC | 公共预览版| -|
| - Office 365 GCC High |-|公共预览版 |
| - Office 365 DoD |- |公共预览版 |
| **[用于终结点的 Microsoft Defender](../../sentinel/connect-microsoft-defender-advanced-threat-protection.md)**                                       |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |不可用 |
| - Office 365 DoD |- | 不可用|
| **[Microsoft Defender for Identity](../../sentinel/connect-azure-atp.md)**                                        |              |                      |
| - Office 365 GCC |公共预览版 | -|
| - Office 365 GCC High |- | 不可用 |
| - Office 365 DoD |- |不可用 |
| **[Microsoft Defender for Office 365](../../sentinel/connect-office-365-advanced-threat-protection.md)**               |              |                      |
| - Office 365 GCC |公共预览版 |- |
| - Office 365 GCC High |- |不可用 |
| - Office 365 DoD | -|不可用 |
| **[Office 365](../../sentinel/connect-office-365.md)**                                      |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |GA |
| - Office 365 DoD |- |GA |
| | |


## <a name="next-steps"></a>后续步骤

- 了解[共担责任](shared-responsibility.md)模型、由云服务提供商处理的安全任务，以及由你处理的任务。
- 了解 [Azure 政府版云](../../azure-government/documentation-government-welcome.md)功能以及可靠的设计和安全性，其可满足美国联邦、州和地方政府组织及其合作伙伴的合规性要求。
- 了解 [Office 365 政府版计划](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments)。
- 了解 [Azure 是否符合法律法规标准](../../compliance/index.yml)。
