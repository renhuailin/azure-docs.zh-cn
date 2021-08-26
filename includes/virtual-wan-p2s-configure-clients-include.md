---
ms.author: cherylmc
author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 06b4d9f2830bfd9599aee30c68235d3078eb32fa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734656"
---
**OpenVPN**

* [为 Azure 虚拟 WAN 配置 OpenVPN 客户端](../articles/virtual-wan/howto-openvpn-clients.md)
* [Azure AD 身份验证 - Windows 10](../articles/virtual-wan/openvpn-azure-ad-client.md)
* [Azure AD 身份验证 - macOS](../articles/virtual-wan/openvpn-azure-ad-client-mac.md)

**IKEv2**

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。

1. 双击所需的包进行安装。 如果看到弹出 SmartScreen，选择“详细信息”，然后选择“仍要运行” 。

1. 在客户端计算机上，导航到“网络设置”，并选择“VPN” 。 VPN 连接显示所连接到的虚拟网络的名称。

1. 尝试连接前，请验证客户端计算机上是否已安装客户端证书。 使用本机 Azure 证书身份验证类型时，客户端证书是身份验证必需的。 有关生成证书的详细信息，请参阅[生成证书](../articles/virtual-wan/certificates-point-to-site.md)。 有关如何安装客户端证书的信息，请参阅[安装客户端证书](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)。
