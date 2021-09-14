---
title: 在 Azure Kubernetes 服务 (AKS) 中启用容器存储接口 (CSI) 驱动程序
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中为 Azure 磁盘和 Azure 文件存储启用容器存储接口 (CSI) 驱动程序。
services: container-service
ms.topic: article
ms.date: 08/31/2021
author: palma21
ms.openlocfilehash: 7fe0aa073cf1ecb959bc7999ba59a2486c65b7e1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429003"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中为 Azure 磁盘和 Azure 文件存储启用容器存储接口 (CSI) 驱动程序

容器存储接口 (CSI) 是有关在 Kubernetes 上的容器化工作负载中公开任意块和文件存储系统的一套标准。 Azure Kubernetes 服务 (AKS) 可以采用 CSI 来编写、部署和迭代插件，以在 Kubernetes 中公开新的或改进现有的存储系统，而无需改动核心 Kubernetes 代码并等待经历代码发布周期。

借助 AKS 中的 CSI 存储驱动程序支持，可在本地使用以下功能：
- [Azure 磁盘](azure-disk-csi.md)，可用于创建 Kubernetes DataDisk 资源 。 Azure 磁盘可以使用由高性能 SSD 支持的 Azure 高级存储，也可以使用由普通 HDD 或标准 SSD 支持的 Azure 标准存储。 对于大部分生产和开发工作负载，请使用高级存储。 Azure 磁盘以 ReadWriteOnce 的形式装载，因此仅可用于单个 Pod。 对于可同时由多个 Pod 访问的存储卷，请使用 Azure 文件存储。
- [Azure 文件存储](azure-files-csi.md)，可用于将 Azure 存储帐户支持的 SMB 3.0 共享装载到 Pod。 使用 Azure 文件存储可在多个节点和 Pod 之间共享数据。 Azure 文件存储可以使用由普通 HDD 支持的 Azure 标准存储，也可以使用由高性能 SSD 支持的 Azure 高级存储。

> [!IMPORTANT]
> 从 Kubernetes 版本 1.21 开始，Kubernetes 仅使用 CSI 驱动程序，这也是它的默认做法。 这些驱动程序代表了 Kubernetes 中将来所支持的存储的趋势。
> 
> 在升级到 AKS 1.21 之前，请删除手动安装的开源 Azure 磁盘和 Azure 文件 CSI 驱动程序。
> 
> “树中驱动程序”是指包含在核心 Kubernetes 代码中的当前存储驱动程序，而不是新的 CSI 驱动程序（插件）。

## <a name="limitations"></a>限制

- 只能在创建群集时设置此功能。
- 支持 CSI 驱动程序的最低 Kubernetes 次要版本为 v1.17。
- 默认存储类将是 `managed-csi` 存储类。

## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>创建可使用 CSI 存储驱动程序的新群集

使用以下 CLI 命令为 Azure 磁盘和 Azure 文件存储创建可使用 CSI 存储驱动程序的新群集。 使用 `--aks-custom-headers` 标志设置 `EnableAzureDiskFileCSIDriver` 功能。

创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

创建支持 CSI 存储驱动程序的 AKS 群集：

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

如果要在树存储驱动程序而不是 CSI 存储驱动程序中创建群集，可以省略自定义参数 `--aks-custom-headers`。


运行以下命令检查可将多少个基于 Azure 磁盘的卷附加到此节点：

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>后续步骤

- 若要将 CSI 驱动器用于 Azure 磁盘，请参阅[通过 CSI 驱动程序使用 Azure 磁盘](azure-disk-csi.md)。
- 若要将 CSI 驱动器用于 Azure 文件存储，请参阅[通过 CSI 驱动程序使用 Azure 文件存储](azure-files-csi.md)。
- 有关存储最佳做法的详细信息，请参阅[有关 Azure Kubernetes 服务中存储和备份的最佳做法][operator-best-practices-storage]。

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
