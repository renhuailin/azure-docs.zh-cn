---
title: 针对 Azure 资产的安全性最佳做法
titleSuffix: Azure security
description: 本文提供了用于保护 Azure 中的数据、应用程序和其他资产的一系列操作最佳做法。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: e6bfa172624ba65c161abcba6dfd97b557fb8d91
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950462"
---
# <a name="azure-operational-security-best-practices"></a>Azure 操作安全性最佳做法
本文提供了用于保护 Azure 中的数据、应用程序和其他资产的一系列操作最佳做法。

最佳做法以观点的共识以及 Azure 平台功能和特性集为基础。 观点和技术将随着时间改变，本文会定期更新以反映这些更改。

## <a name="define-and-deploy-strong-operational-security-practices"></a>定义并部署强大的操作安全做法
Azure 操作安全性是指用户可用于在 Azure 中保护其数据、应用程序和其他资产的服务、控件和功能。 Azure 操作安全性建立在一个框架上，该框架融合了通过 Microsoft 独有的功能获得的知识，包括 [安全开发生命周期 (SDL)](https://www.microsoft.com/sdl)、[Microsoft 安全响应中心](https://www.microsoft.com/msrc?rtc=1)计划以及对网络安全威胁形态的深刻认识。

## <a name="manage-and-monitor-user-passwords"></a>管理和监视用户密码
下表列出了与管理用户密码相关的一些最佳做法：

**最佳做法**：确保你在云中具有适当级别的密码保护。   
**详细信息**：按照 [Microsoft 密码指南](https://www.microsoft.com/research/publication/password-guidance/)中的指南进行操作，该指南的适用范围是 Microsoft 标识平台（Azure Active Directory、Active Directory 和 Microsoft 帐户）的用户。

最佳做法：监视与用户帐户相关的可疑操作。   
详细信息：使用 Azure AD 安全报告监视[存在风险的用户](../../active-directory/identity-protection/overview-identity-protection.md)和[存在风险的登录](../../active-directory/identity-protection/overview-identity-protection.md)。

最佳做法：自动检测和修正高风险密码。   
详细信息：[Azure AD 标识保护](../../active-directory/identity-protection/overview-identity-protection.md) 是 Azure AD Premium P2 版本的功能，支持：

- 检测影响组织标识的潜在漏洞
- 配置自动响应，可检测与组织标识相关的可以操作
- 调查可疑事件，并采取适当的措施进行解决

## <a name="receive-incident-notifications-from-microsoft"></a>接收来自 Microsoft 的事件通知
确保你的安全运营团队接收来自 Microsoft 的 Azure 事件通知。 事件通知让你的安全团队知道你已经破坏了某个 Azure 资源，目的是让他们可以快速响应并修正潜在的安全风险。

在 Azure 注册门户中，你可以确保管理员联系信息包含用来进行安全操作通知的详细信息。 联系人详细信息为电子邮件地址和电话号码。

## <a name="organize-azure-subscriptions-into-management-groups"></a>将 Azure 订阅组织到管理组中
如果你的组织有多个订阅，则可能需要一种方法来高效地管理这些订阅的访问权限、策略和符合性。 [Azure 管理组](../../governance/management-groups/create-management-group-portal.md)提供的范围级别高于订阅。 可将订阅组织到名为“管理组”的容器中，并将治理条件应用到管理组。 管理组中的所有订阅都将自动继承应用于管理组的条件。

可以在目录中构建管理组和订阅的灵活结构。 为每个目录指定了一个称为根管理组的顶级管理组。 此根管理组内置在层次结构中，包含其所有下级管理组和订阅。 该根管理组允许在目录级别应用全局策略和 Azure 角色分配。

下面是管理组使用方面的一些最佳做法：

**最佳做法**：确保在添加新订阅时，它们会应用治理元素，例如策略和权限。   
**详细信息**：使用根管理组分配适用于所有 Azure 资产的企业范围的安全元素。 策略和权限是元素的示例。

**最佳做法**：将顶级管理组与分段策略匹配，以便在每个段中实现控制和策略一致性。   
**详细信息**：在根管理组下为每个段创建一个管理组。 请勿在根下创建任何其他管理组。

**最佳做法**：限制管理组深度，以避免出现影响操作和安全性的混乱。   
**详细信息**：将层次结构限制为三个级别（包括根在内）。

**最佳做法**：使用根管理组，仔细选择要应用于整个企业的项。   
**详细信息**：确保根管理组元素明确需要在每个资源中应用，并且它们的影响很小。

典型的候选项包括：

- 具有明确业务影响的法规要求（例如，与数据主权相关的限制）
- 对操作几乎没有潜在负面影响的要求，例如，其审核效果或 Azure RBAC 权限分配已经过仔细审查的策略

**最佳做法**：在根管理组上应用所有企业范围的更改（策略、Azure RBAC 模型等）之前，请仔细规划并测试它们。   
**详细信息**：根管理组中的更改可能会影响 Azure 上的每个资源。 尽管它们提供了一种强大的方法来确保整个企业中的一致性，但错误或不正确的使用可能会对生产操作产生负面影响。 请在测试实验室或生产试点中测试对根管理组的所有更改。

## <a name="streamline-environment-creation-with-blueprints"></a>利用蓝图简化环境创建
通过 [Azure 蓝图](../../governance/blueprints/overview.md)服务，云架构师和中心信息技术组同样可以定义一组可重复的 Azure 资源，这些资源实现并遵守组织的标准、模式和要求。 使用 Azure 蓝图，开发团队可以快速生成新的环境并将其放在新的环境中，并提供一套内置组件，并确保他们在组织符合性中创建这些环境。

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>监视存储服务的意外行为更改
诊断和排查在云环境中托管的分布式应用程序中的问题可能会比在传统环境中更复杂。 应用程序可以部署在 PaaS 或 IaaS 基础结构、本地、移动设备，或这些环境的某种组合中。 应用程序的网络流量可能会遍历公用和专用网络，你的应用程序可能使用多种存储技术。

应持续监视应用程序使用的存储服务是否存在任何意外行为更改（如响应时间变长）。 若要收集更详细的数据和深度分析问题，请使用日志记录。 从监视和日志记录获取的诊断信息将有助于确定应用程序所遇到问题的根本原因。 然后，可以排查该问题，并确定修正问题的相应步骤。

[Azure 存储分析](../../storage/common/storage-analytics.md)执行日志记录并为 Azure 存储帐户提供指标数据。 建议使用此数据跟踪请求、分析使用情况趋势以及诊断存储帐户的问题。

## <a name="prevent-detect-and-respond-to-threats"></a>防范、检测和应对威胁
[Azure 安全中心](../../security-center/security-center-introduction.md)增强了对 Azure 资源安全的可见性和可控性，可帮助你预防、检测和响应威胁。 它提供对 Azure 订阅的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于各种安全解决方案。

安全中心的免费层仅为 Azure 资源提供有限的安全性。 标准层将这些功能扩展到本地和其他云中。 借助安全中心标准层，可以查找和修复安全漏洞、应用访问控制和应用程序控制来阻止恶意活动、使用分析和智能功能检测威胁，以及在受到攻击时迅速做出响应。 可以尝试安全中心标准版，头 60 天免费。 建议[将 Azure 订阅升级到安全中心标准层](../../security-center/security-center-get-started.md)。

使用安全中心，可以通过一个集中化视图查看所有 Azure 资源的安全状态。 一眼就可验证适当的安全控件是否配置到位且配置正确，还可快速确认任何需要注意的资源。

安全中心还集成了 [Microsoft Defender 高级威胁防护 (ATP)](../../security-center/security-center-wdatp.md)，后者提供了完善的终结点检测和响应 (EDR) 功能。 使用 Microsoft Defender ATP 集成可以查明异常。 你还可以检测和响应安全中心所监视的服务器终结点上出现的高级攻击。

几乎所有的企业组织都有一个安全信息和事件管理 (SIEM) 系统，它可以整合来自不同信号收集设备的日志信息，因此可以识别新出现的威胁。 然后，数据分析系统会对日志进行分析，以便从所有日志收集和分析解决方案的不可避免的干扰内容中找出“需关注”的内容。

[Azure Sentinel](../../sentinel/overview.md) 是可缩放的云原生安全信息和事件管理 (SIEM) 和安全业务流程自动响应 (SOAR) 解决方案。 Azure Sentinel 通过警报检测、威胁可见性、主动搜寻和自动威胁响应提供智能安全分析和威胁智能。

下面是一些用于预防、检测和响应威胁的最佳做法：

最佳做法：使用基于云的 SIEM 提高 SIEM 解决方案的速度和可伸缩性。   
详细信息：调查 [Azure Sentinel](../../sentinel/overview.md) 的特性和功能，并将其与你当前在本地使用的功能进行比较。 如果符合组织的 SIEM 要求，请考虑采用 Azure Sentinel。

**最佳做法**：找到最严重的安全漏洞，以便确定调查优先级。   
**详细信息**：查看你的 [Azure 安全评分](../../security-center/secure-score-security-controls.md)，了解 Azure 安全中心内置的 Azure 策略和计划所产生的建议。 这些建议有助于解决顶级风险，例如安全更新、终结点保护、加密、安全配置、WAF 缺失、VM 连接到 Internet 等方面的风险。

安全评分基于 Internet 安全中心 (CIS) 控件，允许你根据外部源对组织的 Azure 安全性进行基准测试。 外部验证可帮助验证并扩充团队的安全策略。

**最佳做法**：监视计算机、网络、存储和数据服务以及应用程序的安全状况，发现潜在的安全问题并确定其优先级。  
**详细信息**：按照安全中心的 [安全建议](../../security-center/security-center-recommendations.md)操作，并从优先级最高的项开始。

**最佳做法**：将安全中心警报集成到安全信息和事件管理 (SIEM) 解决方案中。   
**详细信息**：采用 SIEM 的大多数组织都使用它充当一个中心交换所来处理需要分析师响应的安全警报。 安全中心生成的事件经过处理后，将被发布到 Azure 活动日志，这是 Azure Monitor 提供的可用日志之一。 Azure Monitor 提供了一个综合管道，可将任何监视数据路由到 SIEM 工具。 有关说明，请参阅[将警报流式传输到 SIEM、SOAR 或 IT 服务管理解决方案](../../security-center/export-to-siem.md)。 如果使用的是 Azure Sentinel，请参阅[连接 Azure 安全中心](../../sentinel/connect-azure-security-center.md)。

**最佳做法**：将 Azure 日志与你的 SIEM 集成。   
**详细信息**：[使用 Azure Monitor 收集和导出数据](../../azure-monitor/overview.md#integrate-and-export-data)。 此做法对于启用安全事件调查至关重要，而在线日志保留期是有限的。 如果使用的是 Azure Sentinel，请参阅[连接数据源](../../sentinel/connect-data-sources.md)。

**最佳做法**：通过将终结点检测和响应 (EDR) 功能集成到攻击调查中，加快调查和搜寻过程，并减少误报。   
**详细信息**：通过安全中心安全策略[启用 Microsoft Defender for Endpoint 集成](../../security-center/security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)。 考虑使用 Azure Sentinel 进行威胁搜寻和事件响应。

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>监视基于端到端方案的网络监视
客户在 Azure 中通过合并虚拟网络、ExpressRoute、应用程序网关和负载均衡器等网络资源来构建端到端网络。 监视适用于每个网络资源。

[Azure 网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)是一项区域性服务。 其诊断和可视化工具可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。

以下是网络监视和可用工具的最佳做法。

**最佳做法**：使用数据包捕获实现远程网络监视的自动化。  
**详细信息**：使用网络观察程序监视和诊断网络问题，无需登录 VM。 通过设置警报触发[数据包捕获](../../network-watcher/network-watcher-alert-triggered-packet-capture.md)，并获取数据包级别上的实时性能信息访问权限。 如果遇到问题，可进行详细调查，获得更精确的诊断。

**最佳做法**：使用流日志深入了解网络流量。  
**详细信息**：使用 [网络安全组流日志](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)更深入地了解网络流量模式。 流日志中的信息可帮助收集符合性数据、审核和监视网络安全配置文件。

**最佳做法**：诊断 VPN 连接问题。  
**详细信息**：使用网络观察程序来 [诊断最常见的 VPN 网关和连接问题](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)。 不仅可以确定问题，还可以使用详细日志进一步调查。

## <a name="secure-deployment-by-using-proven-devops-tools"></a>使用经验证的 DevOps 工具确保安全部署
使用以下 DevOps 最佳做法来确保企业和团队多产且高效。

最佳做法：自动生成和部署服务。  
详细信息：[基础结构即代码](/devops/deliver/what-is-infrastructure-as-code)是一组技术和实践，使 IT 专业人员不再需要日复一日地生成和管理模块化基础结构。 使得 IT 专业人员生成和维护新式服务器环境的方式就像是软件开发人员生成和维护应用程序代码的方式。

[Azure 资源管理器](../../azure-resource-manager/templates/syntax.md)允许用户使用声明性模板预配应用程序。 在单个模板中，可以部署多个服务及其依赖项。 在应用程序生命周期的每个阶段，可使用相同模板重复部署应用程序。

最佳做法：自动生成并部署到 Azure Web 应用或云服务。  
详细信息：可以将 Azure DevOps Projects 配置为[自动生成并部署](/azure/devops/pipelines/index)到 Azure Web 应用或云服务。 Azure DevOps 在每次代码签入后对 Azure 执行一次生成，然后自动部署二进制文件。 包生成过程与 Visual Studio 中的 Package 命令等效，而发布步骤与 Visual Studio 中的 Publish 命令等效。

最佳做法：自动执行发布管理。  
详细信息：[Azure Pipelines](/azure/devops/pipelines/index) 是实现多阶段部署和管理发布过程自动化的解决方案。 创建托管的持续部署管道，快速、轻松地频繁发布。 通过 Azure Pipelines，可以使发布过程自动化，还可以拥有预定义的批准工作流。 根据需要进行本地部署和部署到云、扩展和自定义。

最佳做法：在推出应用或将更新部署到生产环境之前，先检查该应用的性能。  
详细信息：运行基于云的[负载测试](/azure/devops/test/load-test/overview#alternatives)以实现以下目标：

- 在应用中查找性能问题。
- 提高部署质量。
- 请确保应用始终可用。
- 确保应用可以处理下一次启动或市场营销活动的流量。

[Apache JMeter](https://jmeter.apache.org/) 是一个功能强大的免费开源工具，具有强大的社区支持。

最佳做法：监视应用程序性能。  
详细信息：[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用 Application Insights 来监视实时 Web 应用程序。 它会自动检测性能异常。 其中包含分析工具来帮助诊断问题，了解用户在应用中实际执行了哪些操作。 Application Insights 有助于持续提高性能与可用性。

## <a name="mitigate-and-protect-against-ddos"></a>缓解和防范 DDoS
分布式拒绝服务 (DDoS) 是企图耗尽应用程序资源的一种攻击。 其目的是影响应用程序的可用性和处理合法请求的能力。 这些攻击正变得越来越复杂，且规模和影响程度越来越高。 它们可能会将任何可通过 Internet 公开访问的终结点作为目标。

设计和构建 DDoS 复原能力需要规划和设计各种故障模式。 下面是用于在 Azure 上构建 DDoS 可复原服务的最佳做法。

最佳做法：确保优先考虑从设计和实施到部署和操作的整个应用程序生命周期的安全性。 应用程序可能包含 bug，使相对较少的请求使用过多的资源，从而导致服务中断。  
详细信息：为帮助保护 Microsoft Azure 上运行的服务，应该对应用程序体系结构有充分的了解，并重点关注[软件质量的五大要素](/azure/architecture/guide/pillars)。 应该清楚典型的流量大小、应用程序与其他应用程序之间的连接模型，以及向公共 Internet 公开的服务终结点。

至关重要的一点是，确保应用程序具有足够的弹性，可应对针对应用程序本身的拒绝服务攻击。 从[安全开发生命周期 (SDL)](https://www.microsoft.com/sdl) 开始，安全和隐私就已内置到 Azure 平台中。 SDL 可以解决每个开发阶段的安全性，并确保 Azure 不断更新，以变得越来越安全。

最佳做法：采用可[横向缩放](/azure/architecture/guide/design-principles/scale-out)的应用程序设计，以满足放大负载的需求，尤其是防范 DDoS 攻击。 如果应用程序依赖于服务的单个实例，则会造成单一故障点。 预配多个实例能够提高复原能力和可伸缩性。  
详细信息：对于 [Azure 应用服务](../../app-service/overview.md)，请选择提供多个实例的[应用服务计划](../../app-service/overview-hosting-plans.md)。

对于 Azure 云服务，请将每个角色配置为使用[多个实例](../../cloud-services/cloud-services-choose-me.md)。

对于 [Azure 虚拟机](../../virtual-machines/windows/overview.md)，请确保 VM 体系结构包含多个 VM，并且每个 VM 包含在[可用性集](../../virtual-machines/windows/tutorial-availability-sets.md)中。 建议使用虚拟机规模集来实现自动缩放功能。

最佳做法：应用程序中的分层安全防御可以减少攻击成功的可能性。 使用 Azure 平台的内置功能对其应用程序实施安全设计。  
详细信息：攻击风险会随着应用程序的规模（外围应用）的增大而增大。 可以使用审批列表关闭负载均衡器（[Azure 负载均衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)和 [Azure 应用程序网关](../../application-gateway/application-gateway-create-probe-portal.md)）上不需要的公开 IP 地址空间和侦听端口，来减少外围应用。

[网络安全组](../../virtual-network/network-security-groups-overview.md)是缩小受攻击面的另一种方法。 可以使用[服务标记](../../virtual-network/network-security-groups-overview.md#service-tags)和[应用程序安全组](../../virtual-network/network-security-groups-overview.md#application-security-groups)来最大程度地简化安全规则的创建，并将网络安全性配置为应用程序结构的自然扩展。

应尽可能地在[虚拟网络](../../virtual-network/virtual-networks-overview.md)中部署 Azure 服务。 这种做法可让服务资源通过专用 IP 地址通信。 来自虚拟网络的 Azure 服务流量默认使用公共 IP 地址作为源 IP 地址。

使用[服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)时，服务流量会在通过虚拟网络访问 Azure 服务时改用虚拟网络专用地址作为源 IP 地址。

我们经常看到，客户本地资源会连同其在 Azure 中资源的一起受到攻击。 如果将本地环境连接到 Azure，尽量不要在公共 Internet 上公开本地资源。

Azure 具有两个 DDoS [服务产品](../../ddos-protection/ddos-protection-overview.md)，提供网络攻击防护：

- 基本防护默认已集成到 Azure 中，不收取额外的费用。 全球部署的 Azure 网络的规模和容量通过始终开启的监视和实时缓解措施，来防御公用网络层攻击。 基本防护无需用户配置或应用程序更改，并帮助保护所有 Azure 服务，包括 Azure DNS 等 PaaS 服务。
- 标准防护提供针对网络攻击的高级 DDoS 缓解功能。 这些功能自动经过优化，可保护特定的 Azure 资源。 在创建虚拟网络期间，可以轻松启用保护。 也可以在创建之后启用它，而不需要对应用程序或资源做出任何更改。

## <a name="enable-azure-policy"></a>启用 Azure Policy
[Azure Policy](../../governance/policy/overview.md) 是 Azure 中的一项服务，用于创建、分配和管理策略。 这些策略将在整个资源中强制实施规则和效果，使这些资源符合公司标准和服务级别协议。 Azure Policy 通过评估资源是否符合指定策略来满足此需求。

启用 Azure Policy 来进行监视并强制实施组织的书面策略。 这样就可以集中管理混合云工作负荷中的安全策略，确保符合公司或法规安全要求。 了解如何[创建和管理策略以强制实施合规性](../../governance/policy/tutorials/create-and-manage.md)。 有关策略元素的概述，请参阅 [Azure Policy 定义结构](../../governance/policy/concepts/definition-structure.md)。

下面是在采用 Azure Policy 后要遵循的一些安全性最佳做法：

**最佳做法**：Azure Policy 支持多种类型的效果。 可以在 [Azure Policy 定义结构](../../governance/policy/concepts/definition-structure.md#policy-rule)中了解相关信息。 **拒绝** 效果和 **修正** 效果可能会给业务运营带来负面影响，因此请从 **审核** 效果开始以限制策略带来的负面影响风险。   
**详细信息**：[以审核模式开始策略部署](../../governance/policy/concepts/definition-structure.md#policy-rule)，然后推进到 **拒绝** 或 **修正**。 在推进到 **拒绝** 或 **修正** 之前，请测试并查看审核效果的结果。

有关详细信息，请参阅[创建和管理策略以强制实施符合性](../../governance/policy/tutorials/create-and-manage.md)。

**最佳做法**：确定负责监视策略违规的角色，并确保快速执行正确的修正操作。   
**详细信息**：让已分配的角色通过 [Azure 门户](../../governance/policy/how-to/get-compliance-data.md#portal)或 [命令行](../../governance/policy/how-to/get-compliance-data.md#command-line)来监视符合性。

**最佳做法**：Azure Policy 是组织的书面策略的技术表示形式。 将所有 Azure 策略定义映射到组织策略，以减少混乱并增强一致性。   
**详细信息**：通过在 [策略定义](../../governance/policy/concepts/definition-structure.md#display-name-and-description)或 [计划定义](../../governance/policy/concepts/initiative-definition-structure.md#metadata)说明中添加对组织策略的引用，在组织的文档中或 Azure Policy 定义本身中记录映射。

## <a name="monitor-azure-ad-risk-reports"></a>监视 Azure AD 风险报告
大多数安全违规出现在当攻击者通过窃取用户的标识来获取环境的访问权限时。 发现标识是否遭到入侵并不容易。 Azure AD 使用自适应机器学习算法和试探法来检测与用户帐户相关的可疑操作。 检测到的每个可疑操作都存储在称为[风险检测](../../active-directory/identity-protection/overview-identity-protection.md)的记录中。 风险检测记录在 Azure AD 安全报告中。 有关详细信息，请参阅[风险用户安全报告](../../active-directory/identity-protection/overview-identity-protection.md)和[有风险的登录安全报告](../../active-directory/identity-protection/overview-identity-protection.md)。

## <a name="next-steps"></a>后续步骤
有关通过 Azure 设计、部署和管理云解决方案时可以使用的更多安全最佳做法，请参阅 [Azure 安全最佳做法和模式](best-practices-and-patterns.md)。

以下资源提供了有关 Azure 安全性及相关 Microsoft 服务的更多常规信息：
* [Azure 安全团队博客](/archive/blogs/azuresecurity/) - 随时掌握 Azure 安全性的最新信息
* [Microsoft 安全响应中心](https://technet.microsoft.com/library/dn440717.aspx) - 可在其中报告 Microsoft 安全漏洞（包括 Azure 问题）或将其通过电子邮件发送到 secure@microsoft.com