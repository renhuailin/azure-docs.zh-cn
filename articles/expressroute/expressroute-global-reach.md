---
title: Azure ExpressRoute：使用 Global Reach 连接到 Microsoft 云
description: 了解 Azure ExpressRoute Global Reach 如何将 ExpressRoute 线路链接在一起，以在本地网络之间建立专用网络。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: e8fdac875bf6469363dbcf95f7ff347fad4cb55b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733843"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute 是用来将本地网络连接到 Microsoft 云的一种专用且可复原的方式。 可以从专用数据中心或公司网络访问许多 Microsoft 云服务，例如 Azure 和 Microsoft 365。 例如，你可能在旧金山有一家分公司，其 ExpressRoute 线路位于硅谷，在伦敦有另一家分公司，其 ExpressRoute 线路位于同一城市。 这两个分公司都可以与美国西部和英国南部的 Azure 资源高速连接。 但是，分支机构不能彼此连接并直接发送数据。 换句话说，10.0.1.0/24 可以将数据发送到 10.0.3.0/24 和 10.0.4.0/24 网络，但不能发送到 10.0.2.0/24 网络。

![关系图显示未与 Express Route Global Reach 链接在一起的线路。][1]

使用 **ExpressRoute Global Reach**，可以将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络。 在上面的示例中，通过添加 ExpressRoute Global Reach，你的旧金山分公司 (10.0.1.0/24) 便可以通过现有的 ExpressRoute 线路和 Microsoft 的全球网络直接与你的伦敦分公司 (10.0.2.0/24) 交换数据。 

![关系图显示与 Express Route Global Reach 链接在一起的线路。][2]

## <a name="use-case"></a>用例
ExpressRoute Global Reach 旨在补充服务提供商的 WAN 实施，并连接你在世界各地的分公司。 例如，如果你的服务提供商主要在美国运营并且已连接你在美国的所有分公司，但服务提供商不在日本和香港运营，则可以使用 ExpressRoute Global Reach 与本地服务提供商合作，Microsoft 将使用 ExpressRoute 和我们的全球网络将你在那里的分公司连接到美国的分公司。

![显示 Express Route Global Reach 用例的关系图。][3]

## <a name="availability"></a>可用性 
目前，以下地点支持 ExpressRoute Global Reach。 

> [!NOTE] 
> 若要在[不同的地缘政治区域](expressroute-locations-providers.md#locations)之间启用 ExpressRoute Global Reach，你的线路必须是高级 SKU。

* 澳大利亚
* Canada
* 丹麦
* 法国
* 德国
* 香港特别行政区
* 印度
* 爱尔兰
* 日本
* 韩国
* 荷兰
* 新西兰
* 挪威
* 新加坡
* 南非（仅约翰内斯堡）
* 瑞典
* 瑞士
* 台湾
* 英国
* 美国

## <a name="next-steps"></a>后续步骤
- 查看 [Global Reach 常见问题解答](expressroute-faqs.md#globalreach)。
- 了解如何[启用 Global Reach](expressroute-howto-set-global-reach.md)。
- 了解如何将 [ExpressRoute 线路链接到虚拟网络](expressroute-howto-linkvnet-arm.md)。

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "不带 Global Reach 时的示意图"
[2]: ./media/expressroute-global-reach/2.png "带有 Global Reach 时的示意图"
[3]: ./media/expressroute-global-reach/3.png "Global Reach 的用例"
