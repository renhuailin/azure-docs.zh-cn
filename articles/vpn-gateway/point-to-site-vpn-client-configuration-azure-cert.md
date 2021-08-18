---
title: 创建并安装 P2S VPN 客户端配置文件：证书身份验证
titleSuffix: Azure VPN Gateway
description: 了解如何为 Windows、Linux (strongSwan) 和 macOS 生成和安装 VPN 客户端配置文件。 本文适用于使用证书身份验证的 VPN 网关 P2S 配置。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/15/2021
ms.author: cherylmc
ms.openlocfilehash: efa2c4c120ab54e27126d1f40c433cdc1b66a85e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288736"
---
# <a name="generate-and-install-vpn-client-configuration-files-for-p2s-certificate-authentication"></a>生成并安装用于 P2S 证书身份验证的 VPN 客户端配置文件

使用点到站点和证书身份验证连接到 Azure VNet 时，你使用操作系统上原生安装的用来进行连接的 VPN 客户端。 VPN 客户端的所有必需配置设置都包含在 VPN 客户端 zip 配置文件中。 zip 文件中的设置可帮助你轻松地为 Windows、Mac IKEv2 VPN 或 Linux 配置 VPN 客户端。

你生成的 VPN 客户端配置文件特定于虚拟网络的 P2S VPN 网关配置。 如果在生成文件后对点到站点 VPN 配置进行了任何更改（例如，更改了 VPN 协议类型或身份验证类型），则需生成新的 VPN 客户端配置文件并将新配置应用于要连接的所有 VPN 客户端。

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
1. 在虚拟网络网关页上，选择“点到站点配置”以打开“点到站点配置”页。
1. 在“点到站点配置”页的顶部，选择“下载 VPN 客户端”。 这不会下载 VPN 客户端软件，它将生成用来配置 VPN 客户端的配置包。 需要几分钟才能生成客户端配置包。 在此期间，在包生成前，可能不会显示任何指示。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="下载 VPN 客户端配置包。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/download-client.png":::
1. 生成配置包后，浏览器会指出有一个客户端 zip 配置文件可用。 其名称与网关名称相同。 解压缩该文件，查看文件夹。

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

为了连接到 Azure，必须手动配置本机 IKEv2 VPN 客户端。 Azure 不提供 mobileconfig 文件。 可以在 Generic 文件夹中找到配置所需的所有信息。 

如果在下载中没有看到 Generic 文件夹，则可能 IKEv2 未选作隧道类型。 请注意，VPN 网关基本 SKU 不支持 IKEv2。 在 VPN 网关上，验证该 SKU 不是“基本”类别。 然后，选择 IKEv2 并再次生成 zip 文件以检索 Generic 文件夹。

Generic 文件夹包含以下文件：

* **VpnSettings.xml**：包含服务器地址和隧道类型等重要设置。 
* **VpnServerRoot.cer**：包含在 P2S 连接设置过程中验证 Azure VPN 网关所需的根证书。

使用以下步骤在 Mac 中配置用于证书身份验证的本机 VPN 客户端。 必须在要连接到 Azure 的每一台 Mac 上完成这些步骤。

### <a name="import-root-certificate-file"></a>导入根证书文件

1. 将根证书文件复制到 Mac。 双击该证书。 证书将自动安装，或者你将看到“添加证书”页面。
1. 在“添加证书”页面上，从下拉列表中选择“登录名” 。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/login.png" alt-text="该屏幕截图显示了已选中“登录名”的“添加证书”页面。":::
1. 单击“添加”以导入文件。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add.png" alt-text="该屏幕截图显示已选中“添加”的“添加证书”页面。":::

### <a name="verify-certificate-install"></a>验证证书安装

验证是否安装了客户端和根证书。 客户端证书可用于身份验证，且是必需的。 有关如何安装客户端证书的信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

1. 打开 Keychain Access 应用程序。
1. 导航到“证书”选项卡。
1. 验证是否安装了客户端和根证书。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/keychain.png" alt-text="该屏幕截图显示安装了证书的 Keychain Access。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/keychain.png":::
  
### <a name="create-vpn-client-profile"></a>创建 VPN 客户端配置文件

1. 导航到“系统首选项”->“网络”。 在“网络”页面上，选择“+”来为到 Azure 虚拟网络的 P2S 连接创建新的 VPN 客户端连接配置文件。
1. 对于“接口”，从下拉列表中选择 VPN 。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-vpn.png" alt-text="该屏幕截图显示“网络”窗口，其中包括用于选择接口的选项并且已选中 VPN。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-vpn.png":::

