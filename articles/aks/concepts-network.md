---
title: 概念 - Azure Kubernetes 服务 (AKS) 中的网络
description: 了解 Azure Kubernetes 服务 (AKS) 中的网络，包括 kubenet 和 Azure CNI、入口控制器、负载均衡器和静态 IP 地址。
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: bdedde17692aca614c3d33b884950a4cd01b02e4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610705"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中应用程序的网络概念

在基于容器的微服务应用程序开发方法中，应用程序组件会协同工作以处理其任务。 Kubernetes 提供了各种资源来促成这种合作：
* 可以在内部或外部连接应用程序并将其公开。 
* 可以对应用程序进行负载均衡，从而生成高可用性应用程序。 
* 对于更复杂的应用程序，可以配置 SSL/TLS 终止的入口流量或多个组件的路由。 
* 出于安全考虑，可以限制网络流量流入或 Pod 和节点之间的流量。

本文介绍了为 AKS 中的应用程序提供网络的核心概念：

- [服务](#services)
- [Azure 虚拟网络](#azure-virtual-networks)
- [入口控制器](#ingress-controllers)
- [网络策略](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes 基础知识

为允许访问应用程序或是在应用程序组件之间进行访问，Kubernetes 为虚拟网络提供了抽象层。 Kubernetes 节点连接到虚拟网络，从而为 Pod 提供入站和出站连接。 kube-proxy 组件在每个节点上运行，以提供这些网络功能。

在 Kubernetes 中：
* 服务以逻辑方式对 Pod 进行分组，以允许通过 IP 地址或 DNS 名称在特定端口上进行直接访问。 
* 可以使用负载均衡器分发流量。 
* 使用入口控制器也可实现更复杂的应用程序流量路由。 
* 使用 Kubernetes 网络策略可提供安全性，还可筛选 Pod 网络流量。

Azure 平台还可简化 AKS 群集的虚拟网络。 创建 Kubernetes 负载均衡器时，也会创建和配置基础 Azure 负载均衡器资源。 打开 Pod 的网络端口时，会配置相应的 Azure 网络安全组规则。 对于 HTTP 应用程序路由，Azure 还可以在配置新的入口路由时配置外部 DNS。

## <a name="services"></a>服务

为简化应用程序工作负载的网络配置，Kubernetes 使用服务以逻辑方式对一组 Pod 进行分组并提供网络连接。 可用的服务类型如下：

- **群集 IP** 
  
  创建在 AKS 群集中使用的内部 IP 地址。 适用于支持群集中其他工作负载的仅限内部使用的应用程序。

    ![显示 AKS 群集中群集 IP 流量的示意图][aks-clusterip]

- **NodePort** 

  在基础节点上创建端口映射，该映射允许使用节点 IP 地址和端口直接访问应用程序。

    ![显示 AKS 群集中 NodePort 流量的示意图][aks-nodeport]

- **LoadBalancer** 

  创建 Azure 负载均衡器资源、配置外部 IP 地址并将请求的 Pod 连接到负载均衡器后端池。 为允许客户流量发送到应用程序，要在所需端口上创建负载均衡规则。 

    ![显示 AKS 群集中负载均衡器流量的示意图][aks-loadbalancer]

    针对入站流量的额外控制和路由，可能要改用[入口控制器](#ingress-controllers)。

- **ExternalName** 

  创建特定的 DNS 条目，便于访问应用程序。

可以动态分配负载均衡器和服务 IP 地址，也可以指定现有静态 IP 地址。 可以分配内部和外部静态 IP 地址。 现有静态 IP 地址通常与 DNS 条目绑定。

可以创建内部和外部负载均衡器 。 内部负载均衡器仅分配有一个专用 IP 地址，因此无法从 Internet 对其进行访问。

## <a name="azure-virtual-networks"></a>Azure 虚拟网络

在 AKS 中，可以部署使用以下两种网络模型之一的群集：

- Kubenet 网络

  通常在部署 AKS 群集时创建和配置网络资源。

- Azure 容器网络接口 (CNI) 网络
 
  AKS 群集连接到现有的虚拟网络资源和配置。

### <a name="kubenet-basic-networking"></a>Kubenet（基本）网络

kubenet 网络选项是用于创建 AKS 群集的默认配置。 使用 kubenet：
1. 节点从 Azure 虚拟网络子网接收 IP 地址。 
1. Pod 从逻辑上与节点 Azure 虚拟网络子网不同的地址空间接收 IP 地址。 
1. 然后配置网络地址转换 (NAT)，以便 Pod 可以访问 Azure 虚拟网络上的资源。 
1. 流量的源 IP 地址会转换为节点的主 IP 地址。

节点使用 [kubenet][kubenet] Kubernetes 插件。 你可以：
* 让 Azure 平台为你创建和配置虚拟网络，或是 
* 选择将 AKS 群集部署到现有虚拟网络子网中。 

请记住，只有节点才会接收可路由 IP 地址。 Pod 会使用 NAT 与 AKS 群集外部的其他资源通信。 这种方法减少了需要在网络空间中保留供 Pod 使用的 IP 地址数量。

有关详细信息，请参阅[为 AKS 群集配置 kubenet 网络][aks-configure-kubenet-networking]。

### <a name="azure-cni-advanced-networking"></a>Azure CNI（高级）网络

借助 Azure CNI，每个 pod 都可以从子网获取 IP 地址，并且可以直接访问。 这些 IP 地址必须事先计划，并且在网络空间中必须唯一。 每个节点都有一个配置参数来表示它支持的最大 Pod 数。 这样，就会为每个节点预留相应的 IP 地址数。 如果不进行规划，则此方法可能会耗尽 IP 地址，或者在应用程序需求增长时需要在更大的子网中重建群集。

与 kubenet 不同，流向同一虚拟网络中终结点的流量不会通过 NAT 转换到节点的主 IP。 虚拟网络中流量的源地址是 Pod IP。 虚拟网络外部的流量仍然通过 NAT 转换到节点的主 IP。

节点使用 [Azure CNI][cni-networking] Kubernetes 插件。

![显示两个节点的示意图，其中的网桥将每个节点连接到单个 Azure VNet][advanced-networking-diagram]

有关详细信息，请参阅[为 AKS 群集配置 Azure CNI][aks-configure-advanced-networking]。

### <a name="compare-network-models"></a>网络模型的比较

Kubenet 和 Azure CNI 都为 AKS 群集提供网络连接。 不过，这两个模型各有优缺点。 从较高层面讲，需要考虑以下因素：

* **kubenet**
    * 节省 IP 地址空间。
    * 使用 Kubernetes 内部或外部负载均衡器可从群集外部访问 Pod。
    * 可手动管理和维护用户定义的路由 (UDR)。
    * 每个群集最多可包含 400 个节点。
* **Azure CNI**
    * Pod 建立了全面的虚拟网络连接，可以通过其专用 IP 地址直接从已连接的网络对其进行访问。
    * 需要更多的 IP 地址空间。

Kubenet 和 Azure CNI 之间存在以下行为差异：

| 功能                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| 在现有或新的虚拟网络中部署群集                                            | 支持 - 手动应用 UDR | 支持 |
| Pod-Pod 连接                                                                         | 支持 | 支持 |
| Pod-VM 连接；VM 位于同一虚拟网络中                                          | 由 Pod 发起时可正常工作 | 采用两种工作方式 |
| Pod-VM 连接；VM 位于对等互连的虚拟网络中                                            | 由 Pod 发起时可正常工作 | 采用两种工作方式 |
| 使用 VPN 或 Express Route 进行本地访问                                                | 由 Pod 发起时可正常工作 | 采用两种工作方式 |
| 访问服务终结点保护的资源                                             | 支持 | 支持 |
| 使用负载均衡器服务、应用程序网关或入口控制器公开 Kubernetes 服务 | 支持 | 支持 |
| 默认的 Azure DNS 和专用区域                                                          | 支持 | 支持 |
| 对 Windows 节点池的支持                                                               | 不支持 | 支持 |

关于 DNS，kubenet 和 Azure CNI 插件 DNS 都由 CoreDNS 提供，后者是一个在 AKS 中运行的部署，有其自己的自动缩放程序。 有关 Kubernetes 上的 CoreDNS 的详细信息，请参阅[自定义 DNS 服务](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)。 默认情况下，CoreDNS 配置为将未知域转发到部署 AKS 群集的 Azure 虚拟网络的 DNS 功能。 因此，Azure DNS 和专用区域将适用于在 AKS 中运行的 Pod。

### <a name="support-scope-between-network-models"></a>网络模型之间的支持范围

无论使用哪种网络模型，都可以通过以下方式之一部署 kubenet 和 Azure CNI：

* 当你创建 AKS 群集时，Azure 平台可自动创建和配置虚拟网络资源。
* 当你创建 AKS 群集时，可以手动创建和配置虚拟网络资源并附加到这些资源。

尽管 Kubenet 和 Azure CNI 都支持服务终结点或 UDR 之类的功能，但你可以根据 [AKS 的支持策略][support-policies]中的说明进行更改。 例如：

* 如果你为 AKS 群集手动创建虚拟网络资源，则在配置自己的 UDR 或服务终结点时将会获得支持。
* 如果 Azure 平台为 AKS 群集自动创建虚拟网络资源，则无法手动更改 AKS 管理的这些资源来配置你自己的 UDR 或服务终结点。

## <a name="ingress-controllers"></a>入口控制器

创建 LoadBalancer 类型服务时，还会创建基础 Azure 负载均衡器资源。 负载均衡器配置为在给定端口上将流量分发到服务中的 Pod。 

LoadBalancer 仅在第 4 层工作。 在第 4 层，服务不知道实际的应用程序，不会考虑任何其他路由。

入口控制器在第 7 层工作，可使用更智能的规则来分发应用程序流量。 入口控制器通常基于入站 URL 将 HTTP 流量路由到不同的应用程序。

![显示 AKS 群集中入口流量的示意图][aks-ingress]

### <a name="create-an-ingress-resource"></a>创建入口资源
在 AKS 中，可以使用 NGINX 、类似工具或 AKS HTTP 应用程序路由功能创建入口资源。 为 AKS 群集启用 HTTP 应用程序路由时，Azure 平台会创建入口控制器和 External-DNS 控制器。 在 Kubernetes 中创建新的入口资源时，系统会在特定于群集的 DNS 区域中创建所需的 DNS A 记录。 

有关详细信息，请参阅[部署 HTTP 应用程序路由][aks-http-routing]。

### <a name="application-gateway-ingress-controller-agic"></a>应用程序网关入口控制器 (AGIC)

借助应用程序网关入口控制器 (AGIC) 加载项，AKS 客户可利用 Azure 的本机应用程序网关级别 7 负载均衡器向 Internet 公开云软件。 AGIC 监视主机 Kubernetes 群集并持续更新应用程序网关，以便向 Internet 公开所选服务。 

若要了解有关 AKS 的 AGIC 加载项的详细信息，请参阅[什么是应用程序网关入口控制器？][agic-overview]。

### <a name="ssltls-termination"></a>SSL/TLS 终止

SSL/TLS 终止是入口的另一个常见功能。 在通过 HTTPS 访问的大型 Web 应用程序上，入口资源会处理 TLS 终止，而不是在应用程序自身内部处理。 要提供自动 TLS 认证生成和配置，可以将入口资源配置为使用“Let's Encrypt”之类的提供程序。 

有关使用 Let's Encrypt 配置 NGINX 入口控制器的详细信息，请参阅 [Ingress 和 TLS][aks-ingress-tls]。

### <a name="client-source-ip-preservation"></a>客户端源 IP 保留

可配置入口控制器，以便在对 AKS 群集中的容器发出请求时保留客户端源 IP。 当入口控制器将客户端的请求路由到 AKS 群集中的容器时，该请求的原始源 IP 不可用于目标容器。 如果启用客户端源 IP 保留，则可以在请求标头中的 *X-Forwarded-For* 下使用客户端的源 IP。 

如果在入口控制器上使用“客户端源 IP 保留”，则无法使用 TLS 直通。 可对其他服务（例如 LoadBalancer 类型的服务）使用“客户端源 IP 保留”和 TLS 直通。

## <a name="network-security-groups"></a>网络安全组

网络安全组筛选 VM（例如 AKS 节点）的流量。 创建服务（如 LoadBalancer）时，Azure 平台会自动配置所需的任何网络安全组规则。 

无需手动配置网络安全组规则，以筛选 AKS 群集中 Pod 的流量。 只需在 Kubernetes 服务清单中定义任何所需的端口和转发。 让 Azure 平台创建或更新适当的规则。 

还可以使用网络策略自动向 Pod 应用流量筛选器规则。

## <a name="network-policies"></a>网络策略

默认情况下，AKS 群集中的所有 Pod 都可以无限制地发送和接收流量。 为了提高安全性，可定义用来控制流量流的规则，如：
* 后端应用程序只公开给所需的前端服务。 
* 数据库组件只能由与其连接的应用程序层访问。

网络策略是 AKS 中提供的一项 Kubernetes 功能，允许你控制 Pod 之间的流量流。 你可以基于分配的标签、命名空间或流量端口等设置来允许或拒绝到 Pod 的流量。 虽然网络安全组更适用于 AKS 节点，不过网络策略是一种更适合于的云原生方式，可控制 pod 的流量流。 因为 Pod 是在 AKS 群集中动态创建的，则可以动态应用所需的网络策略。

有关详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量][use-network-policies]。

## <a name="next-steps"></a>后续步骤

若要开始使用 AKS 网络，请通过 [kubenet][aks-configure-kubenet-networking] 或 [Azure CNI][aks-configure-advanced-networking] 创建并配置采用你自己的 IP 地址范围的 AKS 群集。

如需相关的最佳做法，请参阅 [AKS 中的网络连接和安全性的最佳做法][operator-best-practices-network]。

有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅以下文章：

- [Kubernetes/AKS 群集和工作负荷][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 访问和标识][aks-concepts-identity]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 存储][aks-concepts-storage]
- [Kubernetes/AKS 规模][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
