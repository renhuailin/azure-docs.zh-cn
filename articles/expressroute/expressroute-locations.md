---
title: 连接提供商和位置：Azure ExpressRoute | Microsoft Docs
description: 本文详细说明了服务的上市区域，以及如何连接到 Azure 区域。 按连接服务提供商排序。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/26/2021
ms.author: duau
ms.openlocfilehash: 7703c1bba0faf69ee5d2640497df64cc1a418564
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665257"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>ExpressRoute 连接合作伙伴和对等互连位置

> [!div class="op_single_selector"]
> * [按提供商列出的位置](expressroute-locations.md)
> * [按位置列出的提供商](expressroute-locations-providers.md)


本文中的表格提供有关 ExpressRoute 地理覆盖范围和位置、ExpressRoute 连接提供商以及 ExpressRoute 系统集成商 (SI) 的信息。

> [!Note]
> Azure 区域和 ExpressRoute 位置是两个截然不同的概念，了解两者之间的差异对于探索 Azure 混合网络连接至关重要。 
>
>

## <a name="azure-regions"></a>Azure 区域
Azure 区域是 Azure 计算、网络和存储资源所在的全球数据中心。 创建 Azure 资源时，客户需要选择资源位置。 资源位置确定了在其中创建资源的 Azure 数据中心（或可用性区域）。

## <a name="expressroute-locations"></a>ExpressRoute 位置
ExpressRoute 位置（有时称为对等互连位置或交会位置）是 Microsoft 企业边缘 (MSEE) 设备所在的归置设施。 ExpressRoute 位置是 Microsoft 网络的入口点，并且是全球分布式的，使客户有机会连接到全球各地的 Microsoft 网络。 这些位置是 ExpressRoute 合作伙伴和 ExpressRoute Direct 客户向 Microsoft 网络发起交叉连接的位置。 一般情况下，ExpressRoute 位置不需要与 Azure 区域匹配。 例如，客户可以在“西雅图”对等互连位置使用“美国东部”资源位置创建 ExpressRoute 线路 。

如果至少与地缘政治区域内的一个 ExpressRoute 位置连接，将有权访问地缘政治区域内所有区域中的 Azure 服务。

[!INCLUDE [expressroute-azure-regions-geopolitical-region](../../includes/expressroute-azure-regions-geopolitical-region.md)]

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 连接服务提供商

下表显示按服务提供商列出的位置。 若要按位置查看可用的提供商，请参阅[按位置列出的服务提供商](expressroute-locations-providers.md)。


### <a name="global-commercial-azure"></a>全球商业 Azure

