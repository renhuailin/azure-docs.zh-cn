---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/28/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6bead4346bdb23dd725fa474ef1bd8a982d2b61a
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336266"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>是否所有 Azure VPN 网关 SKU 都支持 RADIUS 身份验证？

VpnGw1、VpnGw2 和 VpnGw3 SKU 支持 RADIUS 身份验证。 如果使用的是旧版 SKU，则标准和高性能 SKU 支持 RADIUS 身份验证。 基本网关 SKU 不支持该身份验证。 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>经典部署模型是否支持 RADIUS 身份验证？
 
否。 经典部署模型不支持 RADIUS 身份验证。

### <a name="what-is-the-timeout-period-for-radius-requests-sent-to-the-radius-server"></a>发送到 RADIUS 服务器的 RADIUS 请求的超时期限是多少？
RADIUS 请求设置为在 30 秒后超时。 目前不支持用户定义的超时值。
 
### <a name="are-3rd-party-radius-servers-supported"></a>是否支持第三方 RADIUS 服务器？

是的，支持第三方 RADIUS 服务器。
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>若要确保 Azure 网关能够访问本地 RADIUS 服务器，对连接有何要求？

需要具有到本地站点的 VPN 站点到站点连接，并且需要配置正确的路由。  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>是否可以通过 ExpressRoute 连接来传送（从 Azure VPN 网关）流向本地 RADIUS 服务器的流量？

否。 它只能通过站点到站点连接进行传送。
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>RADIUS 身份验证支持的 SSTP 连接数是否有变化？ 支持的最大 SSTP 和 IKEv2 连接数是多少？

RADIUS 身份验证在网关上支持的最大 SSTP 连接数没有变化。 对于 SSTP，仍然为 128；但对于 IKEv2，则取决于网关 SKU。 有关支持的连接数的详细信息，请参阅[网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)。
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trusted-certificate-to-azure"></a>使用 RADIUS 服务器执行证书身份验证与使用 Azure 本机证书身份验证执行身份验证（通过将受信任的证书上传到 Azure）之间有何区别？

在 RADIUS 证书身份验证中，身份验证请求被转发到处理实际证书验证的 RADIUS 服务器。 如果希望通过 RADIUS 与已有的证书身份验证基础结构进行集成，则此选项非常有用。
  
使用 Azure 进行证书身份验证时，由 Azure VPN 网关执行证书验证。 需要将证书公钥上传到网关。 还可以指定不允许进行连接的已吊销证书的列表。

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>RADIUS 身份验证是否同时适用于 IKEv2 和 SSTP VPN？

是的，IKEv2 和 SSTP VPN 都支持 RADIUS 身份验证。 

### <a name="does-radius-authentication-work-with-the-openvpn-client"></a>RADIUS 身份验证是否适用于 OpenVPN 客户端？

只有通过 PowerShell，OpenVPN 协议才支持 RADIUS 身份验证。
