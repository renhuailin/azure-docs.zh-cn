---
title: 创建具有静态专用 IP 地址的 VM - Azure CLI
description: 了解如何使用 Azure CLI 创建具有静态专用 IP 地址的虚拟机。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 1129b4cf06a2de01438f2c83500eee84240183fc
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705264"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-cli"></a>使用 Azure CLI 创建具有静态专用 IP 地址的虚拟机

系统会从你指定的范围自动为虚拟机 (VM) 分配专用 IP 地址。 此范围取决于部署 VM 的子网。 VM 会保留该地址，直到此 VM 被删除。 Azure 从你创建 VM 时所在的子网动态分配下一个可用的专用 IP 地址。 如果需要分配子网中的特定 IP 地址，请向 VM 分配静态 IP 地址。

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

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。 

以下命令将创建一个 Windows Server 虚拟机。 出现提示时，请提供要用作该虚拟机登录凭据的用户名和密码：

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group myResourceGroup \
    --public-ip-address myPublicIP \
    --public-ip-sku Standard \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

## <a name="change-private-ip-address-to-static"></a>将专用 IP 地址更改为静态

在本部分中，你会将之前创建的虚拟机专用 IP 地址从“动态”更改为“静态”。  

使用 [az network nic ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) 更新网络接口配置。

以下命令会将虚拟机专用 IP 地址更改为静态：

```azurecli-interactive
  az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --private-ip-address 10.0.0.4
```

> [!WARNING]
> 虽然可以向操作系统添加专用 IP 地址设置，但建议不要在阅读[向操作系统添加专用 IP 地址](virtual-network-network-interface-addresses.md#private)之前这样做。

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
