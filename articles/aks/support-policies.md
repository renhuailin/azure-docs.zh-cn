---
title: Azure Kubernetes 服务 (AKS) 的支持策略
description: 了解 Azure Kubernetes 服务 (AKS) 预览版（或者 alpha 或 beta 版）中的支持策略、共担责任和功能。
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 1e85effa5a8efca59bfc88aada050ee5ddcc7fcf
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666434"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes 服务的支持策略

本文提供有关 Azure Kubernetes 服务 (AKS) 的技术支持策略和限制的详细信息。 本文还详细介绍了代理节点管理、托管控制平面组件、第三方开源组件，以及安全性或补丁管理。

## <a name="service-updates-and-releases"></a>服务更新和版本

* 有关发行信息，请参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)。
* 有关预览版功能的信息，请参阅 [AKS 预览版功能和相关项目](https://awesomeopensource.com/projects/aks?categoryPage=11)。

## <a name="managed-features-in-aks"></a>AKS 中的托管功能

通过基本的基础结构即服务 (IaaS) 云组件（例如计算或网络组件），可以访问低级别的控件机制和自定义选项。 相比之下，AKS 提供统包式的 Kubernetes 部署，为你的群集提供了一组所需的通用配置和功能。 作为 AKS 用户，你可选的自定义和部署选项并不完整。 但在这种情况下，你无需考虑或无需直接管理 Kubernetes 群集。

借助 AKS，可以获取一个完全托管的控制平面。 该控制平面包含执行操作并向最终用户提供 Kubernetes 群集所需的所有组件和服务。 所有 Kubernetes 组件都由 Microsoft 维护和操作。

Microsoft 通过控制窗格管理和监视以下组件：

* Kubelet 或 Kubernetes API 服务器
* Etcd 或兼容的键-值存储，提供服务质量 (QoS)、可伸缩性和运行时
* DNS 服务（例如 kube-dns 或 CoreDNS）
* Kubernetes 代理或网络
* 在 kube-system 命名空间中运行的任何其他加载项或系统组件

AKS 不是平台即服务 (PaaS) 解决方案。 某些组件（例如代理节点）实行分担责任制，用户需要帮助维护 AKS 群集。 例如，必须提供用户输入才能应用代理节点操作系统 (OS) 安全补丁。

这些服务是托管的，因为 Microsoft 和 AKS 团队负责部署和操作服务，并负责维护服务的可用性和功能。 客户无法更改这些托管组件。 Microsoft 限制了自定义，以确保一致且可缩放的用户体验。 如需一个可以完全地进行自定义的解决方案，请参阅 [AKS 引擎](https://github.com/Azure/aks-engine)。

## <a name="shared-responsibility"></a>共担责任

创建群集时，你将定义 AKS 创建的 Kubernetes 代理节点。 你的工作负载将在这些节点上执行。

由于代理节点会执行专用代码并存储敏感数据，Microsoft 支持部门只能以非常受限的方式访问这些信息。 在未得到你的明确许可或者协助的情况下，Microsoft 支持部门不能登录到这些节点、在其中执行命令或查看其日志。

使用任何 IaaS API 直接对代理节点进行的任何修改都将导致群集不受支持。 对代理节点进行的任何修改都必须使用本机 Kubernetes 机制（如 `Daemon Sets`）来完成。

同样，虽然你可以将任何元数据添加到群集和节点（如标记和标签），但更改任何系统创建的元数据都将导致群集不受支持。

## <a name="aks-support-coverage"></a>AKS 支持范围

Microsoft 为以下示例提供技术支持：

* 连接到 Kubernetes 服务提供和支持的所有 Kubernetes 组件，例如 API 服务器。
* Kubernetes 控制平面服务（例如 Kubernetes 控制平面、API 服务器、etcd 和 coreDNS）的管理、运行时间、QoS 和操作。
* Etcd 数据存储。 支持包括每隔 30 分钟以透明方式自动备份所有 etcd 数据，以实现灾难规划和群集状态还原。 你或任何用户都不可直接使用这些备份。 这些备份用于确保数据的可靠性和一致性。 Etcd。 不支持按需回滚或还原功能。
* 适用于 Azure 云提供程序驱动程序中的任何集成点。 这包括与负载均衡器、永久性卷或网络组件（Kubernetes 和 Azure CNI）等其他 Azure 服务的集成。
* 有关控制平面组件（例如 Kubernetes API 服务器、etcd 和 coreDNS）的自定义的问题。
* 有关网络组件（例如 Azure CNI、kubenet）的问题，或其他网络访问和功能问题。 问题可能包括 DNS 解析、数据包丢失、路由，等等。 Microsoft 为各种网络方案提供支持：
  * 使用托管 VNET 或自定义（自带）子网的 Kubenet 和 Azure CNI。
  * 连接到其他 Azure 服务和应用程序
  * 入口控制器以及入口或负载均衡器配置
  * 网络性能和延迟
  * [网络策略](use-network-policies.md#differences-between-azure-and-calico-policies-and-their-capabilities)


> [!NOTE]
> Microsoft/AKS 所执行的任何群集操作都是经用户同意，在内置 Kubernetes 角色 `aks-service` 和内置角色绑定 `aks-service-rolebinding` 下执行的。 此角色允许 AKS 对群集问题进行故障排除和诊断，但不能修改权限，也不能创建角色或角色绑定，或者执行其他高特权操作。 仅在具有实时 (JIT) 访问权限的活动支持票证下启用角色访问。

Microsoft 不为以下示例提供技术支持：

* 有关如何使用 Kubernetes 的问题。 例如，对于如何创建自定义入口控制器、如何使用应用程序工作负荷，或者如何应用第三方的或开源的软件包或工具，Microsoft 支持部门不提供建议。
  > [!NOTE]
  > Microsoft 支持部门可以针对 AKS 群集功能、自定义和优化（例如，Kubernetes 操作问题和过程）提供建议。
* 未作为 Kubernetes 控制平面的一部分提供的、也不是随 AKS 群集一起部署的第三方开源项目。 这些项目可能包括 Istio、Helm、Envoy 等等。
  > [!NOTE]
  > Microsoft 会尽力为 Helm 之类的第三方开源项目提供支持。 当第三方开源工具与 Kubernetes 集成时，如果出现了特定于 Azure 云提供商或其他 AKS 的 bug，Microsoft 会为来自 Microsoft 文档的示例和应用程序提供支持。
* 第三方闭源软件。 此类软件可能包括安全扫描工具以及网络设备或软件。
* [AKS 文档](./index.yml)中未列出的网络自定义。


## <a name="aks-support-coverage-for-agent-nodes"></a>针对代理节点的 AKS 支持范围

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Microsoft 对 AKS 代理节点的责任

在以下情况下，由 Microsoft 和用户共同承担 Kubernetes 代理节点的责任：

* 基本 OS 映像收到了必需的新增功能（例如监视和网络代理）。
* 代理节点自动收到了 OS 补丁。
* 在代理节点上运行的 Kubernetes 控制平面组件的问题会自动修复。 组件包括以下各项：
  * `Kube-proxy`
  * 为 Kubernetes 主控组件提供通信路径的网络隧道
  * `Kubelet`
  * Docker 或 `containerd`

> [!NOTE]
> 如果代理节点不可操作，AKS 可能会重启单个组件或整个代理节点。 这些重启操作会自动执行，并为常见问题提供自动修正。 如果要了解有关自动修正机制的详细信息，请参阅[节点自动修复](node-auto-repair.md)

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>客户对 AKS 代理节点的责任

Microsoft 每周为你的映像节点提供补丁和新映像，但默认情况下不会自动对其进行修补。 要保持对代理节点 OS 和运行时组件进行修补，应定期执行[节点映像升级](node-image-upgrade.md)计划或使其自动化。

同样，AKS 会定期发布新的 Kubernetes 补丁和次要版本。 这些更新可能包含 Kubernetes 的安全或功能改进。 你负责根据 [AKS Kubernetes 支持版本策略](supported-kubernetes-versions.md)来持续更新集群的 Kubernetes 版本。

#### <a name="user-customization-of-agent-nodes"></a>代理节点的用户自定义
> [!NOTE]
> AKS 代理节点在 Azure 门户中显示为常规 Azure IaaS 资源。 但是这些虚拟机被部署到自定义的 Azure 资源组（通常前缀为 MC_\*）中。 不能更改基础 OS 映像，或使用 IaaS API 或资源对这些节点进行任何直接的自定义。 非通过 AKS API 执行的任何自定义更改都无法在升级、缩放、更新或重启后保留。 除非 Microsoft 支持指示你进行更改，否则请避免更改代理节点。

AKS 代表你管理代理节点的生命周期和操作 - **不支持** 修改与该代理节点关联的 IaaS 资源。 不支持的操作的一个示例是通过虚拟机规模集门户或 API 手动更改配置来自定义节点池虚拟机规模集。
 
对于工作负载特定的配置或包，AKS 建议使用 [Kubernetes `daemon sets`](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)。

使用 Kubernetes 特权 `daemon sets` 和 init 容器，可以在群集代理节点上优化/修改或安装第三方软件。 此类自定义的示例包括添加自定义安全扫描软件或更新 sysctl 设置。

虽然当满足上述要求时，这是建议的路径，但 AKS 工程和支持人员无法协助排查或诊断导致节点因自定义部署 `daemon set` 而不可用的修改。

### <a name="security-issues-and-patching"></a>安全问题和修补

如果在 AKS 的一个或多个托管的组件中发现了安全缺陷，则 AKS 团队将修补所有受影响的群集以缓解此问题。 或者，AKS 团队将为用户提供升级指导。

对于受安全缺陷影响的代理节点，Microsoft 会将有关影响的详细信息以及解决或缓解安全问题的步骤（通常是节点映像升级或群集补丁升级）以通知的形式告知你。

### <a name="node-maintenance-and-access"></a>节点维护和访问

尽管可以登录并更改代理节点，但不建议这样做，因为更改可能会导致群集不受支持。

## <a name="network-ports-access-and-nsgs"></a>网络端口、访问和 NSG

只能在自定义子网中自定义 NSG。 不能在托管子网上或代理节点的 NIC 级别上自定义 NSG。 AKS 对特定终结点有流出量要求，目的是控制流出量并确保必要的连接性，请参阅[限制出口流量](limit-egress-traffic.md)。 对于流入量，要求基于已部署到群集的应用程序。

## <a name="stopped-or-de-allocated-clusters"></a>停止或取消分配的群集

如前所述，通过 IaaS API/CLI/门户以手动方式将所有群集节点取消分配会导致群集不受支持。 停止/取消分配所有节点的唯一受支持的方法是[停止 AKS 群集](start-stop-cluster.md#stop-an-aks-cluster)，这样就可以保留群集状态最多 12 个月。

停止时间超过 12 个月的群集将不再保留状态。 

在 AKS API 外取消分配的群集不保证状态保留。 处于此状态的群集控制平面将在 30 天后存档，并在 12 个月后删除。

对于已配置了“停止支持”规则以将支持期限延长至等于或超过 30 天的控制平面，AKS 保留了将其存档的权利。 AKS 维护群集 etcd 元数据的备份，并可轻松地重新分配群集。 此重新分配可以由任何使群集重获支持的 PUT 操作（例如升级或缩放到活动代理节点）启动。

如果订阅被暂停或删除，则群集的控制平面和状态将在 90 天后删除。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不受支持的 alpha 和 beta Kubernetes 功能

AKS 仅支持上游 Kubernetes 项目中的稳定和 beta 版功能。 除非另有说明，否则，AKS 不支持上游 Kubernetes 项目中可用的任何 alpha 功能。

## <a name="preview-features-or-feature-flags"></a>预览功能或功能标志

对于需要进一步测试并收集用户反馈的功能，Microsoft 会发布新的预览版功能或带功能标志的功能。 请将这些功能视为预发行版或 beta 功能。

预览功能或功能标志功能不适用于生产环境。 API 和行为的不断变化、bug 修复和其他更改可能会导致群集不稳定和停机。

公共预览版中的功能受到“尽力而为”支持，因为这些功能处于预览状态，而不是用于生产环境，并且仅在工作时间由 AKS 技术支持团队提供支持。 有关详细信息，请参阅：

* [Azure 支持常见问题](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>上游 bug 和问题

由于上游 Kubernetes 项目的开发速度，不可避免地会出现 bug。 其中的某些 bug 无法在 AKS 系统内部得到修补或解决。 相反，bug 修复需要对上游项目（例如 Kubernetes、节点或代理操作系统及内核）应用更大的补丁。 对于 Microsoft 拥有的组件（例如 Azure 云提供商），AKS 和 Azure 人员致力于在社区中修复上游问题。

如果某个技术支持问题从根本上来说是一个或多个上游 bug 导致的，则 AKS 支持和工程团队会采取以下措施：

* 使用任何支持详细信息来识别并链接上游 bug，以帮助解释为何此问题会影响群集或工作负荷。 客户会收到所需存储库的链接，以便可以观察问题，并了解何时有新的版本可以提供修复措施。
* 提供可能的解决方法或缓解措施。 如果该问题可以缓解，则会在 AKS 存储库中创建[已知问题](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)的备案。 已知问题备案将会解释：
  * 该问题，包括上游 bug 的链接。
  * 解决方法，以及有关解决方案升级或其他持久化措施的详细信息。
  * 问题包含内容的大致时间线，根据上游版本发布频率提供。
