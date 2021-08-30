---
title: 概念 - Azure Kubernetes 服务 (AKS) 安全性
description: 了解 Azure Kubernetes 服务 (AKS) 安全性，包括 master 和节点通信、网络策略和 Kubernetes 机密。
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: bf589591ae1c4f9fa3dca2b16cc5382def0740e7
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861205"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中应用程序和群集的安全性相关概念

当你在 Azure Kubernetes Service (AKS) 中运行应用程序工作负载时，群集安全将保护你的客户数据。 

Kubernetes 包括安全组件，如网络策略和机密 。 同时，Azure 包括网络安全组和编排的群集升级等组件。 AKS 结合这些安全组件，以便：
* 让 AKS 群集一直运行最新的 OS 安全更新和 Kubernetes 版本。
* 为敏感凭据提供安全的 pod 流量和访问。

本文介绍用于保护 AKS 中应用程序的核心概念：

- [Azure Kubernetes Service (AKS) 中的应用程序和群集的安全性概念](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [主组件安全](#master-security)
  - [节点安全性](#node-security)
    - [计算隔离](#compute-isolation)
  - [群集升级](#cluster-upgrades)
    - [隔离和排空](#cordon-and-drain)
  - [网络安全](#network-security)
    - [Azure 网络安全组](#azure-network-security-groups)
  - [Kubernetes 机密](#kubernetes-secrets)
  - [后续步骤](#next-steps)

## <a name="master-security"></a>主组件安全

在 AKS 中，Kubernetes 主组件是 Microsoft 提供、管理和维护的托管服务的一部分。 每个 AKS 群集都有其自己的单租户专用 Kubernetes 主组件，用于提供 API 服务器、计划程序等。

默认情况下，Kubernetes API 服务器使用公共 IP 地址和完全限定域名 (FQDN)。 可以使用[经授权的 IP 范围][authorized-ip-ranges]将访问范围限制为 API 服务器终结点。 还可以创建完整的[专用群集][private-clusters]，以限制 API 服务器对虚拟网络的访问。

可使用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC) 和 Azure RBAC 控制对 API 服务器的访问。 有关详细信息，请参阅 [Azure AD 与 AKS 集成][aks-aad]。

## <a name="node-security"></a>节点安全性

AKS 节点是由你管理和维护的 Azure 虚拟机 (VM)。 
* Linux 节点使用 `containerd` 或 Docker 容器运行时运行经过优化的 Ubuntu 发行版。 
* Windows Server 节点使用 `containerd` 或 Docker 容器运行时运行经过优化的 Windows Server 2019 版本。

创建或纵向扩展了 AKS 群集时，会自动使用最新的 OS 安全更新和配置来部署节点。

> [!NOTE]
> AKS 群集使用：
> * Linux 节点池的 Kubernetes 1.19 版本及更高版本使用 `containerd` 作为其容器运行时。 将 `containerd` 与 Windows Server 2019 节点池一起使用目前处于预览阶段。 有关详细信息，请参阅[使用 `containerd` 添加 Windows Server 节点池][aks-add-np-containerd]。
> * Linux 节点池的低于 v1.19 版本的 Kubernetes 使用 Docker 作为其容器运行时。 对于 Windows Server 2019 节点池，Docker 是默认容器运行时。

### <a name="node-security-patches"></a>节点安全修补程序

#### <a name="linux-nodes"></a>Linux 节点
每天晚上，AKS 中的 Linux 节点都会通过其发行版安全更新通道获得安全修补程序。 当在 AKS 群集中部署节点时，会​​自动配置此行为。 为了尽量减少对正在运行的工作负荷的中断和潜在影响，AKS 不会在安全修补程序或内核更新需要进行重启时自动重启节点。 有关如何处理节点重启的详细信息，请参阅[将安全更新和内核更新应用于 AKS 中的节点][aks-kured]。

每晚更新将安全更新应用于节点上的 OS，但用于为群集创建节点的节点映像保持不变。 如果将新的 Linux 节点添加到你的群集，则原始映像将用于创建节点。 这个新节点将在每晚自动检查期间接收所有可用的安全更新和内核更新，但在所有检查和重启完成之前将保持未修补状态。 可以使用节点映像升级来检查和更新群集使用的节点映像。 有关节点映像升级的更多详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 节点映像升级][node-image-upgrade]。

#### <a name="windows-server-nodes"></a>Windows Server 节点

对于 Windows Server 节点，Windows 更新不会自动运行和应用最新的更新。 在定期 Windows 更新发布周期和你自己的验证过程中，在 AKS 群集中计划 Windows Server 节点池升级。 此升级过程会创建运行最新 Windows Server 映像和修补程序的节点，然后删除旧节点。 有关此过程的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

### <a name="node-deployment"></a>节点部署
系统将节点部署到专用虚拟网络子网中，且不分配公共 IP 地址。 为进行故障排除和管理，默认启用 SSH，并只能使用内部 IP 地址进行访问。

### <a name="node-storage"></a>节点存储
为提供存储，节点使用 Azure 托管磁盘。 Azure 托管磁盘是由高性能 SSD 支持的高级磁盘，适用于大多数规模的 VM 节点。 托管磁盘上存储的数据在 Azure 平台内会自动静态加密。 为提高冗余，会在 Azure 数据中心内安全复制 Azure 托管磁盘。

### <a name="hostile-multi-tenant-workloads"></a>恶意多租户工作负载

目前，Kubernetes 环境并不安全，因为可能存在恶意的多租户使用情况。 其他安全性功能（如 Pod 安全策略或用于节点的 Kubernetes RBAC）可有效阻止攻击。 若要在运行恶意多租户工作负载时获得真正的安全性，请只信任虚拟机监控程序。 Kubernetes 的安全域成为整个群集，而不是单个节点。 

对于这些类型的恶意多租户工作负荷，应使用物理隔离的群集。 有关如何隔离工作负载的详细信息，请参阅 [AKS 中的群集隔离最佳做法][cluster-isolation]。

### <a name="compute-isolation"></a>计算隔离

由于合规性或法规要求，某些工作负载可能需要与其他客户工作负载高度隔离。 对于这些工作负载，Azure 提供[独立 VM](../virtual-machines/isolation.md)，以将其用作 AKS 群集中的代理节点。 这些 VM 独立于特定硬件类型，并专用于单个客户。 

创建 AKS 群集或添加节点池时，请选择[其中一个独立 VM 大小](../virtual-machines/isolation.md)作为节点大小。

## <a name="cluster-upgrades"></a>群集升级

Azure 提供升级业务流程工具以升级 AKS 群集和组件、维护安全性和合规性以及访问最新功能。 此升级业务流程同时包括 Kubernetes 主组件和代理组件。 

若要开始进行升级，先指定一个[列出的可用 Kubernetes 版本](supported-kubernetes-versions.md)。 接着 Azure 会安全隔离和排空每个 AKS 节点并进行升级。

### <a name="cordon-and-drain"></a>隔离和排空

在升级过程中，AKS 节点会单独从群集中隔离出来，以防止在其上计划新 Pod。 然后将节点排空并进行升级，操作如下：

1.  将新节点部署到节点池中。 
    * 此节点运行最新的 OS 映像和修补程序。
1. 其中一个现有的节点已确定要升级。 
1. 妥善终止此标识的节点上的 Pod 并在节点池中的其他节点上对其进行安排。
1. 从 AKS 群集中删除此清空节点。
1. 重复步骤 1-4，直到在升级过程中成功替换所有节点。

有关详细信息，请参阅[升级 AKS 群集][aks-upgrade-cluster]。

## <a name="network-security"></a>网络安全性

如需实现本地网络的连接和安全性，可将 AKS 群集部署到现有 Azure 虚拟网络子网。 这些虚拟网络通过 Azure 站点到站点 VPN 或 Express Route 连接回本地网络。 使用专用的内部 IP 地址定义 Kubernetes 入口控制器，以限制服务对内部网络连接的访问。

### <a name="azure-network-security-groups"></a>Azure 网络安全组

为筛选虚拟网络流量流，Azure 使用网络安全组规则。 这些规则定义要允许或拒绝哪些源和目标 IP 范围、端口和协议访问资源。 会创建默认规则以允许 TLS 流量流向 Kubernetes API 服务器。 创建具有负载平衡器、端口映射或入口路由的服务。 AKS 会自动修改流量流的网络安全组。

如果为 AKS 群集提供自己的子网（无论是使用 Azure CNI 还是 Kubenet），请不要修改 AKS 管理的 NIC 级网络安全组。 请改为创建更多子网级网络安全组来修改流量流。 确保其不会干扰管理群集所需的流量，例如负载平衡器访问、与控制平面的通信以及[流出量][aks-limit-egress-traffic]。

### <a name="kubernetes-network-policy"></a>Kubernetes 网络策略

为了限制群集中 Pod 之间的网络流量，AKS 提供了对 [Kubernetes 网络策略][network-policy]的支持。 使用网络策略，可以基于命名空间和标签选择器来允许或拒绝群集中的特定网络路径。

## <a name="kubernetes-secrets"></a>Kubernetes 机密

通过 Kubernetes 机密，将敏感数据注入到 pod，例如访问凭据或密钥。 
1. 使用 Kubernetes API 创建机密。 
1. 定义 pod 或部署，并请求特定机密。 
    * 机密只会提供给具有需要它们的计划 pod 的节点。
    * 机密存储在 tmpfs 中，而不是写入磁盘。 
1. 删除节点上最后一个需要机密的 pod 后，会从节点的 tmpfs 中删除机密。 
   * 机密存储在给定的命名空间中，只有同一命名空间中的 pod 能访问该机密。

使用机密会减少 pod 或服务 YAML 清单中定义的敏感信息。 可以请求存储在 Kubernetes API 服务器中的机密，作为 YAML 清单的一部分。 此方法仅为 pod 提供特定的机密访问权限。 

> [!NOTE]
> 原始机密清单文件包含 base64 格式的机密数据（如需更多详细信息，请参阅[官方文档][secret-risks]）。 将这些文件视为敏感信息，切勿将其提交到源代码管理。

Kubernetes 机密存储在分布式密钥-值存储 etcd 中。 Etcd 存储由 AKS 完全托管，并且[数据在 Azure 平台中静态加密][encryption-atrest]。 

## <a name="next-steps"></a>后续步骤

若要开始为 AKS 群集提供保护，请参阅[升级 AKS 群集][aks-upgrade-cluster]。

如需相关的最佳做法，请参阅 [AKS 中群集安全性和升级的最佳做法][operator-best-practices-cluster-security]和 [AKS 中的 Pod 安全的最佳做法][developer-best-practices-pod-security]。

有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅：

- [Kubernetes/AKS 群集和工作负荷][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 标识][aks-concepts-identity]
- [Kubernetes/AKS 虚拟网络][aks-concepts-network]
- [Kubernetes/AKS 存储][aks-concepts-storage]
- [Kubernetes/AKS 规模][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-add-np-containerd]: windows-container-cli.md#add-a-windows-server-node-pool-with-containerd-preview
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-kured]: node-updates-kured.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
[node-image-upgrade]: node-image-upgrade.md
