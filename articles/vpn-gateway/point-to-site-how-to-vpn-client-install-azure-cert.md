---
title: 安装点到站点客户端证书
titleSuffix: Azure VPN Gateway
description: 了解如何安装用于 P2S 证书身份验证的客户端证书 - Windows、Mac、Linux。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/03/2021
ms.author: cherylmc
ms.openlocfilehash: d4ab1009b38d250a39455a9e8a470cd7f1156793
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527094"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>安装用于 P2S 证书身份验证连接的客户端证书

当 P2S VPN 网关配置为需要证书身份验证时，每个客户端都必须在本地安装一个客户端证书。 可基于自签名根证书生成客户端证书，也可基于使用企业 CA 解决方案生成的根证书来生成客户端证书。 

生成客户端证书时，证书通常会自动安装在生成它的客户端计算机上。 如果要从其他客户端计算机连接到 VNet，则需要在要连接的计算机上安装客户端证书。 这是在该计算机上配置 VPN 客户端的补充。

可使用多种方法来生成和导出自签名证书。 有关详细信息，请参阅以下文章：

* [PowerShell](vpn-gateway-certificates-point-to-site.md)
* [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN 客户端仅在资源管理器部署模型中受支持。 经典部署模型不支持它们。
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Linux 客户端证书作为客户端配置的一部分安装在客户端上。 有关说明,请参阅[客户端配置 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。

## <a name="next-steps"></a>后续步骤

继续执行点到站点配置步骤来[创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md)。
