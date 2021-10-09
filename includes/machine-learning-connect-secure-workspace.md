---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 3bd72c4930c510df03856ad31bbea51d21829c1e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646094"
---
若要连接到 VNet 后面保护的工作区，请使用以下方法之一：

* [Azure VPN 网关](/azure/vpn-gateway/vpn-gateway-about-vpngateways) - 通过专用连接将本地网络连接到 VNet。 通过公共 Internet 建立连接。 可以使用两种类型的 VPN 网关：

    * [点到站点](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)：每台客户端计算机使用 VPN 客户端连接到 VNet。
    * [站点到站点](/azure/vpn-gateway/tutorial-site-to-site-portal)：VPN 设备将 VNet 连接到本地网络。

* [ExpressRoute](https://azure.microsoft.com/services/expressroute/) - 通过专用连接将本地网络连接到云。 通过连接提供商建立连接。
* [Azure Bastion](/azure/bastion/bastion-overview) - 在此方案中，你将在 VNet 内部创建 Azure 虚拟机（有时称作 Jump Box）。 然后使用 Azure Bastion 连接到 VM。 Bastion 允许在本地 Web 浏览器中使用 RDP 或 SSH 会话连接到 VM。 然后使用 Jump Box 作为开发环境。 由于 Jump Box 位于 VNet 中，因此它可以直接访问工作区。 有关使用 Jump Box 的示例，请参阅[教程：创建安全工作区](/azure/machine-learning/tutorial-create-secure-workspace)。

> [!IMPORTANT]
> 使用“VPN 网关”或“ExpressRoute”时，需要规划本地资源与 VNet 中资源之间的名称解析工作方式 。 有关详细信息，请参阅[使用自定义 DNS 服务器](/azure/machine-learning/how-to-custom-dns)。
