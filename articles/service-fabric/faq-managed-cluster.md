---
title: 有关 Service Fabric 托管群集的常见问题
description: 有关 Service Fabric 托管群集的常见问题解答，包括功能、用例和常见方案。
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 02/15/2021
ms.custom: references_regions
ms.openlocfilehash: aa77896ba88d0ffd0a6f94a84603b5f4a1803357
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633081"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Service Fabric 托管群集常见问题

下面是一些 Service Fabric 托管群集的常见问题 (FAQ) 和答案（预览版）。

## <a name="general"></a>常规

### <a name="what-are-service-fabric-managed-clusters"></a>什么是 Service Fabric 托管群集？

Service Fabric 托管群集是 Service Fabric 群集资源模型的演变，旨在简化群集的部署和管理。 Service Fabric 托管群集使用 Azure 资源管理器封装模型，因此，用户只需定义和部署单个群集资源，而不是像现在这样必须部署许多独立资源（虚拟机规模集、负载均衡器和 IP 等）。

### <a name="what-regions-are-supported-in-the-preview"></a>预览版支持哪些区域？

Service Fabric 托管群集预览版支持的区域包括 `centraluseuap`、`eastus2euap`、`eastasia`、`northeurope`、`westcentralus` 和 `eastus2`。

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>我是否可以将现有 Service Fabric 群集就地迁移到托管群集资源？

不知道。 此时，需要创建新的 Service Fabric 群集资源以使用新的 Service Fabric 托管群集资源类型。

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Service Fabric 托管群集是否额外收费？

不知道。 除群集所需的基础计算、存储和网络资源成本以外，Service Fabric 托管群集不会产生额外的费用。

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Service Fabric 托管群集资源是否引入了新的 SLA？

SLA 不会从当前 Service Fabric 资源模型发生更改。

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>基本和标准 SKU 群集之间的有什么区别？

基本 SKU 群集是指 Service Fabric 资源提供程序提供的大多数配置。 基本 SKU 群集用于测试和预生产环境。 标准 SKU 群集允许用户配置群集以满足其特定需求。 有关详细信息，请参阅 [Service Fabric 托管群集 SKU](./overview-managed-cluster.md#service-fabric-managed-cluster-skus)。

## <a name="cluster-deployment-and-management"></a>群集部署和管理

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>我在我的虚拟机规模集上运行自定义脚本扩展，是否可以继续使用托管 Service Fabric 资源执行此操作？

可以，你可以在托管群集节点类型上指定 VM 扩展。 有关详细信息，请参阅[将规模集扩展添加到 Service Fabric 托管群集节点类型](how-to-managed-cluster-vmss-extension.md)。

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>我想要使用仅限内部的负载均衡器，这可能吗？

不知道。 目前不能使用仅限内部的负载均衡器。 建议锁定网络安全组 (NSG) 规则以阻止任何不需要的入站/出站流量。

### <a name="can-i-autoscale-my-cluster"></a>我是否可以自动缩放群集？

预览版当前不提供自动缩放功能。

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>是否可以跨可用性区域部署群集？

预览版当前不提供跨可用性区域群集功能。

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>是否可以在群集运行时的自动和手动升级之间进行选择？

在预览版中，所有运行时升级都是自动完成的。

## <a name="applications"></a>应用程序

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>是否存在 Service Fabric 托管群集本地开发体验？

本地开发体验在现有 Service Fabric 群集中保持不变。 有关详细信息，请参阅[设置开发环境](./service-fabric-get-started.md)以获取有关本地开发体验的更多详细信息。

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>我可以将应用程序部署为 Azure 资源管理器资源吗？

是的。 已添加支持，可将应用程序作为 Azure 资源管理器资源部署（使用 PowerShell 和 CLI 进行部署除外）。 若要开始，请参阅[使用 ARM 模板部署 Service Fabric 托管群集应用程序](how-to-managed-cluster-app-deployment-template.md)。
