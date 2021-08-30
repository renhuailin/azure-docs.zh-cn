---
title: 迁移到 Azure Kubernetes 服务 (AKS)
description: 迁移到 Azure Kubernetes 服务 (AKS)。
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c162a782965156545cf238f8f0489409433a0345
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724761"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>迁移到 Azure Kubernetes 服务 (AKS)

为了帮助你规划和执行到 Azure Kubernetes 服务 (AKS) 的成功迁移，本指南提供了当前建议 AKS 配置的详细信息。 尽管本文并未涵盖每种方案，但它包含有关规划成功迁移的更多详细信息的链接。

本文档可帮助支持以下方案：

* 使用 [Azure Migrate](../migrate/migrate-services-overview.md) 将某些应用程序容器化并迁移到 AKS。
* 将[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)支持的 AKS 群集迁移到[虚拟机规模集](../virtual-machine-scale-sets/overview.md)。
* 迁移 AKS 群集以使用[标准 SKU 负载均衡器](./load-balancer-standard.md)。
* 从 [Azure 容器服务 (ACS) - 2020 年 1 月 31 日停用](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)迁移到 AKS。
* 从 [AKS 引擎](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)迁移到 AKS。
* 从不是基于 Azure 的 Kubernetes 群集迁移到 AKS。
* 将现有资源移到另一个区域。

迁移时，请确保目标 Kubernetes 版本在 AKS 支持的范围内。 旧版本可能不在支持的范围内，需要 AKS 支持版本升级。 有关详细信息，请参阅 [AKS 支持的 Kubernetes 版本](./supported-kubernetes-versions.md)。

若要迁移到较新版本的 Kubernetes，请参阅 [Kubernetes 版本和版本偏差支持策略](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)。

根据具体的方案，可以借助多个开源工具来完成迁移：

