---
title: 创建并安装 P2S VPN 客户端配置文件：证书身份验证
titleSuffix: Azure VPN Gateway
description: 了解如何为 Windows、Linux (strongSwan) 和 macOS 生成和安装 VPN 客户端配置文件。 本文适用于使用证书身份验证的 VPN 网关 P2S 配置。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/03/2021
ms.author: cherylmc
ms.openlocfilehash: 80425670d40a32c229d6c9cf5aceab9a37072962
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111558825"
---
# <a name="generate-and-install-vpn-client-configuration-files-for-p2s-certificate-authentication"></a>生成并安装用于 P2S 证书身份验证的 VPN 客户端配置文件

使用点到站点和证书身份验证连接到 Azure VNet 时，可以使用在连接的操作系统上本机安装的 VPN 客户端。 VPN 客户端的所有必需配置设置均包含在 VPN 客户端配置 zip 文件中。 zip 文件中的设置有助于轻松配置 Windows、Mac IKEv2 VPN 或 Linux 的 VPN 客户端。

生成的 VPN 客户端配置文件特定于虚拟网络的 P2S VPN 网关配置。 如果生成文件后点到站点 VPN 配置发生任何更改，例如 VPN 协议类型变更或身份验证类型变更，则需生成新的 VPN 客户端配置文件并在要连接的所有 VPN 客户端中应用新的配置。

* 有关点到站点连接的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。
* 有关 OpenVPN 说明，请参阅[为 P2S 配置 OpenVPN](vpn-gateway-howto-openvpn.md) 和[配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md)。

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>生成 VPN 客户端配置文件

可使用 PowerShell 或使用 Azure 门户生成客户端配置文件。 两种方法之一都会返回相同的 zip 文件。 解压缩该文件，查看以下文件夹：

* **WindowsAmd64** 和 **WindowsX86**：分别包含 Windows 32 位和 64 位安装程序包。 **WindowsAmd64** 安装程序包适用于所有受支持的 64 位 Windows 客户端，而不仅仅是 Amd。
* **Generic**：包含用于创建自己的 VPN 客户端配置的常规信息。 如果网关上配置了 IKEv2 或 SSTP+IKEv2，会提供 Generic 文件夹。 如果仅配置了 SSTP，则不会提供 Generic 文件夹。

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>使用 Azure 门户生成文件

1. 在 Azure 门户中，导航到要连接到的虚拟网络的虚拟网络网关。
1. 在虚拟网络网关页面上，选择“点到站点配置”，打开点到站点配置页面。
1. 在“点到站点配置”页的顶部，选择“下载 VPN 客户端”。 该操作不会下载 VPN 客户端软件，而是生成用于配置 VPN 客户端的配置包。 需要几分钟才能生成客户端配置包。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="下载 VPN 客户端配置。":::
1. 生成配置包后，浏览器将显示客户端配置 zip 文件可用。 其名称与网关名称相同。 解压缩该文件，查看文件夹。

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>使用 PowerShell 生成文件

1. 生成 VPN 客户端配置文件时，“-AuthenticationMethod”的值为“EapTls”。 使用以下命令生成 VPN 客户端配置文件：

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. 将 URL 复制到浏览器，下载 zip 文件，然后解压缩该文件，查看其中的文件夹。

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-macos"></a><a name="installmac"></a>Mac (macOS)

 必须在将连接到 Azure 的每个 Mac 上手动配置本机 IKEv2 VPN 客户端。 Azure 不提供用于本机 Azure 证书身份验证的 mobileconfig 文件。 Generic 文件夹包含需要用于配置的所有信息。 如果在下载中没有看到 Generic 文件夹，则可能 IKEv2 未选作隧道类型。 请注意，VPN 网关基本 SKU 不支持 IKEv2。 选择 IKEv2 后，再次生成 zip 文件，检索 Generic 文件夹。<br>Generic 文件夹包含以下文件：

* **VpnSettings.xml**：包含服务器地址和隧道类型等重要设置。 
* **VpnServerRoot.cer**：包含在 P2S 连接设置过程中验证 Azure VPN 网关所需的根证书。

使用以下步骤在 Mac 中配置用于证书身份验证的本机 VPN 客户端。 必须在将连接到 Azure 的每个 Mac 上完成以下步骤：

1. 将 **VpnServerRoot** 根证书导入 Mac。 为此，可将该文件复制到 Mac，并双击它。 选择“添加”进行导入。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="屏幕截图显示“证书”页":::
  
    >[!NOTE]
    >双击证书可能不会显示“添加”对话框，但该证书将安装在相应的存储中。 可以在证书类别下的登录密钥链中查找该证书。
    >
  
