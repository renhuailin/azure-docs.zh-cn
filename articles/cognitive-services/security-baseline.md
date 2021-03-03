---
title: 认知服务的 Azure 安全基线
description: 认知服务安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b243fa18b17fdd15f3c39545b7d81f5796bd8429
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699855"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>认知服务的 Azure 安全基线

此安全基准应用 [Azure 安全基准1.0 版](../security/benchmarks/overview-v1.md) 到认知服务的指南。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容由 Azure 安全基准定义的 **安全控制** 和适用于认知服务的相关指南进行分组。 排除了不适用于认知服务的 **控件**。

 
若要查看认知服务如何完全映射到 Azure 安全基准，请参阅 [完整认知服务安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**： Microsoft Azure 认知服务提供分层的安全模型。 借助此模型，可保护认知服务帐户，使其可供网络的特定子集访问。 配置网络规则后，仅通过指定网络集请求数据的应用程序才能访问帐户。 可使用请求筛选来限制对资源的访问，仅允许源自指定 IP 地址、IP 范围或 Azure 虚拟网络中部分子网的请求。

认知服务的虚拟网络和服务终结点支持仅限于一组特定的区域。

- [如何配置 Azure 认知服务虚拟网络](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)

- [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指南**：当虚拟机部署在与认知服务容器相同的虚拟网络中时，可以使用网络安全组来降低数据渗透的风险。 启用网络安全组流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 你还可以将网络安全组流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：如果在容器中使用认知服务，则可使用前置 Web 应用程序防火墙解决方案来增强容器部署，该解决方案可过滤恶意流量并支持端到端 TLS 加密，使容器终结点保持私密和安全。 

请记住，需要使用认知服务容器来提交计费所需的计量信息。 唯一例外是脱机容器，因为后者遵循不同的计费方法。 如果无法允许列出认知服务容器依赖的各种网络通道，则容器不能正常运行。 主机应该允许列出端口 443 和以下域：

- *.cognitive.microsoft.com
- *. cognitiveservices.azure.com

另请注意，必须在认知服务容器创建给 Microsoft 服务器的安全通道上禁用防火墙解决方案的深度数据包检查。 如果不能这样做，则容器无法正常运行。

- [了解 Azure 认知服务容器安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：当虚拟机部署在与认知服务容器相同的虚拟网络中时，请通过 Azure 策略为相关的网络资源定义和实施标准安全配置。 使用“Microsoft.CognitiveServices”和“Microsoft.Network”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Cache for Redis 实例的网络配置。 还可以利用内置策略定义，例如：

- 应启用 DDoS 防护标准版

使用 Azure 蓝图可以通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure 基于角色的访问控制 (Azure RBAC) 和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

如果在容器中使用认知服务，则可使用前置 Web 应用程序防火墙解决方案来增强容器部署，该解决方案可过滤恶意流量并支持端到端 TLS 加密，使容器终结点保持私密和安全。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

- [了解 Azure 认知服务容器安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：当虚拟机部署在与认知服务容器相同的虚拟网络中时，可以使用网络安全组来降低数据渗透的风险。 启用网络安全组流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 你还可以将网络安全组流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：如果在容器中使用认知服务，则可使用前置 Web 应用程序防火墙解决方案来增强容器部署，该解决方案可过滤恶意流量并支持端到端 TLS 加密，使容器终结点保持私密和安全。  可从 Azure 市场中选择一种产品/服务，该产品/服务应支持包含禁用有效负载检查功能的 IDS/IPS 功能。

请记住，需要使用认知服务容器来提交计费所需的计量信息。 唯一例外是脱机容器，它们采用不同的计费方法。 如果无法允许列出认知服务容器依赖的各种网络通道，则容器不能正常运行。 主机应该允许列出端口 443 和以下域：

- *.cognitive.microsoft.com
- *. cognitiveservices.azure.com

另请注意，必须在认知服务容器创建给 Microsoft 服务器的安全通道上禁用防火墙解决方案的深度数据包检查。 如果不能这样做，则容器无法正常运行。

- [了解 Azure 认知服务容器安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：如果在容器中使用认知服务，则可使用前置 Web 应用程序防火墙解决方案来增强容器部署，该解决方案可过滤恶意流量并支持端到端 TLS 加密，使容器终结点保持私密和安全。 

请记住，需要使用认知服务容器来提交计费所需的计量信息。 唯一例外是脱机容器，因为后者遵循不同的计费方法。 如果无法允许列出认知服务容器依赖的各种网络通道，则容器不能正常运行。 主机应该允许列出端口 443 和以下域：

- *.cognitive.microsoft.com
- *. cognitiveservices.azure.com

另请注意，必须在认知服务容器创建给 Microsoft 服务器的安全通道上禁用防火墙解决方案的深度数据包检查。 如果不能这样做，则容器无法正常运行。

- [了解 Azure 认知服务容器安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

你还可以使用应用程序安全组来帮助简化复杂的安全配置。 使用应用程序安全组可将网络安全性配置为应用程序结构的固有扩展，从而可以基于这些组将虚拟机分组以及定义网络安全策略。

- [虚拟网络服务标记](../virtual-network/service-tags-overview.md)

- [应用程序安全组](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#application-security-groups)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 Azure 策略的认知服务容器相关的网络资源的标准安全配置。 使用“Microsoft.CognitiveServices”和“Microsoft.Network”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Cache for Redis 实例的网络配置。

你还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、基于角色的访问控制 (Azure RBAC) 和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对与认知服务容器关联的网络资源使用标记，以便以逻辑方式将它们组织到分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与认知服务容器相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 活动日志可让你深入了解在控制平面级别对认知服务容器执行的操作。 通过 Azure 活动日志数据，可确定在控制平面级别针对 Azure Cache for Redis 实例执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/activity-log)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：对于控制平面审核日志记录，请启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 使用 Azure 活动日志数据，可以确定在控制平面级别针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

此外，认知服务将发送诊断事件，这些事件可收集并用于分析、警报和报告目的。 可通过 Azure 门户配置认知服务容器的诊断设置。 可将一个或多个诊断事件发送到存储帐户、事件中心或 Log Analytics 工作区。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [使用 Azure 认知服务的诊断设置](diagnostic-logging.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

- [如何为 Log Analytics 工作区设置日志保留参数](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 这些日志提供频繁生成的有关资源操作的丰富数据用于识别问题和调试。 在 Log Analytics 中执行查询以搜索字词、识别趋势和分析模式，并根据可能已为 Azure 认知服务收集的活动日志数据提供许多其他见解。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/activity-log)

- [如何收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](/azure/azure-monitor/platform/activity-log)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：通过转到 Azure Monitor 中的 "警报和指标" 部分，可以针对认知服务中支持的指标发出警报。

配置 Azure 认知服务容器的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 工作区中，配置发生一组预定义的条件时要触发的警报。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [使用 Azure Monitor 创建、查看和管理日志警报](/azure/azure-monitor/platform/alerts-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (Azure AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**：控制面访问认知服务是通过) Azure Active Directory (Azure AD 控制的。 Azure AD 没有默认密码。

数据平面访问认知服务是通过访问密钥控制的。 这些密钥由连接到缓存的客户端使用，可随时重新生成。

建议不要将默认密码构建到应用程序中。 相反，你可以将你的密码存储在 Azure Key Vault 中，然后使用 Azure AD 来检索它们。

- [如何重新生成 Azure Cache for Redis 访问密钥](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用安全中心的标识和访问管理来监视管理帐户的数量。

此外，为了帮助您跟踪专用管理帐户，可以使用安全中心或内置 Azure 策略中的建议，例如：

- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

- [如何使用 Azure 安全中心监视标识和访问（预览）](../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指南**：认知服务使用访问密钥对用户进行身份验证，而不支持在数据平面级别 (SSO) 进行单一登录。 可以通过 REST API 访问认知服务的控制平面，并支持 SSO。 若要进行身份验证，请将请求的授权标头设置为 JSON (JavaScript 对象表示法从 Azure Active Directory (Azure AD) 获取) Web 令牌。

- [了解 Azure 认知服务 REST API](/rest/api/cognitiveservices/)

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循安全中心身份验证和访问管理建议。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指南**：通过配置为登录和配置 Azure 资源的多重身份验证，使用特权访问工作站 (PAW) 。 

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 生成日志和警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 风险检测](../active-directory/identity-protection/overview-identity-protection.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：在 Azure Active Directory (中配置命名位置 Azure AD) 条件访问，以便仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密以及加盐、哈希处理和安全存储用户凭据来保护数据。 如果你的用例支持 AD 身份验证，请使用 Azure AD 对认知服务 API 的请求进行身份验证。 

目前，只有计算机视觉 API、人脸 API、文本分析 API、沉浸式阅读器、表单识别器、异常检测器和所有必应服务（必应自定义搜索除外）支持使用 Azure AD 进行身份验证。

- [如何对认知服务的请求进行身份验证](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-azure-active-directory)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 此外，客户要使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期查看用户的访问权限，以确保只有活动用户才能继续访问。

客户要维护 API 管理用户帐户的清单并根据需要协调访问。 在 API 管理中，开发人员是使用 API 管理公开的 API 的用户。 默认情况下，新创建的开发人员帐户处于“活动”状态，并且与“开发人员”组相关联。 处于“活动”状态的开发人员帐户可用于访问他们具有订阅的所有 API。

- [如何在 Azure API 管理中管理用户帐户](../api-management/api-management-howto-create-or-invite-developers.md)

- [如何获取 API 管理用户的列表](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-4.8.0&amp;preserve-view=true)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：你有权访问 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源，这允许你与 Azure Sentinel 或第三方 SIEM 集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：对于控制平面上的帐户登录行为偏差，使用 Azure Active Directory (Azure AD) 的标识保护和风险检测功能，以配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：不可用于认知服务。 认知服务尚不支持客户密码箱。

- [支持客户密码箱的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：为开发、测试和生产实施单独的订阅或管理组。 资源应由虚拟网络或子网进行分隔，进行适当标记，并受网络安全组或 Azure 防火墙的保护。 存储或处理敏感数据的资源应当充分隔离。 对于存储或处理敏感数据的虚拟机，执行策略和过程以在不使用时将其关闭。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何通过 Azure 防火墙配置“警报”或“发出警报并拒绝”](../firewall/threat-intel.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：尚不可用。 对于认知服务，数据识别、分类和丢失防护功能尚不可用。

Microsoft 管理认知服务的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：所有认知服务终结点都是通过 HTTP 强制 TLS 1.2 公开的。 使用强制执行的安全协议时，尝试调用认知服务终结点的使用者应遵循以下准则：

- 客户端操作系统 (OS) 需要支持 TLS 1.2。
- 用于进行 HTTP 调用的语言（和平台）需要在请求中指定 TLS 1.2。 根据语言和平台，可以隐式或显式地指定 TLS。

- [了解 Azure 认知服务的传输层安全性](cognitive-services-security.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：对于认知服务，数据识别、分类和丢失防护功能尚不可用。 同样地，标记包含敏感信息的实例；如果出于合规性目的需要，请实施第三方解决方案。

Microsoft 管理底层平台，并将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问 

**指南**：使用 azure RBAC)  (azure 基于角色的访问控制来控制对认知服务控制平面的访问 (即 Azure 门户) 。 

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：认知服务的静态加密由正在使用的特定服务而定。 在大多数情况下，数据将使用符合 FIPS 140-2 的 256 位 AES 加密法进行加密和解密。 加密和解密是透明的，这意味着 Microsoft 为客户管理加密和访问权限。 默认情况下，客户数据是安全的，无需修改其代码或应用程序即可利用加密。

还可使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。

- [加密静态信息的服务列表](/azure/cognitive-services/encryption/cognitive-services-encryption-keys-portal)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以便为认知服务的生产实例以及其他关键或相关资源的发生更改创建警报。

- [如何针对 Azure 活动日志事件创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询或发现订阅中的所有资源 (如计算、存储、网络、端口、协议等) 。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独订阅（若适用）来整理和跟踪 Azure Cache for Redis 实例及相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

此外，使用 Azure 资源关系图查询或发现订阅中的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：通过 Azure 策略为认知服务容器定义和实施标准安全配置。 使用“Microsoft.CognitiveServices”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Cache for Redis 实例的配置。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 azure 策略 [拒绝] 和 [部署（如果不存在）影响跨 Azure 资源强制实施安全设置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果你要将自定义 azure 策略定义或 azure 资源管理器模板用于认知服务容器和相关资源，请使用 Azure Repos 安全地存储和管理你的代码。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用“Microsoft.Cache”命名空间中的 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并设置相关警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用“Microsoft.CognitiveServices”命名空间中的 Azure Policy 别名创建自定义 Azure Policy 定义，以审核、强制实施系统配置并设置相关警报。 使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在]）影响为 Azure 缓存自动强制执行 Redis 实例和相关资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：对于正在用于访问认知服务 API Azure App Service 上运行的 Azure 虚拟机或 web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以简化和安全认知服务密钥管理。 请确保启用 Key Vault 软删除。

- [如何与 Azure 托管标识集成](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [如何创建 Key Vault](/azure/key-vault/quick-create-portal)

- [如何向 Key Vault 进行身份验证](../key-vault/general/authentication.md)

- [如何分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：对于正在用于访问认知服务 API Azure App Service 上运行的 Azure 虚拟机或 web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以简化和安全认知服务密钥管理。 确保启用 Key Vault 软删除。

使用托管标识在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管的标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Azure Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [如何配置托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [如何与 Azure 托管标识集成](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：Microsoft 反恶意软件已在支持 Azure 服务（例如 Azure Cache for Redis）的基础主机上启用，但它不会针对客户内容运行。

预先扫描正在上载到非计算 Azure 资源的任何内容，例如应用服务、Data Lake Storage、Blob 存储、Azure Database for PostgreSQL 等。 Microsoft 无法访问这些实例中的数据。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：始终自动复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性。 Azure 存储功能会复制数据，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心或跨地理上隔离的区域复制数据。 

你还可以使用生命周期管理功能将数据备份到存档层。 此外，为存储在存储帐户中的备份启用软删除。

- [了解 Azure 存储冗余和服务级别协议](../storage/common/storage-redundancy.md)

- [管理 Azure Blob 存储生命周期](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Azure 存储 Blob 的软删除](../storage/blobs/soft-delete-blob-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：使用 Azure 资源管理器部署认知服务及相关资源。 Azure 资源管理器提供导出模板的功能，这样你能在整个开发生命周期内重新部署解决方案，确保以一致的状态部署资源。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。 在 Azure Key Vault 中备份预共享密钥。

- [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

- [如何使用 Azure 资源管理器模板创建认知服务资源](https://docs.microsoft.com/azure/cognitive-services/resource-manager-template?tabs=portal)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [资源组 - 导出模板](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自动化简介](../automation/automation-intro.md)

- [如何在 Azure 中备份密钥保管库密钥](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：确保能够定期将 Azure 资源管理器模板的部署定期执行到隔离订阅（如果需要）。 测试对备份的预共享密钥进行的还原。

- [使用 ARM 模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)

- [如何在 Azure 中还原密钥保管库密钥](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：使用 Azure DevOps 安全地存储和管理 Azure 资源管理器模板。 若要保护在 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或 Azure AD (Azure Active Directory 中定义的组) （如果与 Azure DevOps 集成），或 Active Directory 与 Team Foundation Server 集成。

使用 Azure 基于角色的访问控制保护客户管理的密钥。 在密钥保管库中启用软删除和清除保护，以防止意外删除或恶意删除密钥。 

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

- [Azure 存储 Blob 的软删除](../storage/blobs/soft-delete-blob-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [你还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。 

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

- [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**责任**：客户

**Azure 安全中心监视**：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。  事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 你可以使用安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

**Azure 安全中心监视**：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