1. 对于“VPN 类型”，从下拉列表中选择 IKEv2 。 在“服务名称”字段中，为配置文件指定易记名称。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-type.png" alt-text="屏幕截图显示“网络”窗口，其中包括用于选择接口、选择 VPN 类型和输入服务名称的选项。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-type.png":::

1. 选择“创建”以创建 VPN 客户端连接配置文件。
1. 在 Generic 文件夹中，使用文本编辑器打开 VpnSettings.xml 文件，然后复制 VpnServer 标记值  。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server-tag.png" alt-text="屏幕截图显示了打开的 VpnSettings.xml 文件，其中突出显示了 VpnServer 标记。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/server-tag.png":::

1. 将 VpnServer 标记值粘贴到配置文件的“服务器地址”和“远程 ID”字段中  。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/paste-value.png" alt-text="屏幕截图显示了“网络”窗口，其中粘贴了值。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/paste-value.png":::

1. 配置身份验证设置。 有两组说明。 选择与 OS 版本相对应的说明。

   Catalina： 

     * 对于“身份验证设置”，选择“无” 。 
     * 选择“证书”，单击“选择”并选择之前安装的正确客户端证书 。 然后，单击 **“确定”**。
   
        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="屏幕截图显示“网络”窗口，其中“身份验证设置”选择了“无”并选择了“证书”。":::

   Big Sur：

      * 单击“身份验证设置”，然后选择“证书” 。 

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-certificate.png" alt-text="屏幕截图显示了已选择“证书”的“身份验证设置”。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/authentication-certificate.png":::

      * 单击“选择”以打开“选择标识”页面 。 “选择标识”页面会显示可供选择的证书列表。 如果不确定要使用哪个证书，可以单击“显示证书”以查看各个证书的详细信息。

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/show-certificate.png" alt-text="屏幕截图显示了证书属性。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/show-certificate.png":::
      * 选择适当的证书，然后选择“继续”。

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-identity.png" alt-text="屏幕截图显示了“选择标识”，可在其中选择证书。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-identity.png":::
   
      * 在“身份验证设置”页面上，验证是否显示了正确的证书，然后单击“确定” 。

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="屏幕截图显示可在其中选择正确证书的“选择标识”对话框。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/certificate.png":::

1. 对于 Catalina 和 Big Sur，请在“本地 ID”字段中指定证书的名称。 在此示例中，它是 `P2SChildCert`。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/local-id.png" alt-text="屏幕截图显示了本地 ID 值。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/local-id.png":::
1. 选择“应用”以保存所有更改。 
1. 选择“连接”以启动到 Azure 虚拟网络的 P2S 连接。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="屏幕截图显示了“连接”按钮。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

1. 建立连接后，状态显示为“已连接”，并且你可以查看从 VPN 客户端地址池中拉取的 IP 地址。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/connected.png" alt-text="屏幕截图显示“已连接”。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/connected.png":::

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

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="屏幕截图显示“网络连接”页。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/edit-connections.png":::

1. 从菜单中选择“IPsec/IKEv2 (strongSwan)”，然后双击。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="屏幕截图显示“添加 VPN”页。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/add-connection.png":::

1. 在“添加 VPN”页上，添加 VPN 连接的名称。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="屏幕截图显示“选择连接类型”。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-type.png":::
1. 打开下载的客户端配置文件包含的 **Generic** 文件夹中的 **VpnSettings.xml** 文件。 找到名为 **VpnServer** 的标记，并复制以“azuregateway”开头、以“.cloudapp.net”结尾的名称。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="屏幕截图显示复制数据。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-server.png":::
1. 在“网关”部分中，将此名称粘贴到新 VPN 连接的“地址”字段中 。 接下来，选择“证书”字段末尾的文件夹图标，浏览到 **Generic** 文件夹，并选择 **VpnServerRoot** 文件。
1. 在连接的“客户端”部分，为“身份验证”选择“证书/私钥”。 对于“证书”和“私钥”，请选择前面创建的证书和私钥。 在“选项”中，选择“请求内部 IP 地址”。 然后选择“添加”。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="屏幕截图显示请求内部 IP 地址。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/ip-request.png":::

1. **打开** 连接。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="屏幕截图显示复制。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/turn-on.png":::

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
