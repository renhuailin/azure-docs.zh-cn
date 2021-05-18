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
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96938828"
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
|每个帐户的 API 调用数|对于“4 个容量单位”这一平台大小，每月 1,000 万个 API。 <br>对于“16 个容量单位”这一平台大小，每月 4,000 万个 API。 |对于“4 个容量单位”这一平台大小，每月 1,000 万个 API。 <br>对于“16 个容量单位”这一平台大小，每月 4,000 万个 API。|
|每个帐户的存储大小|10 GB，适用于“4 个容量单位”平台大小。 <br>40 GB，适用于“16 个容量单位”平台大小。 |10 GB，适用于“4 个容量单位”平台大小。 <br> 40 GB，适用于“16 个容量单位”平台大小。 |
|每个帐户的资产大小|1 亿个实物资产 |联系支持人员|
|目录中一个资产的最大大小|2 MB|2 MB|
|资产名称和分类名称的最大长度|4 KB|4 KB|
|资产属性名称和值的最大长度|32 KB|32 KB|
|分类属性名称和值的最大长度|32 KB|32 KB|
|每个帐户的术语表术语最大数|100K|100K|
 
*自承载集成运行时方案超出了上表中定义的限制的范围。 
 
## <a name="next-steps"></a>后续步骤
 
> [!div class="nextstepaction"]
>[教程：使用 Azure Purview 扫描数据](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[教程：在主页中导航并搜索资产](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[教程：浏览资产并查看其世系](tutorial-browse-and-view-lineage.md)
