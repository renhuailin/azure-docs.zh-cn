---
title: 位置和连接提供商：Azure ExpressRoute | Microsoft Docs
description: 本文详细说明了服务的上市区域，以及如何连接到 Azure 区域。 按位置排序。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: duau
ms.openlocfilehash: 7e888b2abe73f0d6d5f614295f7e20f7a7857d60
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623544"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作伙伴和对等位置

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

下表显示连接位置和每个位置的服务提供商。 若要查看服务提供商及其提供服务的位置，请参阅[按服务提供商列出的位置](expressroute-locations.md)。

* “本地 Azure 区域”是每个对等互连位置的 [ExpressRoute Local](expressroute-faqs.md) 可以访问的区域。 “不适用”表示 ExpressRoute Local 在该对等互连位置不可用。

* “区域”是指[定价](https://azure.microsoft.com/pricing/details/expressroute/)级别。

### <a name="global-commercial-azure"></a>全球商业 Azure
| **位置** | **Address** | **区域** | **本地 Azure 区域** | **ER Direct** | **服务提供商** |
| --- | --- | --- | --- | --- | --- |
| **阿姆斯特丹** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 西欧 | 10G，100G | Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Interxion、KPN、IX Reach、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、Telefonica、Telenor、Telia Carrier、Verizon、Zayo |
| **阿姆斯特丹 2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 西欧 | 10G，100G | BICS、British Telecom、CenturyLink Cloud Connect、Colt、DE-CIX、Equinix、euNetworks、GÉANT、Interxion、NOS、NTT Global DataCenters EMEA、Orange、Vodafone |
| **亚特兰大** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | 不适用 | 10G，100G | Equinix、Megaport |
| **奥克兰** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | 不适用 | 10G | Devoli、Kordia、Megaport、REANNZ、Spark NZ、Vocus Group NZ |
| **曼谷** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | 不适用 | 10G | AIS、National Telecom UIH |
| **柏林** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | 德国北部 | 10G | Colt、Equinix、NTT Global DataCenters EMEA|
| **波哥大** | [Equinix BG1](https://www.equinix.com/locations/americas-colocation/colombia-colocation/bogota-data-centers/bg1/) | 4 | 不适用 | 10G | Equinix |
| **釜山** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | 韩国南部 | 不适用 | LG CNS |
| 坎皮纳斯 | [Ascenty](https://www.ascenty.com/en/data-centers-en/campinas/) | 3 | 巴西南部 | 10G，100G | Ascenty |
| **堪培拉** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | 澳大利亚中部 | 10G，100G | CDC |
| **堪培拉 2** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | 澳大利亚中部 2| 10G，100G | CDC、Equinix |
| **开普敦** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 南非西部 | 10G | BCX、Internet Solutions - Cloud Connect、Liquid Telecom、MTN Global Connect、Teraco、Vodacom |
| **金奈** | Tata Communications | 2 | 印度南部 | 10G | BSNL、Global CloudXchange (GCX)、SIFY、Tata Communications、VodafoneIdea |
| **金奈 2** | Airtel | 2 | 印度南部 | 10G | Airtel |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 美国中北部 | 10G，100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Cologix、Colt、Comcast、Coresite、Equinix、InterCloud、Internet2、Level 3 Communications、Megaport、PacketFabric、PCCW Global Limited、Sprint、Telia Carrier、Verizon、Zayo |
| Chicago2 | [CoreSite CH1](https://www.coresite.com/data-center/ch1-chicago-il) | 1 | 美国中北部 | 10G，100G | CoreSite |
| **哥本哈根** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | 不适用 | 10G | Interxion |
| **达拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | 不适用 | 10G，100G | Aryaka Networks、AT&T NetBond、Cologix、Equinix、Internet2、Level 3 Communications、Megaport、Neutrona Networks、PacketFabric、Telmex Uninet、Telia Carrier、Transtelco、Verizon、Zayo|
| **丹佛** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 美国中西部 | 10G，100G | CoreSite、Megaport、PacketFabric、Zayo |
| **迪拜** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | 阿拉伯联合酋长国北部 | 不适用 | Etisalat UAE |
| **迪拜 2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | 阿拉伯联合酋长国北部 | 不适用 | DE-CIX、du datamena、Equinix、GBI、Megaport、Orange、Orixcom |
| **都柏林** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 北欧 | 10G，100G | CenturyLink Cloud Connect、Colt、eir、Equinix、GEANT、euNetworks、Interxion、Megaport |
| Dublin2 | [Interxion DUB2](https://www.interxion.com/locations/europe/dublin) | 1 | 北欧 | 10G，100G | Interxion |
| **法兰克福** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | 德国中西部 | 10G，100G | AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、DE-CIX、Equinix、euNetworks、GBI、GEANT、InterCloud、Interxion、Megaport、NTT Global DataCenters EMEA、Orange、Telia Carrier、T-Systems |
| **法兰克福 2** | [Equinix FR7](https://www.equinix.com/locations/europe-colocation/germany-colocation/frankfurt-data-centers/fr7/) | 1 | 德国中西部 | 10G，100G | Deutsche Telekom AG、Equinix |
| **日内瓦** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | 瑞士西部 | 10G，100G | Colt、Equinix、Megaport、Swisscom |
| **中国香港特别行政区** | [Equinix HK1](https://www.equinix.com/data-centers/asia-pacific-colocation/hong-kong-colocation/hong-kong-data-centers/hk1) | 2 | 东亚 | 10G | Aryaka Networks、British Telecom、CenturyLink Cloud Connect、Chief Telecom、China Telecom Global、China Unicom、Colt、Equinix、InterCloud、Megaport、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telia Carrier、Verizon |
| **香港特别行政区 2** | [iAdvantage MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | 东亚 | 10G | China Mobile International、China Telecom Global、iAdvantage、Megaport、PCCW Global Limited、SingTel |
| **雅加达** | NTT Communications、Telin、Telkom Indonesia | 4 | 不适用 | 10G | Telin |
| **约翰内斯堡** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 南非北部 | 10G | BCX、British Telecom、Internet Solutions - Cloud Connect、Liquid Telecom、MTN Global Connect、Orange、Teraco、Vodacom |
| **吉隆坡** | [TIME dotCom Menara AIMS](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | 不适用 | 不适用 | TIME dotCom |
| **拉斯维加斯** | [Switch LV](https://www.switch.com/las-vegas) | 1 | 不适用 | 10G，100G | CenturyLink Cloud Connect、Megaport、PacketFabric |
| **伦敦** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 英国南部 | 10G，100G | AT&T NetBond、British Telecom、CenturyLink、Colt、Equinix、euNetworks、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telehouse - KDDI、Telenor、Telia Carrier、Verizon、Vodafone、Zayo |
| **London2** | [Telehouse North Two](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | 英国南部 | 10G，100G | BICS、British Telecom、CenturyLink Cloud Connect、Colt、 GTT、IX Reach、Equinix、JISC、Megaport、NTT Global DataCenters EMEA、SES、Sohonet、Telehouse - KDDI |
| **洛杉矶** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | 不适用 | 10G，100G | CoreSite、Equinix*、Megaport、Neutrona Networks、NTT、Zayo</br></br> 在洛杉矶，Equinix 不再支持新的 ExpressRoute 线路。请在 Los Angeles2 中创建新线路。* |
| **洛杉矶 2** | [Equinix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | 不适用 | 10G，100G | Equinix |
| **马德里** | [Interxion MAD1](https://www.interxion.com/es/donde-estamos/europa/madrid) | 1 | 西欧 | 10G，100G | Interxion、Megaport |
| **马赛** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | 法国南部 | 不适用 | Colt、DE-CIX、GEANT、Interxion、Jaguar Network、Ooredoo Cloud Connect |
| **墨尔本** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | 澳大利亚东南部 | 10G，100G | AARNet、Devoli、Equinix、Megaport、NEXTDC、Optus、Telstra Corporation、TPG Telecom |
| **迈阿密** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | 不适用 | 10G，100G | Claro、C3ntro、Equinix、Megaport、Neutrona Networks |
| **米兰** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | 不适用 | 10G | Colt、Equinix、Fastweb、IRIDEOS、Retelit |
| **明尼阿波利斯** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | 不适用 | 10G，100G | Cologix、Megaport |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | 不适用 | 10G，100G | Bell Canada、Cologix、Fibrenoire、Megaport、Telus、Zayo |
| **Mumbai** | Tata Communications | 2 | 印度西部 | 10G | BSNL、DE-CIX、Global CloudXchange (GCX)、Reliance Jio、Sify、Tata Communications、Verizon |
| **孟买 2** | Airtel | 2 | 印度西部 | 10G | Airtel、Sify、Vodafone Idea |
| **慕尼黑** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | 不适用 | 10G | Colt、DE-CIX、Megaport |
| **纽约** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 1 | 不适用 | 10G，100G | CenturyLink Cloud Connect、Colt、Coresite、DE-CIX、Equinix、InterCloud、Megaport、Packet、Zayo |
| **Newport(Wales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | 英国西部 | 10G，100G | British Telecom、Colt、Jisc、Level 3 Communications、Next Generation Data |
| **大阪** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 日本西部 | 10G，100G | AT TOKYO、BBIX、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、Megaport、NTT Communications、NTT SmartConnect、Softbank、Tokai Communications |
| **奥斯陆** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | 挪威东部 | 10G，100G | GlobalConnect、Megaport、Telenor、Telia Carrier |
| **巴黎** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | 法国中部 | 10G，100G | British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Intercloud、Interxion、Jaguar Network、Megaport、Orange、Telia Carrier、Zayo |
| **珀斯** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | 不适用 | 10G | Megaport、NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | 美国西部 3 | 10G，100G | Megaport、Zayo |
| **浦那** | [STT GDC Pune DC1](https://www.sttelemediagdc.in/our-data-centres-in-india) | 2 | 印度中部| 10G | |
| **魁北克市** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | 加拿大东部 | 10G，100G | Bell Canada、Equinix、Megaport、Telus |
| **克雷塔罗（墨西哥）** | [KIO Networks QR01](https://www.kionetworks.com/es-mx/) | 4 | 不适用 | 10G | Transtelco|
| **昆西** | [Sabey Datacenter - Building A](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | 美国西部 2 | 10G，100G | |
| **里约热内卢** | [Equinix-RJ2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/rio-de-janeiro-data-centers/rj2/) | 3 | 巴西东南部 | 10G | Equinix |
| **圣安东尼奥** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 美国中南部 | 10G，100G | CenturyLink Cloud Connect、Megaport |
| **圣保罗** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | 巴西南部 | 10G，100G | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEO |
| Sao Paulo2 | [TIVIT TSM](https://www.tivit.com/en/tivit/) | 3 | 巴西南部 | 10G，100G | |
| **西雅图** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 美国西部 2 | 10G，100G | Aryaka Networks、Equinix、Level 3 Communications、Megaport、Telus、Zayo |
| **首尔** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | 韩国中部 | 10G，100G | KINX、KT、LG CNS、LGUplus、Equinix、Sejong Telecom、SK Telecom |
| Seoul2 | [KT IDC](https://www.kt-idc.com/eng/introduce/sub1_4_10.jsp#tab) | 2 | 韩国中部 | 不适用 | |
| **硅谷** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 美国西部 | 10G，100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Comcast、Coresite、Equinix、InterCloud、Internet2、IX Reach、Packet、PacketFabric、Level 3 Communications、Megaport、Orange、Sprint、Tata Communications、Telia Carrier、Verizon、Zayo |
| **硅谷 2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 美国西部 | 10G，100G | Colt、Coresite | 
| **新加坡** | [Equinix SG1](https://www.equinix.com/data-centers/asia-pacific-colocation/singapore-colocation/singapore-data-center/sg1) | 2 | 东南亚 | 10G，100G | Aryaka Networks、AT&T NetBond、British Telecom、China Mobile International、Epsilon Global Communications、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Telstra Corporation、Verizon、Vodafone |
| **新加坡 2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 东南亚 | 10G，100G | CenturyLink Cloud Connect、China Unicom Global、Colt、Epsilon Global Communications、Equinix、Megaport、PCCW Global Limited、SingTel、Telehouse - KDDI |
| **斯塔万格** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | 挪威西部 | 10G，100G |GlobalConnect、Megaport |
| **斯德哥尔摩** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | 不适用 | 10G | Equinix、Telia Carrier |
| **悉尼** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | 澳大利亚东部 | 10G，100G | AARNet、AT&T NetBond、British Telecom、Devoli、Equinix、Kordia、Megaport、NEXTDC、NTT Communications、Optus、Orange、Spark NZ、Telstra Corporation、TPG Telecom、Verizon、Vocus Group NZ |
| **悉尼 2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | 澳大利亚东部 | 10G，100G | Megaport、NextDC |
| **中国台北** | Chief Telecom | 2 | 不适用 | 10G | Chief Telecom、Chunghwa Telecom、FarEasTone |
| **东京** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 日本东部 | 10G，100G | Aryaka Networks、AT&T NetBond、BBIX、British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Intercloud、Internet Initiative Japan Inc. - IIJ、Megaport、NTT Communications、NTT EAST、Orange、Softbank、Telehouse - KDDI、Verizon |
| **东京 2** | [AT TOKYO](https://www.attokyo.com/) | 2 | 日本东部 | 10G，100G | AT TOKYO、China Unicom Global、Megaport、Tokai Communications |
| **多伦多** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | 加拿大中部 | 10G，100G | AT&T NetBond、Bell Canada、CenturyLink Cloud Connect、Cologix、Equinix、IX Reach Megaport、Telus、Verizon、Zayo |
| **多伦多 2** | [Allied REIT](https://www.alliedreit.com/property/905-king-st-w/) | 1 | 加拿大中部 | 10G，100G | |
| **温哥华** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | 不适用 | 10G | Bell Canada、Cologix、Megaport、Telus |
| **华盛顿特区** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 美国东部、美国东部 2 | 10G，100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Cologix、Colt、Comcast、Coresite、Equinix、Internet2、InterCloud、Iron Mountain、IX Reach、Level 3 Communications、Megaport、Neutrona Networks、NTT Communications、Orange、PacketFabric、SES、Sprint、Tata Communications、Telia Carrier、Verizon、Zayo |
| **华盛顿特区 2** | [Coresite VA2](https://www.coresite.com/data-center/va2-reston-va) | 1 | 美国东部、美国东部 2 | 10G，100G | CenturyLink Cloud Connect、Coresite、Intelsat、Megaport、Viasat、Zayo | 
| **苏黎世** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | 瑞士北部 | 10G，100G | Colt、Equinix、Intercloud、Interxion、Megaport、Swisscom |

 **+** 表示即将推出

### <a name="national-cloud-environments"></a>国家/地区云环境

Azure 国家/地区云彼此独立，是全球商业 Azure 的一部分。 一个 Azure 云的 ExpressRoute 无法连接到其他 Azure 云中的 Azure 区域。

### <a name="us-government-cloud"></a>美国政府云
| **位置** | **Address** | **本地 Azure 区域**| **ER Direct** | **服务提供商** |
| --- | --- | --- | --- | --- |
| **亚特兰大** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | 不适用 | 10G，100G | Equinix |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 不适用 | 10G，100G | AT&T NetBond、British Telecom、Equinix、Level 3 Communications、Verizon |
| **达拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 不适用 | 10G，100G | Equinix、Internet2、Megaport、Verizon |
| **纽约** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 不适用 | 10G，100G | Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov 亚利桑那州 | 10G，100G | AT&T NetBond、CenturyLink Cloud Connect、Megaport |
| **圣安东尼奥** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov 德克萨斯州 | 10G，100G | CenturyLink Cloud Connect、Megaport |
| **硅谷** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | 不适用 | 10G，100G | AT&T、Equinix、Level 3 Communications、Verizon |
| **西雅图** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 不适用 | 10G，100G | Equinix、Megaport |
| **华盛顿特区** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD 东部、US Gov 弗吉尼亚州 | 10G，100G | AT&T NetBond、CenturyLink Cloud Connect、Equinix、Level 3 Communications、Megaport、Verizon |

### <a name="china"></a>中国
| **位置** | **Address** | **本地 Azure 区域** | **ER Direct** | **服务提供商** |
| --- | --- | --- | --- | --- |
| **北京** | 中国电信 | 不适用 | 10G | 中国电信 |
| **北京 2** | GDS | 不适用 | 10G | 中国电信、中国联通、GDS |
| **上海** | 中国电信 | 不适用 | 10G | 中国电信 |
| **上海 2** | GDS | 不适用 | 10G | 中国电信、中国联通、GDS |

若要了解详细信息，请参阅 [位于中国的 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>德国
| **位置** | **服务提供商** |
| --- | --- |
| **柏林** |e-shelter、Megaport+、T-Systems |
| **法兰克福** |Colt、Equinix、Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>通过 Exchange 提供商建立的连接
如果前面部分中未列出连接服务提供商，仍可以建立连接。

* 请咨询连接服务提供商，以确定他们是否已连接到上表中列出的任何 Exchange 提供商。 可以访问以下链接，以收集 Exchange 提供商所提供的服务的详细信息。 已有多个连接提供商连接到以太网 Exchange。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/resources/videos/cloud-exchange-overview)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
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

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>通过其他服务提供商建立的连接
| **位置** | **Exchange** | **连接提供商** |
| --- | --- | --- |
| **阿姆斯特丹** | Equinix、Interxion、Level 3 Communications | BICS、CloudXpress、Eurofiber、Fastweb S.p.A、Gulf Bridge International、Kalaam Telecom Bahrain B.S.C、MainOne、Nianet、POST Telecom Luxembourg、Proximus、RETN、TDC Erhverv、Telecom Italia Sparkle、Telekom Deutschland GmbH、Telia |
| **亚特兰大** | Equinix| Crown Castle
| **开普敦** | Teraco | MTN |
| **金奈** | Tata Communications | Tata Teleservices |
| **芝加哥** | Equinix| Crown Castle、Spectrum Enterprise、Windstream |
| **达拉斯** | Equinix、Megaport | Axtel、C3ntro Telecom、Cox Business、Crown Castle、Data Foundry、Spectrum Enterprise、Transtelco |
| **法兰克福** | Interxion | BICS、Cinia、Equinix、Nianet、QSC AG、Telekom Deutschland GmbH |
| **汉堡** | Equinix | Cinia |
| **香港特别行政区** | Equinix | Chief、Macroview Telecom |
| **约翰内斯堡** | Teraco | MTN |
| **伦敦** | BICS、Equinix、euNetworks| Bezeq International Ltd.、CoreAzure、Epsilon、Telecommunications Limited、Exponential E、HSO、NexGen Networks、Proximus、Tamares Telecom、Zain |
| **洛杉矶** | Equinix |Crown Castle、Spectrum Enterprise、Transtelco |
| **马德里** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc.Aptum Technologies、Rogers、Zirro |
| **Mumbai** | Tata Communications | Tata Teleservices |
| **纽约** |Equinix、Megaport | Altice Business、Crown Castle、Spectrum Enterprise、Webair |
| **巴黎** | Equinix | Proximus |
| **魁北克市** | Megaport | Fibrenoire |
| **圣保罗** | Equinix | Venha Pra Nuvem |
| **西雅图** |Equinix | Alaska Communications |
| **硅谷** |Coresite、Equinix | Cox Business、Spectrum Enterprise、Windstream、X2nsat Inc. |
| **新加坡** |Equinix |1CLOUDSTAR、BICS、CMC Telecom、Epsilon Telecommunications Limited、LGA Telecom、United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **悉尼** | Megaport | Macquarie Telecom Group|
| **东京** | Equinix | ARTERIA Networks Corporation、BroadBand Tower, Inc. |
| **多伦多** | Equinix、Megaport | Airgate Technologies Inc.、Beanfield Metroconnect、Aptum Technologies、IVedha Inc、Oncore Cloud Services Inc.、Rogers、Thinktel、Zirro|
| **华盛顿特区** |Equinix | Altice Business、BICS、Cox Business、Crown Castle、Gtt Communications Inc、Epsilon Telecommunications Limited、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute 系统集成商
根据网络的规模，有时，很难启用专用连接来满足需要。 可以与下表中列出的任一系统集成商合作，以帮助你将加入 ExpressRoute。

| **洲** | **系统集成商** |
| --- | --- |
| **亚洲** |Avanade Inc.、OneAs1a |
| **澳大利亚** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **欧洲** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北美** |Avanade Inc.、Equinix Professional Services、FlexManage、Lightstream、Perficient、Presidio |
| **南美洲** |Avanade Inc.、Venha Pra Nuvem |
## <a name="next-steps"></a>后续步骤
* 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置地图"
