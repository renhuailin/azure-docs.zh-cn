---
title: 快速入门 - 使用 Azure CLI 创建 Azure 专用终结点
description: 借助本快速入门了解如何使用 Azure CLI 创建专用终结点。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: bba912930a9dff0a79e0b0d81025b7524c238db0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368672"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>快速入门：使用 Azure CLI 创建专用终结点

使用专用终结点安全连接到 Azure Web 应用以开始使用 Azure 专用链接。

在本快速入门中，你将创建 Azure Web 应用的专用终结点，并部署虚拟机以测试专用连接。  

可以为不同类型的 Azure 服务（例如 Azure SQL 和 Azure 存储）创建专用终结点。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure 订阅中部署了 PremiumV2 层或更高版本应用服务计划的 Azure Web 应用。  
    * 有关详细信息及示例，请参阅[快速入门：在 Azure 中创建 ASP.NET Core Web 应用](../app-service/quickstart-dotnetcore.md)。 
    * 有关创建 Web 应用和终结点的详细教程，请参阅[教程：使用 Azure 专用终结点连接到 Web 应用](tutorial-private-endpoint-webapp-portal.md)。
* 登录 Azure 门户，并通过运行 `az login` 来检查订阅是否处于活动状态。
* 通过运行 `az --version` 在终端或命令窗口中检查 Azure CLI 版本。 有关最新版本，请参阅[最新发行说明](/cli/azure/release-notes-azure-cli?tabs=azure-cli)。
  * 如果没有最新版本，请按照[适用于你操作系统或平台的安装指南](/cli/azure/install-azure-cli)来更新安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组：

* 命名为 CreatePrivateEndpointQS-rg。 
* 在位置“eastus”中。

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>创建虚拟网络和堡垒主机

在本部分中，你将创建虚拟网络、子网和堡垒主机。 

堡垒主机将用于安全地连接到虚拟机，以测试专用终结点。

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络

* 命名为“myVNet”。
* 地址前缀为 10.0.0.0/16。
* 子网命名为“myBackendSubnet”。
* 子网前缀为 10.0.0.0/24。
* 在 CreatePrivateEndpointQS-rg 资源组中。
* eastus 的位置。

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

更新子网，以使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 禁用专用终结点的专用终结点网络策略：

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 为堡垒主机创建公共 IP：

* 创建名为“myBastionIP”的标准区域冗余公共 IP 地址。
* 在 CreatePrivateEndpointQS-rg 中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 创建堡垒子网：

* 命名为 AzureBastionSubnet。
* 地址前缀为 10.0.1.0/24。
* 在虚拟网络“myVNet”中。
* 在资源组 CreatePrivateEndpointQS-rg 中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 创建堡垒主机：

* 命名为 myBastionHost。
* 在 CreatePrivateEndpointQS-rg 中。
* 与公共 IP myBastionIP 相关联。
* 与虚拟网络 myVNet 相关联。
* 在位置“eastus”中。

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主机需要几分钟时间。

## <a name="create-test-virtual-machine"></a>创建测试虚拟机

在本部分中，你将创建将用来测试专用终结点的虚拟机。

使用  [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 出现提示时，请提供要用作 VM 凭据的密码：

* 命名为 myVM。
* 在 CreatePrivateEndpointQS-rg 中。
* 在网络 myVNet 中。
* 在子网“myBackendSubnet”中。
* 服务器映像 Win2019Datacenter。

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>创建专用终结点

在本部分中，你将创建专用终结点。

使用 [az webapp list](/cli/azure/webapp#az_webapp_list) 将之前创建的 Web 应用的资源 ID 放到 shell 变量中。

使用 [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) 创建终结点和连接：

* 命名为 myPrivateEndpoint。
* 在资源组 CreatePrivateEndpointQS-rg 中。
* 在虚拟网络“myVNet”中。
* 在子网“myBackendSubnet”中。
* 名为 myConnection 的连接。
* 你的 Webapp \<webapp-resource-group-name>。

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域

在本部分中，你将使用 [az network private-dns zone create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create) 创建和配置专用 DNS 区域。  

你将使用 [az network private-dns link vnet create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) 创建指向 DNS 区域的虚拟网络链接。

你将使用 [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create) 创建 DNS 区域组。

* 名为 privatelink.azurewebsites.net 的区域
* 在虚拟网络“myVNet”中。
* 在资源组 CreatePrivateEndpointQS-rg 中。
* 名为 myDNSLink 的 DNS 链接。
* 与 myPrivateEndpoint 相关联。
* 名为 MyZoneGroup 的区域组。

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>测试到专用终结点的连接

本部分将使用在上一步骤中创建的虚拟机通过专用终结点连接到 SQL 服务器。

1. 登录到 [Azure 门户](https://portal.azure.com) 
 
2. 在左侧导航窗格中选择“资源组”。

3. 选择“CreatePrivateEndpointQS-rg”。

4. 选择“myVM”。

5. 在 **myVM** 的“概述”页上，选择“连接”，然后选择“堡垒”。

6. 选择蓝色的“使用堡垒”按钮。

7. 输入在创建虚拟机期间输入的用户名和密码。

8. 连接后，在服务器上打开 Windows PowerShell。

9. 输入 `nslookup <your-webapp-name>.azurewebsites.net`。 将 \<your-webapp-name> 替换为在之前的步骤中创建的 Web 应用的名称。  你将收到类似于以下所示内容的消息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    将为 Web 应用名称返回专用 IP 地址 10.0.0.5。  此地址位于你之前创建的虚拟网络的子网中。

10. 在到 myVM 的堡垒连接中，打开 Internet Explorer。

11. 输入 Web 应用的 URL： https://\<your-webapp-name>.azurewebsites.net。

12. 如果你的应用程序尚未部署，你将收到默认 Web 应用页：

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="默认 Web 应用页面。" border="true":::

13. 关闭到 **myVM** 的连接。

## <a name="clean-up-resources"></a>清理资源 
用完专用终结点和 VM 后，请使用 [az group delete](/cli/azure/group#az_group_delete) 删除资源组和组内所有资源：

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：

* 虚拟网络和堡垒主机。
* 虚拟机。
* Azure Web 应用的专用终结点。

你使用虚拟机通过专用终结点安全测试了到 Web 应用的连接。

有关支持专用终结点的服务的详细信息，请参阅：
> [!div class="nextstepaction"]
> [专用链接可用性](private-link-overview.md#availability)
