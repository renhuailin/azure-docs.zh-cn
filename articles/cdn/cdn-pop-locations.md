---
title: 按区域列出的 Azure CDN 的 POP 位置 | Microsoft Docs
description: 本文列出了 Azure CDN 产品的 Azure CDN POP 位置（按区域排序）。
services: cdn
documentationcenter: ''
author: sohamnchatterjee
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2021
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: f51ca01988a090c4c61b821c16ec67f20d2c679f
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416994"
---
# <a name="azure-cdn-coverage-by-metro"></a>按 Metro 列出的 Azure CDN 覆盖范围 
> [!div class="op_single_selector"]
> * [按区域列出的 POP 位置](cdn-pop-locations.md)
> * [按缩写列出的 Verizon POP 位置](cdn-pop-abbreviations.md)
> * [按缩写列出的 Microsoft POP 位置](microsoft-pop-abbreviations.md)
>


本文列出了 Azure 内容分发网络 (CDN) 产品的包含接入点 (POP) 位置的最新 Metro（按区域排序）。 每个 Metro 可能包含多个 POP。 例如，Microsoft 的 Azure CDN 在 100 个大城市中拥有 116 个 POP。 

> [!IMPORTANT]
> 每种 Azure CDN 产品都采用不同的方式来构建其 CDN 基础结构，因此 Microsoft 不建议使用 POP 位置来决定使用哪种 Azure CDN 产品。 应考虑其功能和最终用户性能。 请在使用每种 Azure CDN 产品的情况下对性能进行测试来选择适合你的用户的产品。 
> 

## <a name="microsoft"></a>Microsoft

[!INCLUDE [front-door-edge-location](../../includes/front-door-edge-locations.md)]

## <a name="partners"></a>合作伙伴

> [!IMPORTANT]
> **来自 Akamai 的 Azure CDN** 的 POP 城市位置不单独公开。  
> 

| 区域 | Verizon | Akamai |
|--|--|--|
| 北美 | 墨西哥瓜达拉哈拉<br />墨西哥墨西哥市<br />墨西哥普埃布拉<br />墨西哥克雷塔罗市<br />美国乔治亚州亚特兰大<br />美国马萨诸塞州波士顿<br />美国伊利诺斯州芝加哥<br />美国德克萨斯州达拉斯市<br />美国科罗拉多州丹佛<br />美国密歇根州底特律<br />美国加利福尼亚州洛杉矶<br />美国弗罗里达州迈阿密<br />美国纽约州纽约市<br />美国宾夕法尼亚州费城<br />美国加州圣何塞<br />美国华盛顿州西雅图<br />美国华盛顿特区 <br /> 美国维吉尼亚州阿什本 <br /> 美国亚利桑那州菲尼克斯 | Canada<br />墨西哥<br />USA |
| 南美洲 | 阿根廷布宜诺斯艾利斯<br />巴西里约热内卢<br />巴西圣保罗<br />智利瓦尔帕莱索<br />哥伦比亚波哥大<br />哥伦比亚巴兰基亚<br />哥伦比亚麦德林<br />厄瓜多尔基多<br />秘鲁利马 | 阿根廷<br />巴西<br />智利<br />哥伦比亚<br />厄瓜多尔<br />秘鲁<br />乌拉圭 |
| 欧洲 | 奥地利维也纳<br />丹麦哥本哈根<br />赫尔辛基，芬兰<br />法国马赛<br />法国巴黎<br />德国法兰克福<br />意大利米兰<br />拉脱维亚里加<br />荷兰阿姆斯特丹<br />波兰华沙<br />西班牙马德里<br />瑞典斯德哥尔摩<br />英国伦敦 <br /> 英国曼彻斯特 | 奥地利<br />保加利亚<br />丹麦<br />芬兰<br />法国<br />德国<br />希腊<br />爱尔兰<br />意大利<br />荷兰<br />波兰<br />俄罗斯<br />西班牙<br />瑞典<br />瑞士<br />United Kingdom |
| 非洲 | 南非约翰内斯堡 <br/> 肯尼亚奈洛比 | 南非 |
| 中东 | 阿曼马斯喀特<br />阿拉伯联合酋长国富查伊拉 | 卡塔尔<br />阿拉伯联合酋长国 |
| 印度 | 印度班加罗尔<br />印度钦奈<br />印度孟买<br />印度新德里<br /> | 印度 |
| 亚洲 | 香港特别行政区<br />印度尼西亚雅加达<br />日本大阪<br />日本东京<br />新加坡<br />台湾高雄<br />台湾台北 <br />菲律宾马尼拉 | 香港特别行政区<br />印度尼西亚<br />以色列<br />日本<br />澳门特别行政区<br />马来西亚<br />菲律宾<br />新加坡<br />韩国<br />中国台湾<br />泰国<br />土耳其<br />越南 |
| 澳大利亚和新西兰 | 澳大利亚墨尔本<br />澳大利亚悉尼<br />新西兰奥克兰 | 澳大利亚<br />新西兰 |

## <a name="next-steps"></a>后续步骤

* 若要获取最新的 IP 地址允许列表，请参阅 [Azure CDN 边缘节点 API](/rest/api/cdn/cdn/edgenodes)。
