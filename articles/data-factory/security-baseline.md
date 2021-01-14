---
title: 适用于 Azure 数据工厂的 Azure 安全基线
description: 适用于 Azure 数据工厂的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4e82b4adbfde7623de9497c94e56a9ae303131ad
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202770"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>适用于 Azure 数据工厂的 Azure 安全基线

适用于 Azure 数据工厂的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：创建 Azure-SSIS Integration Runtime (IR) 时，可选择将其加入虚拟网络。 这将允许 Azure 数据工厂创建某些网络资源，例如网络安全组 (NSG) 和负载均衡器。 你还能够提供自己的静态公共 IP 地址，或让 Azure 数据工厂为你创建一个。 在 Azure 数据工厂自动创建的 NSG 上，端口 3389 默认对所有流量开放。 将其锁定，确保只有管理员才有访问权限。

可在本地计算机或虚拟网络中的 Azure 虚拟机上部署自承载 IR。 确保虚拟网络子网部署已将 NSG 配置为仅允许管理访问。 根据每个 IR 节点的 Windows 防火墙规则，Azure-SSIS IR 默认禁止端口 3389 出站，以进行保护。 可将 NSG 与子网关联并设置严格规则来保护虚拟网络配置的资源。

若专用链接可用，请使用专用终结点来保护链接到 Azure 数据工厂管道的任何资源，例如 Azure SQL Server。 使用专用链接，虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。

* [如何创建 Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)

