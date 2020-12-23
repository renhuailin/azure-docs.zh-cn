---
title: 创建并安装 P2S VPN 客户端配置文件：证书身份验证
titleSuffix: Azure VPN Gateway
description: 创建和安装 Windows、Linux、Linux (strongSwan) 和 macOS X VPN 客户端配置文件，用于 P2S 证书身份验证。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2020
ms.author: cherylmc
ms.openlocfilehash: c7b186aa1a6f63b1bc3e9dbefa5001faac967762
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556062"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>为本机 Azure 证书身份验证 P2S 配置创建并安装 VPN 客户端配置文件

VPN 客户端配置文件包含在一个 zip 文件中。 配置文件提供了本机 Windows、Mac IKEv2 VPN 或 Linux 客户端通过使用本机 Azure 证书身份验证的点到站点连接连接到虚拟网络所需的设置。

客户端配置文件特定于虚拟网络的 VPN 配置。 如果在生成 VPN 客户端配置文件后，点到站点 VPN 配置（例如 VPN 协议类型或身份验证类型）发生变化，请务必为用户设备生成新的 VPN 客户端配置文件。

* 有关点到站点连接的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。
* 有关 OpenVPN 说明，请参阅[为 P2S 配置 OpenVPN](vpn-gateway-howto-openvpn.md) 和[配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md)。

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>生成 VPN 客户端配置文件

在开始之前，请确保所有连接方用户的设备上安装了有效的证书。 有关安装客户端证书的详细信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

可使用 PowerShell 或使用 Azure 门户生成客户端配置文件。 两种方法之一都会返回相同的 zip 文件。 解压缩该文件，查看以下文件夹：

* **WindowsAmd64** 和 **WindowsX86** ：分别包含 Windows 32 位和 64 位安装程序包。 **WindowsAmd64** 安装程序包适用于所有受支持的 64 位 Windows 客户端，而不仅仅是 Amd。
* **Generic** ：包含用于创建自己的 VPN 客户端配置的常规信息。 如果网关上配置了 IKEv2 或 SSTP+IKEv2，会提供 Generic 文件夹。 如果仅配置了 SSTP，则不会提供 Generic 文件夹。

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>使用 Azure 门户生成文件

1. 在 Azure 门户中，导航到要连接到的虚拟网络的虚拟网络网关。
1. 在 "虚拟网络网关" 页上，选择 " **点到站点配置** "。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="下载 VPN 客户端":::
1. 在 "点到站点配置" 页的顶部，选择 " **下载 VPN 客户端** "。 需要几分钟才能生成客户端配置包。
1. 浏览器会指示客户端配置 zip 文件可用。 其名称与网关名称相同。 解压缩该文件，查看文件夹。

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>使用 PowerShell 生成文件

1. 生成 VPN 客户端配置文件时，“-AuthenticationMethod”的值为“EapTls”。 使用以下命令生成 VPN 客户端配置文件：

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. 将 URL 复制到浏览器，下载 zip 文件，然后解压缩该文件，查看其中的文件夹。

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X) 

 必须在将连接到 Azure 的每个 Mac 上手动配置本机 IKEv2 VPN 客户端。 Azure 不提供用于本机 Azure 证书身份验证的 mobileconfig 文件。 **Generic** 包含需要用于配置的所有信息。 如果在下载中没有看到 Generic 文件夹，则可能 IKEv2 未选作隧道类型。 请注意，VPN 网关基本 SKU 不支持 IKEv2。 选择 IKEv2 后，再次生成 zip 文件，检索 Generic 文件夹。<br>Generic 文件夹包含以下文件：

* **VpnSettings.xml** ，其中包含服务器地址和隧道类型等重要设置。 
* **VpnServerRoot** ，其中包含在 P2S 连接设置过程中验证 Azure VPN 网关所需的根证书。

使用以下步骤在 Mac 中配置用于证书身份验证的本机 VPN 客户端。 必须在将连接到 Azure 的每个 Mac 上完成以下步骤：

1. 将 **VpnServerRoot** 根证书导入 Mac。 为此，可将该文件复制到 Mac，并双击它。 选择 " **添加** " 以导入。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="屏幕截图显示证书页":::
  
    >[!NOTE]
    >双击证书可能不会显示“添加”对话框，但该证书将安装在相应的存储中。 可以在证书类别下的登录密钥链中查找该证书。
    >
  
1. 验证已安装由根证书颁发的客户端证书，该根证书在配置 P2S 设置时已上传到 Azure。 这不同于上一步中安装的 VPNServerRoot。 客户端证书可用于身份验证，且是必需的。 有关生成证书的详细信息，请参阅[生成证书](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)。 有关如何安装客户端证书的信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。
1. 打开 " **网络首选项** " 下的 " **网络** " 对话框，然后选择 **"+"** 以创建新的 VPN 客户端连接配置文件，以便将 P2S 连接到 Azure 虚拟网络。

   “接口”值为“VPN”，“VPN 类型”值为“IKEv2”。 在 " **服务名称** " 字段中指定配置文件的名称，然后选择 " **创建** " 以创建 VPN 客户端连接配置文件。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="屏幕截图显示 &quot;网络&quot; 窗口，其中包含用于选择接口的选项，请选择 &quot;VPN 类型&quot;，然后输入服务名称":::
