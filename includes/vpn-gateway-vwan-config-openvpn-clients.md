---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/27/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e142cbbb3980ddd491b4d3ac8d35bd9f0e0d6633
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729511"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows 客户端

1. 从官方 [OpenVPN 网站](https://openvpn.net/index.php/open-source/downloads.html)下载并安装 OpenVPN 客户端（版本 2.4 或更高版本）。
2. 从 Azure 门户下载 VPN 客户端配置文件包，或使用 PowerShell 中的 'New-AzVpnClientConfiguration' cmdlet。
3. 解压缩该配置文件。 接下来，使用记事本打开 OpenVPN 文件夹中的 *vpnconfig.ovpn* 配置文件。
4. 导出创建和上传的点到站点客户端证书。 请参阅以下文章链接：

   * [VPN 网关](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)说明
   
   * [虚拟 WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)说明
5. 从 *.pfx* 中提取私钥和 base64 指纹。 有多种方法可实现此操作。 其中一种方法是在计算机上使用 OpenSSL。 *profileinfo.txt* 文件包含 CA 和客户端证书的私钥与指纹。 请务必使用客户端证书的指纹。

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. 在记事本中打开 *profileinfo.txt*。 若要获取客户端（子）证书的指纹，请选择并复制子证书的“-----BEGIN CERTIFICATE-----”与“-----END CERTIFICATE-----”之间的文本（包括这两行）。 查看 subject=/ 行可以识别子证书。
7. 切换到执行步骤 3 时在记事本中打开的 *vpnconfig.ovpn* 文件。 找到下面所示的节，并替换“cert”与“/cert”之间的所有内容。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 在记事本中打开 *profileinfo.txt*。 若要获取私钥，请选择并复制“-----BEGIN PRIVATE KEY-----”与“-----END PRIVATE KEY-----”之间的文本（包括这两行）。
9. 返回到记事本中的 vpnconfig.ovpn 文件，并找到此节。 粘贴私钥，替换“key”与“/key”之间的所有内容。

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
11. 将 vpnconfig.ovpn 文件复制到 C:\Program Files\OpenVPN\config 文件夹。
12. 右键单击系统托盘中的 OpenVPN 图标，然后单击“连接”。

## <a name="mac-clients"></a><a name="mac"></a>Mac 客户端

1. 下载并安装 OpenVPN 客户端，如 [TunnelBlick](https://tunnelblick.net/downloads.html)。 
2. 从 Azure 门户下载 VPN 客户端配置文件包，或使用 PowerShell 中的 'New-AzVpnClientConfiguration' cmdlet。
3. 解压缩该配置文件。 在某个文本编辑器中打开 OpenVPN 文件夹中的 vpnconfig.ovpn 配置文件。
4. 使用 base64 中的 P2S 客户端证书公钥填写 P2S 客户端证书部分。 在 PEM 格式的证书中，可以打开 .cer 文件并在证书标头之间复制 base64 密钥。 若要了解如何导出证书以获取已编码的公钥，请访问以下文章链接：

   * [VPN 网关](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer)说明 
   
   * [虚拟 WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)说明
5. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 有关如何提取私钥的信息，请参阅 OpenVPN 网站上的[导出私钥](https://openvpn.net/community-resources/how-to/#pki)。
6. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
7. 双击配置文件以在 Tunnelblick 中创建配置文件。
8. 启动应用程序文件夹中的 Tunnelblick。
9. 单击系统托盘中的 Tunnelblick 图标，然后单击“连接”。

> [!IMPORTANT]
>仅 iOS 11.0 及更高版本和 MacOS 10.13 及更高版本支持 OpenVPN 协议。
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS 客户端

1. 从 App store 中安装 OpenVPN 客户端（版本 2.4 或更高版本）。
2. 从 Azure 门户下载 VPN 客户端配置文件包，或使用 PowerShell 中的 'New-AzVpnClientConfiguration' cmdlet。
3. 解压缩该配置文件。 在某个文本编辑器中打开 OpenVPN 文件夹中的 vpnconfig.ovpn 配置文件。
4. 使用 base64 中的 P2S 客户端证书公钥填写 P2S 客户端证书部分。 在 PEM 格式的证书中，可以打开 .cer 文件并在证书标头之间复制 base64 密钥。 若要了解如何导出证书以获取已编码的公钥，请访问以下文章链接：

   * [VPN 网关](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer)说明 
   
   * [虚拟 WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)说明
5. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 有关如何提取私钥的信息，请参阅 OpenVPN 网站上的[导出私钥](https://openvpn.net/community-resources/how-to/#pki)。
6. 不要更改任何其他字段。
7. 将配置文件 (.ovpn) 通过电子邮件发送到你的电子邮件帐户，该帐户是在 iPhone 上的邮件应用中配置的。 
8. 在 iPhone 上的邮件应用中打开电子邮件，并点击附加的文件

    ![打开电子邮件](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. 如果没有看到“复制到 OpenVPN”选项，请点击“更多”。

    ![更多](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. 点击“复制到 OpenVPN” 

    ![复制到 OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. 在“导入配置文件”页面中点击“添加”

    ![添加](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. 在“导入的配置文件”页面中点击“添加”

    ![点击“添加”](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. 启动 OpenVPN 应用，并将“配置文件”页面中的开关向右滑动以进行连接

    ![连接](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux 客户端

1. 打开新的终端会话。 可以同时按“Ctrl + Alt + T”打开新会话。
2. 输入以下命令以安装所需的组件：

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. 下载网关的 VPN 配置文件。 可以通过 Azure 门户中的“点到站点配置”选项卡完成此操作。
4. 导出创建的 P2S 客户端证书，并将其上传到网关上的 P2S 配置。 请参阅以下文章链接：

   * [VPN 网关](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)说明 
   
   * [虚拟 WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)说明
5. 从 .pfx 中提取私钥和 base64 指纹。 有多种方法可实现此操作。 其中一种方法是在计算机上使用 OpenSSL。

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   *profileinfo.txt* 文件将包含 CA 和客户端证书的私钥与指纹。 请务必使用客户端证书的指纹。

6. 在文本编辑器中打开 *profileinfo.txt*。 若要获取客户端（子）证书的指纹，请选择并复制子证书的“-----BEGIN CERTIFICATE-----”与“-----END CERTIFICATE-----”之间的文本（包括这两行）。 查看 subject=/ 行可以识别子证书。

7. 打开 *vpnconfig.ovpn* 文件并找到下面所示的节。 替换“cert”与“/cert”之间的所有内容。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 在文本编辑器中打开 profileinfo.txt。 若要获取私钥，请选择并复制“-----BEGIN PRIVATE KEY-----”与“-----END PRIVATE KEY-----”之间的文本（包括这两行）。

9. 在文本编辑器中打开 vpnconfig.ovpn 文件，并找到此节。 粘贴私钥，替换“key”与“/key”之间的所有内容。

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
11. 若要使用命令行进行连接，请键入以下命令：
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. 要使用 GUI 进行连接，请转到系统设置。
13. 单击 + 添加新的 VPN 连接。
14. 在“添加 VPN”下，选择“从文件导入...”
15. 浏览到配置文件，然后双击或选择“打开”。
16. 单击“添加 VPN”窗口上的“添加”。
  
    ![从文件导入](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. 可以通过在“网络设置”页面上或在系统托盘中的网络图标下打开 VPN 进行连接。
