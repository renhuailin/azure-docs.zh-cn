---
title: 从计算机连接到 VNet - P2S VPN 和 Azure 证书身份验证：PowerShell
description: 了解如何使用 P2S 和自签名证书或 CA 颁发的证书将 Windows 和 macOS 客户端安全地连接到 Azure 虚拟网络。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/03/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62765ceb53d7a66d591b0fa62397b50c7fc04b9a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774037"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-azure-certificate-authentication-powershell"></a>使用 Azure 证书身份验证配置与 VNet 的点到站点 VPN 连接：PowerShell

本文介绍如何将运行 Windows、Linux 或 macOS 的单个客户端安全地连接到 Azure VNet。 若要从远程位置连接到 VNet，例如从家里或会议室进行远程通信，则可使用点到站点 VPN。 如果只有一些客户端需要连接到 VNet，也可使用 P2S VPN 来代替站点到站点 VPN。 点到站点连接不需要 VPN 设备或面向公众的 IP 地址。 P2S 基于 SSTP（安全套接字隧道协议）或 IKEv2 创建 VPN 连接。

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="从计算机连接到 Azure VNet - 点到站点连接示意图":::

有关点到站点 VPN 的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。 若要使用 Azure 门户创建此配置，请参阅[使用 Azure 门户配置点到站点 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>先决条件

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> 本文中的多数步骤都可以使用 Azure Cloud Shell。 但是，不能使用 Cloud Shell 来生成证书。 另外，若要上传根证书公钥，必须使用本地 Azure PowerShell 或 Azure 门户。
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="sign-in"></a><a name="signin"></a>登录

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="declare-variables"></a><a name="declare"></a>声明变量

我们在本文中使用变量，以便你可以轻松地更改值以应用于自己的环境，而无需更改示例本身。 声明要使用的变量。 可以使用以下示例，根据需要将值替换为自己的值。 如果在练习期间的任何时候关闭了 PowerShell/Cloud Shell 会话，只需再次复制和粘贴该值，重新声明变量。

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="create-a-vnet"></a><a name="ConfigureVNet"></a>创建 VNet

1. 创建资源组。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. 为虚拟网络创建子网配置，并将其命名为 FrontEnd 和 GatewaySubnet 。 这些前缀必须是已声明的 VNet 地址空间的一部分。

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. 创建虚拟网络。

   在本示例中，-DnsServer 服务器参数是可选的。 指定一个值不会创建新的 DNS 服务器。 指定的 DNS 服务器 IP 地址应该是可以解析从 VNet 所连接到的资源名称的 DNS 服务器。 此示例使用了专用 IP 地址，但这可能不是你 DNS 服务器的 IP 地址。 请务必使用自己的值。 你指定的值将由部署到 VNet 的资源使用，而不是由 P2S 连接或 VPN 客户端使用。

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. 指定所创建的虚拟网络的变量。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. VPN 网关必须具有公共 IP 地址。 请先请求 IP 地址资源，并在创建虚拟网关时参阅该资源。 创建 VPN 网关时，IP 地址是动态分配给资源的。 VPN 网关当前仅支持动态  公共 IP 地址分配。 不能请求静态公共 IP 地址分配。 但这并不意味着 IP 地址在分配到 VPN 网关后会更改。 公共 IP 地址只在删除或重新创建网关时更改。 该地址不会因为 VPN 网关大小调整、重置或其他内部维护/升级而更改。

   请求动态分配的公共 IP 地址。

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="create-the-vpn-gateway"></a><a name="creategateway"></a>创建 VPN 网关

在此步骤中，将为 VNet 配置和创建虚拟网络网关。

* -GatewayType 必须是 **Vpn**，-VpnType 必须是 **RouteBased**。
* -VpnClientProtocol 用来指定要启用的隧道的类型。 隧道选项为 OpenVPN、SSTP 和 IKEv2 。 可以选择启用其中之一或任何受支持的组合。 如果要启用多个类型，请以逗号分隔的形式指定名称。 不能同时启用 OpenVPN 和 SSTP。 Android 和 Linux 上的 strongSwan 客户端以及 iOS 和 macOS 上的本机 IKEv2 VPN 客户端仅会使用 IKEv2 隧道进行连接。 Windows 客户端会首先尝试 IKEv2，如果不能连接，则会回退到 SSTP。 可以使用 OpenVPN 客户端连接到 OpenVPN 隧道类型。
* 虚拟网络网关“基本”SKU 不支持 IKEv2、OpenVPN 或 RADIUS 身份验证。 如果计划让 Mac 客户端连接到虚拟网络，请不要使用基本 SKU。
* VPN 网关可能需要 45 分钟或更长时间才能完成，具体取决于所选[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md)。 

1. 为 VNet 配置和创建虚拟网络网关。 创建网关大约需要 45 分钟。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. 创建网关后，可以使用以下示例进行查看。 如果在创建网关时关闭了 PowerShell 或 PowerShell 超时，则可以再次[声明变量](#declare)。

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="add-the-vpn-client-address-pool"></a><a name="addresspool"></a>添加 VPN 客户端地址池

创建完 VPN 网关后即可添加 VPN 客户端地址池。 VPN 客户端地址池是 VPN 客户端在连接时要从中接收 IP 地址的范围。 使用专用 IP 地址范围时，该范围不得与要通过其进行连接的本地位置重叠，也不得与要连接到其中的 VNet 重叠。

在此示例中，VPN 客户端地址池在之前的步骤中声明为[变量](#declare)。

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="generate-certificates"></a><a name="Certificates"></a>生成证书

>[!IMPORTANT]
> 不能使用 Azure Cloud Shell 生成证书。 必须使用本节中所述的方法之一。 如果要使用 PowerShell，则必须在本地安装。
>

Azure 使用证书对点到站点 VPN 的 VPN 客户端进行身份验证。 请将根证书的公钥信息上传到 Azure， 然后即可将该公钥视为“可信”公钥。 必须根据可信根证书生成客户端证书，并将其安装在每个客户端计算机的 Certificates-Current User/个人证书存储中。 当客户端启动到 VNet 的连接时，使用证书进行身份验证。 

如果使用自签名证书，这些证书必须使用特定的参数创建。 可以按照 [PowerShell 和 Windows 10](vpn-gateway-certificates-point-to-site.md) 或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)（如果没有 Windows 10）的说明，创建自签名证书。 生成自签名根证书和客户端证书时，必须按说明中的步骤操作，这一点很重要。 否则，生成的证书将不兼容 P2S 连接，并且会出现连接错误。

### <a name="root-certificate"></a><a name="cer"></a>根证书

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. 创建根证书后，将公共证书数据（不是私钥）作为 Base64 编码的 X.509 .cer 文件[导出](vpn-gateway-certificates-point-to-site.md#cer)。

### <a name="client-certificate"></a><a name="generate"></a>客户端证书

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. 生成客户端证书后，将其[导出](vpn-gateway-certificates-point-to-site.md#clientexport)。 客户端证书将分发到将要连接的客户端计算机。

## <a name="upload-root-certificate-public-key-information"></a><a name="upload"></a>上传根证书的公钥信息

验证 VPN 网关是否已创建完毕。 创建完以后，即可为委托给 Azure 的根证书上传 .cer 文件（其中包含公钥信息）。 上传 .cer 文件后，Azure 可以使用该文件对已安装客户端证书（根据可信根证书生成）的客户端进行身份验证。 可在以后根据需要上传更多的可信根证书文件（最多 20 个）。

>[!NOTE]
> 不能使用 Azure Cloud Shell 上传 .cer 文件。 可以在本地计算机上使用 PowerShell，或者可以使用 [Azure 门户步骤](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)。
>

1. 为证书名称声明变量，将值替换为自己的值。

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. 将文件路径替换为自己的路径，然后运行 cmdlet。

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. 将公钥信息上传到 Azure。 上传证书信息以后，Azure 就会将该证书视为受信任的根证书。 上传时，请确保在本地计算机上运行 PowerShell，或者可以使用 [Azure 门户步骤](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)。 不能使用 Azure Cloud Shell 上传。

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="install-an-exported-client-certificate"></a><a name="clientcertificate"></a>安装已导出的客户端证书

以下步骤有助于在 Windows 客户端上进行安装。 有关其他客户端和详细信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

确保已将客户端证书与整个证书链（默认）一起作为 .pfx 导出。 否则，根证书信息就不会出现在客户端计算机上，客户端将无法进行正常的身份验证。

## <a name="configure-the-vpn-client"></a><a name="clientconfig"></a>配置 VPN 客户端

为了使用 P2S 连接到虚拟网络网关，每台计算机都使用作为操作系统的一部分在本地安装的 VPN 客户端。 例如，在 Windows 计算机上转到 VPN 设置时，可以添加 VPN 连接，而无需安装单独的 VPN 客户端。 使用客户端配置包配置每个 VPN 客户端。 客户端配置包包含特定于创建的 VPN 网关的设置。 

可以使用以下快速示例生成和安装客户端配置包。 有关包内容的详细信息以及有关生成和安装 VPN 客户端配置文件的其他说明，请参阅[创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md)。

如果需要再次声明变量，可以在[此处](#declare)找到它们。

### <a name="to-generate-configuration-files"></a>生成配置文件

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>安装客户端配置包

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10.连接到 Azure

### <a name="windows-vpn-client"></a>Windows VPN 客户端

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Mac VPN 客户端

在“网络”对话框中，找到要使用的客户端配置文件，单击“连接”。 
请查看[安装 - Mac (macOS)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) 以获取详细说明。 如果连接有问题，请验证虚拟网络网关是否未使用基本 SKU。 Mac 客户端不支持基本 SKU。

  ![Mac 连接](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>验证连接

这些说明适用于 Windows 客户端。

1. 如果要验证用户的 VPN 连接是否处于活动状态，请打开提升的命令提示符，并运行 *ipconfig/all*。
2. 查看结果。 请注意，收到的 IP 地址是在配置中指定的点到站点 VPN 客户端地址池中的地址之一。 结果与以下示例类似：

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>连接到虚拟机

这些说明适用于 Windows 客户端。

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* 验证是否在为 VNet 指定 DNS 服务器 IP 地址之后，才生成 VPN 客户端配置包。 如果更新了 DNS 服务器 IP 地址，请生成并安装新的 VPN 客户端配置包。

* 使用“ipconfig”检查分配给以太网适配器的 IPv4 地址，该适配器所在的计算机正是你要从其进行连接的计算机。 如果该 IP 地址位于要连接到的 VNet 的地址范围内，或者位于 VPNClientAddressPool 的地址范围内，则称为地址空间重叠。 当地址空间以这种方式重叠时，网络流量不会抵达 Azure，而是呆在本地网络中。

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>添加或删除根证书

可以在 Azure 中添加和删除受信任的根证书。 删除根证书时，如果客户端的证书是从该根证书生成的，则客户端不能进行身份验证，因此无法进行连接。 如果希望客户端进行身份验证和连接，则需安装新客户端证书，该证书是从委托（上传）给 Azure 的根证书生成的。 这些步骤需要 Azure PowerShell cmdlet 本地安装在计算机上（而不是 Azure Cloud Shell）。 还可以使用 Azure 门户来添加根证书。

**添加：**

最多可以将 20 个根证书 .cer 文件添加到 Azure。 以下步骤用于添加根证书。 

1. 准备要上传的 .cer 文件：

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. 上传该文件。 一次只能上传一个文件。

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. 若要验证是否已上传证书文件，请执行以下操作：

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**删除：**

1. 声明变量。 修改该示例中的变量，以匹配要删除的证书。

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. 删除证书。

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. 使用以下示例来验证是否已成功删除证书。

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>撤销或恢复客户端证书

可以吊销客户端证书。 通过证书吊销列表，可以选择性地拒绝基于单个客户端证书的点到站点连接。 这不同于删除受信任的根证书。 如果从 Azure 中删除受信任的根证书 .cer，它会吊销由吊销的根证书生成/签名的所有客户端证书的访问权限。 如果吊销客户端证书而非根证书，则可继续使用从根证书生成的其他证书进行身份验证。

常见的做法是使用根证书管理团队或组织级别的访问权限，并使用吊销的客户端证书针对单个用户进行精细的访问控制。

**若要撤消：**

1. 检索客户端证书指纹。 有关详细信息，请参阅[如何检索证书的指纹](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)。

1. 将信息复制到一个文本编辑器，删除所有空格，使之成为一个连续的字符串。 该字符串在下一步声明为变量。

1. 声明变量。 确保声明在前面的步骤中检索的指纹。

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. 将指纹添加到已吊销证书的列表。 添加指纹后，会显示“成功”。

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. 确认指纹已添加到证书吊销列表。

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. 添加指纹后，不再可以使用证书来连接。 客户端在尝试使用此证书进行连接时，会收到一条消息，指出证书不再有效。

**复原：**

可以通过从吊销的客户端证书列表中删除指纹来恢复客户端证书。

1. 声明变量。 确保为需要恢复的证书声明正确的指纹。

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. 从证书吊销列表中删除证书指纹。

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. 检查指纹是否已从吊销列表中删除。

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>点到站点常见问题解答

有关点到站点的其他信息，请参阅 [VPN 网关点到站点常见问题解答](vpn-gateway-vpn-faq.md#P2S)

## <a name="next-steps"></a>后续步骤

连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](../index.yml)。 若要详细了解网络和虚拟机，请参阅 [Azure 和 Linux VM 网络概述](../virtual-network/network-overview.md)。

有关 P2S 故障排除信息，请参阅[故障排除：Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。