1. 验证已安装由根证书颁发的客户端证书，该根证书在配置 P2S 设置时已上传到 Azure。 这不同于上一步中安装的 VPNServerRoot。 客户端证书可用于身份验证，且是必需的。 有关生成证书的详细信息，请参阅[生成证书](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)。 有关如何安装客户端证书的信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。
1. 在“网络首选项”下打开“网络”对话框，然后选择“+”为与 Azure 虚拟网络的 P2S 连接新建 VPN 客户端连接配置文件  。

   “接口”值为“VPN”，“VPN 类型”值为“IKEv2”。 在“服务名称”字段中为配置文件指定一个名称，然后选择“创建”以创建 VPN 客户端连接配置文件 。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="屏幕截图显示“网络”窗口，其中包括用于选择接口、选择 VPN 类型和输入服务名称的选项":::
1. 从 **Generic** 文件夹中的 **VpnSettings.xml** 文件复制 **VpnServer** 标记值。 将该值粘贴到配置文件的“服务器地址”和“远程 ID”字段中。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="屏幕截图显示服务器信息。":::
1. 选择“身份验证设置”，然后选择“证书” 。 对于 Catalina，请选择“无”，然后选择“证书”  。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="屏幕截图显示身份验证设置。":::

   对于 Catalina，请选择“无”，然后选择“证书”。 **选择** 正确的证书：
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="屏幕截图显示“网络”窗口，其中“身份验证设置”选择了“无”并选择了“证书”。":::

1. 单击“选择…” 选择要用于身份验证的客户端证书。 这是你在步骤 2 中安装的证书。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="屏幕截图显示具有“身份验证设置”的“网络”窗口，你可在其中选择证书。":::
1. “选择标识”会显示可供选择的证书列表。 选择适当的证书，然后选择“继续”。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="屏幕截图显示可在其中选择正确证书的“选择标识”对话框。":::

1. 在“本地 ID”字段中，指定证书的名称（见步骤 6）。 在此示例中，它是 `ikev2Client.com`。 然后，选择“应用”保存更改。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="屏幕截图显示“应用”。":::
1. 在“网络”对话框中，选择“应用”以保存所有更改 。 然后，选择“连接”以启动到 Azure 虚拟网络的 P2S 连接。

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>安装 strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>生成证书

如果尚未生成证书，请执行以下步骤：

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>安装和配置

以下说明是在 Ubuntu 18.0.4 上创建的。 Ubuntu 16.0.10 不支持 strongSwan GUI。 如果想要使用 Ubuntu 16.0.10，则必须使用[命令行](#linuxinstallcli)。 以下示例可能与你看到的屏幕不同，具体取决于所用的 Linux 和 strongSwan 版本。

1. 打开 **终端** 并运行示例中的命令，安装 **strongSwan** 及其网络管理器。

   ```
   sudo apt install network-manager-strongswan
   ```
1. 选择“设置”，然后选择“网络”。 选择 + 按钮创建新连接。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="屏幕截图显示“网络连接”页。":::

1. 从菜单中选择“IPsec/IKEv2 (strongSwan)”，然后双击。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="屏幕截图显示“添加 VPN”页。":::

1. 在“添加 VPN”页上，添加 VPN 连接的名称。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="屏幕截图显示“选择连接类型”。":::
1. 打开下载的客户端配置文件包含的 **Generic** 文件夹中的 **VpnSettings.xml** 文件。 找到名为 **VpnServer** 的标记，并复制以“azuregateway”开头、以“.cloudapp.net”结尾的名称。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="屏幕截图显示复制数据。":::
1. 在“网关”部分中，将此名称粘贴到新 VPN 连接的“地址”字段中 。 接下来，选择“证书”字段末尾的文件夹图标，浏览到 **Generic** 文件夹，并选择 **VpnServerRoot** 文件。
1. 在连接的“客户端”部分，为“身份验证”选择“证书/私钥”。 对于“证书”和“私钥”，请选择前面创建的证书和私钥。 在“选项”中，选择“请求内部 IP 地址”。 然后选择“添加”。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="屏幕截图显示请求内部 IP 地址。":::

1. **打开** 连接。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="屏幕截图显示复制。":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>安装 strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>生成证书

如果尚未生成证书，请执行以下步骤：

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>安装和配置

1. 从 Azure 门户下载 VPNClient 程序包。
1. 将该文件解压缩。
1. 从 Generic 文件夹中，将 VpnServerRoot.cer 复制或移动到 /etc/ipsec.d/cacerts  。
1. 将 cp client.p12 复制或移动到 /etc/ipsec.d/private/ 。 此文件是 VPN 网关的客户端证书。
1. 打开 VpnSettings.xml 文件并复制 `<VpnServer>` 值。 下一步骤中将使用此值。
1. 调整以下示例中的值，然后将该示例添加到 /etc/ipsec.conf 配置。
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. 将以下值添加到 /etc/ipsec.secrets。

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. 运行以下命令：

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>后续步骤

返回到你之前处理的原始文章，然后完成 P2S 配置。

* [PowerShell 配置步骤](vpn-gateway-howto-point-to-site-rm-ps.md)。
* [Azure 门户配置步骤](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
