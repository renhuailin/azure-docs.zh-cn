---
title: 教程：创建 NAT 网关 - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: 开始使用 Azure CLI 创建 NAT 网关。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: c798651cd4a79cee247ac79811cb633fb9a6be5e
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438762"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>教程：使用 Azure CLI 创建 NAT 网关

本教程介绍如何使用 Azure 虚拟网络 NAT 服务。 你将创建一个 NAT 网关，以便为 Azure 中的虚拟机提供出站连接。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建虚拟网络。
> * 创建虚拟机。
> * 创建 NAT 网关并将其与虚拟网络关联。
> * 连接到虚拟机并验证 NAT IP 地址。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本快速入门需要 Azure CLI 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在 **eastus2** 位置创建名为 **myResourceGroupNAT** 的资源组。

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>创建 NAT 网关

本部分介绍如何创建 NAT 网关和支持资源。

### <a name="create-public-ip-address"></a>创建公共 IP 地址

若要访问 Internet，需要提供 NAT 网关的一个或多个公共 IP 地址。 使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 在 **myResourceGroupNAT** 中创建名为 **myPublicIP** 的公共 IP 地址资源。 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>创建 NAT 网关资源

使用 [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create) 创建全局 Azure NAT 网关。 此命令的结果就是创建名为“myNATgateway”的网关资源，该资源使用公共 IP 地址“myPublicIP” 。 空闲超时设置为 10 分钟。  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 在 myResourceGroup 资源组中创建名为“myVnet”的虚拟网络（其中包含名为“mySubnet”的子网）  。 虚拟网络的 IP 地址空间为 10.1.0.0/16。 虚拟网络中的子网为 10.1.0.0/24。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>创建堡垒主机

创建名为“myBastionHost”的 Azure Bastion 主机以访问虚拟机。 

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 创建 Azure Bastion 子网。

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 为堡垒主机创建公共 IP 地址。 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

使用 [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) 创建堡垒主机。 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>配置源子网的 NAT 服务

我们将使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) 在虚拟网络 **myVnet** 中配置源子网 **mySubnet**，以使用特定的 NAT 网关资源 **myNATgateway**。 此命令将激活指定子网中的 NAT 服务。

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

发往 Internet 目标的所有出站流量现在将使用该 NAT 网关。  无需配置 UDR。


## <a name="virtual-machine"></a>虚拟机

在本部分中，你将创建虚拟机来测试 NAT 网关，以验证出站连接的公共 IP 地址。

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

请先等待虚拟机创建完成，然后再转到下一部分。

## <a name="test-nat-gateway"></a>测试 NAT 网关

在本部分中，我们将测试 NAT 网关。 首先，我们将发现 NAT 网关的公共 IP。 然后，我们将连接到测试虚拟机，并通过 NAT 网关验证出站连接。
    
1. 登录到 [Azure 门户](https://portal.azure.com)

1. 在“概述”屏幕上找到 NAT 网关的公共 IP 地址。 在左侧菜单中选择“所有服务”，选择“所有资源”，然后选择“myPublicIP”。

2. 记下公共 IP 地址：

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="发现 NAT 网关的公共 IP 地址" border="true":::

3. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后从资源列表中选择位于“myResourceGroupNAT”资源组中的“myVM”   。

4. 在“概述”页上，选择“连接”，然后选择“Bastion”  。

5. 选择蓝色的“使用堡垒”按钮。

6. 输入在 VM 创建过程中输入的用户名和密码。

7. 在 myTestVM 中打开 Internet Explorer 。

8. 在地址栏中输入“https://whatsmyip.com”。

9. 验证显示的 IP 地址与你在上一步中记下的 NAT 网关地址是否匹配：

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="显示外部出站 IP 的 Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除虚拟网络、虚拟机和 NAT 网关：

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>后续步骤

有关 Azure 虚拟网络 NAT 的详细信息，请参阅：
> [!div class="nextstepaction"]
> [虚拟网络 NAT 概述](nat-overview.md)