1. 从 **Generic** 文件夹中的 **VpnSettings.xml** 文件复制 **VpnServer** 标记值。 将该值粘贴到配置文件的“服务器地址”和“远程 ID”字段中。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="屏幕截图显示服务器信息。":::
1. 选择 " **身份验证设置** "，然后选择 " **证书** "。 对于 **Catalina** ，选择 " **无** "，然后选择 " **证书** "。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="屏幕截图显示身份验证设置。":::

   对于 Catalina，请选择“无”，然后选择“证书”。 **选择** 正确的证书：
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="屏幕截图显示未选择 &quot;身份验证设置&quot; 和 &quot;证书&quot; 的 &quot;网络&quot; 窗口。":::

1. 单击 " **选择 ...** " 选择要用于身份验证的客户端证书。 这是你在步骤 2 中安装的证书。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="屏幕截图显示具有身份验证设置的网络窗口，你可以在其中选择证书。":::
1. “选择标识”会显示可供选择的证书列表。 选择正确的证书，然后选择 " **继续** "。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="屏幕截图显示 &quot;选择标识&quot; 对话框，你可以在其中选择正确的证书。":::

1. 在“本地 ID”字段中，指定证书的名称（见步骤 6）。 在此示例中，它是 `ikev2Client.com`。 然后选择 " **应用** " 保存更改。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="屏幕截图显示应用。":::
1. 在 " **网络** " 对话框中，选择 " **应用** " 保存所有更改。 然后，选择“连接”以启动到 Azure 虚拟网络的 P2S 连接。

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>安装 strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>生成证书

如果尚未生成证书，请执行以下步骤：

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>安装和配置

以下说明是在 Ubuntu 18.0.4 上创建的。 Ubuntu 16.0.10 不支持 strongSwan GUI。 如果要使用 Ubuntu 16.0.10，则必须使用 [命令行](#linuxinstallcli)。 以下示例可能与你看到的屏幕不同，具体取决于所用的 Linux 和 strongSwan 版本。

1. 打开 **终端** 并运行示例中的命令，安装 **strongSwan** 及其网络管理器。

   ```
   sudo apt install network-manager-strongswan
   ```
1. 选择 " **设置** "，然后选择 " **网络** "。 选择 **+** 按钮以创建新连接。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="屏幕截图显示 &quot;网络连接&quot; 页。":::

1. 从菜单中选择“IPsec/IKEv2 (strongSwan)”，然后双击。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="屏幕截图显示 &quot;添加 VPN&quot; 页。":::

1. 在 " **添加 vpn** " 页上，添加 vpn 连接的名称。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="屏幕截图显示 &quot;选择连接类型&quot;。":::
1. 打开下载的客户端配置文件包含的 **Generic** 文件夹中的 **VpnSettings.xml** 文件。 找到名为 **VpnServer** 的标记，并复制名称，以 "azuregateway" 开头，以 ". cloudapp.net" 结尾。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="屏幕截图显示复制数据。":::
1. 将名称粘贴到新 VPN 连接的 " **地址** " 字段中的 " **网关** " 部分。 接下来，选择“证书”字段末尾的文件夹图标，浏览到 **Generic** 文件夹，并选择 **VpnServerRoot** 文件。
1. 在连接的“客户端”部分，为“身份验证”选择“证书/私钥”。 对于“证书”和“私钥”，请选择前面创建的证书和私钥。 在“选项”中，选择“请求内部 IP 地址”。 然后选择“添加”。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="屏幕截图显示 &quot;请求内部 IP 地址&quot;。":::

1. **打开** 连接。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="屏幕截图显示副本。":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>安装 strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>生成证书

如果尚未生成证书，请执行以下步骤：

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>安装和配置

1. 从 Azure 门户下载 VPNClient 程序包。
1. 将该文件解压缩。
1. 从 **通用** 文件夹中，复制 **VpnServerRoot** 并将其移动到 **/etc/ipsec.d/cacerts** 。
1. 将 **cp client. p12** 复制或移动到 **/etc/ipsec.d/private/** 。 此文件是 VPN 网关的客户端证书。
1. 打开 **VpnSettings.xml** 文件并复制 `<VpnServer>` 值。 下一步骤中将使用此值。
1. 调整下面的示例中的值，然后将该示例添加到 **/etc/ipsec.conf** 配置。
  
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
1. 将以下值添加到 **/etc/ipsec.secrets** 。

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. 运行以下命令：

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>后续步骤

返回到所使用的原始文章，然后完成 P2S 配置。

* [PowerShell 配置步骤](vpn-gateway-howto-point-to-site-rm-ps.md)。
* [Azure 门户配置步骤](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
