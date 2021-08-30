---
title: Microsoft Azure Stack Edge Pro 设备上的 Kubernetes 群集概述 | Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro 设备上实现 Kubernetes。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 22a66ca16d8d2cb7ade82cb665f50cc9aae4b27b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747360"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 设备上的 Kubernetes

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Kubernetes 是一种常用的开源平台，用于协调容器化应用程序。 本文概述了 Kubernetes 并介绍了 Kubernetes 在 Azure Stack Edge Pro 设备上的工作方式。 

## <a name="about-kubernetes"></a>关于 Kubernetes 

Kubernetes 提供了一个简单可靠的平台，用于管理基于容器的应用程序及其相关网络和存储组件。 可以通过 Kubernetes 快速生成、交付和缩放容器化应用。

可以通过开放平台 Kubernetes 使用首选的编程语言、OS 库或消息总线来生成应用程序。 若要计划和部署版本，可以将 Kubernetes 与现有的持续集成和持续交付工具集成。

有关详细信息，请观看 [How Kubernetes works](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s)（Kubernetes 工作原理）。

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上的 Kubernetes

在 Azure Stack Edge Pro 设备上，可以通过配置计算角色来创建 Kubernetes 群集。 在你配置计算角色后，系统会为你部署和配置 Kubernetes 群集（包括主节点和工作器节点）。 然后，可以通过 `kubectl`、IoT Edge 或 Azure Arc 使用此群集进行工作负荷部署。

Azure Stack Edge Pro 设备作为构成基础结构群集的单节点配置提供。 Kubernetes 群集与基础结构群集分开，部署在基础结构群集之上。 基础结构群集为 Azure Stack Edge Pro 设备提供永久性存储，而 Kubernetes 群集仅负责应用程序业务流程。 

在此示例中，Kubernetes 群集有一个主节点和一个工作器节点。 群集中的 Kubernetes 节点是运行应用程序和云工作流的虚拟机。 

Kubernetes 主节点负责维护群集的所需状态。 主节点还控制工作器节点，后者又运行容器化应用程序。 

下图说明了 Kubernetes 在单节点 Azure Stack Edge Pro 设备上的实现。 单节点设备不具备高可用性，如果单个节点出现故障，设备会关闭。 Kubernetes 群集也会关闭。