| **服务提供商** | **Microsoft Azure** | **Microsoft 365**  | **位置** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |支持 |支持 |墨尔本、悉尼 |
| **[Airtel](https://www.airtel.in/business/#/)** | 支持 | 支持 | 金奈 2、孟买 2 |
| **[AIS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | 支持 | 支持 | 曼谷 |
| **[Aryaka Networks](https://www.aryaka.com/)** |支持 |支持 |阿姆斯特丹、芝加哥、达拉斯、香港特别行政区、圣保罗、西雅图、硅谷、新加坡、东京、华盛顿特区 |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |支持 |支持 | 圣保罗坎皮纳斯 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |支持 |支持 |阿姆斯特丹、芝加哥、达拉斯、法兰克福、伦敦、硅谷、新加坡、悉尼、东京、多伦多、华盛顿特区 |
| **[AT TOKYO](https://www.attokyo.com/connectivity/azure.html)** | 支持 | 支持 | 大坂、东京 2 |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/bics-cloud-connect-an-official-microsoft-azure-technology-partner/)** | 支持 | 支持 | 阿姆斯特丹 2、伦敦 2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | 支持 | 支持 | 大坂、东京 |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |支持 |支持 |开普敦、约翰内斯堡|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |支持 |支持 |蒙特利尔、多伦多、魁北克市、温哥华 |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |支持 |支持 |阿姆斯特丹、阿姆斯特丹 2、芝加哥、法兰克福、香港特别行政区、约翰内斯堡、伦敦、伦敦 2、纽波特（威尔士）、巴黎、圣保罗、硅谷、新加坡、悉尼、东京、华盛顿特区 |
| [BSNL](https://www.bsnl.co.in/opencms/bsnl/BSNL/services/enterprises/cloudway.html) |支持 |支持 |金奈、孟买 |
| **[C3ntro](https://www.c3ntro.com/)** |支持 |支持 |迈阿密 |
| **CDC** | 支持 | 支持 | 堪培拉、堪培拉 2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |支持 |支持 |阿姆斯特丹 2、芝加哥、都柏林、法兰克福、香港、拉斯维加斯、伦敦、伦敦 2、纽约、巴黎、圣安东尼奥、硅谷、新加坡 2、东京、多伦多、华盛顿特区、华盛顿特区 2 |
| **[Chief Telecom](https://www.chief.com.tw/)** |支持 |支持 |香港、台北 |
| **中国移动国际** |支持 |支持 | 香港特别行政区、香港特别行政区 2、新加坡 |
| **China Telecom Global** |支持 |支持 |香港、香港 2 |
| **[中国联通全球](https://cloudbond.chinaunicom.cn/home-en)** |支持 |支持 | 香港、新加坡 2、东京 2 |
| **[Chunghwa Telecom](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |支持 |支持 |台北 |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |支持 |支持 |迈阿密 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |支持 |支持 |芝加哥、达拉斯、明尼阿波利斯、蒙特利尔、多伦多、温哥华、华盛顿特区 |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |支持 |支持 |阿姆斯特丹、阿姆斯特丹 2、柏林、芝加哥、都柏林、法兰克福、日内瓦、香港、伦敦、伦敦 2、马赛、米兰、慕尼黑、纽波特、纽约、大阪、巴黎、硅谷、硅谷 2、新加坡 2、东京、华盛顿特区、苏黎世 |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |支持 |支持 |芝加哥、硅谷、华盛顿特区 |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |支持 |支持 |芝加哥、芝加哥 2、丹佛、洛杉矶、纽约、硅谷、硅谷 2、华盛顿特区、华盛顿特区 2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | 支持 |支持 |阿姆斯特丹 2，迪拜 2、法兰克福、马赛、孟买、慕尼黑、纽约 |
| **[Devoli](https://devoli.com/expressroute)** | 支持 |支持 | 奥克兰、墨尔本、悉尼 |
| **[Deutsche Telekom AG IntraSelect](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** | 支持 |支持 |法兰克福 |
| **[Deutsche Telekom AG](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** | 支持 |支持 |法兰克福 2 |
| **du datamena** |支持 |支持 | 迪拜 2 |
| **eir** |支持 |支持 |都柏林|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |支持 |支持 |新加坡、新加坡 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |支持 |支持 |阿姆斯特丹、阿姆斯特丹 2、亚特兰大、柏林、波哥大、堪培拉 2、芝加哥、达拉斯、迪拜 2、都柏林、法兰克福、法兰克福 2、日内瓦、香港、伦敦、伦敦 2、洛杉矶*、洛杉矶 2、墨尔本、迈阿密、米兰、纽约、大阪、巴黎、魁北克市、里约热内卢、圣保罗、西雅图、首尔、硅谷、新加坡、新加坡 2、斯德哥尔摩、悉尼、东京、多伦多、华盛顿特区、苏黎世</br></br> *洛杉矶的 Equinix 不再支持新的 ExpressRoute 线路。请在洛杉矶 2 创建新线路。 |
| **Etisalat UAE** |支持 |支持 |迪拜|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |支持 |支持 |阿姆斯特丹、阿姆斯特丹 2、都柏林、法兰克福、伦敦 |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |支持 |支持 |台北|
| [Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/) | 支持 |支持 |Milan|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |支持 |支持 |Montreal|
| **[GBI](https://www.gbiinc.com/microsoft-azure/)** |支持 |支持 |迪拜 2、法兰克福|
| **[GÉANT](https://www.geant.org/Networks)** |支持 |支持 |阿姆斯特丹、阿姆斯特丹 2、都柏林、法兰克福、马赛 |
| [GlobalConnect](https://www.globalconnect.no/tjenester/nettverk/cloud-access) | 支持 |支持 | 奥斯陆、斯塔万格 | 
| GTT |支持 |支持 |London2 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | 支持| 支持 | 金奈、孟买 |
| [iAdvantage](https://www.scx.sunevision.com/) | 支持 | 支持 | 香港特别行政区 2 |
| **Intelsat** | 支持 | 支持 | 华盛顿特区 2 |
| **[InterCloud](https://www.intercloud.com/)** |支持 |支持 |阿姆斯特丹、芝加哥、法兰克福、香港特别行政区、伦敦、纽约、巴黎、硅谷、新加坡、东京、华盛顿特区、苏黎世 |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |支持 |支持 |芝加哥、达拉斯、硅谷、华盛顿特区 |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |支持 |支持 |大坂、东京 |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |支持 |支持 |开普敦、约翰内斯堡、伦敦 |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |支持 |支持 |阿姆斯特丹、阿姆斯特丹 2、哥本哈根、都柏林、迪拜 2、法兰克福、伦敦、马德里、马赛、巴黎、苏黎世 |
| **[IRIDEOS](https://irideos.it/)** |支持 |支持 |Milan |
| Iron Mountain | 支持 |支持 |华盛顿特区 |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|支持 |支持 | 阿姆斯特丹、伦敦 2、硅谷、多伦多、华盛顿特区 |
| **Jaguar Network** |支持 |支持 |马赛、巴黎 |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |支持 |支持 |伦敦、纽波特（威尔士） |
| **[KINX](https://www.kinx.net/service/cloudhub/ms-expressroute/?lang=en)** |支持 |支持 |首尔 |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | 支持 |支持 |奥克兰、悉尼 |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | 支持 | 支持 | 阿姆斯特丹 |
| **[KT](https://cloud.kt.com/)** | 支持 | 支持 | 首尔 |
| **[Level 3 Communications](https://www.lumen.com/en-us/hybrid-it-cloud/cloud-connect.html)** |支持 |支持 |阿姆斯特丹、芝加哥、达拉斯、伦敦、新港（威尔士）、圣保罗、西雅图、硅谷、新加坡、华盛顿特区 |
| **LG CNS** |支持 |支持 |釜山、首尔 |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |支持 |支持 |开普敦、约翰内斯堡 |
| [LGUplus](http://www.uplus.co.kr/) |支持 |支持 |首尔 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支持 |支持 |阿姆斯特丹、亚特兰大、奥克兰、金奈、芝加哥、达拉斯、丹佛、迪拜 2、都柏林、法兰克福、日内瓦、香港特别行政区、香港特别行政区 2、拉斯维加斯、伦敦、伦敦 2、洛杉矶、马德里、墨尔本、迈阿密、明尼阿波利斯、蒙特利尔、慕尼黑、纽约、大阪、奥斯陆、巴黎、珀斯、菲尼克斯、魁北克市、圣安东尼奥、西雅图、硅谷、新加坡、新加坡 2、斯塔万格、斯德哥尔摩、悉尼、悉尼 2、东京、东京 2、多伦多、温哥华、华盛顿特区、华盛顿特区 2、苏黎世 |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |支持 |支持 |London |
| MTN Global Connect |支持 |支持 |开普敦、约翰内斯堡|
| **[National Telecom](https://www.nc.ntplc.co.th/cat/category/264/855/CAT+Direct+Cloud+Connect+for+Microsoft+ExpressRoute?lang=en_EN)** |支持 |支持 |曼谷 |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |支持 |支持 |达拉斯、洛杉矶、迈阿密、圣保罗、华盛顿特区 |
| **[Next Generation Data](https://vantage-dc-cardiff.co.uk/)** |支持 |支持 |Newport(Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |支持 |支持 |墨尔本、珀斯、悉尼、悉尼 2 |
| [NOS](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx) |支持 |支持 |阿姆斯特丹 2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |支持 |支持 |阿姆斯特丹、香港特别行政区、雅加达、伦敦、洛杉矶、大阪、新加坡、悉尼、东京、华盛顿特区 |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |支持 |支持 |东京 |
| **[NTT Global DataCenters EMEA](https://hello.global.ntt/)** |支持 |支持 |阿姆斯特丹 2、柏林、法兰克福、伦敦 2 |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |支持 |支持 |大阪 |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |支持 |支持 |马赛 |
| **[Optus](https://www.optus.com.au/enterprise/)** |支持 |支持 |墨尔本、悉尼 |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |支持 |支持 |阿姆斯特丹、阿姆斯特丹 2、迪拜 2、法兰克福、香港特别行政区、约翰内斯堡、伦敦、巴黎、圣保罗、硅谷、新加坡、悉尼、东京、华盛顿特区 |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | 支持 | 支持 | 迪拜 2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |支持 |支持 |芝加哥、达拉斯、丹佛、拉斯维加斯、硅谷、华盛顿特区 |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |支持 |支持 |芝加哥、香港特别行政区、香港 2、伦敦、新加坡 2 |
| [REANNZ](https://www.reannz.co.nz/products-and-services/cloud-connect/) | 支持 | 支持 | 奥克兰 |
| [Retelit](https://www.retelit.it/EN/Home.aspx) | 支持 | 支持 | Milan | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |支持 |支持 |首尔 |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | 支持 |支持 | 伦敦 2、华盛顿特区 |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |支持 |支持 |金奈、孟买 2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |支持 |支持 |香港特别行政区 2、新加坡、新加坡 2 |
| **[SK Telecom](http://b2b.tworld.co.kr/bizts/solution/solutionTemplate.bs?solutionId=0085)** |支持 |支持 |首尔 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |支持 |支持 |大坂、东京 |
| [Sohonet](https://www.sohonet.com/fastlane/) |支持 |支持 |London2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |支持 |支持 |奥克兰、悉尼 |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |支持 |支持 |芝加哥、硅谷、华盛顿特区 |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | 支持 | 支持 | 日内瓦、苏黎世 |
| **[Tata Communications](https://www.tatacommunications.com/solutions/network/cloud-ready-networks/)** |支持 |支持 |阿姆斯特丹、金奈、香港特别行政区、伦敦、孟买、圣保罗、硅谷、新加坡、华盛顿特区 |
| **[Telefonica](https://www.telefonica.com/es/home)** |支持 |支持 |阿姆斯特丹、圣保罗 |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |支持 |支持 |伦敦、伦敦 2、新加坡 2、东京 |
| **Telenor** |支持 |支持 |阿姆斯特丹、伦敦、奥斯陆 |
| **[Telia Carrier](https://www.teliacarrier.com/)** | 支持 | 支持 |阿姆斯特丹、芝加哥、达拉斯、法兰克福、香港特别行政区、伦敦、奥斯陆、巴黎、硅谷、斯德哥尔摩、华盛顿特区 |
| [Telin](https://www.telin.net/product/data-connectivity/telin-cloud-exchange) | 支持 | 支持 |雅加达 |
| **Telmex Uninet**| 支持 | 支持 | 达拉斯 |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |支持 |支持 |墨尔本、新加坡、悉尼 |
| **[Telus](https://www.telus.com)** |支持 |支持 |蒙特利尔、西雅图、魁北克市、多伦多、温哥华 |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |支持 |支持 |开普敦、约翰内斯堡 |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | 支持 | 支持 | 吉隆坡 |
| [Tokai Communications](https://www.tokai-com.co.jp/en/) | 支持 | 支持 | 大坂、东京 2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |支持 |支持 |达拉斯、克雷塔罗（墨西哥）|
| **[T-Systems](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** |支持 |支持 |法兰克福|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |支持 |支持 |圣保罗 |
| [UIH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute) | 支持 | 支持 | 曼谷 |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |支持 |支持 |阿姆斯特丹、芝加哥、达拉斯、香港特别行政区、伦敦、孟买、硅谷、新加坡、悉尼、东京、多伦多、华盛顿特区 |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | 支持 | 支持 | 华盛顿特区 2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | 支持 | 支持 | 奥克兰、悉尼 |
| **Vodacom** |支持 |支持 |开普敦、约翰内斯堡|
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |支持 |支持 |阿姆斯特丹 2、伦敦、新加坡 |
| **[Vodafone Idea](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | 支持 | 支持 | 孟买 2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |支持 |支持 |阿姆斯特丹、芝加哥、达拉斯、丹佛、伦敦、洛杉矶、蒙特利尔、纽约、巴黎、菲尼克斯、西雅图、硅谷、多伦多、华盛顿特区、华盛顿特区 2 |

 **+** 表示即将推出

### <a name="national-cloud-environment"></a>国家/地区云环境

Azure 国家/地区云彼此独立，是全球商业 Azure 的一部分。 一个 Azure 云的 ExpressRoute 无法连接到其他 Azure 云中的 Azure 区域。 

### <a name="us-government-cloud"></a>美国政府云

| **服务提供商** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |支持 |支持 |芝加哥、菲尼克斯、硅谷、华盛顿特区 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |支持 |支持 |纽约、菲尼克斯、圣安东尼奥、华盛顿特区 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |支持 |支持 |亚特兰大、芝加哥、达拉斯、纽约、西雅图、硅谷、华盛顿特区 |
| **[Internet2]()** |支持 |支持 |达拉斯 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |支持 |支持 |芝加哥、硅谷、华盛顿特区 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支持 | 支持 | 芝加哥、达拉斯、圣安东尼奥、西雅图、华盛顿特区 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |支持 |支持 |芝加哥、达拉斯、纽约、硅谷、华盛顿特区 |

### <a name="china"></a>中国

| **服务提供商** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **中国电信** |支持 |不支持 |北京、北京 2、上海、上海 2 |
| **中国联通** | 支持 | 不支持 | 北京 2、上海 2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |支持 |不支持 |北京 2、上海 2 |

若要了解详细信息，请参阅 [位于中国的 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)。

### <a name="germany"></a>德国

| **服务提供商** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |支持 |不支持 |法兰克福 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |支持 |不支持 |法兰克福 |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |支持 |不支持 |柏林 |
| **Interxion** |支持 |不支持 |法兰克福 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支持  | 不支持 | 柏林 |
| **[T-Systems](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** |支持 |不支持 |柏林 |

## <a name="connectivity-through-exchange-providers"></a>通过 Exchange 提供商建立的连接

如果前面部分中未列出连接服务提供商，仍可以建立连接。

* 请咨询连接服务提供商，以确定他们是否已连接到上表中列出的任何 Exchange 提供商。 可以访问以下链接，以收集 Exchange 提供商所提供的服务的详细信息。 已有多个连接提供商连接到以太网 Exchange。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/interconnection-services/equinix-fabric)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* 让连接提供商将网络扩展到选择的对等互连位置。
  * 确保连接提供商以高可用性方式扩展连接，以防出现单点故障。
* 从 Exchange 连接服务提供商处订购一条 ExpressRoute 线路以连接到 Microsoft。
  * 根据 [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md) 中的步骤来设置连接。

## <a name="connectivity-through-satellite-operators"></a>通过卫星运营商建立的连接
如果你是远程用户并且没有光纤连接，或者想要了解其他连接选项，可以咨询以下卫星服务运营商。 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>通过其他服务提供商建立的连接

| **连接服务提供商** | **Exchange** | **位置** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** | Equinix |新加坡 |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix Cologix | 多伦多、蒙特利尔 |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |纽约、华盛顿特区 |
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | 蒙特利尔、多伦多 |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |东京 |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |达拉斯|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://www.bics.com/services/capacity-solutions/cloud-connect/)** | Equinix | 阿姆斯特丹、法兰克福、伦敦、新加坡、华盛顿特区 |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | 东京 |
| **[C3ntro Telecom](https://www.c3ntro.com/)** | Equinix、Megaport | 达拉斯 |
| **[Chief](https://www.chief.com.tw/)** | Equinix | 香港特别行政区 |
| **[Cinia](https://www.cinia.fi/palvelutiedotteet)** | Equinix、Megaport | 法兰克福、汉堡 |
| **[CloudXpress](https://www2.telenet.be/content/www-telenet-be/fr/business/sme-le/aanbod/internet/cloudxpress)** | Equinix | 阿姆斯特丹 | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | 新加坡 | 
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | 达拉斯、硅谷、华盛顿特区 |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | 亚特兰大、芝加哥、达拉斯、洛杉矶、纽约、华盛顿特区 |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | 达拉斯 |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | 伦敦、新加坡、华盛顿特区 |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | 阿姆斯特丹 |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | 阿姆斯特丹 |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | 魁北克市 |
| [FPT Telecom International](https://cloudconnect.vn/en) |Equinix |新加坡|
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | 华盛顿特区 |
| **[Gulf Bridge International](https://gbiinc.com/)** | Equinix | 阿姆斯特丹 |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | 伦敦、斯劳 |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |阿姆斯特丹 |
| **LGA Telecom** |Equinix |新加坡|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |香港特别行政区 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | 悉尼 |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | 阿姆斯特丹 |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | 华盛顿特区 |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | 开普敦、约翰内斯堡 |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | 阿姆斯特丹、法兰克福 |
| [Oncore Cloud Service Inc](https://www.oncore.cloud/services/ue-for-expressroute)| Equinix | Toronto |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | 阿姆斯特丹 |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | 阿姆斯特丹、都柏林、伦敦、巴黎 |
| **[QSC AG](https://www2.qbeyond.de/en/)** |Interxion | 法兰克福 |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | 阿姆斯特丹 |
| **Rogers** | Cologix、Equinix | 蒙特利尔、多伦多 |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | 芝加哥、达拉斯、洛杉矶、纽约、硅谷 | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| [Tata Teleservices](https://www.tatateleservices.com/business-services/data-services/secure-cloud-connect) | Tata Communications | 金奈、孟买 |
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | 阿姆斯特丹 | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | 阿姆斯特丹 |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | 阿姆斯特丹、法兰克福 |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | 阿姆斯特丹 |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | 新加坡 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | 圣保罗 |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | 纽约 |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | 芝加哥、硅谷、华盛顿特区 |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |硅谷、硅谷 2|
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| Level 3 | 马德里 |
| **[Zirro](https://zirro.com/services/)**| Cologix、Equinix | 蒙特利尔、多伦多 |

## <a name="connectivity-through-datacenter-providers"></a>通过数据中心提供商建立的连接

| **提供程序** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport、PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport、PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach、Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport、PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach、Megaport、PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport、PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach、Megaport、PacketFabric |
| **[T5 Datacenters](https://t5datacenters.com/)** | IX Reach |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach、Megaport |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>通过国家科研与教育网络 (NREN) 建立的连接

| **提供程序**|
| --- |
| **AARNET**| 
| **DeIC，通过 GÉANT**|
| **GARR，通过 GÉANT**|
| **GÉANT**|
| **HEAnet，通过 GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS，通过 GÉANT**|
| **SINET**|
| **Surfnet，通过 GÉANT**|

* 如果连接提供商未在此处列出，请查看其是否连接到上面列出的任何 ExpressRoute Exchange 合作伙伴。

## <a name="expressroute-system-integrators"></a>ExpressRoute 系统集成商
根据网络的规模，有时，很难启用专用连接来满足需要。 可以与下表中列出的任一系统集成商合作，以帮助你将加入 ExpressRoute。

| **系统集成商** | **洲** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | 欧洲 |
| **[Avanade Inc.](https://www.avanade.com/)** | 亚洲、欧洲、北美、南美 |
| **[Bright Skies GmbH](https://www.rackspace.com/bright-skies)** | 欧洲
| **[Ensyst](https://www.ensyst.com.au)** | 亚洲
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | 北美 |
| **[FlexManage](https://www.flexmanage.com/cloud)** | 北美 |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | 北美 |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | 澳大利亚 |
| **[MOQdigital](https://www.moqdigital.com/insights)** | 澳大利亚 |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | 欧洲（德国） |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | 欧洲 |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | 欧洲 |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | 亚洲 |
| **[Orange Networks](https://www.orange-networks.com/blog/88-azureexpressroute)** | 欧洲 |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 北美 |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | 北美 |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | 欧洲 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | 南美洲 |
| **[Vigilant.IT](https://vigilant.it/networking-services/microsoft-azure-networking/)** | 澳大利亚 |

## <a name="next-steps"></a>后续步骤
* 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置地图"
