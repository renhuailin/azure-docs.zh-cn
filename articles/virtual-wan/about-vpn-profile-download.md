---
title: Azure 虚拟 WAN：用户 VPN 客户端配置文件
description: 这可帮助你使用客户端配置文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980909"
---
# <a name="working-with-user-vpn-client-profile-files"></a>使用用户 VPN 客户端配置文件

配置文件包含配置 VPN 连接所需的信息。 本文可帮助你获取和了解用户 VPN 客户端配置文件所需的信息。

## <a name="download-the-profile"></a>下载配置文件

你可以使用 [下载配置文件](global-hub-profile.md) 一文中的步骤来下载客户端配置文件 zip 文件。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN 文件夹** 包含需要修改以包括密钥和证书的 *ovpn* 配置文件。 有关详细信息，请参阅[配置 OpenVPN 客户端](../virtual-wan/howto-openvpn-clients.md#windows)。

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 用户 VPN 的详细信息，请参阅[创建用户 VPN 连接](virtual-wan-point-to-site-portal.md)。
