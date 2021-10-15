---
title: 创建具有静态公共 IP 地址的 VM - Azure CLI
titlesuffix: Azure Virtual Network
description: 使用 Azure CLI 创建具有静态公共 IP 地址的虚拟机 (VM)。 静态公共 IP 地址是永远不会更改的地址。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ea60d6b2784972f2a4e3210ad815c4823a27a19
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367524"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>使用 Azure CLI 创建具有静态公共 IP 地址的虚拟机

在本文中，我们将创建具有静态公共 IP 地址的 VM。 使用公共 IP 地址可以通过 Internet 与虚拟机通信。 分配静态公共 IP 地址而非动态地址可以确保地址永远不会改变。 

公共 IP 地址会产生[少许费用](https://azure.microsoft.com/pricing/details/ip-addresses)。 每个订阅可以使用的公共 IP 地址数有[限制](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本教程需要 Azure CLI 版本 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#az_group_create) 在 eastus2 位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 创建标准公共 IPv4 地址。

以下命令在 myResourceGroup 中创建名为 myPublicIP 的区域冗余公共 IP 地址。

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。 

以下命令将创建一个 Windows Server 虚拟机。 我们将输入先前在 `-PublicIPAddressName` 参数中创建的公共 IP 地址的名称。 出现提示时，请提供要用作虚拟机的凭据的用户名和密码：

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group TutorVMRoutePref-rg \
    --public-ip-address myPublicIP \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

有关公共 IP SKU 的详细信息，请参阅[公共 IP 地址 SKU](public-ip-addresses.md#sku)。 可将虚拟机添加到 Azure 负载均衡器的后端池。 公共 IP 地址的 SKU 必须与负载均衡器的公共 IP 的 SKU 相匹配。 有关详细信息，请参阅 [Azure 负载均衡器](../../load-balancer/skus.md)。

使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 查看分配的公共 IP 地址，并确认它是否创建为静态地址：

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress,publicIpAllocationMethod,sku] \
    --output table
```

> [!WARNING]
> 不要修改虚拟机操作系统中的 IP 地址设置。 操作系统不知道 Azure 公共 IP 地址。 虽然可以向操作系统添加专用 IP 地址设置，但除非必要，否则我们建议不要这样做，而只能阅读[向操作系统添加专用 IP 地址](virtual-network-network-interface-addresses.md#private)之后才执行此操作。

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 将其删除：

```azurecli-interactive
  az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的[公共 IP 地址](public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。
- 详细了解[专用 IP 地址](private-ip-addresses.md)以及如何为 Azure 虚拟机分配[静态公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)。
- 详细了解如何创建 [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机。