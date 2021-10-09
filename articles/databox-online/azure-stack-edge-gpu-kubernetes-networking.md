---
title: Azure Stack Edge Pro GPU Kubernetes 网络
description: 介绍 Kubernetes 网络如何在 Azure Stack Edge Pro GPU 设备上工作。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: alkohli
ms.openlocfilehash: a969486194f66be5c7ce4c3973352ca036a025f8
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093110"
---
# <a name="kubernetes-networking-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 设备上的 Kubernetes 网络

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

在 Azure Stack Edge Pro GPU 设备上配置计算角色时，会创建 Kubernetes 群集。 创建 Kubernetes 群集后，便可在 Pod 中的 Kubernetes 群集上部署容器化应用程序。 网络通过不同的方式用于 Kubernetes 群集中的 Pod。 

本文大致介绍 Kubernetes 群集中的网络，特别是在 Azure Stack Edge Pro GPU 设备的上下文中。 

## <a name="networking-requirements"></a>网络要求

下面是一个部署到 Kubernetes 群集的典型双层应用的示例。

- 该应用前端有一个 Web 服务器，后端有一个数据库应用程序。 
- 每个 Pod 都分配有一个 IP，但这些 IP 可能会在 Pod 重启和故障转移时发生变化。 
- 每个应用都由多个 Pod 组成，所有 Pod 副本都应具有流量的负载均衡。 

![Kubernetes 网络要求](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1-m.png)

上述情况导致出现以下网络要求：

 - 需要由 Kubernetes 群集以外的应用程序用户通过名称或 IP 地址来访问面向外部的应用程序。 
 - 例如，Kubernetes 群集中的应用程序，此处的前端和后端 Pod 应能够相互通信。

为了满足上述需求，我们引入了 Kubernetes 服务。 


## <a name="networking-services"></a>网络服务

存在两种类型的 Kubernetes 服务： 

- 群集 IP 服务 - 将此服务看作是为应用程序 Pod 提供一个固定终结点。 与这些服务关联的 Pod 都不能从 Kubernetes 群集外部访问。 用于这些服务的 IP 地址来自专用网络中的地址空间。 
    
    若要公开 Kubernetes 群集中的 Pod，以使其像其他 Pod 一样（而不是作为外部公开的负载均衡器服务）可供访问，请参阅如何[将 Kubernetes 服务公开为用于内部通信的群集 IP 服务]()。

- **负载均衡器 IP** - 与群集 IP 服务类似，但相关的 IP 来自外部网络，可从 Kubernetes 群集外部访问。


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->

## <a name="kubernetes-network-configuration"></a>Kubernetes 网络配置

用于 Kubernetes 节点和外部服务的 IP 地址通过设备本地 UI 中的“计算”页提供。

