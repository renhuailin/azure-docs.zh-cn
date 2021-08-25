---
title: Azure Kubernetes 服务简介
description: 了解 Azure Kubernetes 服务的功能和优势，以便在 Azure 中部署和管理基于容器的应用程序。
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: 43313026bec06082b101629556b3b5b0f45ae2ef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741641"
---
# <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

Azure Kubernetes 服务 (AKS) 通过将操作开销卸载到 Azure，简化了在 Azure 中部署托管 Kubernetes 群集的过程。 作为一个托管的 Kubernetes 服务，Azure 可以自动处理运行状况监视和维护等关键任务。 由于 Kubernetes 主节点由 Azure 管理，因此你只需要管理和维护代理节点。 因此，AKS 是免费的，你只需支付群集中的代理节点费，不需支付主节点的费用。  

可使用以下方式创建 AKS 群集：
* [Azure CLI](kubernetes-walkthrough.md)
* [Azure 门户](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* 使用模板驱动的部署选项，例如 [Azure 资源管理器模板](kubernetes-walkthrough-rm-template.md)和 Terraform 

当你部署 AKS 群集时，系统会为你部署和配置 Kubernetes 主节点和所有节点。 在部署过程中，可以配置高级网络、Azure Active Directory (Azure AD) 集成、监视和其他功能。 

有关 Kubernetes 基础知识的详细信息，请参阅 [AKS 的 Kubernetes 核心概念][concepts-clusters-workloads]。

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> AKS 还支持 Windows Server 容器。

## <a name="access-security-and-monitoring"></a>访问权限、安全性和监视

要改善安全性和管理，AKS 允许集成 Azure AD 来实现以下目的：
* 使用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)。 
* 监视群集和资源的运行状况。

### <a name="identity-and-security-management"></a>标识和安全管理

#### <a name="kubernetes-rbac"></a>Kubernetes RBAC

为了限制对群集资源的访问，AKS 支持 [Kubernetes RBAC][kubernetes-rbac]。 Kubernetes RBAC 控制对 Kubernetes 资源与命名空间的访问和权限。  

#### <a name="azure-ad"></a>Azure AD

可将 AKS 群集配置为与 Azure AD 集成。 使用 Azure AD 集成可以基于现有的标识和组成员身份设置 Kubernetes 访问权限。 可以为现有的 Azure AD 用户和组提供集成式登录体验，以及对 AKS 资源的访问权限。  

有关标识的详细信息，请参阅 [AKS 的访问权限和标识选项][concepts-identity]。

若要确保 AKS 群集的安全性，请参阅[将 Azure Active Directory 与 AKS 集成][aks-aad]。

### <a name="integrated-logging-and-monitoring"></a>集成式日志记录和监视

负责监视容器运行状况的 Azure Monitor 会从 AKS 群集和部署的应用程序中的容器、节点和控制器收集内存和处理器性能指标。 可以查看容器日志和 [Kubernetes 主日志][aks-master-logs]，这些日志：
* 存储在 Azure Log Analytics 工作区中。
* 可通过 Azure 门户、Azure CLI 或 REST 终结点获取。

有关详细信息，请参阅[监视 Azure Kubernetes 服务容器运行状况][container-health]。

## <a name="clusters-and-nodes"></a>群集和节点

AKS 节点在 Azure 虚拟机 (VM) 上运行。 通过 AKS 节点，可以将存储连接到节点和 Pod、升级群集组件以及使用 GPU。 AKS 支持运行多个节点池的 Kubernetes 群集，以支持混合操作系统和 Windows Server 容器。  

有关 Kubernetes 群集、节点和节点池功能的详细信息，请参阅 [AKS 的 Kubernetes 核心概念][concepts-clusters-workloads]。

### <a name="cluster-node-and-pod-scaling"></a>群集节点和 Pod 缩放

如果对资源的需求发生变化，用于运行服务的群集节点或 Pod 的数目会自动增多或减少。 可以调整横向 Pod 自动缩放程序或群集自动缩放程序以根据需求做出调整，并只运行必要的资源。

有关详细信息，请参阅[缩放 Azure Kubernetes 服务 (AKS) 群集][aks-scale]。

### <a name="cluster-node-upgrades"></a>群集节点升级

AKS 提供多个 Kubernetes 版本。 有新的版本可在 AKS 中使用后，可以使用 Azure 门户或 Azure CLI 升级群集。 在升级过程中，节点会被仔细封锁和排除以尽量减少对正在运行的应用程序造成中断。  

若要详细了解生命周期版本，请参阅 [AKS 中支持的 Kubernetes 版本][aks-supported versions]。 有关升级步骤，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集][aks-upgrade]。

### <a name="gpu-enabled-nodes"></a>启用了 GPU 的节点

