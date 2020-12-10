---
title: 管理资源和配额
titleSuffix: Azure Purview
description: 了解有关 Azure 监控范围的资源配额和限制以及如何请求增加配额。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938828"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>利用 Azure 监控范围管理和提高资源配额
 
Azure 监控范围是供数据用户使用的云服务。 使用 Azure 监控范围跨数据中心集中管理跨云和本地环境的数据管理。 该服务使业务分析人员能够使用有意义的业务术语搜索相关数据。 若要将限制提高到订阅的最高限制，请联系支持部门。
 
## <a name="azure-purview-limits"></a>Azure Purview 限制
 
|**资源**|  **默认限制**  |**最大限制**|
|---|---|---|
|每个区域的监控范围帐户，每个租户 (合并所有订阅) |3|联系支持人员|
|Vcore 可用于扫描，每个帐户 *|160|160|
|并发扫描，在给定点按帐户。 此限制基于所扫描的数据源类型 *|5 | 10 |
|扫描可运行的最长时间|7 天|7 天|
|API 调用，每个帐户|4个容量单位平台大小的 10M Api/月。 <br>每月16个容量单元平台大小的 40M Api |4个容量单位平台大小的 10M Api/月。 <br>每月16个容量单元平台大小的 40M Api|
|存储，每个帐户|10 GB，适用于4个容量单位平台大小。 <br>40 GB，适用于16个容量单位平台大小 |10 GB，适用于4个容量单位平台大小。 <br> 40 GB，适用于16个容量单位平台大小 |
|每个帐户的资产大小|100M 实物资产 |联系支持人员|
|目录中资产的最大大小|2 MB|2 MB|
|资产名称和分类名称的最大长度|4 KB|4 KB|
|资产属性名称和值的最大长度|32 KB|32 KB|
|分类属性名称和值的最大长度|32 KB|32 KB|
|每个帐户的最大术语表术语数|100K|100K|
 
* 自承载集成运行时方案超出了上表中定义的限制的范围。 
 
## <a name="next-steps"></a>后续步骤
 
> [!div class="nextstepaction"]
>[教程：通过 Azure 监控范围扫描数据](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[教程：在主页上导航并搜索资产](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[教程：浏览资产并查看其沿袭](tutorial-browse-and-view-lineage.md)