* [Velero](https://velero.io/)（需要 Kubernetes 1.7+）
* [Azure Kube CLI 扩展](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

本文将汇总以下各项的迁移详细信息：

> [!div class="checklist"]
> * 通过 Azure Migrate 将应用程序容器化 
> * 包含标准负载均衡器和虚拟机规模集的 AKS
> * 现有的附加 Azure 服务
> * 确保有效配额
> * 高可用性和业务连续性
> * 无状态应用程序的注意事项
> * 有状态应用程序的注意事项
> * 群集配置的部署

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>使用 Azure Migrate 将应用程序迁移到 AKS

Azure Migrate 提供了一个统一平台来评估本地服务器、基础结构、应用程序和数据并将其迁移到 Azure。 对于 AKS，可以使用 Azure Migrate 进行以下任务：

* [将 ASP.NET 应用程序容器化并迁移到 AKS](../migrate/tutorial-app-containerization-aspnet-kubernetes.md)
* [将Java Web 应用程序容器化并迁移到 AKS](../migrate/tutorial-app-containerization-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>包含标准负载均衡器和虚拟机规模集的 AKS

AKS 是一个托管服务，它提供独特的功能和较低的管理开销。 由于 AKS 是托管服务，因此必须从 AKS 支持的一系列[区域](./quotas-skus-regions.md)中进行选择。 可能需要修改现有应用程序，使其在从现有群集转换到 AKS 期间在 AKS 托管的控制平面上保持正常运行。

建议使用[虚拟机规模集](../virtual-machine-scale-sets/index.yml)和 [Azure 标准负载均衡器](./load-balancer-standard.md)支持的 AKS 群集，以确保获得各种功能，例如：
* [多个节点池](./use-multiple-node-pools.md)，
* [可用性区域](../availability-zones/az-overview.md)，
* [授权的 IP 范围](./api-server-authorized-ip-ranges.md)，
* [群集自动缩放程序](./cluster-autoscaler.md)，
* [适用于 AKS 的 Azure Policy](../governance/policy/concepts/policy-for-kubernetes.md)，以及
* 其他新功能（如果发布）。

[虚拟机可用性集](../virtual-machines/availability.md#availability-sets)支持的 AKS 群集缺少上述许多功能的支持。

以下示例创建了一个 AKS 群集，其中包含一个虚拟机 (VM) 规模集支持的单一节点池。 群集：
* 使用标准负载均衡器。 
* 在群集的节点池中启用群集自动缩放程序。
* 设置节点（最少 1 个，最多 3 个） 。

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>现有的附加 Azure 服务

在迁移群集时，你可能附加了外部 Azure 服务。 虽然以下服务不需要重新创建资源，但需要更新从以前群集到新群集的连接以维持正常运行。

* Azure 容器注册表
* Log Analytics
* Application Insights
* 流量管理器
* 存储帐户
* 外部数据库

## <a name="ensure-valid-quotas"></a>确保有效配额

由于在迁移期间要将其他 VM 部署到订阅中，因此，应该验证配额和限制是否足以应对这些资源。 如有必要，可请求增加 [vCPU 配额](../azure-portal/supportability/per-vm-quota-requests.md)。

可能需要请求提高[网络配额](../azure-portal/supportability/networking-quota-requests.md)，以确保不会耗尽 IP。 有关详细信息，请参阅 [AKS 的网络和 IP 范围](./configure-kubenet.md)。

有关详细信息，请参阅 [Azure 订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。 若要查看当前配额，请在 Azure 门户中转到[订阅边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，选择自己的订阅，然后选择“用量 + 配额”。

## <a name="high-availability-and-business-continuity"></a>高可用性和业务连续性

如果应用程序无法处理故障时间，则需要遵循高可用性迁移方案的最佳做法。 请详细阅读 [Azure Kubernetes 服务 (AKS) 中实现复杂业务连续性规划、灾难恢复和最大程度延长运行时间的最佳做法](./operator-best-practices-multi-region.md)。

对于复杂应用程序，通常会在一段时间内迁移，而不是一次性全部迁移，这意味着旧环境和新环境可能需要通过网络进行通信。 以前使用 `ClusterIP` 服务进行通信的应用程序可能需要公开为 `LoadBalancer` 类型，并得到相应的保护。

若要完成迁移，需将客户端指向 AKS 上运行的新服务。 重定向流量的建议方法是将 DNS 更新为指向 AKS 群集前面的负载均衡器。

[Azure 流量管理器](../traffic-manager/index.yml)可将客户定向到所需的 Kubernetes 群集和应用程序实例。 流量管理器是可以在区域间分布网络流量的基于 DNS 的流量负载均衡器。 为获得最佳性能和冗余，在进入 AKS 群集之前，通过流量管理器来定向所有应用程序流量。 

在多群集部署中，客户应连接到指向每个 AKS 群集上的服务的流量管理器 DNS 名称。 使用流量管理器终结点定义这些服务。 每个终结点都是服务负载均衡器 IP。 使用此配置可将网络流量从一个区域的流量管理器终结点定向到另一个区域的终结点。

![将 AKS 与流量管理器配合使用](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door 服务](../frontdoor/front-door-overview.md)是路由 AKS 群集流量的另一种选择。 借助 Azure Front Door 服务可以进行优化以实现最佳性能以及进行即时全球故障转移以实现高可用性，并以此定义、管理和监视 Web 流量的全局路由。 

### <a name="considerations-for-stateless-applications"></a>无状态应用程序的注意事项

无状态应用程序的迁移最直截了当：
1. 将资源定义（YAML 或 Helm）应用于新群集。
1. 确保所有内容都按预期方式运行。
1. 重定向流量以激活新群集。

### <a name="considerations-for-stateful-applications"></a>有状态应用程序的注意事项

精心规划有状态应用程序的迁移，以避免数据丢失或意外停机。

* 如果使用 Azure 文件存储，则可以将文件共享作为卷装载到新群集中。 请参阅[将静态 Azure 文件存储作为卷装载](./azure-files-volume.md#mount-file-share-as-an-persistent-volume)。
* 如果使用 Azure 托管磁盘，则只能装载未附加到任何 VM 的磁盘。 请参阅[将静态 Azure 磁盘作为卷装载](./azure-disk-volume.md#mount-disk-as-volume)。
* 如果这两种方法都不起作用，可以使用备份和还原选项。 请参阅 [Azure 上的 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)。

#### <a name="azure-files"></a>Azure 文件

与磁盘不同，Azure 文件可同时装载到多个主机。 在 AKS 群集中，Azure 和 Kubernetes 都不会阻止你创建 AKS 群集仍在使用的 Pod。 若要防止数据丢失和意外行为，请确保这两个群集不会同时向相同的文件写入数据。

如果应用程序可以托管指向同一文件共享的多个副本，请遵循无状态迁移步骤，将 YAML 定义部署到新群集。 

否则，可以采用包括以下步骤的可行迁移方法：

1. 验证应用程序是否正常工作。
1. 将实时流量指向新的 AKS 群集。
1. 断开旧群集的连接。

若要从空共享开始，然后创建源数据的副本，可以使用 [`az storage file copy`](/cli/azure/storage/file/copy) 命令迁移数据。


#### <a name="migrating-persistent-volumes"></a>迁移永久性卷。

将现有的永久性卷迁移到 AKS 时，通常需要遵循以下步骤：

1. 暂停写入到应用程序。 
    * 此步骤是可选的，需要停机。
1. 创建磁盘的快照。
1. 从快照创建新的托管磁盘。
1. 在 AKS 中创建永久性卷。
1. 将 Pod 规范更新为[使用现有卷](./azure-disk-volume.md)而不是 PersistentVolumeClaims（静态预配）。
1. 将应用程序部署到 AKS。
1. 验证应用程序是否正常工作。
1. 将实时流量指向新的 AKS 群集。

> [!IMPORTANT]
> 如果不暂停写入，则需要将数据复制到新部署。 否则在创建磁盘快照后，写入的数据将会丢失。

可以借助一些开源工具来创建托管磁盘，并在 Kubernetes 群集之间迁移卷：

* [Azure CLI 磁盘复制扩展](https://github.com/noelbundick/azure-cli-disk-copy-extension)可跨资源组和 Azure 区域复制并转换磁盘。
* [Azure Kube CLI 扩展](https://github.com/yaron2/azure-kube-cli)可枚举 ACS Kubernetes 卷并将其迁移到 AKS 群集。


### <a name="deployment-of-your-cluster-configuration"></a>群集配置的部署

建议使用现有的持续集成 (CI) 和持续交付 (CD) 管道将已知正常的配置部署到 AKS。 可以使用 Azure Pipelines [生成应用程序并将其部署到 AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template)。 克隆现有的部署任务，并确保 `kubeconfig` 指向新的 AKS 群集。

如果无法做到这一点，请从现有 Kubernetes 群集导出资源定义，并将其应用到 AKS。 可以使用 `kubectl` 导出对象。 例如：

```console
kubectl get deployment -o yaml > deployments.yaml
```

请务必检查输出并删除任何不必要的实时数据字段。

### <a name="moving-existing-resources-to-another-region"></a>将现有资源移到另一个区域

你可能需要将 AKS 群集移到 [AKS 支持的不同区域][region-availability]。 我们建议你在其他区域中创建一个新群集，然后将资源和应用程序部署到新群集。 

此外，如果在 AKS 群集上运行任何服务，则需要在新区域中的群集上安装和配置这些服务。


本文汇总了以下各项的迁移详细信息：

> [!div class="checklist"]
> * 包含标准负载均衡器和虚拟机规模集的 AKS
> * 现有的附加 Azure 服务
> * 确保有效配额
> * 高可用性和业务连续性
> * 无状态应用程序的注意事项
> * 有状态应用程序的注意事项
> * 群集配置的部署


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service