AKS 支持创建启用了 GPU 的节点池。 Azure 目前提供单个或多个启用了 GPU 的 VM。 启用了 GPU 的 VM 是针对计算密集型、图形密集型和可视化工作负荷设计的。

有关详细信息，请参阅[使用 AKS 上的 GPU][aks-gpu]。

### <a name="confidential-computing-nodes-public-preview"></a>机密计算节点（公共预览版）

AKS 支持创建基于 Intel SGX 的机密计算节点池 (DCSv2 VM)。 机密计算节点允许容器在基于硬件的受信任执行环境 (enclave) 中运行。 通过证明合并代码完整性的容器之间的隔离可提供深层防御容器安全策略方面的帮助。 机密计算节点支持机密容器（现有 docker 应用）和 enclave 感知容器。

有关详细信息，请参阅 [AKS 上的机密计算节点][conf-com-node]。

### <a name="storage-volume-support"></a>存储卷支持

若要支持应用程序工作负载，可以装载静态或动态存储卷来保存持久性数据。 根据预期要共享存储卷的已连接 Pod 数目，可以：
* 使用 Azure 磁盘支持的存储访问单个 Pod，或 
* 使用 Azure 文件存储支持的存储并发访问多个 Pod。

有关详细信息，请参阅 [AKS 中应用程序的存储选项][concepts-storage]。

使用 [Azure 磁盘][azure-disk]或 [Azure 文件存储][azure-files]完成动态永久性卷的入门。

## <a name="virtual-networks-and-ingress"></a>虚拟网络和入口

AKS 群集可以部署到现有的虚拟网络中。 在此配置中，群集中的每个 Pod 在虚拟网络中分配有一个 IP 地址，可直接与以下对象通信：
* 群集中的其他 Pod 
* 虚拟网络中的其他节点。 

Pod 还可以连接到对等互连虚拟网络中的其他服务，通过 ExpressRoute 或站点到站点 (S2S) VPN 连接连接到本地网络。  

有关详细信息，请参阅 [AKS 中应用程序的网络概念][aks-networking]。

### <a name="ingress-with-http-application-routing"></a>使用 HTTP 应用程序路由的入口

HTTP 应用程序路由加载项可帮助你轻松访问部署到 AKS 群集的应用程序。 启用后，HTTP 应用程序路由解决方案可以在 AKS 群集中配置入口控制器。  

部署应用程序后，会自动配置可以公开访问的 DNS 名称。 HTTP 应用程序路由会设置一个 DNS 区域并将其与 AKS 群集集成。 然后，你可以照常部署 Kubernetes 入口资源。  

有关入口流量的入门，请参阅 [HTTP 应用程序路由][aks-http-routing]。

## <a name="development-tooling-integration"></a>开发工具集成

Kubernetes 提供丰富的开发和管理工具生态系统，可与 AKS 无缝配合使用。 这些工具包括用于 Visual Studio Code 的 Helm 和 Kubernetes 扩展。 

Azure 还提供多种工具来帮助简化 Kubernetes，例如 DevOps Starter。  

### <a name="devops-starter"></a>DevOps Starter

DevOps Starter 提供简单的解决方案，用于将现有代码和 Git 存储库带到 Azure 中。 DevOps Starter 自动执行以下操作：
* 创建 Azure 资源（如 AKS）; 
* 在 Azure DevOps Services 中创建一个发布管道，其中包括用于 CI 的生成管道； 
* 为 CD 设置发布管道；以及 
* 生成用于监视的 Azure Application Insights 资源。 

有关详细信息，请参阅 [DevOps Starter][azure-devops]。

## <a name="docker-image-support-and-private-container-registry"></a>Docker 映像支持和专用容器注册表

AKS 支持 Docker 映像格式。 若要对 Docker 映像进行专用存储，可以将 AKS 与 Azure 容器注册表 (ACR) 集成。

要创建专用映像存储，请参阅 [Azure 容器注册表][acr-docs]。

## <a name="kubernetes-certification"></a>Kubernetes 认证

AKS 已被 CNCF 认证为符合 Kubernetes 规范。

## <a name="regulatory-compliance"></a>法规符合性

AKS 符合 SOC、ISO、PCI DSS 和 HIPAA 规范。 有关详细信息，请参阅 [Microsoft Azure 合规性概述][compliance-doc]。

## <a name="next-steps"></a>后续步骤

学习 Azure CLI 快速入门，了解有关部署和管理 AKS 的详细信息。

> [!div class="nextstepaction"]
> [使用 Azure CLI 部署 AKS 群集][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/overview/trusted-cloud/compliance/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: /previous-versions/azure/dev-spaces/
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: monitor-aks-reference.md#resource-logs
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md