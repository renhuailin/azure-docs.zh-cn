---
title: '在 Azure Kubernetes Service 上启用基于主机的加密 (AKS) '
description: 了解如何在 Azure Kubernetes Service (AKS) 群集中配置基于主机的加密
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 66e71dfd6a76fb4e6b464eb5c44dcc809fb9be38
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039727"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service 上基于主机的加密 (AKS)  (预览) 

通过基于主机的加密，存储在 AKS 代理节点 Vm 的 VM 主机上的数据将静态加密，并加密为存储服务。 这意味着临时磁盘会以平台管理的密钥加密。 操作系统和数据磁盘的缓存会以平台管理的密钥或客户托管的密钥进行加密，具体取决于在这些磁盘上设置的加密类型。 默认情况下，使用 AKS 时，操作系统和数据磁盘使用平台管理的密钥进行静态加密，这意味着这些磁盘的缓存也会默认使用平台管理的密钥进行静态加密。  可以 [在 "自带密钥" 中指定自己的托管密钥， (在 Azure Kubernetes Service 中将 azure 磁盘与 azure 磁盘) ](azure-disk-customer-managed-keys.md)。 这些磁盘的缓存还将使用您在此步骤中指定的密钥进行加密。


## <a name="before-you-begin"></a>准备阶段

此功能只能在创建群集或创建节点池时设置。

> [!NOTE]
> 基于主机的加密在支持 Azure 托管磁盘的服务器端加密的 [azure 区域][supported-regions] 中提供，并且仅具有特定 [支持的 VM 大小][supported-sizes]。

### <a name="prerequisites"></a>先决条件

- 确保 `aks-preview` 安装了 CLI extension v 0.4.73 或更高版本。
- 确保 `EnableEncryptionAtHostPreview` 功能标志处于 `Microsoft.ContainerService` 启用状态。

为了能够为 VM 或虚拟机规模集使用主机加密，必须在订阅上启用该功能。 向你的订阅 Id 发送电子邮件 **encryptionAtHost@microsoft.com** ，以便为你的订阅启用该功能。 

> [!IMPORTANT]
> 你必须通过电子邮件发送 **encryptionAtHost@microsoft.com** 订阅 id 才能为计算资源启用该功能。 不能自行为计算资源启用。


### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建基于主机的加密的 AKS 群集，需要使用最新的 *AKS* CLI 扩展。 使用 [az extension add][az-extension-add]命令安装 *aks-preview* Azure CLI 扩展，或使用 [az extension update][az-extension-update]命令检查是否有任何可用更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

- 只能在新的节点池或新群集上启用。
- 只能在支持 Azure 托管磁盘的服务器端加密且仅支持特定[支持的 VM 大小][supported-sizes]的[azure 区域][supported-regions]中启用。
- 需要基于虚拟机规模集 (VMSS) 为 *VM 集类型* 的 AKS 群集和节点池。

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>在新群集上使用基于主机的加密 (预览) 

将群集代理节点配置为在创建群集时使用基于主机的加密。 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

如果要创建不包含基于主机的加密的群集，可以通过省略参数来执行此操作 `--enable-encryption-at-host` 。

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>在现有群集上使用基于主机的加密 (预览) 

通过向群集中添加新的节点池，可以在现有群集上启用基于主机的加密。 使用参数配置新的节点池以使用基于主机的加密 `--enable-encryption-at-host` 。

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

如果要在没有基于主机的加密功能的情况下创建新的节点池，可以通过省略参数来执行此操作 `--enable-encryption-at-host` 。

## <a name="next-steps"></a>后续步骤

查看 [AKS 群集安全性的最佳实践][best-practices-security] 了解有关 [基于主机的加密](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)的详细信息。


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
