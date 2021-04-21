---
title: 创建虚拟网络 - 快速入门 - Azure CLI
titlesuffix: Azure Virtual Network
description: 本快速入门介绍如何使用 Azure CLI 创建虚拟网络。 虚拟网络能让 Azure 资源互相通信以及与 Internet 通信。
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 407207c0dcb6270f08fb511a01e6e4e835b9fab9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776746"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建虚拟网络

虚拟网络能让 Azure 资源（例如虚拟机 (VM)）彼此之间私下通信以及与 Internet 进行通信。 

本快速入门介绍如何创建虚拟网络。 创建虚拟网络后，将两个 VM 部署到该虚拟网络中。 然后可以从 Internet 连接到 VM，并通过新的虚拟网络进行私下通信。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本快速入门需要 Azure CLI 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group-and-a-virtual-network"></a>创建资源组和虚拟网络

在创建虚拟网络之前，必须创建一个资源组用于托管该虚拟网络。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 此示例在 Eastus 位置创建一个名为“CreateVNetQS-rg”的资源组：

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 此示例创建一个名为“myVNet”的默认虚拟网络，其中包含一个名为“default”的子网 ：

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM。

### <a name="create-the-first-vm"></a>创建第一个 VM

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 

如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。 

`--no-wait` 选项在后台创建 VM。 可以继续执行下一步。 

此示例创建一个名为“myVM1”的 VM：

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>创建第二个 VM

你在上一步中使用了 `--no-wait` 选项。 你可以继续创建名为“myVM2”的第二个 VM。

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="azure-cli-output-message"></a>Azure CLI 输出消息

创建 VM 可能需要数分钟的时间。 Azure 创建 VM 后，Azure CLI 会返回如下输出：

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>VM 公共 IP

若要获取公共 IP 地址 myVM2，请使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)：

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

在此命令中，将 `<publicIpAddress>` 替换为 myVM2 VM 的公共 IP 地址：

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>VM 之间进行通信

若要确认 myVM2 VM 和 myVM1 VM 之间的专用通信，请输入此命令 ：

```bash
ping myVM1 -c 4
```

将从 10.0.0.4 收到四条回复  。

退出与 myVM2 VM 的 SSH 会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 将其删除：

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>后续步骤

在本快速入门： 

* 已创建了默认虚拟网络和两个 VM。 
* 从 Internet 连接到了其中一个 VM，并在两个 VM 之间进行了私下通信。

VM 之间的专用通信在虚拟网络中不受限制。 

转到下一篇文章，详细了解如何配置不同类型的 VM 网络通信：
> [!div class="nextstepaction"]
> [筛选网络流量](tutorial-filter-network-traffic.md)