![本地 UI 中的 Kubernetes IP 分配](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP 分配适用于：

- **Kubernetes 节点 IP**：此 IP 范围用于 Kubernetes 主节点和工作器节点。 当 Kubernetes 节点相互通信时，会使用这些 IP。
- **Kubernetes 外部服务 IP**：此 IP 范围用于在 Kubernetes 群集之外公开的外部服务（也称为负载均衡器服务）。

## <a name="kubernetes-networking-components"></a>Kubernetes 网络组件

Calico、Metallb 和 Core DNS 都是为 Azure Stack Edge Pro GPU 上的网络安装的组件。 

- Calico 将 IP 地址从专用 IP 范围分配到每个 Pod，并为这些 Pod 配置网络，以便一个节点上的 Pod 可与另一节点上的 Pod 通信。 
- Metallb 在群集内的 Pod 上运行，并将 IP 地址分配给 `load balancer` 类型的服务。 负载均衡器 IP 地址是从通过本地 UI 提供的服务 IP 范围中选择的。 
- Core DNS - 此加载项将 DNS 记录映射服务名称配置到群集 IP 地址。

连接到设备的 PowerShell 界面时，可以看到上述网络组件在 Kubernetes 群集上运行。

## <a name="network-interfaces-switches"></a>网络接口、交换机 

你的设备是作为构成基础结构群集的单节点配置提供的。 Kubernetes 群集与基础结构群集分开，部署在基础结构群集之上。 Kubernetes 群集有一个主节点和一个工作器节点。 这两个 Kubernetes 节点是运行应用程序和云工作流的虚拟机。

主 VM 和工作器 VM 各有两个网络接口，其中一个接口连接到内部虚拟交换机，另一个接口连接到外部虚拟交换机。 

- 外部虚拟交换机：通过本地 UI 中的“计算”页为计算启用设备端口时，会创建此交换机 。 这是用于计算基础结构的交换机，例如，此交换机用于设备上部署的虚拟机。 
- 内部虚拟交换机：此交换机是在设备上作为出厂默认设置的一部分创建的。 内部虚拟交换机使用网络地址转换 (NAT) 通过在默认网关上配置的端口来路由流量。 例如，此交换机将所有 IoT 运行时请求从 VM 路由到 Azure 门户。 

   <!--![Azure Stack Edge networking simplified diagram](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-0.png)-->

## <a name="network-routes"></a>网络路由 

对于设备上的 Kubernetes VM，可以通过添加新的路由配置来路由流量。 路由配置是包含以下字段的路由表条目：

| 参数     | 说明                                                                              |
|---------------|------------------------------------------------------------------------------------------|
| 目标   | IP 地址或 IP 地址前缀。                                            |
| 前缀长度 | 与目标中的地址或地址范围对应的前缀长度。 |
| 下一个跃点      | 数据包转发到的 IP 地址。                                         |
| 接口     | 转发 IP 数据包的网络接口。                                       |
| 指标        | 路由指标确定用于访问目标的首选网络接口。 |


## <a name="change-routing-on-compute-network"></a>更改计算网络上的路由

使用 `Add-HcsNetRoute` cmdlet 修改 Kubernetes 工作器 VM 和主 VM 上的路由。 考虑下图中的布局。 

![Azure Stack Edge 网络示意图](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-1.png)

- 端口 2 连接到 Internet，是出站流量的所需路径。 
- 已在端口 3 上启用计算，因此已在此网络接口上创建了外部虚拟交换机。 
- 端口 3 连接到专用网络，该网络包含用于将原始数据馈送到 Azure Stack Edge 设备进行处理的摄像头和其他传感器。 


如果在专用网络上的环境中配置了网关，请考虑为 Kubernetes 主 VM 和工作器 VM 设置自定义路由，使这些 VM 能够与网关通信，以便仅处理相关的流量。 这样，你便可以控制在计算网络上或者可能已在 Azure Stack Edge 计算设备上配置的端口上传送的流量。 例如，你可能希望所有其他面向 Internet 的流量流经设备上的其他物理端口。 在这种情况下，面向 Internet 的流量可以通过端口 2。 

还应考虑到以下因素：

- 如果你有一个扁平的子网，则无需将这些路由添加到专用网络。 如果专用网络中有多个子网，则你可以（选择性地）添加这些路由。
- 只能将这些路由添加到 Kubernetes 主 VM 和工作器 VM，而不能添加到设备（Windows 主机）。
- 在添加此路由之前无需配置 Kubernetes 计算。 也可以在配置 Kubernetes 计算后添加或更新路由。 
- 只能通过设备的 PowerShell 界面添加新的路由配置，而不能通过本地 UI 添加。
- 确保使用的网络接口采用静态配置。

## <a name="add-a-route-configuration"></a>添加路由配置

若要将新的自定义路由添加到专用网络，请使用如下所示的 cmdlet：

```powershell
Add-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP address or IP address prefix> -NextHop <IP address of next hop> -RouteMetric <Route metric number> 
```
下面是示例输出。

```output
Add-HcsNetRoute -InterfaceAlias "Port3" -DestinationPrefix "192.168.20.0/24" -NextHop "192.168.20.1" -RouteMetric 100 
```

上述命令将在路由表中创建一个条目，用于定义目标子网 192.168.20.0/24，将下一个跃点指定为 192.168.20.1，并为此路由条目分配路由指标 100。 路由指标越低，分配给路由的优先级就越高。

## <a name="check-route-configuration"></a>检查路由配置

使用以下 cmdlet 检查在设备上添加的所有自定义路由配置。 这些路由不包括设备上已存在的所有系统路由或默认路由。

```powershell
Get-HcsNetRoute -InterfaceAlias <Port number>
```

## <a name="remove-a-route-configuration"></a>删除路由配置

使用以下 cmdlet 删除在设备上添加的路由配置。

```powershell
Remove-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP or IP prefix>
```

## <a name="routing-with-multiple-network-interfaces"></a>具有多个网络接口的路由

如果已连接多个设备端口，则标准 NIC 组合或交换机嵌入式组合 (SET)（可用于将多个物理网络适配器组合成 Hyper-V 环境内的单个虚拟网络适配器）不受支持。


## <a name="next-steps"></a>后续步骤

若要在 Azure Stack Edge Pro GPU 上配置 Kubernetes 网络，请参阅：

- [通过 IoT Edge 在 Azure Stack Edge Pro GPU 外部公开无状态应用程序](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。

- [通过 kuebctl 在 Azure Stack Edge Pro GPU 外部公开无状态应用程序](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)。
