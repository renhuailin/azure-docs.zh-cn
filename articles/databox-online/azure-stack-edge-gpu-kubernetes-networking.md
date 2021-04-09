---
title: 了解 Azure Stack Edge Pro 设备上的 Kubernetes 网络 | Microsoft Docs
description: 介绍 Kubernetes 网络如何在 Azure Stack Edge Pro 设备上工作。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 139eeaa83629f3ef51f8eda414622f7da54dfab4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560244"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 设备中的 Kubernetes 网络

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

在 Azure Stack Edge Pro 设备上，配置计算角色时，会创建 Kubernetes 群集。 创建 Kubernetes 群集后，便可在 Pod 中的 Kubernetes 群集上部署容器化应用程序。 网络通过不同的方式用于 Kubernetes 群集中的 Pod。 

本文大致介绍 Kubernetes 群集中的网络，特别是在 Azure Stack Edge Pro 设备的上下文中。 

## <a name="networking-requirements"></a>网络要求

下面是一个部署到 Kubernetes 群集典型的 2 层应用的示例。

- 该应用前端有一个 Web 服务器，后端有一个数据库应用程序。 
- 每个 Pod 都分配有一个 IP，但这些 IP 可能会在 Pod 重启和故障转移时发生变化。 
- 每个应用都由多个 Pod 组成，所有 Pod 副本都应具有流量的负载均衡。 

![Kubernetes 网络要求](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

上述情况导致出现以下网络要求：

 - 需要由 Kubernetes 群集以外的应用程序用户通过名称或 IP 地址来访问面向外部的应用程序。 
 - 例如，Kubernetes 群集中的应用程序，此处的前端和后端 Pod 应能够相互通信。

为了满足上述需求，我们引入了 Kubernetes 服务。 


## <a name="networking-services"></a>网络服务

存在两种类型的 Kubernetes 服务： 

- **群集 IP 服务** - 将此看作是为应用程序 Pod 提供一个常量终结点。 与这些服务关联的 Pod 都不能从 Kubernetes 群集外部访问。 用于这些服务的 IP 地址来自专用网络中的地址空间。 
- **负载均衡器 IP** - 与群集 IP 服务类似，但相关的 IP 来自外部网络，可从 Kubernetes 群集外部访问。


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上的 Kubernetes 网络

Calico、Metallb 和 Core DNS 都是为 Azure Stack Edge Pro 上的网络安装的组件。 

- Calico 将 IP 地址从专用 IP 范围分配到每个 Pod，并为这些 Pod 配置网络，以便一个节点上的 Pod 可与另一节点上的 Pod 通信。 
- Metallb 在群集内的 Pod 上运行，并将 IP 地址分配给负载均衡器类型的服务。 负载均衡器 IP 地址是从通过本地 UI 提供的服务 IP 范围中选择的。 
- Core DNS - 此加载项将 DNS 记录映射服务名称配置到群集 IP 地址。

用于 Kubernetes 节点和外部服务的 IP 地址通过设备本地 UI 中的“计算网络”页面提供。

![本地 UI 中的 Kubernetes IP 分配](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP 分配适用于：

- **Kubernetes 节点 IP**：此 IP 范围用于 Kubernetes 主节点和工作器节点。 当 Kubernetes 节点相互通信时，会使用这些 IP。

- **Kubernetes 外部服务 IP**：此 IP 范围用于在 Kubernetes 群集之外公开的外部服务（也称为负载均衡器服务）。  


## <a name="next-steps"></a>后续步骤

若要在 Azure Stack Edge Pro 上配置 Kubernetes 网络，请参阅：

- [通过 IoT Edge 在 Azure Stack Edge Pro 外部公开无状态应用程序](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。

- [通过 kuebctl 在 Azure Stack Edge Pro 外部公开无状态应用程序](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)。