![单节点 Azure Stack Edge Pro 设备的 Kubernetes 体系结构](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

有关 Kubernetes 群集体系结构的详细信息，请参阅 [Kubernetes 核心概念](https://kubernetes.io/docs/concepts/architecture/)。

主节点和辅助节点是消耗 CPU 和内存的虚拟机。 部署 Kubernetes 工作负载时，必须了解主 VM 和辅助 VM 的计算要求。

|Kubernetes VM 类型|CPU 和内存要求|
|---------|---------|
|主 VM|4 核，4 GB RAM|
|辅助 VM|12 核，32 GB RAM|
<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>存储卷预配

为了支持应用程序工作负荷，可以在 Azure Stack Edge Pro 设备共享上装载存储卷以保存永久性数据。 静态和动态卷都可以使用。 

有关详细信息，请参阅 [Azure Stack Edge Pro 设备的 Kubernetes 存储](azure-stack-edge-gpu-kubernetes-storage.md)中的应用程序存储预配选项。

## <a name="networking"></a>网络

Kubernetes 网络允许配置 Kubernetes 网络内的通信，其中包括容器到容器网络、Pod 到 Pod 网络、Pod 到服务网络以及 Internet 到服务网络。 有关详细信息，请参阅 [Azure Stack Edge Pro 设备的 Kubernetes 网络](azure-stack-edge-gpu-kubernetes-networking.md)中的网络模型。

## <a name="updates"></a>更新

当新的 Kubernetes 版本可用时，可以使用提供给 Azure Stack Edge Pro 设备的标准更新来升级群集。 有关升级步骤，请参阅[为 Azure Stack Edge Pro 应用更新](azure-stack-edge-gpu-install-update.md)。

## <a name="access-monitoring"></a>访问权限和监视

Azure Stack Edge Pro 设备上的 Kubernetes 群集允许 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)。 有关详细信息，请参阅 [Azure Stack Edge Pro GPU 设备上的 Kubernetes 基于角色的访问控制](azure-stack-edge-gpu-kubernetes-rbac.md)。

还可以通过 Kubernetes 仪表板监视群集和资源的运行状况。 容器日志也可用。 有关详细信息，请参阅[使用 Kubernetes 仪表板监视 Azure Stack Edge Pro 设备上的 Kubernetes 群集运行状况](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)。

Azure Monitor 也可作为加载项提供，用于从容器、节点和控制器收集运行状况数据。 有关详细信息，请参阅 [Azure Monitor 概述](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>应用程序管理

在 Azure Stack Edge Pro 设备上创建 Kubernetes 群集后，可通过以下任一方法管理在此群集上部署的应用程序：

- 通过 `kubectl` 进行本机访问
- IoT Edge 
- Azure Arc

这些方法将在以下部分进行介绍。


### <a name="kubernetes-and-kubectl"></a>Kubernetes 和 kubectl

部署 Kubernetes 群集后，你可以从客户端计算机以本地方式管理群集上部署的应用程序。 通过命令行使用本机工具（例如 kubectl）与应用程序交互。 

若要详细了解如何部署 Kubernetes 群集，请参阅[在 Azure Stack Edge Pro 设备上部署 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。 有关管理的信息，请参阅[使用 kubectl 在 Azure Stack Edge Pro 设备上管理 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。


### <a name="kubernetes-and-iot-edge"></a>Kubernetes 和 IoT Edge

Kubernetes 还可以与 Azure Stack Edge Pro 设备上的 IoT Edge 工作负荷集成，其中，Kubernetes 提供缩放和生态系统，IoT 提供以 IoT 为中心的生态系统。 Kubernetes 层用作部署 Azure IoT Edge 工作负荷的基础结构层。 模块生存期和网络负载均衡由 Kubernetes 管理，而边缘应用程序平台则由 IoT Edge 管理。

若要详细了解如何通过 IoT Edge 在 Kubernetes 群集上部署应用程序，请参阅： 

- [通过 IoT Edge 在 Azure Stack Edge Pro 设备上公开无状态应用程序](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。


### <a name="kubernetes-and-azure-arc"></a>Kubernetes 和 Azure Arc

Azure Arc 是一种混合管理工具，用于在 Kubernetes 群集上部署应用程序。 Azure Arc 还允许你使用适用于容器的 Azure Monitor 来查看和监视群集。 有关详细信息，请参阅[什么是已启用 Azure Arc 的 Kubernetes？](../azure-arc/kubernetes/overview.md)。 有关 Azure Arc 定价的信息，请参阅 [Azure Arc 定价](https://azure.microsoft.com/services/azure-arc/#pricing)。

已启用 Azure Arc 的 Kubernetes 将于 2021 年 3 月面向用户正式发布，并且将收取标准使用费用。 作为重要的预览版客户，你可以在 Azure Stack Edge 设备上免费使用启用了 Azure Arc 的 Kubernetes。 若要获得预览版产品/服务，请创建[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)：

1. 在“问题类型”下，选择“计费”。  
2. 在“订阅”下，选择自己的订阅。
3. 在“服务”下选择“我的服务”，然后选择“Azure Stack Edge”。
4. 在“资源”下，选择你的资源。
5. 在“摘要”下，键入问题说明。
6. 在“问题类型”下，选择“意外费用”。
7. 在“问题子类型”下，选择“帮助我了解免费试用版的费用”。


## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Stack Edge Pro 设备](azure-stack-edge-gpu-kubernetes-storage.md)上的 Kubernetes 存储。
- 了解 [Azure Stack Edge Pro 设备](azure-stack-edge-gpu-kubernetes-networking.md)上的 Kubernetes 网络模型。
- 在 Azure 门户中部署 [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)。
