---
title: 使用 P2S VPN 和证书身份验证连接到 VNet：门户
titleSuffix: Azure VPN Gateway
description: 了解如何使用 VPN 网关点到站点连接和自签名或 CA 颁发的证书将 Windows、macOS 和 Linux 客户端安全地连接到 VNet。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: 70bb2f54d6e56574a5cbbb98d1c0c467f4715f3d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818146"
---
# <a name="configure-a-point-to-site-vpn-connection-using-azure-certificate-authentication-azure-portal"></a>使用 Azure 证书身份验证配置点到站点 VPN 连接：Azure 门户

本文介绍如何将运行 Windows、Linux 或 macOS 的单个客户端安全地连接到 Azure VNet。 若要从远程位置连接到 VNet，例如从家里或会议室进行远程通信，则可使用点到站点 VPN。 如果只有一些客户端需要连接到 VNet，也可使用 P2S VPN 来代替站点到站点 VPN。 点到站点连接不需要 VPN 设备或面向公众的 IP 地址。 P2S 基于 SSTP（安全套接字隧道协议）或 IKEv2 创建 VPN 连接。 有关点到站点 VPN 的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="从计算机连接到 Azure VNet - 点到站点连接示意图。":::

有关点到站点 VPN 的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。 若要使用 Azure PowerShell 创建此配置，请参阅[使用 Azure PowerShell 配置点到站点 VPN](vpn-gateway-howto-point-to-site-rm-ps.md)。

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>先决条件

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

### <a name="example-values"></a><a name="example"></a>示例值

可使用以下值创建测试环境，或参考这些值以更好地理解本文中的示例：

**VNet**

* **VNet 名称：** VNet1
* **地址空间：** 10.1.0.0/16<br>对于此示例，我们只使用一个地址空间。 VNet 可以有多个地址空间。
* **子网名称：** FrontEnd
* **子网地址范围：** 10.1.0.0/24
* **订阅：** 如果有多个订阅，请确保使用正确的订阅。
* **资源组：** TestRG1
* **位置：** 美国东部

**虚拟网络网关**

* 虚拟网关名称：VNet1GW
* **网关类型：** VPN
* **VPN 类型：** 基于路由
* SKU：VpnGw2
* **代系：** 第 2 代
* **网关子网地址范围：** 10.1.255.0/27
* 公共 IP 地址名称：VNet1GWpip

**连接类型和客户端地址池**

* 连接类型：点到站点
* 客户端地址池：172.16.201.0/24<br>使用此点到站点连接连接到 VNet 的 VPN 客户端接收来自客户端地址池的 IP 地址。

## <a name="create-a-vnet"></a><a name="createvnet"></a>创建 VNet

在本部分，请创建虚拟网络。

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vpn-gateway"></a><a name="creategw"></a>创建 VPN 网关

在此步骤中，为 VNet 创建虚拟网络网关。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。

>[!NOTE]
>基本网关 SKU 不支持 IKEv2 或 RADIUS 身份验证。 如果计划将 Mac 客户端连接到虚拟网络，请不要使用基本 SKU。
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

可以在网关的“概述”页上查看部署状态。 创建网关后，可以通过在门户中查看虚拟网络，来查看已分配给网关的 IP 地址。 网关显示为连接的设备。

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="generate-certificates"></a><a name="generatecert"></a>生成证书

