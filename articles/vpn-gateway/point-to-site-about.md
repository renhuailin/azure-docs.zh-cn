---
title: 关于 Azure 点到站点 VPN 连接
titleSuffix: Azure VPN Gateway
description: 了解点到站点 VPN。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/28/2021
ms.author: cherylmc
ms.openlocfilehash: 1be0ecfe773bfa56900295db8701d9a582389046
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111554671"
---
# <a name="about-point-to-site-vpn"></a>关于点到站点 VPN

点到站点 (P2S) VPN 网关连接用于创建从单个客户端计算机到虚拟网络的安全连接。 可通过从客户端计算机启动连接来建立 P2S 连接。 对于要从远程位置（例如从家里或会议室）连接到 Azure VNet 的远程工作者，此解决方案很有用。 如果只有一些客户端需要连接到 VNet，则还可以使用 P2S VPN 这一解决方案来代替 S2S VPN。 本文适用于 Resource Manager 部署模型。

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S 使用哪种协议？

点到站点 VPN 可使用以下协议之一：

* **OpenVPN® 协议**，一种基于 SSL/TLS 的 VPN 协议。 由于大多数防火墙都会打开 TLS 所用的出站 TCP 端口 443，因此 TLS VPN 解决方案可以穿透防火墙。 OpenVPN 可用于从 Android、iOS（11.0 及更高版本）、Windows、Linux 和 Mac 设备（macOS 10.13 及更高版本）进行连接。

* 安全套接字隧道协议 (SSTP)，一个基于 TLS 的专属 VPN 协议。 由于大多数防火墙都会打开 TLS 所用的出站 TCP 端口 443，因此 TLS VPN 解决方案可以穿透防火墙。 只有 Windows 设备支持 SSTP。 Azure 支持所有采用 SSTP 的 Windows 版本（Windows 7 和更高版本）。

* IKEv2 VPN，这是一种基于标准的 IPsec VPN 解决方案。 IKEv2 VPN 可用于从 Mac 设备进行连接（macOS 10.11 和更高版本）。


>[!NOTE]
>P2S 的 IKEv2 和 OpenVPN 仅可用于资源管理器部署模型。 它们不可用于经典部署模型。
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>如何对 P2S VPN 客户端进行身份验证？

在 Azure 接受 P2S VPN 连接之前，必须先对用户进行身份验证。 Azure 提供两种机制用于对连接方用户进行身份验证。

### <a name="authenticate-using-native-azure-certificate-authentication"></a>使用本机 Azure 证书身份验证进行身份验证

使用本机 Azure 证书身份验证时，设备上的客户端证书用于对连接方用户进行身份验证。 客户端证书从受信任的根证书生成，并安装在每台客户端计算机上。 可以使用通过企业解决方案生成的根证书，也可以生成自签名证书。

客户端证书的验证由 VPN 网关执行，在建立 P2S VPN 连接期间发生。 验证时需要使用根证书，必须将该证书上传到 Azure。

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>使用本机 Azure Active Directory 身份验证进行身份验证

Azure AD 身份验证允许用户使用其 Azure Active Directory 凭据连接到 Azure。 本机 Azure VPN 身份验证只有 OpenVPN 协议和 Windows 10 支持，并且需要使用 [Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554)。

凭借本机 Azure AD 身份验证，你可以利用 Azure AD 的条件访问以及适用于 VPN 的多重身份验证 (MFA) 功能。

大致说来，需要执行以下步骤来配置 Azure AD 身份验证：

1. [配置 Azure AD 租户](openvpn-azure-ad-tenant.md)

