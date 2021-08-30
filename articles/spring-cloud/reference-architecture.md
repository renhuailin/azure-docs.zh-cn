---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: karlerickson
title: Azure Spring Cloud 参考体系结构
ms.author: akaleshian
ms.service: spring-cloud
description: 此参考体系结构是使用典型企业中心和分支设计的基础，以便使用 Azure Spring Cloud。
ms.openlocfilehash: f0cc7a1345ff15a63c7cb9b0ebca51863fdf2791
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271213"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Azure Spring Cloud 参考体系结构

此参考体系结构是使用典型企业中心和分支设计的基础，以便使用 Azure Spring Cloud。 此结构的设计将 Azure Spring Cloud 部署在一个分支中，该分支依赖于中心托管的共享服务。 此体系结构由组件构建而成，以实现 [Microsoft Azure 架构良好的框架][16]中的原则。

有关此体系结构的实现，请参阅 GitHub 上的 [Azure Spring Cloud 参考体系结构][10]存储库。

此体系结构的部署选项包括 Azure 资源管理器 (ARM)、Terraform、Azure CLI 和 Bicep。 此存储库中的项目提供了可以对环境进行自定义的基础。 可以将 Azure 防火墙或应用程序网关等资源分组到不同的资源组或订阅中。 这种分组有助于区分不同的功能，如 IT 基础结构、安全性、业务应用程序团队等。

## <a name="planning-the-address-space"></a>规划地址空间

Azure Spring Cloud 需要两个专用子网：

* 服务运行时
* Spring Boot 应用程序

其中每个子网都需要一个专用群集。 多个群集不能共享相同的子网。 每个子网的最小大小为/28。 Azure Spring Cloud 可支持的应用程序实例数因子网大小而异。 可以在[在虚拟网络中部署 Azure Spring Cloud][17]的[虚拟网络要求][11]部分中找到详细的虚拟网络 (VNET) 要求。

> [!WARNING]
> 所选的子网大小不能与现有的 VNET 地址空间重叠，也不应与任何对等或本地子网地址范围重叠。

## <a name="use-cases"></a>用例

此体系结构的典型用途包括：

* 在混合云环境中部署的内部应用程序
* 面向外部的应用程序

这些用例除安全性和网络流量规则外都相似。 此体系结构旨在支持各个用例的细微差别。

## <a name="private-applications"></a>专用应用程序

以下列表描述了专用应用程序的基础结构要求。 这些要求在高度管控环境中较为典型。

* 除控制平面流量外不会直接流出到公共互联网。
* 出口流量应通过中央网络虚拟设备 (NVA)（例如 Azure 防火墙）。
* 静态数据应当加密。
* 传输中的数据应当加密。
* 可以使用 DevOps 部署管道（例如 Azure DevOps），管道要求与 Azure Spring Cloud 建立网络连接。
* Microsoft 的零信任安全方法要求将机密、证书和凭据存储在安全的保管库中。 建议的服务为 Azure Key Vault。
* 应用程序主机域名服务 (DNS) 记录应存储在 Azure 专用 DNS 中。
* 本地和云中的主机名称解析应是双向的。
* 应强制遵循至少一个安全基准。
* Azure 服务依赖关系应通过服务终结点或专用链接进行通信。
* 不能修改由 Azure Spring Cloud 部署管理的资源组。
* 不能修改由 Azure Spring Cloud 部署管理的子网。
* 子网必须只有一个 Azure Spring Cloud 实例。
* 如果使用 [Azure Spring Cloud Config Server][8] 从存储库加载配置属性，则该存储库必须是专用的。

以下列表显示了组成设计的组件：

* 本地网络
  * 域名服务 (DNS)
  * 网关
* 中心订阅
  * 防火墙子网
  * 应用程序网关子网
  * 共享服务子网
* 已连接订阅
  * 虚拟网络对等
  * Azure Bastion 子网

以下列表描述了此参考体系结构中的 Azure 服务：