Azure 使用证书对通过点到站点 VPN 连接连接到 VNet 的客户端进行身份验证。 获得根证书后，即可将公钥信息[上传](#uploadfile)到 Azure。 然后，Azure 就会将该根证书视为通过 P2S 连接到虚拟网络时需要使用的“受信任的”证书。 也可从受信任的根证书生成客户端证书，然后将其安装在每个客户端计算机上。 当客户端发起与 VNet 的连接时，需使用客户端证书对客户端进行身份验证。

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>生成根证书

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>生成客户端证书

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="add-the-vpn-client-address-pool"></a><a name="addresspool"></a>添加 VPN 客户端地址池

客户端地址池是指定的专用 IP 地址的范围。 通过点到站点 VPN 进行连接的客户端动态接收此范围内的 IP 地址。 使用专用 IP 地址范围时，该范围不得与要通过其进行连接的本地位置重叠，也不得与要连接到其中的 VNet 重叠。 如果配置了多个协议，并且 SSTP 是其中一个协议，则配置的地址池将在配置的协议之间平均分配。

1. 创建虚拟网关后，请导航到虚拟网关页的“设置”部分。 在“设置”中，选择“点到站点配置” 。 选择“立即配置”，打开配置页。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="“点到站点配置”页。" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. 在“点到站点”配置页的“地址池”框中，添加要使用的专用 IP 地址范围。  VPN 客户端动态接收指定范围内的 IP 地址。 主动/被动配置的最小子网掩码为 29 位，主动/主动配置的最小子网掩码为 28 位。
1. 转到下一部分以配置身份验证和隧道类型。

## <a name="specify-tunnel-type-and-authentication-type"></a><a name="type"></a>指定隧道类型和身份验证类型

在本部分，你将指定隧道类型和身份验证类型。 如果“点到站点配置”页上未显示隧道类型或身份验证类型，则表示网关使用的是基本 SKU。 基本 SKU 不支持 IKEv2 或 RADIUS 身份验证。 若要使用这些设置，需要使用另一网关 SKU 删除并重新创建网关。

### <a name="tunnel-type"></a><a name="tunneltype"></a>隧道类型

在“点到站点配置”页上，选择“隧道类型” 。 选择隧道类型时请注意以下几点：

* Android 和 Linux 上的 strongSwan 客户端以及 iOS 和 macOS 上的本机 IKEv2 VPN 客户端只会使用 IKEv2 隧道类型进行连接。
* Windows 客户端会首先尝试 IKEv2，如果不能连接，则会回退到 SSTP。
* 可以使用 OpenVPN 客户端连接到 OpenVPN 隧道类型。

### <a name="authentication-type"></a><a name="authenticationtype"></a>身份验证类型

对于“身份验证类型”，请选择“Azure 证书”。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication.png" alt-text="“身份验证类型”的屏幕截图，其中选择了“Azure 证书”。" :::

## <a name="upload-root-certificate-public-key-information"></a><a name="uploadfile"></a>上传根证书的公钥信息

在本部分中，你会将公共根证书数据上传到 Azure。 上传公共证书数据后，Azure 即可使用该数据对已安装客户端证书（根据受信任的根证书生成）的客户端进行身份验证。

1. 导航到“虚拟网络网关”->“点到站点配置”页中的“根证书”部分 。 仅当已选择“Azure 证书”作为身份验证类型时，此部分才可见。
1. 确保在前面的步骤中，已将根证书导出为 Base-64 编码的 X.509 (.CER) 文件。 需要以这种格式导出证书，以便使用文本编辑器打开该证书。 不需要导出私钥。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/base-64.png" alt-text="显示导出为 Base-64 编码的 X.509 文件的屏幕截图。" :::
1. 使用记事本之类的文本编辑器打开该证书。 复制证书数据时，请确保将文本复制为一个无回车符或换行符的连续行。 可能需要在文本编辑器中将视图修改为“显示符号/显示所有字符”以查看回车符和换行符。 仅将以下部分复制为一个连续行：

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="显示记事本中的根证书信息的屏幕截图。" border="false":::
1. 在“根证书”部分，最多可以添加 20 个受信任的根证书。

   * 将证书数据粘贴到“公共证书数据”字段中。 
   * 将证书命名。 

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/root-certificate.png" alt-text="证书数据字段的屏幕截图。" :::
1. 选择页面顶部的“保存”，保存所有配置设置。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save-configuration.png" alt-text="P2S 配置的屏幕截图，其中选择了“保存”。" :::

## <a name="install-exported-client-certificate"></a><a name="installclientcert"></a>安装已导出的客户端证书

如果想要从另一台客户端计算机（而不是用于生成客户端证书的计算机）创建 P2S 连接，需要安装客户端证书。 安装客户端证书时，需要使用导出客户端证书时创建的密码。

确保已将客户端证书与整个证书链（默认）一起作为 .pfx 导出。 否则，根证书信息就不会出现在客户端计算机上，客户端将无法进行正常的身份验证。

有关安装步骤，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

## <a name="configure-settings-for-vpn-clients"></a><a name="clientconfig"></a>配置 VPN 客户端的设置

为了使用 P2S 连接到虚拟网络网关，每台计算机都使用作为操作系统的一部分在本地安装的 VPN 客户端。 例如，在 Windows 计算机上转到 VPN 设置时，可以添加 VPN 连接，而无需安装单独的 VPN 客户端。 使用客户端配置包配置每个 VPN 客户端。 客户端配置包包含特定于创建的 VPN 网关的设置。 

有关生成和安装 VPN 客户端配置文件的步骤，请参阅[为 Azure 证书身份验证 P2S 配置创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md)。

## <a name="connect-to-azure"></a><a name="connect"></a>连接到 Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>从 Windows VPN 客户端进行连接

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>从 Mac VPN 客户端进行连接

在“网络”对话框中，找到要使用的客户端配置文件，在 [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac) 中指定设置，然后选择“连接”。 有关详细说明，请参阅[生成并安装 VPN 客户端配置文件 - macOS](./point-to-site-vpn-client-configuration-azure-cert.md#installmac)。 

如果连接有问题，请验证虚拟网络网关是否未使用基本 SKU。 Mac 客户端不支持基本 SKU。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="屏幕截图显示了“连接”按钮。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>验证连接

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
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>连接到虚拟机

这些说明适用于 Windows 客户端。

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* 验证是否在为 VNet 指定 DNS 服务器 IP 地址之后，才生成 VPN 客户端配置包。 如果更新了 DNS 服务器 IP 地址，请生成并安装新的 VPN 客户端配置包。

* 使用“ipconfig”检查分配给以太网适配器的 IPv4 地址，该适配器所在的计算机正是你要从其进行连接的计算机。 如果该 IP 地址位于要连接到的 VNet 的地址范围内，或者位于 VPNClientAddressPool 的地址范围内，则称为地址空间重叠。 当地址空间以这种方式重叠时，网络流量不会抵达 Azure，而是呆在本地网络中。

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>添加或删除受信任的根证书

可以在 Azure 中添加和删除受信任的根证书。 删除根证书时，如果客户端的证书是从该根生成的，则客户端不能进行身份验证，因此无法进行连接。 如果希望客户端进行身份验证和连接，则需安装新客户端证书，该证书是从委托（上传）给 Azure 的根证书生成的。

最多可以将 20 个受信任的根证书 .cer 文件添加到 Azure。 有关说明，请参阅[上传受信任的根证书](#uploadfile)部分。

若要删除受信任的根证书，请执行以下操作：

1. 导航到虚拟网络网关的“点到站点配置”页。
1. 在页面的“根证书”部分，找到要删除的证书。
1. 选择证书旁边的省略号图标，然后选择“删除”。

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>吊销客户端证书

可以吊销客户端证书。 证书吊销列表用于选择性地拒绝基于单个客户端证书的点到站点连接。 这不同于删除受信任的根证书。 如果从 Azure 中删除受信任的根证书 .cer，它会吊销由吊销的根证书生成/签名的所有客户端证书的访问权限。 如果吊销客户端证书而非根证书，则可继续使用从根证书生成的其他证书进行身份验证。

常见的做法是使用根证书管理团队或组织级别的访问权限，并使用吊销的客户端证书针对单个用户进行精细的访问控制。

可以通过将指纹添加到吊销列表来吊销客户端证书。

1. 检索客户端证书指纹。 有关详细信息，请参阅[如何检索证书的指纹](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)。
1. 将信息复制到一个文本编辑器，删除所有空格，使之成为一个连续的字符串。
1. 导航到虚拟网关的“点到站点配置”页。 此页面正是用来[上传受信任的根证书](#uploadfile)的页面。
1. 在“吊销的证书”部分，输入证书的友好名称（不必是证书 CN）。
1. 将指纹字符串复制并粘贴到“指纹”字段。
1. 指纹将进行验证，并会自动添加到吊销列表。 屏幕上会显示一条消息，指出列表正在进行更新。 
1. 更新完成后，不再可以使用证书来连接。 客户端在尝试使用此证书进行连接时，会收到一条消息，指出证书不再有效。

## <a name="point-to-site-faq"></a><a name="faq"></a>点到站点常见问题解答

有关常见问题解答，请参阅[常见问题解答](vpn-gateway-vpn-faq.md#P2S)。

## <a name="next-steps"></a>后续步骤
连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](../index.yml)。 若要详细了解网络和虚拟机，请参阅 [Azure 和 Linux VM 网络概述](../virtual-network/network-overview.md)。

有关 P2S 故障排除信息，请参阅[排查 Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。