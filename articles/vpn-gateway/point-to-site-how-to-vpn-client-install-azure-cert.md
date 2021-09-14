---
title: 安装点到站点客户端证书
titleSuffix: Azure VPN Gateway
description: 了解如何安装用于 P2S 证书身份验证的客户端证书 - Windows、Mac、Linux。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2021
ms.author: cherylmc
ms.openlocfilehash: b924750f30cfa3e00b0a1ce2f6326036ef272f76
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477719"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>安装用于 P2S 证书身份验证连接的客户端证书

将 P2S VPN 网关配置为要求证书身份验证时，每个客户端计算机都必须在本地安装客户端证书。 本文帮助你在客户端计算机本地安装客户端证书。 还可以使用 [Intune](https://docs.microsoft.com/mem/intune/configuration/vpn-settings-configure) 来安装某些 VPN 客户端配置文件和证书。

如果要从自签名根证书生成客户端证书，请参阅以下文章之一：

* [创建证书 - PowerShell](vpn-gateway-certificates-point-to-site.md)
* [创建证书 - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [生成证书 - Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN 客户端仅在[资源管理器部署模型](../azure-resource-manager/management/deployment-models.md)中受支持。 经典部署模型不支持它们。
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Linux 客户端证书作为客户端配置的一部分安装在客户端上。 有关说明,请参阅[客户端配置 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。

## <a name="next-steps"></a>后续步骤

继续执行点到站点配置步骤来[创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md)。
