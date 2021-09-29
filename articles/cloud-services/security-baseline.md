---
title: 适用于 Azure 云服务的 Azure 安全基线
description: Azure 云服务安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 814514ede598d0de435c767ee29ef6aa4564d003
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616567"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>适用于 Azure 云服务的 Azure 安全基线

此安全基线将 [Azure 安全基准 1.0 版](../security/benchmarks/overview-v1.md)中的指南应用到 Microsoft Azure 云服务。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容分为 Azure 安全基准定义的安全控制和适用于云服务的相关指南。 不适用于云服务的控制措施已排除。

 
若要了解云服务如何完全映射到 Azure 安全基准，请参阅[完整的云服务安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：创建具有独立的公共和专用子网的经典 Azure 虚拟网络，以根据受信任的端口和 IP 范围强制实施隔离。 这些虚拟网络和子网必须是基于经典虚拟网络（经典部署）的资源，而不是当前的 Azure 资源管理器资源。  

使用网络安全组允许或拒绝流量，网络安全组包含基于流量方向、协议、源地址和端口以及目标地址和端口的访问控制规则。 可以随时更改网络安全组的规则，所做的更改将应用于所有关联的实例。

不能将 Microsoft Azure 云服务（经典）置于 Azure 资源管理器虚拟网络中。 但是，可以通过对等互连将基于资源管理器的虚拟网络与基于经典部署的虚拟网络连接起来。 

- [网络安全组概述](../virtual-network/network-security-groups-overview.md)

- [虚拟网络对等互连](./cloud-services-connectivity-and-networking-faq.yml#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services-)

**责任**：客户

Azure 安全中心监视：无

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：记录你的 Azure 云服务配置并监视其是否发生更改。 使用服务的配置文件指定要为服务中的每个角色部署的角色实例数、任何配置设置的值，以及与角色关联的任何证书的缩略图。 

如果服务属于虚拟网络，必须在服务配置文件以及虚拟网络配置文件中提供网络的配置信息。 服务配置文件的默认扩展名为 .csdef。 请注意，不支持将 Azure Policy 用于经典部署来强制实施配置。

在服务配置文件 (.cscfg) 中设置云服务的配置值，并在服务定义 (.csdef) 文件中设置定义。 使用服务定义文件定义应用程序的服务模型。 定义可用于云服务的角色，并指定服务终结点。 使用服务配置文件记录 Azure 云服务的配置。 可以通过 ServiceConfig.cscfg 文件进行任何重新配置。 

监视可选的 NetworkTrafficRules 元素服务定义，该定义对哪些角色可以与指定的内部终结点通信进行了限制。 在服务定义文件中配置 NetworkTrafficRules 节点，该节点是一个可选元素，用于指定角色应如何相互通信。 对哪些角色可以访问特定角色的内部终结点进行限制。  请注意，服务定义无法更改。 

启用网络安全组流日志，并将日志发送到 Azure 存储帐户进行审核。 将流日志发送到 Log Analytics 工作区，通过流量分析来了解 Azure 租户中的流量模式。 流量分析的优势包括能够可视化网络活动、识别热点和安全威胁、了解通信流模式，以及查明网络不当配置。

- [Azure 资源管理器部署与经典部署 - 了解部署模型和资源状态](../azure-resource-manager/management/deployment-models.md)

- [云服务配置文件](schema-cscfg-file.md)

- [Azure Policy 支持的服务列表](/cli/azure/azure-services-the-azure-cli-can-manage)

**责任**：客户

Azure 安全中心监视：无

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：Microsoft 使用传输层安全性 (TLS) 协议 v1.2 ，在 Azure 云服务和客户之间传输数据时对数据进行保护。 Microsoft 的数据中心与连接到 Azure 服务的客户端系统协商建立 TLS 连接。 TLS 提供严格的身份验证，消息隐私性和完整性强（允许检测消息篡改、拦截和伪造），具有良好的互操作性，算法灵活，易于部署和使用。

- [加密基础知识](../security/fundamentals/encryption-overview.md)

- [配置 TLS/SSL 证书](cloud-services-configure-ssl-certificate-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：Azure 云实施多层网络安全性来防范其平台服务遭受分布式拒绝服务 (DDoS) 攻击。 Azure DDoS 防护是 Azure 云的持续监视过程的一部分，已通过渗透测试持续得到改善。 根据设计，此 DDoS 防护不但能够承受外部的攻击，而且能够承受其他 Azure 租户的攻击。 

除了 Azure 云服务内的平台级保护之外，还有几种不同的方法可以阻止或拒绝通信。 这些是： 

-  创建一个启动任务来选择性地阻止某些特定的 IP 地址
-  通过修改 IIS web.config 文件来限制某个 Azure Web 角色对一组指定的 IP 地址的访问权限

阻止发往云服务的默认 URL 或名称（例如 .cloudapp.net）的传入流量。在云服务定义 (.csdef) 文件中的站点绑定配置下将主机头设置为自定义 DNS 名称。

为经典订阅管理员分配配置“拒绝应用”规则。 默认情况下，在定义内部终结点后，通信可以从任意角色流动到角色的内部终结点，而不会受到任何限制。 若要限制通信，你必须将 NetworkTrafficRules 元素添加到服务定义文件中的 ServiceDefinition 元素。

- [如何阻止/禁用发往云服务的默认 URL 的传入流量](./cloud-services-connectivity-and-networking-faq.yml#how-can-i-block-disable-incoming-traffic-to-the-default-url-of-my-cloud-service-)

- [Azure DDOS 防护](./cloud-services-connectivity-and-networking-faq.yml#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service-)

- [阻止特定 IP 地址](./cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**责任**：客户

Azure 安全中心监视：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：使用 Azure 网络观察程序（网络性能监视、诊断和分析服务），可以对 Azure 网络进行监视。 网络观察程序代理虚拟机扩展是按需捕获网络流量和运行 Azure 虚拟机上的其他高级功能所必需的。 安装网络观察程序代理虚拟机扩展，并开启网络安全组流日志。

在网络安全组上配置流日志记录。 查看有关如何将网络观察程序虚拟机扩展部署到已通过经典部署模型部署的现有虚拟机的详细信息。

- [在网络安全组上配置流日志记录](../virtual-machines/extensions/network-watcher-linux.md)

- [有关配置流日志的详细信息，请访问此文](/cli/azure/azure-services-the-azure-cli-can-manage)

**责任**：客户

Azure 安全中心监视：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：Azure 云服务没有内置的 IDS 或 IPS 功能。 客户可以根据组织的要求从 Azure 市场中选择并部署基于网络的补充 IDS 或 IPS 解决方案。 使用第三方解决方案时，请确保使用 Azure 云服务全面测试所选的 IDS 或 IPS 解决方案，以确保操作正确和功能正常。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**责任**：客户

Azure 安全中心监视：无

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：附加到 Azure 云服务的服务证书可以确保与服务的安全通信。 在服务的定义中定义的这些证书会自动部署到运行 Web 角色实例的虚拟机。 例如，对于 Web 角色，你可以使用可对公开的 HTTPS 终结点进行身份验证的服务证书。 

若要更新证书，只需上传新证书并更改服务配置文件中的指纹值。

使用 TLS 1.2 协议（用于保护数据的最常用方法）来提供机密性和完整性保护。 

通常情况下，若要保护 Web 应用程序，使其免受 OWASP 十大安全风险之类的攻击，你可以部署启用了 Azure Web 应用程序防火墙来保护 Web 应用程序的 Azure 应用程序网关。 

- [服务证书](cloud-services-certs-create.md)

- [为 Azure 中的应用程序配置 TLS](cloud-services-configure-ssl-certificate-portal.md)

- [如何部署应用程序网关](../application-gateway/quick-create-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：强化你的 Azure 云服务配置并监视其是否发生更改。 服务配置文件指定要为服务中每个角色部署的角色实例数、任何配置设置的值，以及与角色关联的任何证书的缩略图。 

如果你的服务是虚拟网络的一部分，必须在服务配置文件以及虚拟网络配置文件中提供网络的配置信息。 服务配置文件的默认扩展名为 .csdef。

请注意，不支持将 Azure Policy 用于 Azure 云服务来强制实施配置。

**责任**：客户

Azure 安全中心监视：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：可以使用 Azure 网络安全组来筛选 Azure 虚拟网络中出入 Azure 资源的网络流量。 网络安全组包含安全规则，这些规则可允许或拒绝多种 Azure 资源的入站和出站网络流量。 可以为每项规则指定源和目标、端口以及协议。

对于 Azure 云服务中的各个网络安全组规则，请使用“说明”字段来记录那些允许流量进出网络的规则。

- [如何使用网络安全组规则筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 流量管理器内置的终结点监视和终结点自动故障转移功能。 这些功能可以帮助你更好地交付高可用性应用程序，以便应对终结点故障和 Azure 区域故障。 若要配置终结点监视，必须在流量管理器配置文件中指定某些设置。

从活动日志（Azure 中的一种平台日志）中收集对订阅级事件的见解。 这包括何时修改了资源或何时启动了虚拟机之类的信息。 通过 Azure 门户查看活动日志，或通过 PowerShell 和 CLI 检索条目。 

创建诊断设置，以便将活动日志发送到 Azure Monitor，也可将其发送到 Azure 事件中心以转发到 Azure 外部，或发送到 Azure 存储进行存档。 配置 Azure Monitor，以便在 Azure 云服务中的关键资源发生更改时获得通知警报。 

- [Azure 活动日志](../azure-monitor/essentials/activity-log.md)

- [使用 Azure Monitor 创建、查看和管理活动日志警报](../azure-monitor/alerts/alerts-activity-log.md)

- [流量管理器监视](../traffic-manager/traffic-manager-monitoring.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指导**：Microsoft 为 Azure 云服务维护 Azure 资源的时间源。 客户可能需要创建网络规则，以允许使用 UDP 协议通过端口 123 访问其环境中使用的时间服务器。

- [NTP 服务器访问](../firewall/protect-azure-virtual-desktop.md#additional-considerations)

**责任**：共享

Azure 安全中心监视：无

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：通过 Azure 事件中心以编程方式使用云服务流数据。 将所有这些数据集成并发送到 Azure Sentinel 以监视和查看你的日志，或者使用第三方 SIEM。 为了进行集中的安全日志管理，请通过配置连续导出将所选 Azure 安全中心数据导出到 Azure 事件中心，然后为 SIEM 设置相应的连接器。 下面是适用于 Azure Sentinel 的一些选项，包括第三方工具：

- Azure Sentinel - 使用原生的安全中心警报数据连接器
- Splunk - 使用适用于 Splunk 的 Azure Monitor 加载项
- IBM QRadar - 使用手动配置的日志源
- ArcSight – 使用 SmartConnector

有关 Azure Sentinel 的可用连接器的其他详细信息，请参阅 Azure Sentinel 文档。 

- [连接数据源](../sentinel/connect-data-sources.md)

- [与 SIEM 集成](../security-center/continuous-export.md)

- [存储诊断数据](diagnostics-extension-to-storage.md)

- [通过 Azure 事件中心配置 SIEM 集成](../security-center/continuous-export.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：配置 Visual Studio 以设置用于对 Azure 云服务进行故障排除的 Azure 诊断，这将捕获虚拟机（包括运行你的 Azure 云服务的虚拟机实例）上的系统和日志记录数据。 诊断数据将传输到你选择的存储帐户。 在 Azure 云服务项目中启用诊断，然后再完成其部署。

 
在 Azure Monitor 的活动日志中查看某些事件的更改历史记录。 审核某个事件时间段内发生的更改。 从活动日志中选择事件，以便通过“更改历史记录(预览)”选项卡进行更深入的检查。从 Visual Studio 发布 Azure 云服务时，将诊断数据发送到 Application Insights。 在那时创建 Application Insights Azure 资源，或者将数据发送到现有 Azure 资源。 

Application Insights 可以监视 Azure 云服务的可用性、性能、故障和使用情况。 可将自定义图表添加到 Application Insights，以便查看最重要的数据。 在 Azure 云服务项目中使用 Application Insights SDK 可以收集角色实例数据。 

- [部署前在 Visual Studio 中启用诊断](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [查看更改历史记录](../azure-monitor/essentials/activity-log.md#view-change-history)

- [适用于 Azure 云服务（经典）的 Application Insights](../azure-monitor/app/cloudservices.md)

- [为 Azure 云服务（经典）和虚拟机设置诊断](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**责任**：客户

Azure 安全中心监视：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：你可以为 Azure 云服务使用高级监视，以便每隔 5 分钟、1 小时和 12 小时对其他指标执行采样和收集操作。 聚合的数据存储在存储帐户的表中，10 天后予以清除。 但是，使用的存储帐户是按角色配置的；可以对不同的角色使用不同的存储帐户。 这种配置是使用 .csdef 和 .cscfg 文件中的连接字符串完成的。

请注意，高级监视涉及到对你想要监视的角色使用 Azure 诊断扩展（Application Insights SDK 是可选的）。 该诊断扩展使用名为 diagnostics.wadcfgx 的配置文件（按角色）来配置所监视的诊断指标。 Azure 诊断扩展收集数据，并将数据存储在 Azure 存储帐户中。 在 .wadcfgx、.csdef 和 .cscfg 文件中配置这些设置。

- [云服务监视简介](cloud-services-how-to-monitor.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：Azure 云服务可以使用基本或高级监视模式。 Azure 云服务自动从主机虚拟机收集基本监视数据（CPU 百分比、网络传入/传出和磁盘读/写）。 在 Azure 门户中云服务的概述和指标页上查看所收集的监视数据。 

在使用 Azure 诊断扩展的同时，在 Azure 云服务中启用诊断，以便收集应用程序日志、性能计数器等诊断数据。 使用 Set-AzureServiceDiagnosticsExtension cmdlet 在已运行的云服务上启用或更新诊断配置，或者使用诊断扩展自动部署云服务。 （可选）安装 Application Insights SDK。 将性能计数器发送到 Azure Monitor。

Azure 诊断扩展收集数据，并将数据存储在 Azure 存储帐户中。 将诊断数据传输到 Microsoft Azure 存储模拟器或 Azure 存储，因为该数据不会永久存储。 将数据保存到存储中后，可以选择使用多种可用工具之一（例如 Visual Studio 中的服务器资源管理器、Microsoft Azure 存储资源管理器、Azure Management Studio）查看这些数据。 配置诊断指标，在诊断扩展中使用名为 diagnostics.wadcfgx 的配置文件（按角色）对其进行监视。 

- [云服务监视简介](cloud-services-how-to-monitor.md)

- [如何在辅助角色中启用诊断 - 与 SIEM 集成](../security-center/continuous-export.md)

- [使用 PowerShell 在 Azure 云服务中启用诊断](cloud-services-diagnostics-powershell.md)

- [在 Azure 存储中存储和查看诊断数据](./diagnostics-extension-to-storage.md?preserve-view=)

**责任**：客户

Azure 安全中心监视：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：可以通过与 Azure Sentinel 集成或使用第三方 SIEM 来监视 Azure 云服务日志数据，方法是针对异常活动启用警报。

- [与 SIEM 集成](../security-center/continuous-export.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：适用于 Azure 的 Microsoft Antimalware 可保护 Azure 云服务和虚拟机。 此外，你可以选择部署第三方安全解决方案，例如 Web 应用程序防火墙、网络防火墙、反恶意软件、入侵检测和防护系统 (IDS 或 IPS)，等等。

- [Azure 基本 IPS/IDS 和 DDOS 提供哪些特性和功能](./cloud-services-configuration-and-management-faq.yml#what-are-the-features-and-capabilities-that-azure-basic-ips-ids-and-ddos-provides-)

**责任**：客户

Azure 安全中心监视：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Microsoft 建议使用 Azure 基于角色的访问控制 (Azure RBAC) 来管理对 Azure 资源的访问。 但是，Azure 云服务不支持 Azure RBAC 模型，因为它不是基于 Azure 资源管理器的服务，你必须使用经典订阅

默认情况下，帐户管理员、服务管理员和共同管理员是 Azure 中的三种经典订阅管理员角色。 

经典订阅管理员对 Azure 订阅拥有完全访问权限。 他们可以使用 Azure 门户、Azure 资源管理器 API 和经典部署模型 API 来管理资源。 用于注册 Azure 的帐户会自动同时设置为帐户管理员和服务管理员。 以后，可以添加其他共同管理员。 

服务管理员和共同管理员拥有在订阅范围内分配有“所有者”角色（一个 Azure 角色）的用户的等效访问权限。 在 Azure 门户中，可以使用“经典管理员”选项卡管理共同管理员或查看服务管理员。 

可以使用 PowerShell 通过以下命令列出经典服务管理员和共同管理员的角色分配：

Get-AzRoleAssignment -IncludeClassicAdministrators

查看经典订阅管理角色之间的差异。 

- [三个经典订阅管理角色之间的差异](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**责任**：客户

Azure 安全中心监视：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：建议根据可用角色以及操作和管理 Azure 云服务资源所需的权限，围绕专用管理帐户的使用创建标准操作规程。

- [经典订阅管理角色之间的差异](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**责任**：客户

Azure 安全中心监视：无

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

指南：避免为在 Azure 云服务上运行的应用程序管理单独的标识。 实现单一登录，以避免要求用户管理多个标识和凭据。

- [什么是单一登录 (SSO)？](../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：对于需要提升的权限的管理任务，建议使用安全的 Azure 托管工作站（也称为特权访问工作站）。

- [了解安全的 Azure 托管工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何启用 Azure Active Directory (Azure AD) 多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用 Azure 云服务 REST API 列出包含敏感信息的 Azure 云服务资源的清单。 轮询所部署的云服务资源以获取配置和 .pkg 资源。

 例如，下面列出了几个 API：

- 获取部署 -“获取部署”操作返回某个部署的配置信息、状态和系统属性。
- 获取包 -“获取包”操作检索某个部署的云服务包，并将包文件存储在 Microsoft Azure Blob 存储中
- 获取云服务属性 -“获取云服务属性”操作检索指定的云服务的属性

查看 Azure 云服务 REST API 文档，并根据组织的要求，创建用于对敏感信息进行数据保护的流程。

- [获取部署](/rest/api/compute/cloudservices/rest-get-deployment)

- [获取云服务属性](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [获取包](/rest/api/compute/cloudservices/rest-get-package)

**责任**：客户

Azure 安全中心监视：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：使用单独的订阅和管理组对各个安全域（例如，Azure 云服务的环境类型和数据敏感度级别）实施隔离。

你还可以在 Azure 云服务的 Certificate 元素中编辑“permissionLevel”，以指定赋予角色进程的访问权限。 如果只希望提升的进程能够访问私钥，则指定提升的权限。 limitedOrElevated 权限允许所有角色进程访问私钥。 可能的值为 limitedOrElevated 或 elevated。 默认值为 limitedOrElevated。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [WebRole 架构](./schema-csdef-webrole.md#Certificate)

**责任**：客户

Azure 安全中心监视：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：建议在网络外围中使用 Azure 市场提供的第三方解决方案，以便监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

Azure 安全中心监视：无

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：为 Azure 云服务配置 TLS v2。 使用 Azure 门户将证书添加到暂存的 Azure 云服务部署，并将证书信息添加到服务的 CSDEF 和 CSCFG 文件中。 重新打包应用程序，并更新暂存的部署以使用新包。 

使用 Azure 中附加到 Azure 云服务的服务证书来确保与服务的安全通信。 提供可对公开的 HTTPS 终结点进行身份验证的证书。 在云服务的服务定义中定义服务证书，并自动将其部署到运行你的角色实例的虚拟机。

使用管理证书通过管理 API 进行身份验证。管理证书允许你通过经典部署模型进行身份验证。 许多程序和工具（如 Visual Studio 或 Azure SDK）会使用这些证书来自动配置和部署各种 Azure 服务。 

经典部署模型 API 允许以编程方式访问通过 Azure 门户提供的经典部署模型功能，以获得更多参考信息。 可以使用 Azure SDK for Python 管理 Azure 云服务和 Azure 存储帐户。 Azure SDK for Python 包装了经典部署模型 API（一个 REST API）。 所有 API 操作都通过 TLS 执行，并使用 X.509 v3 证书互相进行身份验证。 可以从在 Azure 中运行的服务内访问管理服务。 还可以直接通过 Internet 从可发送 HTTPS 请求和接收 HTTPS 响应的任意应用程序访问管理服务。

- [为 Azure 中的应用程序配置 TLS](cloud-services-configure-ssl-certificate-portal.md)

- [从 Python 中使用经典部署模型](cloud-services-python-how-to-use-service-management.md)

**责任**：共享

Azure 安全中心监视：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：建议使用第三方主动发现工具来确定组织的技术系统（包括现场或远程服务提供商处的技术系统）存储、处理或传输的所有敏感信息，然后更新组织的敏感信息清单。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

Azure 安全中心监视：无

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：不适用于云服务（经典）。 它不强制实施数据丢失防护。

建议实施第三方工具（如基于主机的数据丢失自动防护解决方案），以便对数据强制实施访问控制，即使数据是从系统复制的。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据始终安全，Microsoft 实施并维护了一套可靠的数据保护控制机制和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

Azure 安全中心监视：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：Azure 云服务不支持静态加密。 这是因为 Azure 云服务设计为无状态的服务。 Azure 云服务支持外部存储，例如，默认情况下静态加密的 Azure 存储。  

存储在临时磁盘中的应用程序数据不会加密。 客户负责根据需要管理和加密这些数据。  

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：可以使用 Azure Monitor 中的经典指标警报，在应用于关键资源的指标之一超出阈值时获得通知。 经典指标警报是一个较旧的功能，它仅可针对非维度指标发出警报。 有一个称为“指标警报”的较新功能，它具有基于经典指标警报改进的功能。 

另外，Application Insights 可以监视 Azure 云服务应用的可用性、性能、故障和使用情况。 这将结合使用来自 Application Insights SDK 的数据与来自 Azure 云服务的 Azure 诊断数据。

- [使用 Azure Monitor 创建、查看和管理经典指标警报](../azure-monitor/alerts/alerts-classic-portal.md)

- [指标警报概述](../azure-monitor/alerts/alerts-metric-overview.md) 

- [适用于 Azure 云服务（经典）的 Application Insights](../azure-monitor/app/cloudservices.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：请注意，此信息与 Azure 云服务辅助角色的 Azure 来宾操作系统以及使用平台即服务 (PaaS) 的 Web 角色相关。 但是，它不适用于采用基础结构即服务 (IaaS) 的虚拟机。

默认情况下，Azure 会定期将客户的来宾操作系统更新为操作系统系列（在服务配置 (.cscfg) 中指定）中受支持的最新映像，例如 Windows Server 2016。

当客户为其 Azure 云服务部署选择特定的操作系统版本时，它会禁用操作系统自动更新，并负责进行修补。 客户必须确保其角色实例可以接收更新，否则其应用程序可能会出现安全漏洞。

- [Azure 来宾 OS](cloud-services-guestos-msrc-releases.md)

- [Azure 来宾 OS 可支持性和停用策略](cloud-services-guestos-retirement-policy.md)

- [如何配置云服务（经典）](cloud-services-how-to-configure-portal.md)

- [管理来宾 OS 版本](./cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**责任**：共享

Azure 安全中心监视：无

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动化补丁管理解决方案

**指导**：使用第三方修补程序管理解决方案。 已在其环境中使用 Configuration Manager 的客户还可以使用 System Center Updates Publisher，以便将自定义更新发布到 Windows Server 更新服务中。 

这样更新管理就可以通过第三方软件来修补使用 Configuration Manager 作为其更新存储库的计算机。

**责任**：客户

Azure 安全中心监视：无

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：建议客户持续了解 DDoS 攻击的风险范围。 

建议全面考虑以下情况：

- 哪些新公开发布的 Azure 资源需要保护？
- 服务中是否存在单一故障点？
- 如何隔离服务，以限制某项攻击造成的影响，同时使服务仍可供合法客户使用？
- 是否有虚拟网络应启用标准 DDoS 防护，但却没有启用？
- 我的服务在跨多个区域故障转移后是否保持主动/主动状态？

支持性文档：

- [针对 Azure 资源的风险评估](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**责任**：客户

Azure 安全中心监视：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：不适用于 Azure 云服务。 此建议适用于 IaaS 计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：建议定期核对清单，确保及时地从订阅中删除未经授权的资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指导**：客户应当为计算资源定义批准的 Azure 资源和批准的软件。

**责任**：客户

Azure 安全中心监视：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure 安全中心提供的自适应应用程序控制功能。 它是安全中心提供的一种智能的、自动化的端到端解决方案，有助于控制可在 Windows 和 Linux、Azure 和非 Azure 计算机上运行的应用程序。 它还有助于增强计算机抵御恶意软件的能力。 

此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

安全中心使用机器学习来分析计算机上运行的应用程序，并根据此智能创建允许列表。 此功能大大简化了配置和维护应用程序允许列表策略的过程，让你可以：
- 阻止运行恶意应用程序的尝试（包括在其他情况下可能会被反恶意软件解决方案遗漏的尝试）或者向用户发出此方面的警报。

- 遵循组织要求只能使用许可软件的安全策略。
- 避免在环境中使用不需要的软件。
- 避免运行旧的不受支持的应用。
- 防止使用组织不允许的特定软件工具。
- 允许 IT 部门控制用户使用应用来访问敏感数据。

可通过以下参考链接获得更多详细信息。

- [自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：使用 Azure 安全中心提供的自适应应用程序控制功能。 它是安全中心提供的一种智能的、自动化的端到端解决方案，有助于控制可在 Windows 和 Linux、Azure 和非 Azure 计算机上运行的应用程序。 它还有助于增强计算机抵御恶意软件的能力。 

此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

安全中心使用机器学习来分析计算机上运行的应用程序，并根据此智能创建允许列表。 此功能大大简化了配置和维护应用程序允许列表策略的过程，让你可以：

- 阻止运行恶意应用程序的尝试（包括在其他情况下可能会被反恶意软件解决方案遗漏的尝试）或者向用户发出此方面的警报。

- 遵循组织要求只能使用许可软件的安全策略。

- 避免在环境中使用不需要的软件。

- 避免运行旧的不受支持的应用。

- 防止使用组织不允许的特定软件工具。

- 允许 IT 部门控制用户使用应用来访问敏感数据。

可通过以下参考链接获得更多详细信息。

- [自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：使用 Azure 安全中心提供的自适应应用程序控制功能。 它是安全中心提供的一种智能的、自动化的端到端解决方案，有助于控制可在 Windows 和 Linux、Azure 和非 Azure 计算机上运行的应用程序。 它还有助于增强计算机抵御恶意软件的能力。 

此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

安全中心使用机器学习来分析计算机上运行的应用程序，并根据此智能创建允许列表。 此功能大大简化了配置和维护应用程序允许列表策略的过程，让你可以：

- 阻止运行恶意应用程序的尝试（包括在其他情况下可能会被反恶意软件解决方案遗漏的尝试）或者向用户发出此方面的警报。

- 遵循组织要求只能使用许可软件的安全策略。

- 避免在环境中使用不需要的软件。

- 避免运行旧的不受支持的应用。

- 防止使用组织不允许的特定软件工具。

- 允许 IT 部门控制用户使用应用来访问敏感数据。

可通过以下参考链接获得更多详细信息。

- [自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：使用 Azure 安全中心提供的自适应应用程序控制功能。 它是安全中心提供的一种智能的、自动化的端到端解决方案，有助于控制可在 Windows 和 Linux、Azure 和非 Azure 计算机上运行的应用程序。 它还有助于增强计算机抵御恶意软件的能力。 

此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

安全中心使用机器学习来分析计算机上运行的应用程序，并根据此智能创建允许列表。 此功能大大简化了配置和维护应用程序允许列表策略的过程，让你可以：

- 阻止运行恶意应用程序的尝试（包括在其他情况下可能会被反恶意软件解决方案遗漏的尝试）或者向用户发出此方面的警报。

- 遵循组织要求只能使用许可软件的安全策略。

- 避免在环境中使用不需要的软件。

- 避免运行旧的不受支持的应用。

- 防止使用组织不允许的特定软件工具。

- 允许 IT 部门控制用户使用应用来访问敏感数据。

可通过以下参考链接获得更多详细信息。

- [自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：使用 Azure 安全中心提供的自适应应用程序控制功能。 它是安全中心提供的一种智能的、自动化的端到端解决方案，有助于控制可在 Windows 和 Linux、Azure 和非 Azure 计算机上运行的应用程序。 它还有助于增强计算机抵御恶意软件的能力。 

此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

安全中心使用机器学习来分析计算机上运行的应用程序，并根据此智能创建允许列表。 此功能大大简化了配置和维护应用程序允许列表策略的过程，让你可以：
- 阻止运行恶意应用程序的尝试（包括在其他情况下可能会被反恶意软件解决方案遗漏的尝试）或者向用户发出此方面的警报。

- 遵循组织要求只能使用许可软件的安全策略。
- 避免在环境中使用不需要的软件。
- 避免运行旧的不受支持的应用。
- 防止使用组织不允许的特定软件工具。
- 允许 IT 部门控制用户使用应用来访问敏感数据。

可通过以下参考链接获得更多详细信息。

- [自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指导**：使用 Azure 安全中心提供的自适应应用程序控制功能。 它是安全中心提供的一种智能的、自动化的端到端解决方案，有助于控制可在 Windows 和 Linux、Azure 和非 Azure 计算机上运行的应用程序。 它还有助于增强计算机抵御恶意软件的能力。 

此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

安全中心使用机器学习来分析计算机上运行的应用程序，并根据此智能创建允许列表。 此功能大大简化了配置和维护应用程序允许列表策略的过程，让你可以：

- 阻止运行恶意应用程序的尝试（包括在其他情况下可能会被反恶意软件解决方案遗漏的尝试）或者向用户发出此方面的警报。

- 遵循组织要求只能使用许可软件的安全策略。

- 避免在环境中使用不需要的软件。

- 避免运行旧的不受支持的应用。

- 防止使用组织不允许的特定软件工具。

- 允许 IT 部门控制用户使用应用来访问敏感数据。

可通过以下参考链接获得更多详细信息。

- [自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：对于包含 Azure 云服务的敏感或高风险应用程序，请实现单独的订阅或管理组以提供隔离。

使用网络安全组，创建入站安全规则，选择一个服务（如 http），选择一个自定义端口，并为其指定优先级和名称。 优先级会影响应用规则的顺序：数值越小，越先应用规则。 你需要将网络安全组关联到某个子网或特定网络接口，以根据业务需求对网络流量进行隔离或分段。

可通过以下参考链接获得更多详细信息。

- [教程 - 在 Azure 门户中使用网络安全组筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用来自 Azure 安全中心的建议作为 Azure 云服务资源的安全配置基线。 

在 Azure 门户上，依次选择“安全中心”、“计算 &amp; 应用”和“Azure 云服务”，查看适用于你的服务资源的建议。

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：不适用于 Azure 云服务。 它基于经典部署模型。 建议使用第三方解决方案来维护安全的 Azure 资源配置

**责任**：客户

Azure 安全中心监视：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：Azure 云服务的配置文件存储资源的操作属性。 你可以将配置文件的副本存储到安全的存储帐户。

**责任**：客户

Azure 安全中心监视：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：不适用于 Azure 云服务。 它基于经典部署模型，无法通过基于 Azure 资源管理器部署的配置工具进行管理。

**责任**：客户

Azure 安全中心监视：无

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用于 Azure 云服务。 此建议适用于基于基础结构即服务 (IaaS) 的计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用 Azure 安全中心对 Azure 资源执行基线扫描。  

- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：在 Azure 安全中心内，选择“计算 &amp; 应用”功能，并根据针对虚拟机、服务器和容器的建议进行操作。

- [了解 Azure 安全中心容器建议](../security-center/container-security.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：Azure 云服务基于经典部署模型，不与 Azure Key Vault 集成。

你可以保护 Azure 云服务中使用的机密（例如凭据），以避免每次都键入密码。 首先指定纯文本密码，并使用 PowerShell 命令 ConvertTo-SecureString 将其转换为安全字符串。 接下来，使用 ConvertFrom-SecureString 将此安全字符串转换为加密的标准字符串。  现在，可以使用 Set-Content 将此加密的标准字符串保存到文件。

此外，建议将 Azure 云服务中使用的证书的私钥存储到受保护的存储中。

- [从 PowerShell 配置远程桌面](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**责任**：客户

Azure 安全中心监视：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指导**：保护 Azure 云服务中使用的机密（例如凭据），以避免每次都键入密码。 
 

首先指定纯文本密码，并使用 PowerShell 命令 ConvertTo-SecureString 将其更改为安全字符串。 接下来，使用 ConvertFrom-SecureString 将此安全字符串转换为加密的标准字符串。 现在，使用 Set-Content 命令将此加密的标准字符串保存到文件。

将 Azure 云服务中使用的证书的私钥存储到受保护的存储位置。

- [从 PowerShell 配置远程桌面](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**责任**：客户

Azure 安全中心监视：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：适用于 Azure 的 Microsoft Antimalware 可用于 Azure 云服务和虚拟机。 它是一种免费实时保护，可帮助识别并删除病毒、间谍软件和其他恶意软件。 当已知恶意软件或不需要的软件试图在 Azure 系统上安装自己或运行时，该服务会生成警报。 

使用基于 PowerShell 的 Antimalware cmdlet 以及“Get-AzureServiceAntimalwareConfig”来获取 Antimalware 配置。

在 Azure 云服务的启动任务中使用 PowerShell 脚本启用 Antimalware 扩展。

选择 Azure 安全中心内的自适应应用程序控制功能，这是一个智能的自动化端到端解决方案。 它有助于增强计算机抵御恶意软件的能力，让你能够阻止运行恶意应用程序的尝试（包括在其他情况下可能会被反恶意软件解决方案遗漏的尝试）或者向用户发出此方面的警报。

- [如何以自动化方式为 Azure 云服务添加 Antimalware 扩展](./cloud-services-configuration-and-management-faq.yml#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way-)

- [反恶意软件部署方案](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**责任**：客户

Azure 安全中心监视：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。 

清楚地标记订阅（例如生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**责任**：客户

Azure 安全中心监视：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。 

- [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**责任**：客户

Azure 安全中心监视：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 你可以使用安全中心数据连接器将警报流式传输到 Azure Sentinel。 

- [如何配置连续导出](../security-center/continuous-export.md) 

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

Azure 安全中心监视：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)
