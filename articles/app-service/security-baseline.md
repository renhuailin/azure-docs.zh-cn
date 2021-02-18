---
title: 适用于应用服务的 Azure 安全基准
description: 应用服务安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d7753de473d90c45fecb20403352a594ef52dafe
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586299"
---
# <a name="azure-security-baseline-for-app-service"></a>适用于应用服务的 Azure 安全基准

适用于应用服务的 Azure 安全基准包含有助于改进部署安全状况的建议。 此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。 内容由 Azure 安全基准定义的 **安全控制** 和适用于应用服务的相关指南进行分组。 排除了不适用于应用服务的 **控件**。

若要查看应用服务如何完全映射到 Azure 安全基准，请参阅 [完整的应用服务安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：使用隔离定价层中的应用服务时，也称为应用服务环境 (ASE) ，可以直接将其部署到 Azure 虚拟网络中的子网中。 使用网络安全组来保护你的 Azure 应用服务环境，方法是阻止入站和出站流量到你的虚拟网络中的资源，或限制对应用服务环境中的应用的访问。

默认情况下，网络安全组以最低优先级包含隐式拒绝规则，这要求你显式添加允许规则。 根据最小特权网络方法为网络安全组添加允许规则。 用于承载应用服务环境的底层虚拟机不能直接访问，因为它们在 Microsoft 管理的订阅中。

通过 Web 应用程序防火墙 (WAF) 启用 Azure 应用程序网关来路由流量，从而保护应用服务环境。 将服务终结点与应用程序网关结合使用，以保护应用程序的入站发布流量。  

在多租户应用服务中 () 不在隔离层中的应用，请使用网络安全组来阻止应用的出站流量。 通过虚拟网络集成功能，使你的应用能够通过虚拟网络访问中的资源。 此功能还可用于阻止从应用发送到公共地址的出站流量。 虚拟网络集成无法用于提供对应用程序的入站访问。  

通过以下方式保护应用程序的入站流量：
- 访问限制-一系列控制入站访问的允许或拒绝规则
- 服务终结点-可以拒绝来自指定虚拟网络或子网外部的入站流量
- 专用终结点-使用专用 IP 地址将应用公开到虚拟网络。 在应用上启用专用终结点后，它将无法再访问 internet

在同一区域中对虚拟网络使用虚拟网络集成功能时，请使用网络安全组和路由表和用户定义的路由。 可以将用户定义的路由置于集成子网中，以按预期发送出站流量。  

请考虑实现 Azure 防火墙，以便跨订阅和虚拟网络集中创建、强制和记录应用程序和网络连接策略。 Azure 防火墙对虚拟网络资源使用静态公共 IP 地址，从而允许外部防火墙识别源自虚拟网络的流量。 

- [锁定应用服务环境](environment/firewall-integration.md)

- [打开 Web 应用程序安全项目 (OWASP) 十大漏洞防护](https://owasp.org/www-project-top-ten/)

- [网络安全组](../virtual-network/network-security-groups-overview.md)

- [将应用与 Azure 虚拟网络集成](web-sites-integrate-with-vnet.md)

- [应用服务环境的网络注意事项](environment/network-info.md)

- [如何创建外部 ASE](environment/create-external-ase.md)

- [如何创建内部 ASE](environment/create-ilb-ase.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指南**：从 Azure 安全中心实施网络保护建议，以确保与应用服务应用和 api 相关的网络资源和配置。

使用 Azure 防火墙发送流量，并跨订阅和虚拟网络集中创建、强制和记录应用程序和网络连接策略。 Azure 防火墙对虚拟网络资源使用静态公共 IP 地址，从而允许外部防火墙识别源自虚拟网络的流量。 Azure 防火墙服务还与用于日志记录和分析的 Azure Monitor 完全集成。

- [Azure 防火墙概述](../firewall/overview.md)

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

- [如何启用应用服务的监视和保护](../security-center/defender-for-app-service-introduction.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：通过以下方式保护应用服务环境 () ASE 中的可访问 internet 的应用：
- 在面向 internet 的应用程序前面使用 Azure 应用程序网关部署 Web 应用程序防火墙 (WAF) 
- 使用访问限制来保护应用程序网关的入站流量
- Azure Active Directory (Azure AD) 保护应用，确保身份验证
- 将最低 TLS 版本设置为1。2
- 仅将应用设置为 HTTPS

通过 Azure 防火墙设备对所有应用程序流量进行出站驱动，并监视这些日志。 

若要保护多租户应用服务中可访问 internet 的应用，请 (例如，而不是隔离层) 
- 在应用程序的前面部署启用了防火墙的 Web 应用程序防火墙
- 使用访问限制或服务终结点来保护 Web 应用程序防火墙 (WAF) 设备的入站流量
- 通过 Azure AD 保护应用以确保身份验证
- 将最低 TLS 版本设置为1。2
- 仅将应用设置为 HTTPS
- 使用 "虚拟网络集成" 和 "应用设置" WEBSITE_VIRTUAL NETWORK_ROUTE_ALL，以使受网络安全组和用户定义的路由的所有出站流量在集成子网上。

类似于应用程序服务环境应用，通过 Azure 防火墙设备驱动所有应用程序流量，并监视应用程序中的日志。

此外，请查看并遵循锁定应用服务环境文档中的建议。

- [锁定应用服务环境](environment/firewall-integration.md)

- [Azure 应用程序网关上的 Azure Web 应用程序防火墙](../web-application-firewall/ag/ag-overview.md)

- [Azure 应用服务访问限制](app-service-ip-restrictions.md)

- [跟踪 WAF 警报并通过 Azure Monitor 轻松监视趋势 ](../azure-monitor/overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：按照锁定应用服务环境文档中所述，保护应用服务环境。 应用 Azure 安全中心的集成威胁情报功能，拒绝与已知恶意或未使用的公共 IP 地址进行通信。 使用访问限制来保护应用程序网关的入站流量。 

将多租户应用服务安全 (不在隔离层) 中的应用，并提供面向公共 internet 的终结点。 它只允许来自虚拟网络中特定子网的流量，并阻止其他所有内容。 使用访问限制配置网络访问控制列表 (IP 限制) 锁定允许的入站流量。

定义按序允许或拒绝列表的优先级，以管理对应用的网络访问。 此列表可以包含 IP 地址或虚拟网络子网。 当列表的末尾有一个或多个项时，存在一个隐式 "拒绝全部" 规则。 此功能适用于所有应用服务托管的工作负载，包括 Web 应用、API 应用、Linux 应用、Linux 容器应用和功能。 

使用服务终结点限制对 Azure 虚拟网络中的 web 应用的访问。 使用服务终结点从所选子网中，将对多租户应用服务的访问限制 (不在隔离层中的应用) 。 

- [Azure App Service 静态 IP 限制](app-service-ip-restrictions.md)

- [Azure 应用程序网关上的 Azure Web 应用程序防火墙](../web-application-firewall/ag/ag-overview.md)

- [如何配置 Web 应用程序防火墙 (WAF) 用于应用服务环境](environment/app-service-app-service-environment-web-application-firewall.md)

- [如锁定应用服务环境中所述，保护 ASE](environment/firewall-integration.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：监视通过安全中心发送到应用服务应用的请求和响应。 可以通过使用具有 Web 应用程序防火墙的实时应用程序网关来监视对 web 应用程序的攻击，通过 Azure Monitor 的集成日志记录功能来跟踪 Web 应用程序防火墙警报并轻松监视趋势。

- [Azure 应用程序网关上的 Azure Web 应用程序防火墙](../web-application-firewall/ag/ag-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：管理应用服务环境中的应用的流量：

- 按照锁定应用服务环境中的说明保护应用服务环境
- 在面向 internet 的应用程序前面部署具有 Azure Web 应用程序防火墙的应用程序网关
- 将应用设置为仅可通过 HTTPS 访问

在多租户应用服务中管理可访问 internet 的应用的流量， (不在隔离层) ： 

- 部署在面向 internet 的应用前面启用了 Azure Web 应用程序防火墙的应用程序网关
- 使用访问限制或服务终结点来保护 Web 应用程序防火墙的入站流量。 访问限制功能适用于所有应用服务托管的工作负载，包括 Web 应用、API 应用、Linux 应用、Linux 容器应用和功能。

- 将应用设置为只能通过 HTTPS 访问
- 使用静态 IP 限制来限制对应用服务应用的访问权限，以便它仅接收来自应用程序网关上的 VIP 的流量，作为唯一具有访问权限的地址。

查看引用的链接以获取其他信息。

- [Azure App Service 静态 IP 限制](app-service-ip-restrictions.md)

- [Azure 应用程序网关上的 Azure Web 应用程序防火墙](../web-application-firewall/ag/ag-overview.md)

- [如何通过门户使用应用程序网关配置端到端 TLS](../application-gateway/end-to-end-ssl-portal.md)

- [如锁定应用服务中所述，保护 ASE](./environment/firewall-integration.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：应用服务有多个用于管理服务的终结点。 这些终结点地址也包含在 AppServiceManagement IP 服务标记中。 AppServiceManagement 标记仅与应用服务环境一起使用，以允许此类流量。 

您可以通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称来允许或拒绝相应服务的流量。 在 AppService IP 服务标记中跟踪应用服务的入站地址。 没有任何 IP 服务标记包含应用服务使用的出站地址。

Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [虚拟网络服务标记](../virtual-network/service-tags-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与应用服务应用相关的网络设置的标准安全配置。 

使用 "Microsoft" 和 "Microsoft" 命名空间中的 Azure 策略别名维护安全配置。 创建自定义策略，以便审核或强制实施应用服务应用的网络配置。 

使用应用服务的内置策略定义，例如：
- 应用应使用虚拟网络服务终结点
- 应用只能通过 HTTPS 访问
- 将最低 TLS 版本设置为最新版本

查看引用的链接以获取其他信息。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何通过门户使用应用程序网关配置端到端 TLS](../application-gateway/end-to-end-ssl-portal.md)

- [如锁定应用服务中所述，保护 ASE](./environment/firewall-integration.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对网络安全组和其他相关资源使用标记，包括应用服务中的流量。

指定 "业务需求"、"持续时间" 等，其中包含任何规则的 "描述" 字段，这些规则允许来自网络的流量或来自网络的流量用于单个网络安全组规则。

应用与标记效果相关的任何内置 Azure 策略定义（如 "需要标记和值"），以确保使用标记创建所有资源并通知所有现有未标记资源。 使用 Azure PowerShell 或 Azure CLI，基于资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [Azure 应用服务访问限制](./app-service-ip-restrictions.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测对网络设置以及与应用服务相关的任何资源所做的更改。 

应用应用服务的多个 Azure 策略内置定义之一，例如审核应用以使用虚拟网络终结点服务的策略。 在 Azure Monitor 中创建警报，以便在对关键网络设置或资源进行更改时触发。 

在安全中心、门户或通过编程工具查看详细的安全警报和建议。 导出此信息，或将其发送到你的环境中的其他监视工具。 这些工具可用于以手动方式或以持续的方式导出警报和建议。 借助这些工具，你可以：
 
- 持续导出到 Log Analytics 工作区
- 将内容连续导出到 Azure 事件中心（适用于与第三方 SIEM 的集成）
- 导出到 CSV 文件 (一次) 

建议使用自动工具创建一个过程来监视网络资源配置并快速检测更改。

- [如何查看和检索 Azure 活动日志事件](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/alerts/alerts-activity-log.md)

- [导出安全警报和建议](../security-center/continuous-export.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：将应用服务环境 (ASE) 与 Azure Monitor 集成，以便将日志发送到 azure 存储、Azure 事件中心或 Log Analytics。 启用控制平面审核日志记录的 Azure 活动日志诊断设置。 安全中心的安全警报发布到 Azure 活动日志。 审核 Azure 活动日志数据，这让你可以确定在 Azure App Service 和其他 Azure 资源的控制平面级别执行任何写入操作的 "操作内容、操作人员和操作时间")  (。 保存查询以供将来使用，将查询结果固定到 Azure 仪表板，并创建日志警报。 此外，还可以使用 Application Insights 中的数据访问 REST API 以编程方式访问遥测数据。

根据你的业务需求，使用 Microsoft Azure Sentinel，这是一个可缩放的云本机安全信息事件管理 (SIEM) 可用于连接到各种数据源和连接器。 你还可以在 SIEM) 系统（如 Azure Marketplace 中的 Barracuda）上启用和数据到第三方安全信息事件管理 (。

- [记录 ASE 活动](environment/using-an-ase.md#logging)

- [如何启用 Azure App Service 的诊断设置](troubleshoot-diagnostic-logs.md)

- [如何启用 Application Insights](../azure-monitor/app/app-insights-overview.md)

- [从 Application Insights 导出遥测数据](../azure-monitor/app/export-telemetry.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：启用应用服务的控制平面审核日志记录的 Azure 活动日志诊断设置。 将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户。
可以使用应用服务和其他 Azure 资源的 Azure 活动日志数据，确定任何写入操作 (PUT、POST、DELETE) 在控制平面级别执行的 "操作内容、操作人员和操作时间"。

此外，Azure Key Vault 通过访问策略和审核历史记录提供集中式密钥管理。 

- [如何启用 Azure 活动日志的诊断设置](../azure-monitor/essentials/activity-log.md)

- [如何启用 Azure App Service 的诊断设置](troubleshoot-diagnostic-logs.md)

- [资源管理器操作](../role-based-access-control/resource-provider-operations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的符合性规定，设置与应用服务资源关联的 Log Analytics 工作区的日志保持期。
- [如何设置日志保留参数](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：在应用服务资源中查看 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，以搜索字词、识别趋势、分析模式，并根据收集的数据提供许多其他见解。

使用应用服务应用的 Application Insights，并收集日志、性能和错误数据。 查看 Azure 门户中 Application Insights 收集的遥测数据。

如果已部署 Web 应用程序防火墙 (WAF) ，则可以使用实时 Web 应用程序防火墙日志监视对应用服务应用的攻击。 日志与 Azure Monitor 集成，以跟踪 Web 应用程序防火墙警报并轻松监视趋势。

根据要求，使用 Azure Sentinel （一种可缩放的云和本机安全信息事件管理） (SIEM) ，与各种数据源和连接器集成。 在 Azure Marketplace 中，可以选择启用和将数据到第三方安全信息事件管理解决方案。

- [如何启用 Azure 活动日志的诊断设置](../azure-monitor/essentials/activity-log.md)

- [如何启用 Application Insights](../azure-monitor/app/app-insights-overview.md)

- [如何将应用服务环境与 Azure 应用程序关 (WAF) ](environment/integrate-with-application-gateway.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：在 Azure 订阅中配置安全中心，并查看生成的警报。 使用 Azure Monitor 将活动日志数据获取到事件中心，该中心可供安全信息事件管理 (SIEM) 解决方案（例如 Azure Sentinel）读取。 

通过使用已部署的 Azure Web 应用程序防火墙 (WAF) 的实时 Web 应用程序防火墙日志监视对应用服务应用的攻击。 该日志与 Azure Monitor 集成，以跟踪 Web 应用程序防火墙 (WAF) 警报并轻松监视趋势。

- [如何将应用服务环境与 Azure 应用程序关 (WAF) ](environment/integrate-with-application-gateway.md)

- [导出安全警报和建议](../security-center/continuous-export.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： Azure Active Directory (Azure AD) 的内置角色必须明确分配并可查询。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [如何使用应用服务和 Azure Functions 的托管标识](overview-managed-identity.md?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [使用 Azure 门户添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure Active Directory (Azure AD) 没有默认密码的概念。 它为应用服务提供控制平面访问权限。

通常，在构建自己的应用时，应避免实现用户访问的默认密码。 使用应用服务默认可用的标识提供程序之一，例如 Azure AD、Microsoft 帐户、Facebook、Google 或 Twitter。

禁用匿名访问，除非你需要对其进行支持。 

- [默认情况下，Azure App Service 提供标识提供者](overview-authentication-authorization.md#identity-providers)

- [Azure 应用服务和 Azure Functions 中的身份验证和授权](overview-authentication-authorization.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用安全中心的标识和访问管理功能来监视和跟踪管理帐户的数量。 

使用安全中心或内置 Azure 策略中的建议，例如：

- 应该有多个所有者分配给你的订阅。 
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

创建一个进程来监视网络资源配置，并检测对管理帐户所做的更改。

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解有关向用户授予应用程序访问权限的详细信息](../role-based-access-control/overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指南**：通过 (Azure AD) Azure Active Directory 验证应用服务。 它为标识提供者提供 OAuth 2.0 服务，并允许对移动和 web 应用程序进行授权访问。 

应用服务应用使用联合标识，其中第三方标识提供者为你管理用户标识和身份验证流。 默认情况下，可以使用以下标识提供程序：

- Azure AD
- Microsoft 帐户

- Facebook

- Google

- Twitter

当你使用其中一个提供程序启用身份验证和授权时，它的登录终结点可用于用户身份验证，并可用于验证来自提供程序的身份验证令牌。

- [了解 Azure App Service 中的身份验证和授权](overview-authentication-authorization.md#identity-providers)

- [了解 Azure App Service 中的身份验证和授权](overview-authentication-authorization.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：在 Azure Active Directory () Azure AD 中启用多重身份验证功能，并遵循安全中心的标识和访问管理建议。

为 Azure AD 实施多重身份验证。 管理员需要确保门户中的订阅帐户受到保护。 订阅容易受到攻击，因为它管理你创建的资源。 

- [Azure 安全 MFA](/previous-versions/azure/security/develop/secure-aad-app)

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指南**：通过配置为登录和配置 Azure 资源的多重身份验证，使用特权访问工作站 (PAW) 。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：在环境中发生可疑或不安全活动时，使用 PRIVILEGED IDENTITY MANAGEMENT (PIM) Azure Active Directory (Azure AD) 生成日志和警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

安全中心的威胁防护为你的环境提供了综合防御，其中包括对 Azure 计算资源（例如 Windows 计算机、Linux 计算机、应用服务和 Azure 容器）的威胁保护。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 风险检测](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 计算资源的威胁防护](../security-center/azure-defender.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，以仅允许从 IP 地址范围、国家或地区的特定逻辑分组访问 Azure 门户。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 Azure Active Directory (Azure AD) 作为应用服务应用的中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密以及加盐、哈希处理和安全存储用户凭据来保护数据。

- [如何将 Azure App Service 应用配置为使用 Azure AD 登录](configure-authentication-provider-aad.md)

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：使用 Azure Active Directory (Azure AD) 中的日志发现过期帐户。 使用 Azure 标识访问评审有效地管理组成员身份和对企业应用程序的访问权限，以及角色分配。 定期查看用户访问权限，以确保只有预期的用户才能继续访问。 

- [了解 Azure AD 报告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：使用 Azure Active Directory (Azure AD) 作为应用服务应用的中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中的数据使用强加密、salts、哈希，并安全地存储用户凭据来保护数据。

Azure AD 登录活动、审核和风险事件日志源的访问权限，可用于与 Azure Sentinel 或第三方安全信息事件管理 (SIEM) 解决方案集成。 通过创建 Azure AD 用户帐户的诊断设置，并将审核和登录日志发送到 Log Analytics 工作区，简化此过程。 可在 Log Analytics 中配置所需的日志警报。

- [如何将 Azure App Service 应用配置为使用 Azure AD 登录](configure-authentication-provider-aad.md)

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory (Azure AD) 作为应用服务应用的中央身份验证和授权系统。 

使用 Azure AD Identity Protection 配置自动响应，以检测与用户标识相关的可疑操作，如使用 Azure 门户控制平面上的帐户登录行为偏差。 还可将数据引入 Azure Sentinel 以做进一步调查。 

- [如何将 Azure App Service 应用配置为使用 Azure AD 登录](configure-authentication-provider-aad.md)

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：不适用于应用服务。 Azure App Service 不支持客户密码箱。

- [支持客户密码箱的服务列表](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记帮助跟踪存储或处理敏感信息的应用服务资源。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：对于应用服务环境，可为开发、测试和生产环境实施单独的订阅和/或管理组。 可以采用相同的方式将处理来自其他应用的敏感信息的应用隔离开来。 将应用服务应用部署到虚拟网络。 使用网络安全组和子网进一步实现应用程序隔离。 

 (ASE) 的应用服务环境有两种部署类型。 这两种方法都可让你根据业务需求隔离流量。

- 外部应用程序服务环境-在可通过 internet 访问的 IP 地址上公开应用服务环境托管的应用。

-  内部负载均衡器 (ILB) 应用程序服务环境-在虚拟网络中的 IP 地址上公开应用服务环境托管的应用。 内部终结点是一个内部负载均衡器 (ILB) ，这就是所谓 ILB ASE 的原因。 

对于多租户应用服务 (不在隔离层) 中的应用，请使用虚拟网络集成，以便应用访问虚拟网络中的资源。  使用 "专用站点访问" 使应用只能从专用网络（例如 Azure 虚拟网络中的一个）访问。 虚拟网络集成仅用于从应用到虚拟网络的出站调用。 如果虚拟网络集成功能适用于同一区域中的虚拟网络和其他区域中的虚拟网络，则其行为方式有所不同。 
 
- [应用服务环境的网络注意事项](environment/network-info.md)

- [如何创建外部 ASE](environment/create-external-ase.md)

- [如何创建内部 ASE](environment/create-ilb-ase.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：尽管应用服务尚不支持数据标识、分类和丢失防护功能，但你可以通过删除目标对 Internet 或 Azure 服务使用 "标记" 的所有规则来减少数据渗透的风险。 

Microsoft 管理应用服务的底层基础结构，并实施了严格控制来防止数据丢失或泄露。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：使用 TLS/SSL 设置中配置的默认最低版本的 tls 1.2 加密传输中的所有信息。 还要确保将所有 HTTP 连接请求重定向到 HTTPS。

- [了解 Azure App Service web 应用传输的加密](security-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：当前不可用。 数据标识、分类和丢失防护功能尚不可用于应用服务。 

标记可能正在处理敏感信息的应用服务应用。 实现第三方解决方案（如有必要）以实现符合性。

Microsoft 管理底层平台，并将所有客户数据视为敏感数据，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：使用基于角色的访问控制 (Azure Active Directory (Azure AD) 中的 Azure RBAC) 在 Azure 门户控制对应用服务控制平面的访问。

- [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：应用服务应用中的网站内容（如文件）存储在 Azure 存储中，后者会自动加密静态内容。 选择将应用程序机密存储在 Key Vault 中，并在运行时检索它们。

客户提供的机密在存储在应用服务配置数据库中时静态加密。

请注意，虽然本地附加的磁盘可以由网站作为临时存储（ (例如 D:\local 和% TMP% ) ）使用，但它们不会静态加密。

- [了解 Azure App Service 的数据保护控件]()

- [了解静态 Azure 存储加密](../storage/common/storage-service-encryption.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以便在对生产应用服务应用和其他关键或相关资源进行任何更改时创建警报。

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/alerts/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：采用 DevSecOps 做法来确保应用服务应用的安全，并在生命周期的整个持续时间内保持安全。 DevSecOps 将你的组织的安全团队及其功能合并到你的 DevOps 做法中，从而确保团队中每个人的责任。

查看并遵循安全中心提供的建议，确保应用服务应用的安全。

- [如何将持续安全验证添加到 CI/CD 管道](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [如何实现 Azure 安全中心漏洞评估建议](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**： Microsoft 对支持应用服务的基础系统执行漏洞管理。 不过，你可以使用安全中心内的建议的严重性以及安全分数来衡量你的环境中的风险。 安全分数以您已缓解的安全中心建议的数量为基础。 若要确定首先要解决的建议的优先级，请考虑每个建议的严重性。

- [安全建议参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询或发现订阅中的所有资源 (如计算、存储、网络、端口、协议等) 。 确保对租户应用适当的权限，并且可以枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：使用元数据将标记应用于 Azure 资源，以逻辑方式将它们组织到分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：根据需要使用标记、管理组和单独的订阅来组织和跟踪 Azure 资源。 定期协调清点，并确保在此过程中从订阅中删除未经授权的资源。

使用以下内置策略定义，选择 "Azure 策略"，对可在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

查看引用的链接以获取其他信息。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指南**：根据组织需求为计算资源创建已批准的 Azure 资源和批准的软件的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

使用 Azure 资源关系图可以在其订阅中查询或发现资源。  确保环境中存在的所有 Azure 资源已获得批准。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：使用 Azure 资源关系图可查询或发现订阅中的资源，并确保基于你的组织策略批准发现的 Azure 资源。

使用应用服务中的 Web 作业监视在计算资源中部署的未批准的软件应用程序。 使用 web 作业在与 web 应用、API 应用或移动应用相同的实例中运行程序或脚本。 定义 Web 作业配置和通过日志进行监视。 在“Web 作业运行详细信息”页中，选择“切换输出”查看日志内容的文本。  请注意，Linux 上的应用服务尚不支持 Web 作业。

- [在 Azure 应用服务中使用 WebJobs 运行后台任务](webjobs-create.md)

- [教程-创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [快速入门-使用 Azure 资源关系图资源管理器运行第一个资源图形查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：确保环境中存在的所有 Azure 资源都已获得批准。 使用 Azure 策略限制可在订阅中创建的资源类型。 删除任何已部署的未按组织策略批准的软件应用程序。

- [在 Azure 应用服务中使用 WebJobs 运行后台任务](webjobs-create.md)

- [教程-创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [快速入门-使用 Azure 资源关系图资源管理器运行第一个资源图形查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：确保环境中存在的所有 Azure 资源都已获得批准。 使用 Azure 策略限制可在订阅中创建的资源类型。 删除任何已部署的未按组织策略批准的软件应用程序。 

- [在 Azure 应用服务中使用 WebJobs 运行后台任务](webjobs-create.md)

- [教程-创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [快速入门-使用 Azure 资源关系图资源管理器运行第一个资源图形查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：创建一个流程以定期查看未经授权的 azure 服务，以确保仅在订阅中使用已授权的 azure 服务。

使用此过程中的 Azure 资源关系图来查询或发现其订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

使用以下内置策略定义，将 Azure 策略配置为对可在订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

使用应用服务中的 Web 作业监视在计算机资源中部署的未批准的软件应用程序。 使用 web 作业在与 web 应用、API 应用或移动应用相同的实例中运行程序或脚本。 定义 Web 作业配置和通过日志进行监视。 在“Web 作业运行详细信息”页中，选择“切换输出”查看日志内容的文本。  请注意，Linux 上的应用服务尚不支持 Web 作业。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/built-in-policies.md#general)

- [在 Azure 应用服务中使用 WebJobs 运行后台任务](webjobs-create.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指南**：实施一个过程来定期清点和查看订阅中的软件标题，以确保仅在订阅中使用已授权的 Azure 服务。

使用此过程中的 Azure 资源关系图来查询或发现订阅中的资源。 确保在环境中发现的所有 Azure 资源都已获得批准。

使用以下内置策略定义，将 Azure 策略配置为对可在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

同样，使用应用服务中的 Web 作业来清点计算机资源中部署的未批准的软件应用程序。 定义日志的配置和监视。 在“Web 作业运行详细信息”页中，选择“切换输出”查看日志内容的文本。  请注意，Linux 上的应用服务尚不支持 Web 作业。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/built-in-policies.md#general)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，配置 azure 条件性访问，以限制用户与 Azure 资源管理器的交互能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：应用服务中的 web 作业使客户能够在与 web 应用、API 应用或移动应用相同的实例中运行程序或脚本。 你负责定义你的配置，以限制或限制组织不允许的任何脚本。 应用服务不提供一种机制来限制本机执行脚本。 请注意，Linux 上的应用服务尚不支持 Web 作业。

- [在 Azure 应用服务中使用 WebJobs 运行后台任务](webjobs-create.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：实施单独的订阅或管理组，以便为高风险应用服务应用提供隔离。 将较高风险的应用部署到其自己的虚拟网络中，因为应用服务中的外围安全性是通过虚拟网络的使用来实现的。 应用服务环境是将应用服务部署到 Azure 虚拟网络中的子网中。 

存在两种类型的应用程序服务环境、外部应用程序服务环境和 ILB (内部负载均衡器) 应用程序服务环境。 根据你的需求选择最佳体系结构。

- [应用服务环境的网络注意事项](environment/network-info.md)

- [创建外部应用服务环境](environment/create-external-ase.md)

- [创建和使用内部负载均衡器应用服务环境](environment/create-ilb-ase.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：通过 Azure 策略为应用服务部署的应用定义和实施标准安全配置。

使用 "Microsoft Web" 命名空间中的 Azure 策略别名创建自定义策略，以审核或强制实施应用服务 Web 应用的配置。

应用内置策略定义，例如：
- 应用服务应使用虚拟网络服务终结点
- 只能通过 HTTPS 访问 Web 应用程序

- 在应用中使用最新的 TLS 版本

建议将此过程记录为应用用于标准化使用的内置策略定义。   

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在]）影响跨 Azure App Service 应用执行安全设置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：若要在使用自定义 azure 策略定义时安全地存储和管理代码，请选择 "Azure DevOps" 或 "Azure Repos"。

使用现有的持续集成 (CI) 和持续交付 (CD) 管道部署已知安全的配置。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos 文档](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：使用内置的 azure 策略定义以及 "Microsoft" 命名空间中的 azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用内置的 azure 策略定义以及 "Microsoft" 命名空间中的 azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 

应用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在），以自动强制执行 Azure 资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：使用托管标识，通过 Azure Active Directory (Azure AD) 中的自动托管标识提供应用服务应用。 使用托管标识，你的应用可以对任何支持 Azure AD 身份验证的服务进行身份验证，包括 Key Vault，而无需在代码中提供任何凭据。 确保在 Azure Key Vault 中启用软删除。

- [如何在 Azure Key Vault 中启用软删除](../key-vault/general/key-vault-recovery.md)

- [如何将托管标识用于应用服务](overview-managed-identity.md)

- [如何使用托管标识提供 Key Vault 身份验证](../key-vault/general/assign-access-policy-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：使用托管标识向应用服务部署的应用提供 Azure Active Directory (Azure AD) 中的自动托管标识。 通过托管标识，你的应用可以对任何支持 Azure AD 身份验证的服务进行身份验证，包括 Key Vault，而无需在代码中提供任何凭据。

- [如何将托管标识用于应用服务](overview-managed-identity.md)

- [如何使用托管标识提供 Key Vault 身份验证](../key-vault/general/assign-access-policy-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：通过应用服务中的备份和还原功能，可以轻松地手动或按计划创建应用备份。 可以将备份配置为无限期保留。 通过覆盖现有应用或还原为另一应用可将应用还原为先前状态的快照。

应用服务可将以下信息备份到 Azure 存储帐户和容器，你已将应用配置为使用：
- 应用配置
- 文件内容
- 连接到应用的数据库

确保定期和自动备份按组织策略定义的频率进行。

- [了解 Azure App Service 备份功能](manage-backup.md)

- [用于 Azure 存储加密的客户管理的密钥](../storage/common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：使用应用服务的备份和还原功能来备份应用程序。 备份功能需要使用 Azure 存储帐户来存储应用程序的备份信息。

- Azure 存储提供静态加密-使用系统提供的密钥或你自己的客户管理的密钥。 当应用程序数据未在 Azure 中的 web 应用中运行时，会将其存储在其中。
- 从部署包运行是应用服务的部署功能。 借助此功能可以使用共享访问签名 (SAS) URL 从 Azure 存储帐户部署站点内容。

- Key Vault 引用是应用服务的一项安全功能。 借助此功能可以在运行时将机密作为应用程序设置导入。 使用此机密可以加密 Azure 存储帐户的 SAS URL。

有关详细信息，请参阅引用的链接。

- [在 Azure 中备份应用](manage-backup.md)

- [还原 Azure App Service 中运行的应用](web-sites-restore.md)

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [加密模型和密钥管理表](../security/fundamentals/encryption-atrest.md)

- [使用客户管理的密钥进行静态加密](configure-encrypt-at-rest-using-cmk.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：对应用服务应用程序的任何备份定期测试还原过程。

- [在 Azure 中备份应用](manage-backup.md)

- [如何还原 Azure App Service web 应用](web-sites-restore.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：应用服务备份存储在 Azure 存储帐户中。 Azure 存储中的数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 Azure 存储加密法类似于 Windows 上的 BitLocker 加密法。

已为所有存储帐户（包括资源管理器和经典存储帐户）启用 Azure 存储加密。 无法禁用 Azure 存储加密。 由于数据默认受到保护，因此无需修改代码或应用程序，即可利用 Azure 存储加密。

默认情况下，存储帐户中的数据使用 Microsoft 托管的密钥进行加密。 可以依赖于使用 Microsoft 托管的密钥来加密数据，也可以使用你自己的密钥来管理加密。 确保在 Azure Key Vault 中启用软删除。

- [了解静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)

- [如何在 Azure Key Vault 中启用软删除](../key-vault/general/key-vault-recovery.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，还可以清楚地标记订阅 (例如，生产、非生产) ，并创建一个命名系统来明确识别和分类 Azure 资源。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：执行练习以测试系统的事件响应功能是否定期发生。 识别弱点和差距，并根据需要修改计划。

- [参阅 NIST 出版物 - IT 计划和功能的测试、训练和演练计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。  事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 根据业务需要，使用安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

指南：遵循 Microsoft Rules of Engagement 以确保渗透测试不违反 Microsoft 政策： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

你可以在 microsoft 管理的云基础结构、服务和应用程序上找到有关 Microsoft 策略和执行红色组合以及实时站点渗透测试的详细信息。

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)