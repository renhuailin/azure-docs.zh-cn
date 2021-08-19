---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/12/2021
ms.author: cherylmc
ms.openlocfilehash: b676e4c801b447291288fdd07ff64177f1201e6c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732636"
---
只要版本与 Windows 客户端的体系结构匹配，就可以在每台客户端计算机上使用相同的 VPN 客户端配置包。 有关支持的客户端操作系统列表，请参阅 [VPN 网关常见问题解答](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)中的“点到站点”部分。

>[!NOTE]
>在要从其进行连接的 Windows 客户端计算机上，必须拥有管理员权限。
>

### <a name="install-the-configuration-files"></a>安装配置文件

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。 
1. 双击所需的包进行安装。 如果显示 SmartScreen 弹出窗口，请依次单击“更多信息”、“仍要运行”。

### <a name="verify-and-connect"></a>验证并连接

1. 验证客户端计算机上是否已安装客户端证书。 使用本机 Azure 证书身份验证类型时，客户端证书是身份验证必需的。 若要查看客户端证书，请打开“管理用户证书”。 客户端证书安装在“Current User\Personal\Certificates”中。
1. 若要进行连接，请导航到“网络设置”，然后单击“VPN”。 VPN 连接显示所连接到的虚拟网络的名称。
