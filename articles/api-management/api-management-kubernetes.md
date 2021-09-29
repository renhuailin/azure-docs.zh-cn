---
title: 将 Azure API 管理与 Azure Kubernetes 服务中部署的微服务结合使用 | Microsoft Docs
description: 本文介绍使用 AKS 部署 API 管理的选项
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: danlep
ms.openlocfilehash: 8d2fd83052ee2979bf2a14a949c6e80348767256
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594192"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>将 Azure API 管理与 Azure Kubernetes 服务中部署的微服务结合使用

微服务非常适合用于生成 API。 通过 [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/) (AKS)，可在云中快速部署和运行[基于微服务的体系结构](/azure/architecture/guide/architecture-styles/microservices)。 然后，可利用 [Azure API 管理](https://aka.ms/apimrocks)（API 管理）将微服务作为 API 发布，以供内部和外部使用。 本文将介绍使用 AKS 部署 API 管理的选项。 它假定你基本了解 Kubernetes、API 管理和 Azure 网络。 

## <a name="background"></a>背景

将微服务发布为 API 以供使用时，管理微服务与使用它们的客户端之间的通信可能很具挑战性。 有许多横切关注点，例如身份验证、授权、限制、缓存、转换和监视。 无论是否向内部客户端或外部客户端公开微服务，这些关注点都是有根据的。 

[API 网关](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern)模式解决了这些问题。 API 网关充当微服务的前门、将客户端与微服务分离、增加额外的安全层，并通过消除处理横切关注点的负担来降低微服务的复杂性。 

[Azure API 管理](https://aka.ms/apimrocks)是一种统包解决方案，用于解决 API 网关需求。 可快速为微服务创建一致的新式网关，并将这些微服务发布为 API。 作为具有完整生命周期的 API 管理解决方案，它还提供了其他功能，包括用于 API 发现、API 生命周期管理和 API 分析的自助服务开发人员门户。

AKS 和 API 管理结合使用时，共同提供一个平台用来部署、发布、保护、监视和管理基于微服务的 API。 本文将介绍一些将 AKS 与 API 管理一起部署的选项。 

## <a name="kubernetes-services-and-apis"></a>Kubernetes 服务和 API

在 Kubernetes 群集中，容器部署在 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 中，这些 Pod 是临时的且具有生命周期。 当某个工作器节点失效时，在此节点上运行的 Pod 将丢失。 因此，Pod 的 IP 地址随时都可能变化。 无法依赖它来与 Pod 通信。 

为了解决此问题，Kubernetes 引入了[服务](https://kubernetes.io/docs/concepts/services-networking/service/)的概念。 Kubernetes 服务是一个抽象层，它定义了 Pod 的逻辑组，并为这些 Pod 启用外部流量公开、负载均衡和服务发现。 

当我们准备好通过 API 管理将微服务发布为 API 时，我们需要考虑如何将 Kubernetes 中的服务映射到 API 管理中的 API。 没有既定规则。 这取决于你在开始时如何设计业务功能或域并将其划分到微服务中。 例如，如果某项服务背后的 Pod 负责给定资源（如客户）的所有操作，则该服务可能会映射到一个 API。 如果将对资源的操作划分到多个微服务（例如 GetOrder、PlaceOrder），则多个服务可能会以逻辑方式聚合到 API 管理中的单个 API（参见图 1）。 

映射也会逐步发展。 由于 API 管理在微服务的前面创建了一个外观，因此我们可随着时间的推移重构微服务并调整其大小。 

![将服务映射到 API](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>在 AKS 的前面部署 API 管理

有几个选项可用于在 AKS 群集的前面部署 API 管理。 

当 AKS 群集始终部署在虚拟网络 (VNet) 中时，不需要在 VNet 中部署 API 管理实例。 当 API 管理未在群集 VNet 中时，AKS 群集必须发布 API 管理要连接到的公共终结点。 在这种情况下，需要保护 API 管理与 AKS 之间的连接。 换句话说，需要确保只能通过 API 管理以独占方式访问群集。 让我们来看看这些选项。 

### <a name="option-1-expose-services-publicly"></a>选项 1：公开服务

可使用 NodePort、LoadBalancer 或 ExternalName [服务类型](../aks/concepts-network.md)公开 AKS 群集中的服务。 在这种情况下，可直接从公共 Internet 访问服务。 在群集的前面部署 API 管理后，需要在微服务中应用身份验证来确保所有入站流量均通过 API 管理。 例如，API 管理可在向群集发出的每个请求中包含一个访问令牌。 每个微服务负责在处理请求之前验证令牌。 


这可能是在 AKS 的前面部署 API 管理的最简单的方式，尤其是当你已在微服务中实现身份验证逻辑时。 

![直接发布服务](./media/api-management-aks/direct.png)

优点：
* 可在 API 管理端上轻松配置，因为无需将其注入群集 VNet
* 如果服务已公开且微服务中已存在身份验证逻辑，则 AKS 端将保持不变

缺点：
* 服务终结点的公共可见性可能会带来安全风险
* 入站群集流量没有单一入口点
* 重复的身份验证逻辑使微服务复杂化

### <a name="option-2-install-an-ingress-controller"></a>选项 2：安装入口控制器

尽管选项 1 可能更简单，但它存在上述明显缺点。 如果某个 API 管理实例不在群集 VNet 中，则相互 TLS 身份验证 (mTLS) 方法很可靠，它可确保 API 管理实例与 AKS 群集之间的双向通信中的流量是安全且可信任的。 

相互 TLS 身份验证由 API 管理提供[本机支持](./api-management-howto-mutual-certificates.md)，可通过[安装入口控制器](../aks/ingress-own-tls.md)在 Kubernetes 中启用（图 3）。 因此，将在入口控制器中执行身份验证，这简化了微服务。 此外，可通过 Ingress 将 API 管理的 IP 地址添加到允许列表，以确保只有 API 管理可访问群集。  

 
![通过入口控制器发布](./media/api-management-aks/ingress-controller.png)


优点：
* 可在 API 管理端上轻松配置，因为无需将其注入群集 VNet，并且 mTLS 受到本机支持
* 可在入口控制器层上集中保护入站群集流量
* 通过最大程度地减少公开可见的群集终结点，降低了安全风险

缺点：
* 增加了群集配置的复杂性，因为增加了安装、配置和维护入口控制器以及管理用于 mTLS 的证书的工作量
* 入口控制器终结点的公共可见性会带来安全风险


通过 API 管理发布 API 时，使用订阅密钥保护对这些 API 的访问容易且常见。 需要使用已发布 API 的开发人员在调用这些 API 时必须在 HTTP 请求中包括一个有效的订阅密钥。 否则，API 管理网关会立即拒绝调用。 不会将它们转发到后端服务。

若要获取访问 API 所需的订阅密钥，必须拥有订阅。 订阅实质上是一个已命名的容器，该容器包含一对订阅密钥。 需要使用已发布 API 的开发人员可以获取订阅。 不需要 API 发布者批准。 API 发布者也可以直接为 API 使用者创建订阅。

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>选项 3：在群集 VNet 中部署 APIM

在某些情况下，由于公开的终结点，具有法规约束或严格安全要求的客户可能会发现选项 1 和 2 这两种解决方案不可行。 在其他情况下，AKS 群集和使用微服务的应用程序可能驻留在同一 VNet 中，因此没有理由公开群集，原因是所有 API 流量都将保留在 VNet 中。 在这些情况下，可将 API 管理部署到群集 VNet 中。 [API Management 的“开发人员”层和“高级”层](https://aka.ms/apimpricing)支持 VNet 部署。 

可采用两种模式[将 API 管理部署到 VNet](./api-management-using-with-vnet.md) - 外部和内部。 

如果 API 使用者不在群集 VNet 中，则应使用外部模式（图 4）。 在此模式下，API 管理网关会被注入到群集 VNet 中，但可通过外部负载均衡器从公共 Internet 进行访问。 它有助于完全隐藏群集，同时仍允许外部客户端使用微服务。 此外，还可使用网络安全组 (NSG) 等 Azure 网络功能来限制网络流量。

![外部 VNet 模式](./media/api-management-aks/vnet-external.png)

如果所有 API 使用者都位于群集 VNet 中，则可使用内部模式（图 5）。 在此模式下，API 管理网关会被注入到群集 VNET 中，并且仅可通过内部负载均衡器从该 VNet 中进行访问。 无法从公共 Internet 访问 API 管理网关或 AKS 群集。 

![内部 VNet 模式](./media/api-management-aks/vnet-internal.png)

 在这两种情况下，AKS 群集都不公开可见。 与选项 2 相比，入口控制器可能不是必需的。 根据你的方案和配置，可能仍需要在 API 管理与微服务之间进行身份验证。 例如，如果采用服务网格，则它始终需要相互 TLS 身份验证。 

优点：
* 该选项最安全，因为 AKS 群集没有公共终结点
* 简化了群集配置，因为它没有公共终结点
* 能够使用内部模式在 VNet 中隐藏 API 管理和 AKS
* 能够使用网络安全组 (NSG) 等 Azure 网络功能来控制网络流量

缺点：
* 若要在 VNet 中部署和配置 API 管理来使其运行，则操作更加复杂

## <a name="next-steps"></a>后续步骤

* 详细了解 [AKS 中应用程序的网络概念](../aks/concepts-network.md)
* 详细了解[如何将 API 管理与虚拟网络配合使用](./api-management-using-with-vnet.md)
