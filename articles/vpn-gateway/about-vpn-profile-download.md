---
title: 关于点到站点 VPN 客户端配置文件
titleSuffix: Azure VPN Gateway
description: 了解 P2S VPN 客户端配置文件。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d26f65dfa8419b3c07825774423dec4bd5557a05
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227805"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>使用 P2S VPN 客户端配置文件

配置文件包含配置 VPN 连接所需的信息。 本文将帮助你获取和了解 VPN 客户端配置文件所需的信息。

## <a name="generate-and-download-profile"></a>生成并下载配置文件

可使用 PowerShell 或使用 Azure 门户生成客户端配置文件。 两种方法之一都会返回相同的 zip 文件。

### <a name="portal"></a>门户

1. 在 Azure 门户中，导航到要连接到的虚拟网络的虚拟网络网关。
1. 在虚拟网络网关页面上，选择“点到点配置”。
1. 在“点到站点配置”页的顶部，选择“下载 VPN 客户端”。 需要几分钟才能生成客户端配置包。
1. 浏览器会指示客户端配置 zip 文件可用。 其名称与网关名称相同。 解压缩该文件，查看文件夹。

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 生成，可以使用以下示例：

1. 生成 VPN 客户端配置文件时，“-AuthenticationMethod”的值为“EapTls”。 使用以下命令生成 VPN 客户端配置文件：

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. 将 URL 复制到浏览器，下载 zip 文件，然后解压缩该文件，查看其中的文件夹。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* OpenVPN 文件夹包含 ovpn 配置文件，需要对该文件进行修改以包含密钥和证书。 有关详细信息，请参阅[为 Azure VPN 网关配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md#windows)。 如果在 VPN 网关上选择 Azure AD 身份验证，则 zip 文件中不存在此文件夹。 而是应该导航到 AzureVPN 文件夹并找到 azurevpnconfig.xml。

## <a name="next-steps"></a>后续步骤

有关点到站点的详细信息，请参阅[关于点到站点](point-to-site-about.md)。
