---
title: 在 Azure Kubernetes 服务 (AKS) 中启用基于主机的加密
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中配置基于主机的加密
services: container-service
ms.topic: article
ms.date: 04/26/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3eb2f0023cbd0bbe36b466ecf4a1380aa20a2c5c
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446307"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的基于主机的加密

使用基于主机的加密，可将存储在 AKS 代理节点 VM 的 VM 主机上的数据静态加密，并以加密形式将其传送到存储服务。 这意味着，临时磁盘将通过平台管理的密钥静态加密。 OS 和数据磁盘的缓存将通过平台管理的密钥或客户管理的密钥（具体取决于在这些磁盘上设置的加密类型）进行静态加密。 默认情况下，使用 AKS 时，OS 和数据磁盘将通过平台管理的密钥静态加密，这意味着，这些磁盘的缓存默认也会通过平台管理的密钥静态加密。  可以遵循[对 Azure Kubernetes 服务中的 Azure 磁盘使用自带密钥 (BYOK)](azure-disk-customer-managed-keys.md) 中所述指定自己管理的密钥。 然后，这些磁盘的缓存也会使用你在此步骤中指定的密钥进行加密。


## <a name="before-you-begin"></a>准备阶段

只能在创建群集或创建节点池时设置此功能。

> [!NOTE]
> 基于主机的加密可在支持 Azure 托管磁盘服务器端加密的 [Azure 区域][supported-regions]中使用，并且只能对特定的[受支持 VM 大小][supported-sizes]使用。

### <a name="prerequisites"></a>先决条件

- 确保已安装 CLI 扩展 v2.23 或更高版本。
- 确保已在 `Microsoft.Compute` 下启用 `EncryptionAtHost` 功能标志。

### <a name="register-encryptionathost--preview-features"></a>注册 `EncryptionAtHost` 预览功能

若要创建使用基于主机加密的 AKS 群集，必须在订阅上启用 `EncryptionAtHost` 功能标志。

使用 [az feature register][az-feature-register] 命令注册 `EncryptionAtHost` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"
```

状态显示为“已注册”需要几分钟时间。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 `Microsoft.Compute` 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="limitations"></a>限制

- 只能在新的节点池上启用。
- 只能在支持 Azure 托管磁盘服务器端加密的 [Azure 区域][supported-regions]中启用，并且只能对特定的[受支持 VM 大小][supported-sizes]启用。
- 需要一个 AKS 群集，以及一个基于虚拟机规模集 (VMSS)（用作 VM 集类型）的节点池。

## <a name="use-host-based-encryption-on-new-clusters"></a>在新群集上使用基于主机的加密

创建群集时，将群集代理节点配置为使用基于主机的加密。 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

若要创建不使用基于主机的加密的群集，可以省略 `--enable-encryption-at-host` 参数。

## <a name="use-host-based-encryption-on-existing-clusters"></a>在现有群集上使用基于主机的加密

可以通过将新节点池添加到群集，在现有群集上启用基于主机的加密。 使用 `--enable-encryption-at-host` 参数将新节点池配置为使用基于主机的加密。

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

若要创建不使用基于主机的加密功能的新节点池，可以省略 `--enable-encryption-at-host` 参数。

## <a name="next-steps"></a>后续步骤

查看 [AKS 群集安全性最佳做法][best-practices-security]。详细了解[基于主机的加密](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