* [Azure Spring Cloud][1]：一种托管服务，专为基于 Java 的 Spring Boot 应用程序和基于 .NET 的 [Steeltoe][9] 应用程序进行设计和优化。

* [Azure Key Vault][2]：与 Microsoft 标识服务和计算资源紧密集成的硬件支持的凭据管理服务。

* [Azure Monitor][3]：一套全面的监视服务，适用于在 Azure 和本地部署的应用程序。

* [Azure 安全中心][4]：统一的安全管理和威胁防护系统，用于跨本地、多个云和 Azure 的工作负载。

* [Azure Pipelines][5]：可自动将更新的 Spring Boot 应用部署到 Azure Spring Cloud 的功能齐全的持续集成/持续开发 (CI/CD) 服务。

下图表示一种结构良好的中心和分支设计，用于满足上述要求：

![专用应用程序的参考体系结构图](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>公共应用程序

以下列表描述了公共应用程序的基础结构要求。 这些要求在高度管控环境中较为典型。

* 入口流量应至少由应用程序网关或 Azure Front Door 管理。
* 应启用 Azure DDoS 防护标准。
* 除控制平面流量外不会直接流出到公共互联网。
* 出口流量应通过中央网络虚拟设备 (NVA)（例如 Azure 防火墙）。
* 静态数据应当加密。
* 传输中的数据应当加密。
* 可以使用 DevOps 部署管道（例如 Azure DevOps），管道要求与 Azure Spring Cloud 建立网络连接。
* Microsoft 的零信任安全方法要求将机密、证书和凭据存储在安全的保管库中。 建议的服务为 Azure Key Vault。
* 应用程序主机 DNS 记录应存储在 Azure 专用 DNS 中。
* Internet 可路由地址应存储在 Azure 公共 DNS 中。
* 本地和云中的主机名称解析应是双向的。
* 应强制遵循至少一个安全基准。
* Azure 服务依赖关系应通过服务终结点或专用链接进行通信。
* 不能修改由 Azure Spring Cloud 部署管理的资源组。
* 不能修改由 Azure Spring Cloud 部署管理的子网。
* 子网必须只有一个 Azure Spring Cloud 实例。

以下列表显示了组成设计的组件：

* 本地网络
  * 域名服务 (DNS)
  * 网关
* 中心订阅
  * 防火墙子网
  * 应用程序网关子网
  * 共享服务子网
* 已连接订阅
  * 虚拟网络对等
  * Azure Bastion 子网

以下列表描述了此参考体系结构中的 Azure 服务：

* [Azure Spring Cloud][1]：一种托管服务，专为基于 Java 的 Spring Boot 应用程序和基于 .NET 的 [Steeltoe][9] 应用程序进行设计和优化。

* [Azure Key Vault][2]：硬件支持的凭据管理服务，与 Microsoft 标识服务和计算资源紧密集成。

* [Azure Monitor][3]：一套全面的监视服务，适用于在 Azure 和本地部署的应用程序。

* [Azure 安全中心][4]：统一的安全管理和威胁防护系统，用于跨本地、多个云和 Azure 的工作负载。

* [Azure Pipelines][5]：可自动将更新的 Spring Boot 应用部署到 Azure Spring Cloud 的功能齐全的持续集成/持续开发 (CI/CD) 服务。

* [Azure 应用程序网关][6]：负责应用程序流量的负载均衡器，其中传输层安全性 (TLS) 卸载在第 7 层上运行。

* [Azure 应用程序防火墙][7]：Azure 应用程序网关的功能，可以对应用程序进行集中保护，避免其受到常见的攻击和漏洞伤害。

下图表示一种结构良好的中心和分支设计，用于满足上述要求：

![公共应用程序的参考体系结构图](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Azure Spring Cloud 本地连接

在 Azure Spring Cloud 中运行的应用程序可以与 Azure、本地和外部资源等多种资源通信。 通过使用中心和分支设计，应用程序可以使用 Express Route 或站点到站点虚拟专用网 (VPN)，在外部路由流量或将流量路由到本地网络。

## <a name="azure-well-architected-framework-considerations"></a>Azure 架构良好的框架注意事项

[Azure 架构良好的框架][16]是建立强健的基础结构基础时要遵循的一系列指导原则。 此框架包含以下类别：成本优化、卓越运营、性能效率、可靠性和安全性。

### <a name="cost-optimization"></a>成本优化

由于分布式系统设计的特性，基础结构的扩展成为现实。 这种现实会导致意外的不可控成本。 Azure Spring Cloud 使用可扩展的组件构建，以便能够满足需求并优化成本。 此体系结构的核心是 Azure Kubernetes 服务 (AKS)。 此服务旨在降低管理 Kubernetes 的复杂性和运营开销，其中包括提高群集的运营成本效率。

可以将不同的应用程序和应用程序类型部署到 Azure Spring Cloud 中的单个实例。 该服务支持由指标或计划触发的应用程序的自动缩放，可提高利用率和成本效益。

还可以使用 Application Insights 和 Azure Monitor 降低运营成本。 利用综合性日志记录解决方案提供的可见性来实现自动化，以实时扩展系统的组件。 还可以分析日志数据，以显示应用程序代码中的低效情况，对其加以解决来改善系统的整体成本和性能。

### <a name="operational-excellence"></a>卓越运营

Azure Spring Cloud 解决了卓越运营的多方面内容。 可以将这些方面结合起来，以确保服务在生产环境中有效运行，如以下列表所述：

* 可以使用 Azure Pipelines 确保部署的可靠性和一致性，同时帮助避免人为错误。
* 可以使用 Azure Monitor 和 Application Insights 来存储日志和遥测数据。
  可以评估收集的日志和指标数据，以确保应用程序的运行状况和性能。 应用程序性能监视 (APM) 通过 Java 代理完全集成到服务中。 此代理可让你了解所有已部署的应用程序和依赖关系，而无需额外的代码。 有关详细信息，请参阅博客文章[在 Azure Spring Cloud 中轻松监视应用程序和依赖关系][15]。
* 可以使用 Azure 安全中心来确保应用程序的安全性，安全中心提供了分析和评估所提供数据的平台。
* 该服务支持各种部署模式。 有关详细信息，请参阅[在 Azure Spring Cloud 中设置过渡环境][14]。

### <a name="reliability"></a>可靠性

Azure Spring Cloud 的设计以 AKS 作为基础组件。 尽管 AKS 通过群集提供了一定程度的复原能力，但此参考体系结构包含了服务和体系结构注意事项，以便在出现组件故障时提高应用程序的可用性。

此体系结构在定义完善的中心和分支设计之上构建，结构基础可确保你将其部署到多个区域。 对于专用应用程序用例，此体系结构使用 Azure 专用 DNS 来确保其在发生地理故障时继续可用。 对于公共应用程序用例，Azure Front Door和 Azure 应用程序网关可以确保可用性。

### <a name="security"></a>安全性

此体系结构的安全性通过遵守行业定义的控件和基准得到解决。 此体系结构的控件来自[云安全联盟][18] (CSA) 的[云控件矩阵][19] (CCM)和[互联网安全中心][21] (CIS) 的 [Microsoft Azure 基础基准][20] (MAFB)。 在所应用的控件中，重点是管理、网络和应用程序安全的主要安全设计原则。 你负责处理标识、访问管理和存储的设计原则，因为它们与目标基础结构相关。

#### <a name="governance"></a>调控

在管理方面，此体系结构解决的主要问题是通过隔离网络资源进行分离。 在 CCM 中，DCS-08 建议为数据中心提供入口和出口控制。 为了满足控制需要，此体系结构使用网络安全组 (NSG) 的中心和分支设计来筛选资源之间的东西向流量。 此体系结构还筛选中心的中心服务和分支资源之间的流量。 此体系结构使用 Azure 防火墙的实例管理互联网和体系结构中的资源之间的流量。

以下列表显示了此引用中用于解决数据中心安全的控件：

| CSA CCM 控制 ID | CSA CCM 控制域 |
| :----------------- | :----------------------|
| DCS-08 | 数据中心安全未授权人员条目 |

#### <a name="network"></a>网络

支持此体系结构的网络设计从传统的中心和分支模型派生而来。 此决定确保网络隔离成为基础结构。 CCM 控制 IVS-06 建议在受信任和不受信任的环境之间限制和监视网络和虚拟机之间的流量。 此体系结构通过实现 NSG 控制东西流量，通过 Azure 防火墙来控制南北流量。 CCM 控制 IPY-04 建议基础结构应使用安全的网络协议来进行服务之间的数据交换。 支持此体系结构的 Azure 服务都使用标准安全协议，如 HTTP 和 SQL 的 TLS。

以下列表显示了在此引用中解决网络安全的 CCM 控制：

| CSA CCM 控制 ID | CSA CCM 控制域 |
| :----------------- | :----------------------|
| IPY-04             | 网络协议      |
| IVS-06             | 网络安全       |

通过从 MAFB 定义控件，进一步保护网络实现。 控件可确保进入环境的流量受到来自公共互联网的限制。

以下列表显示了在此引用中解决网络安全的 CCM 控件：

| CIS 控件 ID | CIS 控件说明 |
| :------------- | :---------------------- |
| 6.2 | 确保限制从 Internet 进行的 SSH 访问。 |
| 6.3 | 确保任何 SQL 数据库都不允许流入量 0.0.0.0/0 (任何 IP)。 |
| 6.5 | 确保网络观察程序设置为“已启用”。 |
| 6.6 | 确保使用 UDP 的入口受到互联网限制。 |

Azure Spring Cloud 被部署到受保护的环境中时，要求管理流量从 Azure 传出。 若要实现此目的，必须允许应用[在 VNET 中运行 Azure Spring Cloud 的客户责任](./vnet-customer-responsibilities.md)列出的网络和应用程序规则。

#### <a name="application-security"></a>应用程序安全性

此设计主体涵盖标识、数据保护、密钥管理和应用程序配置的基本组件。 根据设计，Azure Spring Cloud 中部署的应用程序以其正常运行所需的最低权限运行。 使用该服务时，授权控件集与数据保护直接相关。 密钥管理加强了这一分层应用程序安全方法。

以下列表显示了在此引用中解决密钥管理的 CCM 控制：

| CSA CCM 控制 ID | CSA CCM 控制域 |
| :----------------- | :--------------------- |
| EKM-01 | 加密和密钥管理权利 |
| EKM-02 | 加密和密钥管理密钥生成 |
| EKM-03 | 加密和密钥管理敏感数据保护 |
| EKM-04 | 加密和密钥管理存储及权限 |

从 CCM、EKM-02 和 EKM-03 的建议策略和过程到管理密钥及使用加密协议来保护敏感数据。 EKM-01 建议所有加密密钥都拥有可识别的所有者，以便对其进行管理。 EKM-04 建议使用标准算法。

以下列表显示了在此引用中解决密钥管理的 CIS 控制：

| CIS 控件 ID | CIS 控件说明 |
| :------------- | :---------------------- |
| 8.1 | 确保已对所有密钥设置到期日期。 |
| 8.2 | 确保已对所有机密设置到期日期。 |
| 8.4 | 确保 Key Vault 可恢复。 |

CIS 控件 8.1 和 8.2 建议为凭据设置过期日期，以确保强制执行轮换。 CIS 控制 8.4 确保可以还原密钥保管库的内容，以维护业务连续性。

应用程序安全的各个方面为使用此参考体系结构以支持 Azure 中的 Spring 工作负载奠定了基础。

## <a name="next-steps"></a>后续步骤

通过[Azure Spring Cloud 参考体系结构][10]存储库中提供的 ARM、Terraform 和 Azure CLI 部署探索此参考体系结构。

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./how-to-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./how-to-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/
