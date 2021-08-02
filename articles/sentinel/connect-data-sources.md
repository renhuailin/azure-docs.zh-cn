---
title: 将数据源连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Microsoft 365 Defender（原 Microsoft 威胁防护）、Microsoft 365 和 Office 365、Azure AD、ATP 和 Cloud App Security 等数据源连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: d6b132fbb3aed541cc602537df1d40fa0d47702a
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421843"
---
# <a name="connect-data-sources"></a>连接数据源

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

启用 Azure Sentinel 后，首先需要连接数据源。 Azure Sentinel 随附许多适用于 Microsoft 解决方案的开箱即用连接器，提供实时集成，包括 Microsoft 365 Defender（之前称为 Microsoft 威胁防护）解决方案、Microsoft 365 源（包括 Office 365）、Azure AD、Microsoft Defender for Identity（之前称为 Azure ATP）、Microsoft Cloud App Security 等。 此外，内置的连接器可以拓宽非 Microsoft 解决方案的安全生态系统。 也可以使用常用事件格式 (CEF)、Syslog 或 REST-API 将数据源与 Azure Sentinel 相连接。

1. 在菜单上，选择“数据连接器”。 通过此页，可以查看 Azure Sentinel 提供的连接器及其状态的完整列表。 选择要连接的连接器，然后选择“打开连接器页”。 

   ![数据连接器库](./media/collect-data/collect-data-page.png)

1. 在特定连接器页上，确保已满足所有先决条件，并按照相关说明将数据连接到 Azure Sentinel。 可能需要一段时间才能让日志开始与 Azure Sentinel 保持同步。 在连接后，可以在“收到的数据”图中查看数据摘要，以及数据类型的连接状态。

   ![配置数据连接器](./media/collect-data/opened-connector-page.png)
  
