---
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/02/2021
ms.author: cherylmc
ms.openlocfilehash: cf11d4614cd4b6c3c387ef83b63da506a1702afc
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411894"
---
### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub-can-i-join-this-partner-program"></a>我是网络设备合作伙伴，想要获取中心内 NVA。 能否加入此合作伙伴计划？

很遗憾，目前暂无余量接纳任何新的合作伙伴产品/服务。 请在日后与我们联系！

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Azure 市场中的任何 NVA 是否都可以部署到虚拟 WAN 中心？

目前，仅支持将 [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer)、[Cisco Cloud vWAN 应用程序](https://azuremarketplace.microsoft.com/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview)和 [VMware Sd-WAN](https://aka.ms/vmwareMarketplaceLink) 部署到虚拟 WAN 中心。

### <a name="what-is-the-cost-of-the-nva"></a>NVA 的成本是多少？

必须从 NVA 供应商处购买 NVA 许可证。 对于来自 Barracuda 许可证的 Barracuda CloudGen WAN NVA，请参阅[“Barracuda 的 CloudGen WAN”页](https://www.barracuda.com/products/cloudgenwan)。 Cisco 目前仅提供 BYOL（自带许可证）许可模型，需要直接从 Cisco 购买。 此外，对于你所使用的 NVA 基础结构单元及其他任何资源，还需向 Microsoft 支付相应费用。 有关详细信息，请参阅[定价概念](../articles/virtual-wan/pricing-concepts.md)。

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>是否可以将 NVA 部署到基本中心？

否。 如果要部署 NVA，必须使用标准中心。

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>是否可以将 NVA 部署到安全中心？

是的。 可以使用 Azure 防火墙将合作伙伴 NVA 部署到中心。

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>是否可以将分支机构中的任何 CPE 设备连接到中心内的 Barracuda CloudGen WAN NVA？

否。 Barracuda CloudGen WAN 仅与 Barracuda CPE 设备兼容。 若要详细了解 CloudGen WAN 要求，请参阅[“Barracuda 的 CloudGen WAN”页](https://www.barracuda.com/products/cloudgenwan)。 对于 Cisco，有几个兼容的 SD-WAN CPE 设备。 有关兼容的 CPE，请参阅[适用于多云的 Cisco Cloud OnRamp](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) 文档。

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>中心内的 NVA 支持哪些路由方案？

只要是虚拟 WAN 支持的所有路由方案，中心内的 NVA 都支持。
