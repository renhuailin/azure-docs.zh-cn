---
title: 如何为 P2S OpenVPN 协议连接创建 Azure AD 租户：Azure AD 身份验证
titleSuffix: Azure VPN Gateway
description: 了解如何为 P2S OpenVPN 身份验证设置 Azure AD 租户，并在 Azure AD 中注册多个应用，使不同的用户和组能够以不同的方式进行访问。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: cherylmc
ms.openlocfilehash: f5b67a1843dd363c727122d77c4c9d574e90a4ab
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108760474"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>为 P2S OpenVPN 协议连接创建 Active Directory (AD) 租户

使用点到站点连接到 VNet 时，可以选择使用哪种协议。 使用的协议决定了可用的身份验证选项。 如果要使用 Azure Active Directory 身份验证，则可以在使用 OpenVPN 协议时这样做。 如果你希望一组不同的用户能够连接到不同的 VPN 网关，可以在 AD 中注册多个应用，并将这些应用链接到不同的 VPN 网关。 本文帮助你设置用于 P2S OpenVPN 的 Azure AD 租户，并在 Azure AD 中创建和注册多个应用，允许不同的用户和组能够以不同的方式进行访问。 有关点到站点 VPN 协议和身份验证的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6.在网关上启用身份验证

在此步骤中，将在 VPN 网关上启用 Azure AD 身份验证。

1. 通过导航到“点到站点配置”并选取“OpenVPN (SSL)”作为“隧道类型”，在 VPN 网关上启用 Azure AD 身份验证。 选择“Azure Active Directory”作为“身份验证类型”，然后在“Azure Active Directory”部分填写信息。

    ![Azure 门户视图](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > 请不要使用 Azure VPN 客户端的应用程序 ID：它会向所有用户授予对 VPN 网关的访问权限。 使用注册的应用程序的 ID。

2. 通过单击“下载 VPN 客户端”链接来创建和下载配置文件。

3. 解压缩已下载的 zip 文件。

4. 浏览到解压缩后的“AzureVPN”文件夹。

5. 记下“azurevpnconfig.xml”文件的位置。 azurevpnconfig.xml 包含 VPN 连接的设置，可以直接导入到 Azure VPN 客户端应用程序中。 还可以将此文件分发给需要通过电子邮件或其他方式建立连接的所有用户。 用户需有有效的 Azure AD 凭据才能成功建立连接。

## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 VPN 客户端以建立 P2S VPN 连接](openvpn-azure-ad-client.md)。
