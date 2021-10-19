---
title: 适用于美国政府客户的 Azure 服务云功能可用性
description: 列出适用于美国政府客户的 Azure 安全服务（如 Azure Sentinel）的功能可用性
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 918743544aeaf60932f14d1d1eed609775b29efd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710563"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>适用于美国政府客户的云功能可用性

本文介绍了 Microsoft Azure 和 Azure 政府版中以下安全服务的云功能可用性：

- [Azure 安全中心](#azure-security-center)
- [Azure Sentinel](#azure-sentinel)
- [适用于 IoT 的 Azure Defender](#azure-defender-for-iot)

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

## <a name="azure-security-center"></a>Azure 安全中心

Azure 安全中心是一个统一的基础结构安全管理系统，可以增强数据中心的安全态势，以及为云中（无论是否在 Azure 中）和本地的混合工作负荷提供高级威胁防护。

有关详细信息，请参阅 [Azure 安全中心产品文档](../../security-center/security-center-introduction.md)。

下表显示 Azure 和 Azure 政府中当前的安全中心功能可用性。


| 功能/服务                                                                                                                                                               | Azure          | Azure Government               |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|
| **安全中心免费功能**                                                                                                                                             |                |                                |
| - [连续导出](../../security-center/continuous-export.md)                                                                                                             | GA             | GA                             |
| - [工作流自动化](../../security-center/continuous-export.md)                                                                                                           | GA             | GA                             |
| - [建议例外规则](../../security-center/exempt-resource.md)                                                                                                  | 公共预览版 | 不可用                  | 
| - [警报抑制规则](../../security-center/alerts-suppression-rules.md)                                                                                                | GA             | GA                             | 
| - [安全警报的电子邮件通知](../../security-center/security-center-provide-security-contact-details.md)                                                        | GA             | GA                             | 
| - [代理和扩展的自动预配](../../security-center/security-center-enable-data-collection.md)                                                              | GA             | GA                             | 
| - [资产清单](../../security-center/asset-inventory.md)                                                                                                                 | GA             | GA                             | 
| - [Azure 安全中心工作簿库中的 Azure Monitor 工作簿报表](../../security-center/custom-dashboards-azure-workbooks.md)                                  | GA             | GA                             | 
| **Azure Defender 计划和扩展**                                                                                                                                       |                |                                | 
| - [适用于服务器的 Azure Defender](../../security-center/defender-for-servers-introduction.md)                                                                                    | GA             | GA                             | 
| - [适用于应用服务的 Azure Defender](../../security-center/defender-for-app-service-introduction.md)                                                                            | GA             | 不可用                  | 
| - [适用于 DNS 的 Azure Defender](../../security-center/defender-for-dns-introduction.md)                                                                                            | GA             | GA                             | 
| - [适用于容器注册表的 Azure Defender](../../security-center/defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> | 
| - [适用于容器注册表的 Azure Defender 扫描 CI/CD 工作流中的容器映像](../../security-center/defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | 公共预览版 | 不可用                  | 
| - [Azure Defender for Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | 
| - [适用于启用了 Azure Arc 的 Kubernetes 群集的 Azure Defender 扩展](../../security-center/defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | 公共预览版 | 不可用                  | 
| - [适用于 Azure SQL 数据库服务器的 Azure Defender](../../security-center/defender-for-sql-introduction.md)                                                                     | GA             | GA                             | 
| - [计算机上的 Azure Defender for SQL 服务器](../../security-center/defender-for-sql-introduction.md)                                                                        | GA             | GA                             |
| - [适用于开源关系数据库的 Azure Defender](../../security-center/defender-for-databases-introduction.md)                                                         | GA             | 不可用                  |
| - [适用于 Key Vault 的 Azure Defender](../../security-center/defender-for-key-vault-introduction.md)                                                                                | GA             | 不可用                  |
| - [适用于资源管理器的 Azure Defender](../../security-center/defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             |
| - [适用于存储的 Azure Defender](../../security-center/defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             |
| - [针对 Cosmos DB 的威胁防护](../../security-center/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | 公共预览版 | 不可用                  |
| - [Kubernetes 工作负载保护](../../security-center/kubernetes-workload-protections.md)                                                                                  | GA             | GA                             |
| - [使用 Sentinel 进行双向警报同步](../../sentinel/connect-azure-security-center.md)                                                                       | 公共预览版 | 不可用                  | 
| **适用于服务器功能的 Azure Defender** <sup>[7](#footnote7)</sup>                                                                                                            |                |                                |
| - [实时 VM 访问](../../security-center/security-center-just-in-time.md)                                                                                             | GA             | GA                             |
| - [文件完整性监视](../../security-center/security-center-file-integrity-monitoring.md)                                                                             | GA             | GA                             |
| - [自适应应用程序控制](../../security-center/security-center-adaptive-application.md)                                                                              | GA             | GA                             |
| - [自适应网络强化](../../security-center/security-center-adaptive-network-hardening.md)                                                                           | GA             | 不可用                  |
| - [Docker 主机强化](../../security-center/harden-docker-hosts.md)                                                                                                       | GA             | GA                             |
| - [计算机的集成漏洞评估](../../security-center/deploy-vulnerability-assessment-vm.md)                                                             | GA             | 不可用                  |
| - [合规性仪表板和报表](../../security-center/security-center-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             |
| - [Microsoft Defender for Endpoint 部署和集成许可证](../../security-center/security-center-wdatp.md)                                                         | GA             | GA                             |
| - [连接 AWS 帐户](../../security-center/quickstart-onboard-aws.md)                                                                                                      | GA             | 不可用                  |
| - [连接 GCP 帐户](../../security-center/quickstart-onboard-gcp.md)                                                                                                      | GA             | 不可用                  |
|                                                                                                                                                                               |                |                                |

<sup><a name="footnote1" /></a>1</sup> 部分 GA：禁用漏洞扫描的特定发现的功能处于公共预览状态。

<sup><a name="footnote2" /></a>2</sup> Azure Gov 上容器注册表的漏洞扫描只能通过“推送扫描”功能来执行。

<sup><a name="footnote3" /></a>3</sup> 需要适用于容器注册表的 Azure Defender。

<sup><a name="footnote4" /></a>4</sup> 部分正式发布：对启用了 Azure Arc 的群集的支持处于公开预览状态，但在 Azure 政府中不可用。

<sup><a name="footnote5" /></a>5</sup> 需要 Azure Defender for Kubernetes。

<sup><a name="footnote6" /></a>6</sup> 部分 GA：适用于存储的 Azure Defender 中的部分威胁防护警报处于公共预览状态。

<sup><a name="footnote7" /></a>7</sup> 这些功能都需要[适用于服务器的 Azure Defender](../../security-center/defender-for-servers-introduction.md)。

<sup><a name="footnote8" /></a>8</sup> 每个云类型提供的标准可能存在差异。

## <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel 是可缩放的云原生安全信息事件管理 (SIEM) 和安全业务流程自动响应 (SOAR) 解决方案。 Azure Sentinel 在整个企业范围内提供智能安全分析和威胁智能，为警报检测、威胁可见性、主动搜寻和威胁响应提供单一解决方案。

有关详细信息，请参阅 [Azure Sentinel 产品文档](../../sentinel/overview.md)。

Azure 和 Azure 政府版中 Azure Sentinel 的当前功能可用性如下表所示。


| 功能 | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [自动化规则](../../sentinel/automate-incident-handling-with-automation-rules.md) | 公共预览版 | 公共预览版 |
|- [自带 ML (BYO-ML)](../../sentinel/bring-your-own-ml.md) | 公共预览版 | 公共预览版 |
| - [跨租户/跨工作区事件视图](../../sentinel/multiple-workspace-view.md) |公共预览版 | 公共预览版 |
| - [实体见解](../../sentinel/enable-entity-behavior-analytics.md) | GA | 公共预览版 |
| - [融合物](../../sentinel/fusion.md)<br>高级多阶段攻击检测<sup>[1](#footnote1)</sup> | GA | GA |
| - [搜寻](../../sentinel/hunting.md) | GA | GA |
|- [笔记本](../../sentinel/notebooks.md) | GA | GA |
|- [SOC 事件审核指标](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [播放列表](../../sentinel/watchlists.md) | GA | GA |
| 威胁情报支持 | | |
| - [威胁情报 - TAXII 数据连接器](../../sentinel/understand-threat-intelligence.md)  | GA | 不可用 |
| - [威胁情报平台数据连接器](../../sentinel/understand-threat-intelligence.md)  | 公共预览版 | 不可用 |
| - [威胁情报研究边栏选项卡](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | GA | 不可用 |
| - [URL 引爆](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | 公共预览版 | 不可用 |
| - [威胁情报工作簿](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | 不可用 |
|检测支持 | | |
| - [异常 Windows 文件共享访问检测](../../sentinel/fusion.md)  | 公共预览版 | 不可用 |
| - [异常 RDP 登录检测](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>内置 ML 检测 | 公共预览版 | 不可用 |
| - [异常 SSH 登录检测](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>内置 ML 检测 | 公共预览版 | 不可用 |
| Azure 服务连接器 |  |  |
| - [Azure 活动日志](../../sentinel/data-connectors-reference.md#azure-activity) | GA | GA |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md) | GA | GA |
| - [Azure ADIP](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) | GA | GA |
| - [Azure DDoS 防护](../../sentinel/data-connectors-reference.md#azure-ddos-protection) | GA | GA |
| - [Azure Defender](../../sentinel/connect-azure-security-center.md) | GA | GA |
| - [适用于 IoT 的 Azure Defender](../../sentinel/data-connectors-reference.md#azure-defender-for-iot) | 公共预览版 | 不可用 |
| - [Azure 防火墙](../../sentinel/data-connectors-reference.md#azure-firewall) | GA | GA |
| - [Azure 信息保护](../../sentinel/data-connectors-reference.md#azure-information-protection) | 公共预览版 | 不可用 |
| - [Azure 密钥保管库](../../sentinel/data-connectors-reference.md#azure-key-vault) | 公共预览版 | 不可用 |
| - [Azure Kubernetes 服务 (AKS)](../../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks) | 公共预览版 | 不可用 |
| - [Azure SQL 数据库](../../sentinel/data-connectors-reference.md#azure-sql-databases) | GA | GA |
| - [Azure WAF](../../sentinel/data-connectors-reference.md#azure-web-application-firewall-waf) | GA | GA |
| Windows 连接器 |  |  |
| - [Windows 防火墙](../../sentinel/data-connectors-reference.md#windows-firewall) | GA | GA |
| - [Windows 安全事件](../../sentinel/connect-windows-security-events.md) | GA | GA |
| 外部连接器 |  |  |
| - [Agari Phishing Defense 和 Agari Brand Protection](../../sentinel/data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview) | 公共预览版 | 公共预览版 |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md) | 公共预览版 | 公共预览版 |
| - [AI Vectra Detect](../../sentinel/data-connectors-reference.md#ai-vectra-detect-preview) | 公共预览版 | 公共预览版 |
| - [Akamai Security Events](../../sentinel/data-connectors-reference.md#akamai-security-events-preview) | 公共预览版 | 公共预览版 |
| - [Alcide kAudit](../../sentinel/data-connectors-reference.md#alcide-kaudit) | 公共预览版 | 不可用 |
| - [适用于 Active Directory 的 Alsid](../../sentinel/data-connectors-reference.md#alsid-for-active-directory) | 公共预览版 | 不可用 |
| - [Apache HTTP Server](../../sentinel/data-connectors-reference.md#apache-http-server) | 公共预览版 | 不可用 |
| - [Aruba ClearPass](../../sentinel/data-connectors-reference.md#aruba-clearpass-preview) | 公共预览版 | 公共预览版 |
| - [AWS](../../sentinel/connect-data-sources.md) | GA | GA |
| - [Barracuda CloudGen 防火墙](../../sentinel/data-connectors-reference.md#barracuda-cloudgen-firewall) | GA | GA |
| - [Barracuda Web 应用防火墙](../../sentinel/data-connectors-reference.md#barracuda-waf) | GA | GA |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/data-connectors-reference.md#better-mobile-threat-defense-mtd-preview) | 公共预览版 | 不可用 |
| - [Beyond Security beSECURE](../../sentinel/data-connectors-reference.md#beyond-security-besecure) | 公共预览版 | 不可用 |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md) | 公共预览版 | 公共预览版 |
| - [Broadcom Symantec DLP](../../sentinel/data-connectors-reference.md#broadcom-symantec-data-loss-prevention-dlp-preview) | 公共预览版 | 公共预览版 |
| - [Check Point](../../sentinel/data-connectors-reference.md#check-point) | GA | GA |
| - [Cisco ASA](../../sentinel/data-connectors-reference.md#cisco-asa) | GA | GA |
| - [Cisco Meraki](../../sentinel/data-connectors-reference.md#cisco-meraki-preview) | 公共预览版 | 公共预览版 |
| - [Cisco Umbrella](../../sentinel/data-connectors-reference.md#cisco-umbrella-preview) | 公共预览版 | 公共预览版 |
| - [Cisco UCS](../../sentinel/data-connectors-reference.md#cisco-unified-computing-system-ucs-preview) | 公共预览版 | 公共预览版 |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md) | 公共预览版 | 公共预览版 |
| - [Citrix Analytics WAF](../../sentinel/data-connectors-reference.md#citrix-web-app-firewall-waf-preview) | GA | GA |
| - [通用事件格式(CEF)](../../sentinel/connect-common-event-format.md) | GA | GA |
| - [CyberArk Enterprise Password Vault (EPV) Events](../../sentinel/data-connectors-reference.md#cyberark-enterprise-password-vault-epv-events-preview) | 公共预览版 | 公共预览版 |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | 公共预览版 | 不可用      |
| - [Eset Security Management Center](../../sentinel/connect-data-sources.md)                  | 公共预览版 | 不可用      |
| - [ExtraHop Reveal(x)](../../sentinel/data-connectors-reference.md#extrahop-revealx)                               | GA             | GA             |
| - [F5 BIG-IP](../../sentinel/data-connectors-reference.md#f5-big-ip)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/data-connectors-reference.md#f5-networks-asm)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | 公共预览版 | 公共预览版 |
| - [Forcepoint CASB](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | 公共预览版 | 公共预览版 |
| - [Forcepoint DLP ](../../sentinel/data-connectors-reference.md#forcepoint-data-loss-prevention-dlp-preview)                                   | 公共预览版 | 不可用      |
| - [适用于 CEF 的 ForgeRock Common Audit](../../sentinel/connect-data-sources.md)                  | 公共预览版 | 公共预览版 |
| - [Fortinet](../../sentinel/data-connectors-reference.md#fortinet)                                         | GA             | GA             |
| - [Google Workspace (G Suite)](../../sentinel/data-connectors-reference.md#google-workspace-g-suite-preview)                      | 公共预览版 | 不可用      |
| - [Illusive Attack Management System](../../sentinel/data-connectors-reference.md#illusive-attack-management-system-ams-preview)                | 公共预览版 | 公共预览版 |
| - [Imperva WAF 网关](../../sentinel/data-connectors-reference.md#imperva-waf-gateway-preview)                             | 公共预览版 | 公共预览版 |
| - [Infoblox NIOS](../../sentinel/data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview)                                    | 公共预览版 | 公共预览版 |
| - [Juniper SRX](../../sentinel/data-connectors-reference.md#juniper-srx-preview)                                      | 公共预览版 | 公共预览版 |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | 公共预览版 | 公共预览版 |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | 公共预览版 | 公共预览版 |
| - [NXLog Windows DNS](../../sentinel/data-connectors-reference.md#nxlog-dns-logs-preview)                                             | 公共预览版 | 不可用      |
| - [NXLog LinuxAudit](../../sentinel/data-connectors-reference.md#nxlog-linuxaudit-preview)                                 | 公共预览版 | 不可用      |
| - [Okta 单一登录](../../sentinel/data-connectors-reference.md#okta-single-sign-on-preview)                              | 公共预览版 | 公共预览版 |
| - [Onapsis 平台](../../sentinel/connect-data-sources.md)                                 | 公共预览版 | 公共预览版 |
| - [One Identity Safeguard](../../sentinel/data-connectors-reference.md#one-identity-safeguard-preview)                          | GA             | GA             |
| - [Orca Security Alerts](../../sentinel/data-connectors-reference.md#orca-security-preview)                            | 公共预览版 | 不可用      |
| - [Palo Alto Networks](../../sentinel/data-connectors-reference.md#palo-alto-networks)                               | GA             | GA             |
| - [Perimeter 81 活动日志](../../sentinel/data-connectors-reference.md#perimeter-81-activity-logs-preview)                      | GA             | 不可用      |
| - [Proofpoint On Demand Email Security](../../sentinel/data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)             | 公共预览版 | 不可用      |
| - [Proofpoint TAP](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                                   | 公共预览版 | 公共预览版 |
| - [Pulse Connect Secure](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                             | 公共预览版 | 公共预览版 |
| - [Qualys Vulnerability Management](../../sentinel/data-connectors-reference.md#qualys-vulnerability-management-vm-preview)                  | 公共预览版 | 公共预览版 |
| - [Salesforce Service Cloud](../../sentinel/data-connectors-reference.md#salesforce-service-cloud-preview)                         | 公共预览版 | 不可用      |
| - [SonicWall 防火墙](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                              | 公共预览版 | 公共预览版 |
| - [Sophos Cloud Optix](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                               | 公共预览版 | 不可用      |
| - [Sophos XG 防火墙](../../sentinel/data-connectors-reference.md#sophos-xg-firewall-preview)                               | 公共预览版 | 公共预览版 |
| - [Squadra Technologies secRMM](../../sentinel/data-connectors-reference.md#squadra-technologies-secrmm)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/data-connectors-reference.md#squid-proxy-preview)                                      | 公共预览版 | 不可用      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/data-connectors-reference.md#symantec-integrated-cyber-defense-exchange-icdx)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/data-connectors-reference.md#symantec-proxysg-preview)                                | 公共预览版 | 公共预览版 |
| - [Symantec VIP](../../sentinel/data-connectors-reference.md#symantec-vip-preview)                                     | 公共预览版 | 公共预览版 |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [威胁情报平台](../../sentinel/connect-threat-intelligence-tip.md)                   | 公共预览版 | 不可用      |
| - [威胁情报 TAXII](../../sentinel/connect-threat-intelligence-tip.md)                       | 公共预览版 | 不可用      |
| - [Thycotic Secret Server](../../sentinel/data-connectors-reference.md#thycotic-secret-server-preview)                          | 公共预览版 | 公共预览版 |
| - [Trend Micro Deep Security](../../sentinel/data-connectors-reference.md#trend-micro-deep-security)                       | GA             | GA             |
| - [Trend Micro TippingPoint](../../sentinel/data-connectors-reference.md#trend-micro-tippingpoint-preview)                         | 公共预览版 | 公共预览版 |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | 公共预览版 | 不可用      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)           | 公共预览版 | 公共预览版 |
| - [VMware ESXi](../../sentinel/data-connectors-reference.md#vmware-esxi-preview)                                      | 公共预览版 | 公共预览版 |
| - [WireX Network Forensics Platform](../../sentinel/data-connectors-reference.md#wirex-network-forensics-platform-preview)                | 公共预览版 | 公共预览版 |
| - [Zimperium Mobile Threat Defense](../../sentinel/data-connectors-reference.md#zimperium-mobile-thread-defense-preview)                  | 公共预览版 | 不可用      |
| - [Zscaler](../../sentinel/data-connectors-reference.md#zscaler)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> 主权云不支持 SSH 和 RDP 检测，因为 Databricks ML 平台不可用。

### <a name="microsoft-365-data-connectors"></a>Microsoft 365 数据连接器

Office 365 GCC 与 Azure 中的 Azure Active Directory (Azure AD) 配对。 Office 365 GCC High 和 Office 365 DoD 与 Azure 政府版中的 Azure AD 配对。

> [!TIP]
> 请务必注意 Azure 环境，以了解[可实现互操作性之处](#microsoft-365-integration)。 在下表中，无法实现的互操作性以破折号 (-) 进行标记，表示不相关的支持。
>

| 连接器 | Azure | Azure Government |
|--|--|--|
| [Dynamics365](../../sentinel/data-connectors-reference.md#dynamics-365) |  |  |
| - Office 365 GCC | 公共预览版 | - |
| - Office 365 GCC High | - | 不可用 |
| - Office 365 DoD | - | 不可用 |
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)** |  |  |
| - Office 365 GCC | 公共预览版 | - |
| - Office 365 GCC High | - | 不可用 |
| - Office 365 DoD | - | 不可用 |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)** <br>影子 IT 日志 |  |  |
| - Office 365 GCC | 公共预览版 | - |
| - Office 365 GCC High | - | 公共预览版 |
| - Office 365 DoD | - | 公共预览版 |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)**                  <br>警报 |  |  |
| - Office 365 GCC | 公共预览版 | - |
| - Office 365 GCC High | - | 公共预览版 |
| - Office 365 DoD | - | 公共预览版 |
| **[用于终结点的 Microsoft Defender](../../sentinel/data-connectors-reference.md#microsoft-defender-for-endpoint)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Defender for Identity](../../sentinel/data-connectors-reference.md#microsoft-defender-for-identity)** |  |  |
| - Office 365 GCC | 公共预览版 | - |
| - Office 365 GCC High | - | 不可用 |
| - Office 365 DoD | - | 不可用 |
| **[Microsoft Defender for Office 365](../../sentinel/data-connectors-reference.md#microsoft-defender-for-office-365)** |  |  |
| - Office 365 GCC | 公共预览版 | - |
| - Office 365 GCC High | - | 不可用 |
| - Office 365 DoD | - | 不可用 |
| **[Office 365](../../sentinel/data-connectors-reference.md#microsoft-office-365)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
|  |  |

## <a name="azure-defender-for-iot"></a>适用于 IoT 的 Azure Defender

Azure Defender for IoT 使你可以通过在所有 IoT/OT 设备上提供全面的安全来加速 IoT/OT 创新。对于最终用户组织，Azure Defender for IoT 提供无代理网络层安全性，可快速部署、使用各种工业设备，以及与 Azure Sentinel 和其他 SOC 工具互操作。 部署在本地或与 Azure 连接的环境中。对于 IoT 设备生成器，Azure Defender for IoT 安全代理使你可以直接在新的 IoT 设备和 Azure IoT 项目中构建安全性。 微代理具有灵活的部署选项，包括以二进制包进行部署或修改源代码的功能。 微代理适用于标准 IoT 操作系统，如 Linux 和 Azure RTO。 有关详细信息，请参阅 [Azure Defender for IoT 产品文档](../../defender-for-iot/index.yml)。

下表显示 Azure 和 Azure 政府中当前的 Azure Defender for IoT 功能可用性。

### <a name="for-organizations"></a>对于组织

| 功能 | Azure | Azure Government |
|--|--|--|
| [本地设备发现和清单](../../defender-for-iot/how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md) | GA | GA |
| [漏洞管理](../../defender-for-iot/how-to-create-risk-assessment-reports.md) | GA | GA |
| [通过 IoT 和 OT 行为分析进行威胁检测](../../defender-for-iot/how-to-work-with-alerts-on-your-sensor.md) | GA | GA |
| [手动和自动威胁情报更新](../../defender-for-iot/how-to-work-with-threat-intelligence-packages.md) | GA | GA |
| 通过 SIEM、SOAR 和 XDR 统一 IT 和 OT 安全性 |  |  |
| [Active Directory](../../defender-for-iot/organizations/how-to-create-and-manage-users.md#integrate-with-active-directory-servers) | GA | GA |
| [ArcSight](../../defender-for-iot/organizations/how-to-accelerate-alert-incident-response.md#accelerate-incident-workflows-by-using-alert-groups) | GA | GA |
| [ClearPass（警报和清单）](../../defender-for-iot/organizations/how-to-install-software.md#attach-a-span-virtual-interface-to-the-virtual-switch) | GA | GA |
| [CyberArk PSM](../../defender-for-iot/organizations/concept-key-concepts.md#integrations) | GA | GA |
| [电子邮件](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#email-address-action) | GA | GA |
| [FortiGate](../../defender-for-iot/organizations/tutorial-fortinet.md) | GA | GA |
| [FortiSIEM](../../defender-for-iot/organizations/tutorial-fortinet.md) | GA | GA |
| [Microsoft Sentinel](../../defender-for-iot/organizations/how-to-configure-with-sentinel.md) | 公共预览版 | 公共预览版 |
| [NetWitness](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#netwitness-action) | GA | GA |
| [Palo Alto NGFW](../../defender-for-iot/organizations/tutorial-palo-alto.md) | GA | GA |
| [Palo Alto Panorama](../../defender-for-iot/organizations/tutorial-palo-alto.md) | GA | GA |
| [ServiceNow（警报和清单）](../../defender-for-iot/organizations/tutorial-servicenow.md) | GA | GA |
| [SNMP MIB 监视](../../defender-for-iot/organizations/how-to-set-up-snmp-mib-monitoring.md) | GA | GA |
| [Splunk](../../defender-for-iot/organizations/tutorial-splunk.md) | GA | GA |
| [SYSLOG 服务器（CEF 格式）](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [SYSLOG 服务器（LEEF 格式）](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [SYSLOG 服务器（对象）](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [SYSLOG 服务器（文本消息）](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [Web 回调 (Webhook)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#webhook-server-action) | GA | GA |

### <a name="for-device-builders"></a>对于设备构建者

| 功能 | Azure | Azure Government |
|--|--|--|
| [适用于 Azure RTOS 的微代理](../../defender-for-iot/iot-security-azure-rtos.md) | GA | GA |
| [使用 Azure Defender for IoT 配置 Sentinel](../../defender-for-iot/how-to-configure-with-sentinel.md) | 公共预览版 | 公共预览版 |
| **适用于 Linux 的独立微代理概述** |  |  |
| [独立代理二进制安装](../../defender-for-iot/quickstart-standalone-agent-binary-installation.md) | 公共预览版 | 公共预览版 |

## <a name="next-steps"></a>后续步骤

- 了解[共担责任](shared-responsibility.md)模型、由云服务提供商处理的安全任务，以及由你处理的任务。
- 了解 [Azure 政府版云](../../azure-government/documentation-government-welcome.md)功能以及可靠的设计和安全性，其可满足美国联邦、州和地方政府组织及其合作伙伴的合规性要求。
- 了解 [Office 365 政府版计划](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments)。
- 了解 [Azure 是否符合法律法规标准](../../compliance/index.yml)。
