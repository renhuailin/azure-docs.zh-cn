---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979057"
---
## <a name="extract-the-zip-file"></a>解压缩 zip 文件

解压缩 zip 文件。 该文件包含以下文件夹：

* AzureVPN
* 泛型
* OpenVPN（如果已在网关上启用 OpenVPN 和 Azure 证书或 RADIUS 身份验证设置） 。 选择对应于你的配置的相应项目以创建租户。

  * [VPN 网关 - 创建租户](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)。
  * [虚拟 WAN - 创建租户](../articles/virtual-wan/openvpn-azure-ad-tenant.md)。

## <a name="retrieve-information"></a>检索信息

在 AzureVPN 文件夹中导航到 azurevpnconfig.xml 文件，然后用记事本打开它 。 记下以下标记之间的文本。

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>配置文件详细信息

添加连接时，请使用在上一步中为配置文件详细信息页面收集的信息。 这些字段对应于以下信息：

* **受众：** 标识令牌所针对的接收方资源。
* **颁发者：** 标识发出令牌的安全令牌服务 (STS) 以及 Azure AD 租户。
* **租户：** 包含颁发令牌的目录租户的一个不变的唯一标识符。
* **FQDN：** Azure VPN 网关上的完全限定的域名 (FQDN)。
* **ServerSecret：** VPN 网关预共享密钥。

## <a name="folder-contents"></a>文件夹内容

* 通用文件夹包含公共服务器证书和 VpnSettings.xml 文件。 VpnSettings.xml 文件包含配置通用客户端所需的信息。

* 下载的 zip 文件可能还包含 WindowsAmd64 和 WindowsX86 文件夹。 这些文件夹包含用于 Windows 客户端的 SSTP 和 IKEv2 安装程序。 需要对客户端具有管理员权限才能安装它们。
