---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042598"
---
只要版本与 Windows 客户端的体系结构匹配，就可以在每台客户端计算机上使用相同的 VPN 客户端配置包。 有关支持的客户端操作系统列表，请参阅 [VPN 网关常见问题解答](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)中的“点到站点”部分。

>[!NOTE]
>在要从其进行连接的 Windows 客户端计算机上，必须拥有管理员权限。
>

请使用以下步骤配置用于证书身份验证的本机 Windows VPN 客户端：

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。 
1. 双击所需的包进行安装。 如果出现 SmartScreen 弹出窗口，请单击 " **详细信息** "，然后 **运行** 。
1. 在客户端计算机上，导航到 " **网络设置** "，并单击 " **VPN** "。 VPN 连接显示所连接到的虚拟网络的名称。
1. 尝试连接前，请验证客户端计算机上是否已安装客户端证书。 使用本机 Azure 证书身份验证类型时，客户端证书是身份验证必需的。