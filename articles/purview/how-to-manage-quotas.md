---
title: 管理资源和配额
titleSuffix: Azure Purview
description: 了解 Azure Purview 资源的配额和限制，学习如何请求调高配额。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 422c096533e8b470446a29b6d45f9127d963f913
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396529"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>管理和增加 Azure Purview 资源的配额
 
Azure Purview 是供数据用户使用的云服务。 使用 Azure Purview 集中管理跨云和本地环境的数据资产的数据管理。 借助该服务，业务分析人员可使用有意义的业务术语搜索相关数据。 若要将限制提高到订阅的最高限制，请联系支持部门。
 
## <a name="azure-purview-limits"></a>Azure Purview 限制
 
|**资源**|  **默认限制**  |**最大限制**|
|---|---|---|
|每个租户在每个区域的 Purview 帐户数（合并所有订阅）|3|联系支持人员|
|每个帐户可用于扫描的 vCore 的数目*|160|160|
|每个帐户在给定时间点的并发扫描数。 此限制基于所扫描的数据源类型*|5 | 10 |
|扫描可运行的最长时间|7 天|7 天|
|[数据映射容量单位 (CU)](concept-elastic-data-map.md) |1 个 CU（25 个操作/秒的吞吐量和 2 GB 元数据存储） | 100 个 CU（若要获得更高的 CU，请联系支持人员）。|
|数据映射操作吞吐量 |每个容量单位 25 个操作/秒 | 如果是 100 个 CU，则为 2,500 个操作/秒（若要获得更高的吞吐量，请联系支持人员）| 
|数据映射存储 |每个容量单位 2 GB | 如果是 100 个 CU，则为 200 GB（若要获得更多存储，请联系支持人员） |
|数据映射弹性时段 | 1 - 8 个 CU（数据映射在弹性时段内可以根据吞吐量自动纵向扩展/缩减） | 若要获得更长的弹性时段，请联系支持人员 |
|每个帐户的资产大小|1 亿个实物资产 |联系支持人员|
|目录中一个资产的最大大小|2 MB|2 MB|
|资产名称和分类名称的最大长度|4 KB|4 KB|
|资产属性名称和值的最大长度|32 KB|32 KB|
|分类属性名称和值的最大长度|32 KB|32 KB|
|每个帐户的术语表术语最大数|100K|100K|
 
*自承载集成运行时方案超出了上表中定义的限制的范围。 
 
## <a name="next-steps"></a>后续步骤
 
> [!div class="nextstepaction"]
>[概念：Azure Purview 中的弹性数据映射](concept-elastic-data-map.md)

> [!div class="nextstepaction"]
>[教程：使用 Azure Purview 扫描数据](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[教程：在主页中导航并搜索资产](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[教程：浏览资产并查看其世系](tutorial-browse-and-view-lineage.md)
