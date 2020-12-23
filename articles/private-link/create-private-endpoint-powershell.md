---
title: 快速入门 - 使用 Azure PowerShell 创建 Azure 专用终结点
description: 借助本快速入门了解如何使用 Azure PowerShell 创建专用终结点。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/02/2020
ms.author: allensu
ms.openlocfilehash: 7add424c23e430a8ca5059d45acd037fff8836ad
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368655"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建专用终结点

使用专用终结点安全连接到 Azure Web 应用以开始使用 Azure 专用链接。

在本快速入门中，你将创建 Azure Web 应用的专用终结点，并部署虚拟机以测试专用连接。  

可以为不同类型的 Azure 服务（例如 Azure SQL 和 Azure 存储）创建专用终结点。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure 订阅中部署了 PremiumV2 层或更高版本应用服务计划的 Azure Web 应用。  
    * 有关详细信息及示例，请参阅[快速入门：在 Azure 中创建 ASP.NET Core Web 应用](../app-service/quickstart-dotnetcore.md)。 
    * 有关创建 Web 应用和终结点的详细教程，请参阅[教程：使用 Azure 专用终结点连接到 Web 应用](tutorial-private-endpoint-webapp-portal.md)。

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组：

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>创建虚拟网络和堡垒主机

在本部分中，你将创建虚拟网络、子网和堡垒主机。 

堡垒主机将用于安全地连接到虚拟机，以测试专用终结点。

使用以下命令创建虚拟网络和堡垒主机：

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

部署 Azure Bastion 主机需要几分钟时间。

## <a name="create-test-virtual-machine"></a>创建测试虚拟机

在本部分中，你将创建将用来测试专用终结点的虚拟机。

使用以下内容创建虚拟机：

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-private-endpoint"></a>创建专用终结点

在本部分中，你将使用以下命令创建专用终结点和连接：

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##
## Replace <your-webapp-name> with your webapp name ##
$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域

在本部分中，你将使用以下命令创建和配置专用 DNS 区域：

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
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
用完专用终结点和 VM 后，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组和组内所有资源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
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
