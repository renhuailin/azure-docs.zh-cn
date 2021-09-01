---
title: 资源、SKU、区域的限制
titleSuffix: Azure Kubernetes Service
description: 了解 Azure Kubernetes 服务 (AKS) 中的默认配额、受限制的节点 VM SKU 大小和适用地区。
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: ea32c0e58894fe57a51cd22fccce8b2fb2fb0489
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113762299"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的配额、虚拟机大小限制和适用地区

所有 Azure 服务都为资源和功能设置默认限制和配额，包括特定虚拟机 (VM) SKU 的使用限制。

本文详述了 Azure Kubernetes 服务 (AKS) 资源的默认资源限制，以及 Azure 区域中 AKS 的可用性。

## <a name="service-quotas-and-limits"></a>服务配额和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>预配的基础结构

所有其他网络、计算和存储限制均适用于预配的基础结构。 若要了解相关限制，请参阅 [Azure 订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。

> [!IMPORTANT]
> 升级 AKS 群集时，会临时使用其他资源。 这些资源包括虚拟网络子网或虚拟机 vCPU 配额中的可用 IP 地址。 
>
> 对于 Windows Server 容器，可以执行升级操作来应用最新的节点更新。 如果没有可用的 IP 地址空间或 vCPU 配额来处理这些临时资源，群集升级流程将失败。 有关 Windows Server 节点升级过程的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="restricted-vm-sizes"></a>受限制的 VM 大小

AKS 群集中的每个节点都包含固定数量的计算资源，例如 vCPU 和内存。 如果某个 AKS 节点包含的计算资源不足，则 Pod 可能无法正常运行。 若要确保所需的 kube-system Pod 和你的应用程序能够可靠地进行计划，请勿在 AKS 中使用以下 VM SKU：

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1ls
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s
- Standard_A2
- Standard_D1
- Standard_D1_v2
- Standard_DS1
- Standard_DS1_v2

有关 VM 类型及其计算资源的详细信息，请参阅 [Azure 中的虚拟机的大小][vm-skus]。

## <a name="region-availability"></a>上市区域

有关可以在其中部署和运行群集的地点的最新列表，请参阅 [AKS 适用地区][region-availability]。

## <a name="cluster-configuration-presets-in-the-azure-portal"></a>Azure 门户中的群集配置预设

使用 Azure 门户创建群集时，你可以选择预设配置，以便根据方案快速自定义。 你随时都可以修改任何预设值。

| 预设           | 说明                                                            |
|------------------|------------------------------------------------------------------------|
| Standard         | 如果不确定要选择什么，这是最佳选择。 适用于大多数应用程序。 |
| 开发/测试         | 最适合试验 AKS 或部署测试应用程序。 |
| 成本优化   | 最适合降低可容忍中断的生产工作负载的成本。 |
| 批处理 | 最适合机器学习、计算密集型和图形密集型工作负载。 适用于需要快速纵向扩展和横向扩展群集的应用程序。 |
| 强化访问  | 最适合需要完全控制安全性和稳定性的大型企业。 |

## <a name="next-steps"></a>后续步骤

可以增加某些默认限制和配额。 如果资源支持提高配额，请通过 [Azure 支持请求][azure-support]来请求提高配额（请选择“配额”作为“问题类型”） 。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
