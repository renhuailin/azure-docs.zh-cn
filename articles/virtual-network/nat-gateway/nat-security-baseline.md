---
title: 虚拟网络 NAT 的 Azure 安全基线
description: 虚拟网络 NAT 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 862c21e162ce43e6d7de6aa6485b01925edbe465
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288190"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>虚拟网络 NAT 的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../../security/benchmarks/overview-v1.md) 中的指南应用于 Microsoft 虚拟网络 NAT。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容按“安全控制”分组，这些控制根据适用于虚拟网络 NAT 的 Azure 安全基准和相关指南进行定义。 不适用于虚拟网络 NAT 的控制已排除在外。

 
若要查看虚拟网络 NAT 如何完全映射到 Azure 安全基准，请参阅[完整的虚拟网络 NAT 安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../../security/benchmarks/security-control-network-security.md)。

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：虚拟网络 NAT 不支持使用网络安全组 (NSG) 控制出站流量。  仅允许入站流量响应出站源流。

但是，网络安全组 (NSG) 流日志可与 NAT 网关资源一起使用，以监视出站源流量。

使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 网络资源。 启用网络安全组流日志，并将日志发送到 Azure 存储帐户进行审核。 你还可以将流日志发送到 Log Analytics 工作区，然后使用流量分析来提供有关 Azure 云中流量模式的见解。 流量分析的优势包括能够可视化网络活动、识别热点和安全威胁、了解通信流模式，以及查明网络不当配置。 

- [虚拟网络 NAT 概述](./nat-overview.md)

- [NAT 网关资源](./nat-gateway-resource.md)

- [如何启用网络安全组流日志](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [如何启用和使用流量分析](../../network-watcher/traffic-analytics.md)

- [了解 Azure 安全中心提供的网络安全性](../../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：启用网络观察程序数据包捕获来调查异常活动。 

- [如何创建网络观察程序实例](../../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：借助内置或自定义 Azure Policy 定义和分配，为配置有 NAT 网关资源的子网定义和实现标准安全配置。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [用于网络的 Azure Policy 示例](../../governance/policy/samples/built-in-policies.md#network)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视资源配置，并检测 NAT 网关资源和虚拟网络资源的更改。 在 Azure Monitor 中创建警报，以便在关键资源发生更改时通知你。

- [如何查看和检索 Azure 活动日志事件](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](../../azure-monitor/alerts/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：通过 Azure Monitor 引入与虚拟网络 NAT 相关的日志，从而将终结点设备、网络资源和其他安全系统生成的安全数据进行聚合。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

或者，可以启用此数据并将其加入 Azure Sentinel 或第三方 SIEM。

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/essentials/diagnostic-settings.md) 

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：自动可用的活动日志包含针对 NAT 网关资源的所有写入操作（PUT、POST、DELETE），但读取操作 (GET) 除外。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

- [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/essentials/diagnostic-settings.md) 

- [了解 Azure 中的日志记录和不同的日志类型](../../azure-monitor/essentials/platform-logs-overview.md) 

虚拟网络 NAT 目前不生成客户可配置的任何其他诊断日志。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：使用 Azure 安全中心和 Log Analytics 工作区监视安全日志和事件中的异常活动并发出警报。 相反，可启用数据并将其加入 Azure Sentinel。

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [如何在 Azure 安全中心管理警报](../../security-center/security-center-managing-and-responding-alerts.md) 

- [如何针对 Log Analytics 日志数据发出警报](../../azure-monitor/alerts/alerts-log.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 

确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可通过 Resource Graph 发现经典 Azure 资源，但强烈建议你今后还是创建和使用基于 Azure 资源管理器的资源。

- [Azure Resource Graph 查询](../../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure 资源、资源组和订阅，以便有条理地将它们组织成分类。 每个标记均由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：应用用于管理 Azure 资源的标记。

- [如何创建其他 Azure 订阅](../../cost-management-billing/manage/create-subscription.md) 

- [如何创建管理组](../../governance/management-groups/create-management-group-portal.md) 

- [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指导**：目前不可用

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。
使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 
- [如何使用 Azure Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：使用 Azure Policy 限制可在环境中预配的服务。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../../governance/policy/samples/built-in-policies.md#general)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../../security/benchmarks/security-control-secure-configuration.md)。

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：不适用；虚拟网络 NAT 没有任何安全配置。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：使用 Azure DevOps 安全地存储和管理代码，例如自定义 Azure Policy 定义、Azure 资源管理器模板和 Desired State Configuration 脚本。 若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。 

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow) 

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：不适用；虚拟网络 NAT 没有任何安全配置。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：应为产品/服务创建事件响应过程来确保可执行适当的事件响应过程，以便它在事件解决之前获得适当的优先级。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：应为产品/服务创建事件评分和优先级流程来确保发生适当的风险或威胁评分，以便它在事件解决之前获得适当的优先级。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：应为产品/服务创建和测试事件安全响应过程，来确保在事件预测解决之前可在适当的优先级下进行适当的缓解。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：应为产品/服务创建和测试事件安全响应过程，来确保在事件预测解决之前可在适当的优先级下进行适当的缓解。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：应为产品/服务创建和测试事件安全响应过程，来确保在事件预测解决之前可在适当的优先级下进行适当的缓解。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：应为产品/服务创建和测试事件安全响应过程，来确保在事件预测解决之前可在适当的优先级下进行适当的缓解。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](../../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../../security/benchmarks/security-baselines-overview.md)