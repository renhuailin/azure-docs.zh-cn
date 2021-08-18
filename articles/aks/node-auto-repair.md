---
title: 自动修复 Azure Kubernetes 服务 (AKS) 节点
description: 了解节点自动修复功能，以及 AKS 如何修复损坏的工作器节点。
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 6ca726445e7593898609c39e0a503405852098fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748912"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes 服务 (AKS) 节点自动修复

AKS 会持续监视工作器节点的运行状况，在节点运行不正常的情况下对其进行自动修复。 Azure 虚拟机 (VM) 平台[对出现问题的 VM 执行维护][vm-updates]。 

AKS 和 Azure VM 协同工作，以最大程度地减少群集的服务中断次数。

本文档介绍了自动节点修复功能在 Windows 和 Linux 节点上的行为方式。 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS 如何检查运行不正常的节点

AKS 使用以下规则来确定节点是否运行不正常以及是否需要修复： 
* 节点在 10 分钟的时间范围内持续检查时报告“未就绪”状态。
* 节点在 10 分钟内未报告任何状态。

可使用 kubectl 手动检查节点的运行状况状态。

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自动修复的工作原理

> [!Note]
> AKS 使用用户帐户“aks-remediator”启动修复操作。

如果 AKS 标识出持续 10 分钟运行不正常的节点，则 AKS 将执行以下操作：

1. 重新启动该节点。
1. 如果重新启动失败，则对该节点重置映像。

如果自动修复不成功，AKS 工程师会调查其他修正措施。 

如果 AKS 在运行状况检查期间发现多个运行不正常的节点，则在开始另一个修复之前，将单独修复每个节点。


## <a name="limitations"></a>限制

在许多情况下，AKS 可以确定节点是否运行不正常并尝试修复问题，但也有 AKS 无法修复问题或无法检测到问题的情况。 例如，如果由于网络配置错误而没有报告节点状态，则 AKS 无法检测问题。

## <a name="next-steps"></a>后续步骤

使用[可用性区域][availability-zones]增加 AKS 群集工作负载的高可用性。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