2. [在网关上启用 Azure AD 身份验证](openvpn-azure-ad-tenant.md#enable-authentication)

3. [下载并配置 Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>使用 Active Directory (AD) 域服务器进行身份验证

AD 域身份验证可让用户使用其组织域凭据连接到 Azure。 它需要一台与 AD 服务器集成的 RADIUS 服务器。 组织也可以利用其现有的 RADIUS 部署。

可将 RADIUS 服务器部署到本地或 Azure VNet 中。 在身份验证期间，Azure VPN 网关充当传递设备，在 RADIUS 服务器与连接方设备之间来回转发身份验证消息。 因此，RADIUS 服务器必须能够访问网关。 如果 RADIUS 服务器位于本地，需要建立从 Azure 到本地站点的 VPN S2S 连接，才能实现这种访问。

RADIUS 服务器还能与 AD 证书服务集成。 这样，便可以使用 RADIUS 服务器以及用于 P2S 证书身份验证的企业证书部署，作为 Azure 证书身份验证的替代方法。 此方法的优点是不需要将根证书和吊销的证书上传到 Azure。

RADIUS 服务器还能与其他外部标识系统集成。 这样就为 P2S VPN 提供了大量的身份验证选项，包括多重身份验证选项。

![显示本地站点中的点到站点 VPN 的图示。](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>客户端配置要求是什么？

>[!NOTE]
>对于 Windows 客户端，你必须具有客户端设备上的管理员权限，才能发起从客户端设备到 Azure 的 VPN 连接。
>

用户使用 Windows 和 Mac 设备上的本机 VPN 客户端建立 P2S 连接。 Azure 提供一个 VPN 客户端配置 zip 文件，其中包含这些本机客户端连接到 Azure 时所需的设置。

* 对于 Windows 设备，VPN 客户端配置包括用户在其设备上安装的安装程序包。
* 对于 Mac 设备，该配置包括用户在其设备上安装的 mobileconfig 文件。

该 zip 文件还提供 Azure 端上的一些重要设置的值，使用这些设置可为这些设备创建你自己的配置文件。 其中一些值包括 VPN 网关地址、配置的隧道类型、路由，以及用于网关验证的根证书。

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>哪些网关 SKU 支持 P2S VPN？

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* 有关网关 SKU 的建议，请参阅[关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

>[!NOTE]
>基本 SKU 不支持 IKEv2 或 RADIUS 身份验证。
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>在 P2S 的 VPN 网关上配置了哪些 IKE/IPsec 策略？


**IKEv2**

| **Cipher** | **完整性** | **PRF** | **DH 组** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Cipher** | **完整性** | **PFS 组** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>在 P2S 的 VPN 网关上配置了哪些 TLS 策略？
**TLS**

|**策略** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>如何配置 P2S 连接？

P2S 配置需要相当多的特定步骤。 以下文章包含引导你完成 P2S 配置的步骤，以及用于配置 VPN 客户端设备的链接：

* [配置 P2S 连接 - RADIUS 身份验证](point-to-site-how-to-radius-ps.md)

* [配置 P2S 连接 - Azure 本机证书身份验证](vpn-gateway-howto-point-to-site-rm-ps.md)

* [配置 OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>删除 P2S 连接的配置

可以使用 PowerShell 或 CLI 删除某个连接的配置。 有关示例，请参阅[常见问题解答](vpn-gateway-vpn-faq.md#removeconfig)。

## <a name="how-does-p2s-routing-work"></a>P2S 路由的工作原理是什么？

请参阅以下文章：

* [关于点到站点 VPN 路由](vpn-gateway-about-point-to-site-routing.md)

* [如何播发自定义路由](vpn-gateway-p2s-advertise-custom-routes.md)

## <a name="faqs"></a>常见问题解答

关于 P2S，有多个基于身份验证的“常见问题解答”部分。

* [常见问题解答 - 证书身份验证](vpn-gateway-vpn-faq.md#P2S)

* [常见问题解答 - RADIUS 身份验证](vpn-gateway-vpn-faq.md#P2SRADIUS)
 
## <a name="next-steps"></a>后续步骤

* [配置 P2S 连接 - RADIUS 身份验证](point-to-site-how-to-radius-ps.md)

* [配置 P2S 连接 - Azure 证书身份验证](vpn-gateway-howto-point-to-site-rm-ps.md)

**“OpenVPN”是 OpenVPN Inc. 的商标。**