* [如何创建和配置自承载 IR](./create-self-hosted-integration-runtime.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [将 Azure-SSIS IR 加入虚拟网络](./join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

* [了解 Azure 专用链接](../private-link/private-link-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：使用 Azure 安全中心并修正与 Integration Runtime 部署关联的虚拟网络和网络安全组的网络保护建议。

此外，为保护 Integration Runtime 部署的 NSG 启用网络安全组 (NSG) 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。

还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

* [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

* [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或托管 Web 应用程序的计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：在与 Integration Runtime 部署相关联的虚拟网络上启用 DDoS 保护标准，以防范分布式拒绝服务攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

* [如何配置 DDoS 防护](../ddos-protection/manage-ddos-protection.md)

* [了解 Azure 安全中心集成的威胁情报](../security-center/azure-defender.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：为保护 Integration Runtime 部署的 NSG 启用网络安全组 (NSG) 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。

还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

* [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：若要检查来自 Azure-SSIS IR 的出站流量，可通过 Azure ExpressRoute 强制隧道将 Azure-SSIS IR 发起的流量路由到本地防火墙设备，或路由到来自 Azure 市场且支持 IDS/IPS 功能的网络虚拟设备 (NVA)。 如果不需要基于有效负载检查的入侵检测和/或防护，则可以使用包含威胁情报功能的 Azure 防火墙。

* [将 Azure-SSIS Integration Runtime 加入虚拟网络](./join-azure-ssis-integration-runtime-virtual-network.md)

* [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；此建议适用于 Azure 应用服务或托管 Web 应用程序的计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：在网络安全组 (NSG) 或 Azure 防火墙中使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 DataFactoryManagement），可以允许或拒绝相应服务的入站流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

* [了解并使用服务标记](../virtual-network/service-tags-overview.md)

* [了解 Azure 数据工厂特定服务标记](./join-azure-ssis-integration-runtime-virtual-network.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：通过 Azure Policy 为与 Azure 数据工厂实例关联的网络设置和网络资源定义和实现标准安全配置。 使用“Microsoft.DataFactory”和“Microsoft.Network”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 数据工厂实例的网络配置。 还可以使用与网络或 Azure 数据工厂实例相关的内置策略定义，例如：
- 应启用 DDoS 防护标准版

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [用于网络的 Azure Policy 示例](../governance/policy/samples/built-in-policies.md#network) 

* [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与 Azure 数据工厂实例的网络安全和流量流相关的资源使用标记，以提供元数据和逻辑组织。

使用与标记相关的任何内置 Azure Policy 定义（如“需要标记及其值”），以确保使用标记创建所有资源并向你告知现有的未标记资源。

可以使用 Azure PowerShell 或 Azure CLI 基于其标记对资源进行查找或执行操作。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure 数据工厂实例相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护用于 azure 资源的时间源，如日志中的时间戳的 Azure 数据工厂。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：通过 Azure Monitor 引入日志来聚合 Azure 数据工厂生成的安全数据。 在 Azure Monitor 中，可以查询配置为接收 Azure 数据工厂活动日志的 Log Analytics 工作区。 将 Azure 存储帐户用于长期/存档日志存储或事件中心，以便将数据导出到其他系统。

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方安全事件和事件管理 (SIEM) 。你还可以将 Azure 数据工厂与 Git 集成，以利用多种源代码管理的优点，例如跟踪/审核更改的能力，以及恢复引入 bug 的更改的能力。

* [如何配置诊断设置](../azure-monitor/platform/diagnostic-settings.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Azure 数据工厂中的源代码管理](./source-control.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：对于控制平面审核日志记录，请启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 使用 Azure 活动日志数据，可以确定在控制平面级别针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

使用诊断设置为 Azure 数据工厂中的非计算资源配置诊断日志，例如指标和管道运行数据。 Azure 数据工厂将管道运行数据存储 45 天。 若要将此数据保留更长时间，请将诊断日志保存到存储帐户以进行审核或手动检查，并指定保留时间（以天为单位）。 还可将日志流式传输到 Azure 事件中心，或将日志发送到 Log Analytics 工作区进行分析。

* [如何启用 Azure 活动日志的诊断设置](../azure-monitor/platform/activity-log.md)

* [了解 Azure 数据工厂诊断日志](./monitor-using-azure-monitor.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，就可以使用 Azure Monitor 从虚拟机收集数据。 安装 Log Analytics VM 扩展后，Azure Monitor 可从 Azure VM 收集数据。 然后，Azure 安全中心可为虚拟机提供安全事件日志监视。 考虑到安全事件日志生成的数据量，默认情况下不会存储它。

如果你的组织想要保留安全事件日志数据，则可以将其存储在某个数据集合层中，此时可以在 Log Analytics 中对其进行查询。

* [如何在 Azure Monitor 中从 Azure 虚拟机收集数据](../azure-monitor/learn/quick-collect-azurevm.md)

* [启用 Azure 安全中心中的数据收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：启用 Azure 数据工厂的诊断设置。 如果选择将日志存储在 Log Analytics 工作区中，请根据组织的合规性规则来设置 Log Analytics 工作区保持期。 将 Azure 存储帐户用于长期/存档存储。

* [如何在 Azure 数据工厂中启用诊断日志](./monitor-using-azure-monitor.md)

* [如何为 Log Analytics 工作区设置日志保留参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：启用 Azure 数据工厂的诊断设置，并将日志发送到 Log Analytics 工作区。 使用 Log Analytics 分析和监视日志中的异常行为，并定期查看结果。 还务必为与 Azure 数据工厂部署相关的任何数据存储启用诊断设置。 参阅每项服务的安全基线，获取关于如何启用诊断设置的指导。

如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，还请为 VM 启用诊断设置。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [Log Analytics 架构](./monitor-using-azure-monitor.md#schema-of-logs-and-events)

* [如何使用 Azure Monitor 从 Azure 虚拟机收集数据](../azure-monitor/learn/quick-collect-azurevm.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：通过转到 Azure Monitor 中的“警报和指标”部分，对数据工厂中支持的指标发出警报。

为 Azure 数据工厂配置诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 工作区中，配置发生一组预定义的条件时要触发的警报。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

此外，确保为与数据存储相关的服务启用诊断设置。 可以参考每项服务的安全基线来获取指导。

* [Azure 数据工厂中的警报](./monitor-visually.md#alerts)

* [所有支持的指标页](../azure-monitor/platform/metrics-supported.md)

* [如何在 Log Analytics 工作区中配置警报](../azure-monitor/platform/alerts-log.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：如果在 Azure 虚拟机中运行 Integration Runtime，则可以使用适用于 Azure 云服务和虚拟机的 Microsoft Antimalware，并将虚拟机配置为将事件记录到 Azure 存储帐户。 配置 Log Analytics 工作区以从存储帐户中引入事件，并视具体情况创建警报。 请遵循 Azure 安全中心中的建议：“计算和应用”。&amp;

* [如何为云服务和虚拟机配置 Microsoft Antimalware](../security/fundamentals/antimalware.md)

* [如何为虚拟机启用来宾级别监视](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用；Azure 数据工厂不会处理或生成与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，则可以启用命令行审核日志记录。 Azure 安全中心为 Azure VM 提供安全事件日志监视。 如果启用了自动预配或可以手动安装代理，则安全中心可在所有受支持的 Azure VM 以及任何新建的 Azure VM 中预配 Microsoft Monitoring Agent。 该代理可启用进程创建事件 4688 和事件 4688 内的 CommandLine 字段。 VM 上创建的新进程由事件日志记录，并由安全中心检测服务监视。

* [Azure 安全中心中的数据收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：在 Azure 数据工厂中，确保定期跟踪和协调用户访问。 若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于参与者或所有者角色，或者是 Azure 订阅的管理员。   

此外，在租户级别，Azure Active Directory (AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员且对 Azure 数据工厂实例的控制平面具有管理访问权限的帐户。

尽管建议使用 Azure AD 方法来管理用户访问，但请记住，如果你在 Azure 虚拟机 (VM) 中运行 Integration Runtime，VM 可能也有本地帐户。 通常应以最少使用量原则来查看和管理本地帐户及域帐户。 此外，我们还建议查看 Privileged Identity Manager 来了解“实时”功能，以降低管理权限的可用性。

* [Azure 数据工厂的角色和权限](./concepts-roles-permissions.md)

* [有关 Privileged Identity Manager 的信息](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [本地帐户的信息](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure 数据工厂使用 Azure Active Directory (AD) 提供对 Azure 门户以及 Azure 数据工厂控制台的访问权限。 Azure AD 没有默认密码的概念，但你有责任更改或不允许使用任何自定义或第三方应用程序的默认密码。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：就专用于访问 Azure 控制平面（Azure 门户）以及 Azure 数据工厂控制台的管理帐户的使用，创建标准操作过程。 使用 Azure 安全中心标识和访问管理来监视 Azure AD 中的管理帐户数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：
- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

如果在 Azure 虚拟机上运行 Integration Runtime，还可以使用 Azure Privileged Identity Manager (PIM) 配置 Azure 虚拟机上的管理员帐户。 Azure Privileged Identity Manager 提供实时提升、多重身份验证等多种选项，以及委派选项（这样权限就仅适用于特定时间范围，并需要另一个人来审批）。

* [了解 Azure 安全中心标识和访问](../security-center/security-center-identity-access.md)

* [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [有关 Privileged Identity Manager 的信息](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure 数据工厂的角色和权限](./concepts-roles-permissions.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：使用 Azure 应用注册 (服务主体) 检索应用程序或函数可用来访问和与恢复服务保管库进行交互的令牌。

* [如何调用 Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [如何将客户端应用程序（服务主体）注册到 Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure 恢复服务 API 信息](/rest/api/recoveryservices/)

* [有关 Azure 数据工厂 REST API 的信息](/rest/api/datafactory/)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

指南：启用 Azure Active Directory 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理建议。

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用配置了多重身份验证 (MFA) 的特权访问工作站 (PAW) 来登录并配置 Azure 资源。

* [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory 安全报告在环境中发生可疑活动或不安全的活动时生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

如果要在 Azure 虚拟机上运行 Integration Runtime (VM) ，还可以将 VM 加入 Azure Sentinel。 Microsoft Azure Sentinel 是可缩放的云原生安全信息事件管理 (SIEM) 和安全业务流程自动响应 (SOAR) 解决方案。 Azure Sentinel 在整个企业范围内提供智能安全分析和威胁智能，为警报检测、威胁可见性、主动搜寻和威胁响应提供单一解决方案。

* [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)

* [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

* [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：可将数据工厂与代表此特定数据工厂的 Azure 资源托管标识相关联。 可将此托管标识用于 Azure SQL 数据库身份验证。 指定的工厂可以使用此标识访问数据库数据或从/向数据库复制数据。

如果在 Azure 虚拟机上运行 Integration Runtime (IR)，则可以使用托管标识向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中包含任何凭据。 在虚拟机上运行的代码可以使用托管标识来请求支持 Azure AD 身份验证的服务的访问令牌。

* [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)

* [使用 Azure 数据工厂在 Azure SQL 数据库中复制和转换数据](./connector-azure-sql-database.md)

* [如何使用 Azure SQL 数据库配置和管理 Azure Active Directory 身份验证](../azure-sql/database/authentication-aad-configure.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

如果在 Azure 虚拟机中运行 Runtime Integration，则需要查看本地安全组和用户，以确保没有可能危及系统的非预期帐户。

* [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

* [了解 Azure AD 报告](../active-directory/reports-monitoring/index.yml)

* [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：你有权访问 Azure Active Directory (AD) 登录活动、审核和风险事件日志源，以便与任何 SIEM/监视工具集成。 可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

如果要在 Azure 虚拟机中运行 Integration Runtime (VM) ，并将 VM 加入 Azure Sentinel。 Microsoft Azure Sentinel 是可缩放的云原生安全信息事件管理 (SIEM) 和安全业务流程自动响应 (SOAR) 解决方案。 Azure Sentinel 在整个企业范围内提供智能安全分析和威胁智能，为警报检测、威胁可见性、主动搜寻和威胁响应提供单一解决方案。

* [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [使用 Azure Active Directory 授权访问事件中心资源](../event-hubs/authorize-access-azure-active-directory.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 AZURE ACTIVE DIRECTORY (AD) 作为 Azure 数据工厂资源（如 Azure SQL 数据库或 Azure 虚拟机）的中央身份验证和授权系统。 对于控制平面（Azure 门户）中帐户登录行为的偏差，可使用 Azure AD 标识保护和风险检测功能进行配置，使其在检测到与用户标识相关的可疑操作时自动进行响应。 还可将数据引入 Azure Sentinel 以做进一步调查。

* [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

* [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [使用 SQL 配置和管理 Azure Active Directory 身份验证](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)

* [为 Azure-SSIS Integration Runtime 启用 Azure Active Directory 身份验证](./enable-aad-authentication-azure-ssis-ir.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：在 Microsoft 需要访问客户数据的支持方案中，Azure 客户密码箱提供了一个界面，供客户查看和批准或拒绝客户数据访问请求。 请注意，虽然 azure 密码箱不适用于 Azure 数据工厂，但 Azure 密码箱支持 Azure SQL 数据库和 Azure 虚拟机。

* [了解客户密码箱](../security/fundamentals/customer-lockbox-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

使用 Azure SQL 数据库数据发现和分类功能。 数据发现和分类提供了内置于 Azure SQL 数据库的高级功能，可用于发现、分类、标记和保护数据库中的敏感数据。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何使用 Azure SQL Server 的数据发现和分类功能](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Integration Runtime 应由虚拟网络 (VNet)/子网分隔并正确标记。

 还可以使用专用终结点来执行网络隔离。 Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效地引入 VNet 中。

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [了解专用链接](../private-link/private-endpoint-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：对于存储或处理 Azure 数据工厂部署的敏感信息的数据源（例如 Azure SQL 数据库），请使用标签将相关资源标记为敏感资源。

若专用链接可用，请使用专用终结点来保护链接到 Azure 数据工厂管道的任何资源。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 还可以通过为网络安全组 (NSG) 配置一组严格的出站规则并将该 NSG 与子网相关联，降低数据外泄的风险。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [了解 Azure 专用链接](../private-link/private-link-overview.md)

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：如果云数据存储支持 HTTPS 或 TLS，则数据工厂中数据移动服务与云数据存储之间的所有数据传输均通过安全通道 HTTPS 或 TLS 进行。 使用的 TLS 版本为 1.2。

在与数据库相互传输数据时，与 Azure SQL 数据库和 Azure Synapse Analytics 的所有连接都需要加密 (SSL/TLS)。 在使用 JSON 创作管道时，请在连接字符串中添加 encryption 属性并将其设置为 true。 对于 Azure 存储，可以在连接字符串中使用 HTTPS。

* [了解 Azure 数据工厂中的传输中加密](./data-movement-security-considerations.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：如果使用 Azure 数据工厂复制和转换 Azure SQL 数据库实例，请使用 Azure SQL 数据库数据发现和分类功能。 数据发现和分类提供了内置于 Azure SQL 数据库的高级功能，可用于发现、分类、标记和保护数据库中的敏感数据。

其他 Azure 服务尚未提供数据发现和分类功能。

* [如何使用 Azure SQL Server 的数据发现和分类功能](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 来控制对 Azure 数据工厂控制平面（Azure 门户）的访问。

若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于参与者或所有者角色，或者是 Azure 订阅的管理员。   

对于数据工厂数据源（如 Azure SQL 数据库），请参阅该服务的安全基线，详细了解 Azure RBAC。

* [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Azure 数据工厂的角色和权限](./concepts-roles-permissions.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：建议为与 Azure 数据工厂部署相关的所有数据存储都启用数据加密机制。 可参阅该服务的安全基线，详细了解静态数据的加密。

如果在 Azure 虚拟机中运行 Integration Runtime，则可以使用服务器端加密或 Azure 磁盘加密 (ADE) 对 Windows 虚拟机 (VM) 上的虚拟磁盘进行静态加密。 Azure 磁盘加密利用 Windows 的 BitLocker 功能，通过来宾 VM 中的客户托管密钥来加密托管磁盘。 使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。

可以在 Azure Key Vault 中存储凭据或机密值，并在管道执行过程中使用它们来传递到活动。 还可以在 Azure Key Vault 中存储数据存储和计算的凭据。 执行使用数据存储/计算的活动时，Azure 数据工厂将检索凭据。

* [了解 Azure 数据工厂中的静态加密](./data-movement-security-considerations.md)

* [Azure 托管磁盘的服务器端加密](../virtual-machines/disk-encryption.md)

* [适用于 Windows VM 的 Azure 磁盘加密](../virtual-machines/windows/disk-encryption-overview.md)

* [如何在管道活动中使用 Azure Key Vault 机密](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [如何在 Azure Key Vault 中存储凭据](./store-credentials-in-key-vault.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志配合使用，以创建要在 Azure 数据工厂和相关资源发生更改时发出的警报。

* [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

* [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

* [Azure 存储分析日志记录](../storage/common/storage-analytics-logging.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：如果将 Azure SQL 数据库用作数据存储，请为 Azure SQL 数据库启用高级数据安全，并遵循 Azure 安全中心关于在 Azure SQL Server 上执行漏洞评估的建议。

如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，请遵循 Azure 安全中心关于在 VM 上执行漏洞评估的建议。 使用建议的 Azure 安全或第三方解决方案对虚拟机执行漏洞评估。

* [如何对 Azure SQL 数据库运行漏洞评估](../azure-sql/database/sql-vulnerability-assessment.md)

* [如何启用高级数据安全](../azure-sql/database/azure-defender-for-sql.md)

* [如何实现 Azure 安全中心漏洞评估建议](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，请使用 Azure 更新管理解决方案来管理 VM 的更新和补丁。 更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 可以使用 System Center Updates Publisher (Updates Publisher) 之类的工具将自定义更新发布到 Windows Server Update Services (WSUS) 中。 在这种情况下，允许更新管理使用第三方软件来修补使用 Configuration Manager 作为其更新存储库的计算机。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [Azure 中的更新管理解决方案](../automation/update-management/overview.md)

* [管理 Azure VM 的更新和修补程序](../automation/update-management/manage-updates-for-vm.md)

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，则可以使用第三方补丁管理解决方案。 可以使用 Azure 更新管理解决方案来管理虚拟机的更新和补丁。 更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 可以使用 System Center Updates Publisher (Updates Publisher) 之类的工具将自定义更新发布到 Windows Server Update Services (WSUS) 中。 在这种情况下，允许更新管理使用第三方软件来修补使用 Configuration Manager 作为其更新存储库的计算机。

* [Azure 中的更新管理解决方案](../automation/update-management/overview.md)

* [管理 Azure VM 的更新和修补程序](../automation/update-management/manage-updates-for-vm.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：如果在 Azure 虚拟机上运行 Integration Runtime，请以一致的间隔导出扫描结果，并比较结果以验证漏洞是否已修复。 使用 Azure 安全中心提出的漏洞管理建议时，可以转到选定解决方案的门户查看历史扫描数据。

* [了解用于虚拟机的集成漏洞扫描程序](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：如果在 Azure 虚拟机中运行 Integration Runtime，则可以使用原生漏洞扫描程序。 Azure 安全中心随附的漏洞扫描程序由 Qualys 提供支持。 Qualys 的扫描程序是用于实时识别 Azure 虚拟机中的漏洞的领先工具。

当安全中心识别到漏洞时，它会提供结果和相关信息作为建议。 相关信息包括修正步骤、相关 CVE、CVSS 分数，等等。 你可以查看为一个或多个订阅或者为特定虚拟机识别出的漏洞。

* [用于虚拟机的集成漏洞扫描程序](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

* [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

* [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已获批 Azure 资源的清单

**指导**：为计算资源定义已批准的 Azure 资源和软件。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中的所有 Azure 资源均已获得批准。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，请利用 Azure 虚拟机清单自动收集有关虚拟机上的所有软件的信息。 可以通过 Azure 自动化在工作负荷和资源的部署、操作和解除授权过程中进行完全的控制。

注意：可从 Azure 门户获得软件名称、版本、发布者和刷新时间。 若要获得安装日期和其他信息的访问权限，客户需要启用来宾级诊断并将 Windows 事件日志置于 Log Analytics 工作区中。

* [Azure 自动化简介](../automation/automation-intro.md)

* [如何启用 Azure VM 清单](../automation/automation-tutorial-installed-software.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：如果在 Azure 虚拟机中运行 Integration Runtime，则可以通过 Azure 自动化在工作负载和资源的部署、操作和解除授权过程中进行完全的控制。 可以使用更改跟踪来识别虚拟机上安装的所有软件。 可以实现自己的过程，也可以使用 Azure Automation State Configuration 来删除未经授权的软件。

* [Azure 自动化简介](../automation/automation-intro.md)

* [使用更改跟踪解决方案跟踪环境中的更改](../automation/change-tracking/overview.md)

* [Azure 自动化 State Configuration 概述](../automation/automation-dsc-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，请使用 Azure 安全中心自适应应用程序控制确保仅执行已授权软件，并阻止所有未授权软件在 VM 上执行。

* [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/index.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：自适应应用程序控制是 Azure 安全中心提供的智能、自动化、端到端的解决方案，有助于控制可在 Azure 和非 Azure 计算机（Windows 和 Linux）上运行的应用程序。 如果此方案不满足组织的要求，则实施第三方解决方案。

请注意，只有当 Integration Runtime 在 Azure 虚拟机中运行时这才适用。

* [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：如果在 Azure 虚拟机中运行 Runtime Integration，根据脚本类型，可以使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。 还可以利用 Azure 安全中心自适应应用程序控制来确保仅执行已授权软件，并阻止所有未授权软件在 Azure 虚拟机上执行。

* [如何在 Windows 环境中控制 PowerShell 脚本的执行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：可以使用虚拟网络、子网、订阅、管理组等隔离 Azure 环境中部署的高风险应用程序，并使用 Azure 防火墙、Web 应用程序防火墙 (WAF) 或网络安全组 (NSG) 对其进行充分保护。

* [Azure 中的虚拟网络和虚拟机](../virtual-machines/network-overview.md)

* [什么是 Azure 防火墙？](../firewall/overview.md)

* [什么是 Azure Web 应用程序防火墙？](../web-application-firewall/overview.md)

* [网络安全组](../virtual-network/network-security-groups-overview.md)

* [什么是 Azure 虚拟网络？](../virtual-network/virtual-networks-overview.md)

* [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)

* [订阅决策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 为 Azure 数据工厂定义和实施标准安全配置。 使用“Microsoft.DataFactory”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 数据工厂实例的配置。

* [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：如果在 Azure 虚拟机中运行 Runtime Integration，请使用 Azure 安全中心建议 [修复虚拟机上安全配置中的漏洞]，维护所有计算资源上的安全配置。

* [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md)

* [如何修正 Azure 安全中心建议](../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

* [有关创建 Azure 资源管理器模板的信息](../virtual-machines/windows/ps-template.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，请注意，以下几种选项可用于维护 VM 的安全配置以进行部署：
- Azure 资源管理器模板：这些是基于 JSON 的文件，用于从 Azure 门户部署 VM，并且需要维护自定义模板。 Microsoft 对基本模板进行维护。
- 自定义虚拟硬盘 (VHD)：在某些情况下，可能需要使用自定义 VHD 文件，例如在处理无法通过其他方式管理的复杂环境时。 - Azure 自动化 State Configuration：部署基本 OS 后，可以将其用于更精细的设置控制，并通过自动化框架强制执行。

对于大部分方案，Microsoft 基本 VM 模板与 Azure Automation Desired State Configuration 相结合可以帮助满足和维护安全要求。

* [有关如何下载 VM 模板的信息](../virtual-machines/windows/download-template.md)

* [有关创建 Azure 资源管理器模板的信息](../virtual-machines/windows/ps-template.md)

* [如何将自定义 VM VHD 上传到 Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果使用自定义的 Azure Policy 定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

* [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：如果使用自定义映像，请使用 Azure 基于角色的访问控制 (Azure RBAC) 来确保只有授权用户才能访问映像。 对于容器映像，请将其存储在 Azure 容器注册表中，并利用 Azure RBAC 确保只有授权用户才能访问这些映像。

可以使用数据工厂参与者角色创建和管理数据工厂以及其中的子资源。

* [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [了解容器注册表的 Azure RBAC](../container-registry/container-registry-roles.md)

* [如何配置 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

* [Azure 数据工厂的角色和权限](./concepts-roles-permissions.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.DataFactory”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：如果 Integration Runtime 在 Azure 虚拟机中运行，此建议就适用。 Azure Automation State Configuration 是一个配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](../automation/automation-dsc-onboarding.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：在“Microsoft.DataFactory”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 使用 Azure Policy“[审核]”、“[拒绝]”和“[不存在则部署]”自动强制实施 Azure 资源的配置。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：如果 Integration Runtime 在 Azure 虚拟机中运行，此建议就适用。 Azure Automation State Configuration 是一个配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](../automation/automation-dsc-onboarding.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。

你还可以在 Azure Key Vault 中存储凭据或机密值，并在管道执行过程中将其用于传递到活动。 确保启用软删除。

* [如何与 Azure 托管标识集成](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [如何创建 Key Vault](../key-vault/secrets/quick-create-portal.md)

* [如何向 Key Vault 进行身份验证](../key-vault/general/authentication.md)

* [如何分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)

* [在管道活动中使用 Azure Key Vault 机密](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Azure Key Vault 中的软删除](../key-vault/general/soft-delete-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：创建数据工厂时，可在创建工厂的同时创建托管标识。 托管标识是注册到 Azure Active Directory 的托管应用程序，表示此特定数据工厂。

* [Azure 数据工厂的托管标识](./data-factory-service-identity.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：如果在 Azure 虚拟机中运行 Integration Runtime，则可以使用适用于 Azure Windows 虚拟机的 Microsoft Antimalware 来持续监视和守护资源。

* [如何为云服务和虚拟机配置 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预扫描要上传到非计算 Azure 资源的文件

**指南**：Microsoft Antimalware 会在支持 Azure 服务（例如 Azure 应用服务）的基础主机上启用，但不会对客户内容运行。

预扫描任何上传到非计算 Azure 资源（例如应用服务、Data Lake Storage、Blob 存储等）的文件。

使用 Azure 安全中心的数据服务威胁检测来检测上传到存储帐户的恶意软件。

* [了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

* [了解 Azure 安全中心的数据服务威胁检测](../security-center/azure-defender.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：部署后，Microsoft Antimalware for Azure 会默认自动安装最新的签名、平台和引擎更新。 请遵循 Azure 安全中心中的建议：“计算和应用”用于确保所有终结点都具有最新的签名。 通过与 Azure 安全中心集成的 Microsoft Defender 高级威胁防护服务，可以使用附加的安全性进一步保护 Windows OS，以降低基于病毒或恶意软件进行攻击的风险。

* [如何为 Azure 云服务和虚拟机部署 Microsoft Antimalware](../security/fundamentals/antimalware.md)

* [Microsoft Defender 高级威胁防护](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，请启用 Azure 备份并配置 VM 以及所需的自动备份频率和保留期。

对于任何数据存储，请参阅该服务的安全基线，获取有关如何定期执行自动备份的建议。

* [概要了解 Azure VM 备份](../backup/backup-azure-vms-introduction.md)

* [通过 VM 设置备份 Azure VM](../backup/backup-azure-vms-first-look-arm.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，请启用 Azure 备份并设置目标 Azure VM，并设置所需的频率和保留期。 在 Azure Key Vault 中备份客户管理的密钥。

对于任何数据存储，请参阅该服务的安全基线，获取有关如何定期执行自动备份的建议。

* [概要了解 Azure VM 备份](../backup/backup-azure-vms-introduction.md)

* [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：如果在 Azure 虚拟机中运行 Integration Runtime，请确保可在 Azure 备份中定期执行内容的数据还原。 如有必要，在隔离的 VLAN 中对还原内容进行测试。 对备份的客户管理的密钥定期进行还原测试。

对于任何数据存储，请参阅该服务的安全基线，获取有关验证备份的指导。

* [如何从 Azure 虚拟机备份恢复文件](../backup/backup-azure-restore-files-from-vm.md)

* [如何在 Azure 中还原密钥保管库密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：如果在 Azure 虚拟机 (VM) 中运行 Integration Runtime，并使用 Azure 备份来备份该 VM，则可以使用存储服务加密 (SSE) 来静态加密 VM。 Azure 备份还可以备份使用 Azure 磁盘加密进行加密的 Azure VM。 Azure 磁盘加密与在 Key Vault 中作为机密受到保护的 BitLocker 加密密钥 (BEK) 相集成。 Azure 磁盘加密还与 Azure Key Vault 密钥加密密钥 (KEK) 相集成。 在 Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。

* [VM 的软删除](../backup/backup-azure-security-feature-cloud.md)

* [Azure Key Vault 软删除概述](../key-vault/general/soft-delete-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

* [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Sentinel。

* [如何配置连续导出](../security-center/continuous-export.md)

* [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

* [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**： 

* [请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [可在此处详细了解如何针对 Microsoft 托管云基础结构、服务和应用程序执行红队测试和实时站点渗透测试，以及 Microsoft 的相关策略](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)