1. 单击“后续步骤”选项卡，以获取 Azure Sentinel 针对特定数据类型提供的现成内容的列表。

   ![连接器的后续步骤](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>数据连接方法

Azure Sentinel 支持以下数据连接方法：

- **服务到服务集成**：<br> 有些服务是本机互连的（如 AWS 和 Microsoft 服务），这些服务利用 Azure 基础进行现成集成，只需单击几次即可连接以下解决方案：
    - [Amazon Web Services - CloudTrail](connect-aws.md)
    - [Azure Active Directory](connect-azure-active-directory.md) - 审核日志和登录日志
    - [Azure Active Directory 标识保护](connect-azure-ad-Identity-protection.md)
    - [Azure 活动](connect-azure-activity.md)
    - [Azure DDoS 防护](connect-azure-ddos-protection.md)
    - Azure 安全中心的 [Azure Defender 警报](connect-azure-security-center.md)
    - [Azure Defender for IoT](connect-asc-iot.md)（以前称为适用于 IoT 的 Azure 安全中心）
    - [Azure 防火墙](connect-azure-firewall.md)
    - [Azure 信息保护](connect-azure-information-protection.md)
    - [Azure Key Vault](connect-azure-key-vault.md)
    - [Azure Kubernetes 服务 (AKS)](connect-azure-kubernetes-service.md)
    - [Azure SQL 数据库](connect-azure-sql-logs.md)
    - [Azure 存储帐户](connect-azure-storage-account.md)
    - [Azure Web 应用程序防火墙 (WAF)](connect-azure-waf.md)（原 Microsoft WAF）
    - [域名服务器](connect-dns.md)
    - [Dynamics 365](connect-dynamics-365.md)
    - [Microsoft 365 Defender](connect-microsoft-365-defender.md) - 包含用于终结点原始数据的 M365D 事件和 Defender
    - [Microsoft Cloud App Security](connect-cloud-app-security.md)
    - [Microsoft Defender for Endpoint](connect-microsoft-defender-advanced-threat-protection.md)（原 Microsoft Defender 高级威胁防护）
    - [Microsoft Defender For Identity](connect-azure-atp.md)（原 Azure 高级威胁防护）
    - [Microsoft Defender for Office 365](connect-office-365-advanced-threat-protection.md)（原 Office 365 高级威胁防护）
    - [Office 365](connect-office-365.md)（包括 Teams）
    - [Windows 防火墙](connect-windows-firewall.md)
    - (Windows) [安全事件](connect-windows-security-events.md)

- **通过 API 连接外部解决方案**：可以使用联网数据源提供的 API 连接某些数据源。 一般情况下，大多数安全技术都会提供一组 API，通过这些 API 可以检索事件日志。这些 API 连接到 Azure Sentinel，收集特定的数据类型并将其发送到 Azure Log Analytics。 通过 API 连接的设备包括：
    
    - [Agari Phishing Defense 和 Agari Brand Protection](connect-agari-phishing-defense.md)
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen 防火墙](connect-barracuda-cloudgen-firewall.md)
    - [BETTER Mobile Threat Defense](connect-better-mtd.md)
    - [Beyond Security beSECURE](connect-besecure.md)
    - [Cisco Umbrella](connect-cisco-umbrella.md)
    - [Citrix Analytics（安全）](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Google Workspace（原 G Suite）](connect-google-workspace.md)
    - [NXLog (Windows) DNS 日志](connect-nxlog-dns.md)
    - [NXLog LinuxAudit](connect-nxlog-linuxaudit.md)
    - [Okta SSO](connect-okta-single-sign-on.md)
    - [Orca Security](connect-orca-security-alerts.md)
    - [Perimeter 81 日志](connect-perimeter-81-logs.md)
    - [Proofpoint On Demand (POD) Email Security](connect-proofpoint-pod.md)
    - [Proofpoint TAP](connect-proofpoint-tap.md)
    - [Qualys VM](connect-qualys-vm.md)
    - [Salesforce 服务云](connect-salesforce-service-cloud.md)
    - [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [VMware Carbon Black Cloud Endpoint Standard](connect-vmware-carbon-black.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **通过代理连接外部解决方案**：可以通过代理使用 Syslog 协议将 Azure Sentinel 连接到可执行实时日志流式处理的其他任何数据源。

    大部分设备使用 Syslog 协议发送包含日志本身以及日志相关数据的事件消息。 虽然日志格式各不相同，但大多数设备都支持基于 CEF 的日志数据格式。 

    Azure Sentinel 代理实际上就是 Log Analytics 代理，它将 CEF 格式的日志转换为可供 Log Analytics 引入的格式。 根据设备类型，可以直接在设备上安装代理，或者在专用的 Linux 日志转发器上安装代理。 适用于 Linux 的代理通过 UDP 从 Syslog 守护程序接收事件，但如果预期 Linux 计算机需要收集大量的 Syslog 事件，则会通过 TCP 将这些事件从 Syslog 守护程序发送到代理，然后从代理发送到 Log Analytics。

    - **防火墙、代理和终结点 - CEF：**
        - [AI Vectra 检测](connect-ai-vectra-detect.md)
        - [Akamai 安全事件](connect-akamai-security-events.md)
        - [Aruba ClearPass](connect-aruba-clearpass.md)
        - [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Citrix WAF](connect-citrix-waf.md)
        - [CyberArk Enterprise Password Vault](connect-cyberark.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Forcepoint 产品](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Illusive Networks AMS](connect-illusive-attack-management-system.md)
        - [Imperva WAF 网关](connect-imperva-waf-gateway.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Thycotic Secret Server](connect-thycotic-secret-server.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
        - [WireX Network Forensics Platform](connect-wirex-systems.md)
        - [Zscaler](connect-zscaler.md)
        - [其他基于 CEF 的设备](connect-common-event-format.md)
    - **防火墙、代理和终结点 - Syslog：**
        - [Alsid for Active Directory](connect-alsid-active-directory.md)
        - [Cisco Meraki](connect-cisco-meraki.md)
        - [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
        - [Infoblox NIOS](connect-infoblox.md)
        - [Juniper SRX](connect-juniper-srx.md)
        - [Pulse Connect Secure](connect-pulse-connect-secure.md)
        - [Sophos XG](connect-sophos-xg-firewall.md)
        - [Squid Proxy](connect-squid-proxy.md)
        - [Symantec Proxy SG](connect-symantec-proxy-sg.md)
        - [Symantec VIP](connect-symantec-vip.md)
        - [VMware ESXi](connect-vmware-esxi.md)
        - [其他基于 Syslog 的设备](connect-syslog.md)
    - [Apache HTTP Server](connect-apache-http-server.md)
    - DLP 解决方案
    - [威胁智能提供程序](connect-threat-intelligence.md)
    - [DNS 计算机](connect-dns.md) - 直接安装在 DNS 计算机上的代理
    - [Azure Stack VM](connect-azure-stack.md)
    - Linux 服务器
    - 其他云
    
## <a name="agent-connection-options"></a>代理连接选项<a name="agent-options"></a>

若要将外部设备连接到 Azure Sentinel，代理必须部署在专用计算机上（VM 或本地），以支持设备与 Azure Sentinel 之间的通信。 可以自动或手动部署代理。 仅当专用计算机是在 Azure 中创建的新 VM 时，才能进行自动部署。 

![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，可以在现有的 Azure VM 上、在其他云中的 VM 上或者在本地计算机上手动部署代理。

![本地 CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>使用 Azure Sentinel 连接选项映射数据类型


| **Data type** | **如何连接** | **数据连接器？** | **注释** |
|------|---------|-------------|------|
| AWSCloudTrail | [连接 AWS](connect-aws.md) | &#10003; | |
| AzureActivity | [连接 Azure 活动](connect-azure-activity.md)和[活动日志概述](../azure-monitor/essentials/platform-logs-overview.md)| &#10003; | |
| AuditLogs | [连接 Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [连接 Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Azure 诊断](../firewall/firewall-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Azure 信息保护报告](/azure/information-protection/reports-aip)<br>[连接 Azure 信息保护](connect-azure-information-protection.md)  | &#10003; | 除数据类型外，这通常还使用 **InformationProtectionEvents** 函数。 有关详细信息，请参阅[如何修改报告和创建自定义查询](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [流量分析架构](../network-watcher/traffic-analytics.md) [流量分析](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [连接 CEF](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [连接 Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [连接 Windows 安全事件](connect-windows-security-events.md)  | &#10003; | 有关不安全协议工作簿的信息，请参阅[不安全协议工作簿设置](./quickstart-get-visibility.md#use-built-in-workbooks)  |
| Syslog | [连接 Syslog](connect-syslog.md) | &#10003; | |
| Microsoft Web 应用程序防火墙 (WAF) - (AzureDiagnostics) |[连接 Microsoft Web 应用程序防火墙](./connect-azure-waf.md) | &#10003; | |
| SymantecICDx_CL | [连接 Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [连接威胁智能](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Monitor 服务映射](../azure-monitor/vm/service-map.md)<br>[Azure Monitor VM 见解载入](../azure-monitor/vm/vminsights-enable-overview.md) <br> [启用 Azure Monitor VM 见解](../azure-monitor/vm/vminsights-enable-overview.md) <br> [使用单一 VM 载入](../azure-monitor/vm/vminsights-enable-portal.md)<br>  [通过 Policy 使用载入](../azure-monitor/vm/vminsights-enable-policy.md)| &#10007; | VM 见解工作簿  |
| DnsEvents | [连接 DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [连接 IIS 日志](../azure-monitor/agents/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [连接 Wire Data](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [连接 Windows 防火墙](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [连接 Azure AD 标识保护](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [连接 Microsoft Defender for Identity](connect-azure-atp.md)（原 Azure ATP） | &#10003; | |
| ASC SecurityAlert  | 从 Azure 安全中心[连接 Azure Defender 警报](connect-azure-security-center.md)  | &#10003; | |
| MCAS SecurityAlert  | [连接 Microsoft Cloud App Security](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon（事件） | [连接 Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [连接 Windows 事件](../azure-monitor/agents/data-sources-windows-events.md) <br> [获取 Sysmon 分析程序](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | 默认情况下，虚拟机上未安装 Sysmon 集合。 有关如何安装 Sysmon 代理的详细信息，请参阅 [Sysmon](/sysinternals/downloads/sysmon)。 |
| ConfigurationData  | [自动执行 VM 清单](../automation/change-tracking/overview.md)| &#10007; | |
| ConfigurationChange  | [自动执行 VM 跟踪](../automation/change-tracking/overview.md) | &#10007; | |
| F5 BIG-IP | [连接 F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10003; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [连接 Barracuda](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>后续步骤

- 若要开始使用 Azure Sentinel，需要订阅 Microsoft Azure。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/free/)。
- 了解如何[将数据载入到 Azure Sentinel](quickstart-onboard.md)，以及[获取数据和潜在威胁的见解](quickstart-get-visibility.md)。
