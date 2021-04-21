---
title: 在 Azure Kubernetes 服务 (AKS) 中启用基于主机的加密
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中配置基于主机的加密
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772912"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes 服务 (AKS) 中基于主机的加密（预览版）

使用基于主机的加密，可将存储在 AKS 代理节点 VM 的 VM 主机上的数据静态加密，并以加密形式将其传送到存储服务。 这意味着，临时磁盘将通过平台管理的密钥静态加密。 OS 和数据磁盘的缓存将通过平台管理的密钥或客户管理的密钥（具体取决于在这些磁盘上设置的加密类型）进行静态加密。 默认情况下，使用 AKS 时，OS 和数据磁盘将通过平台管理的密钥静态加密，这意味着，这些磁盘的缓存默认也会通过平台管理的密钥静态加密。  可以遵循[对 Azure Kubernetes 服务中的 Azure 磁盘使用自带密钥 (BYOK)](azure-disk-customer-managed-keys.md) 中所述指定自己管理的密钥。 然后，这些磁盘的缓存也会使用你在此步骤中指定的密钥进行加密。


## <a name="before-you-begin"></a>准备阶段

只能在创建群集或创建节点池时设置此功能。

> [!NOTE]
> 基于主机的加密可在支持 Azure 托管磁盘服务器端加密的 [Azure 区域][supported-regions]中使用，并且只能对特定的[受支持 VM 大小][supported-sizes]使用。

### <a name="prerequisites"></a>先决条件

- 确保已安装 `aks-preview` CLI 扩展 v0.4.73 或更高版本。
- 确保已在 `Microsoft.ContainerService` 下启用 `EnableEncryptionAtHostPreview` 功能标志。

在对 Azure Kubernetes 服务群集使用 EncryptionAtHost 属性之前，必须先为订阅启用该功能。 请按照以下步骤为订阅启用此功能：

1. 执行以下命令，为订阅注册此功能

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. 在试用该功能之前，请使用以下命令检查注册状态是否为“已注册”（需要几分钟）。

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建使用基于主机的加密的 AKS 群集，需要安装最新的 *aks-preview* CLI 扩展。 使用 [az extension add][az-extension-add] 命令安装 *aks-preview* Azure CLI 扩展，或者使用 [az extension update][az-extension-update] 命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

- 只能在新的节点池上启用。
- 只能在支持 Azure 托管磁盘服务器端加密的 [Azure 区域][supported-regions]中启用，并且只能对特定的[受支持 VM 大小][supported-sizes]启用。
- 需要一个 AKS 群集，以及一个基于虚拟机规模集 (VMSS)（用作 VM 集类型）的节点池。

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>在新群集上使用基于主机的加密（预览版）

创建群集时，将群集代理节点配置为使用基于主机的加密。 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

若要创建不使用基于主机的加密的群集，可以省略 `--enable-encryption-at-host` 参数。

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>在现有群集上使用基于主机的加密（预览版）

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
