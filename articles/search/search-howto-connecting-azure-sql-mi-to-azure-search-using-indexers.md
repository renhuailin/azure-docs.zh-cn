---
title: 索引器与 SQL 托管实例的连接
titleSuffix: Azure Cognitive Search
description: 启用公共终结点以允许从 Azure 认知搜索上的索引器连接到 SQL 托管实例。
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 12ee369bfd69e82a73ccaa766190cedf7910a7a0
ms.sourcegitcommit: 3b371af4c30fce82854b3d45fd8b8e674bbe7517
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112984887"
---
# <a name="indexer-connections-to-azure-sql-managed-instance-through-a-public-endpoint"></a>索引器通过公共终结点连接到 Azure SQL 托管实例

如果设置的 Azure 认知搜索索引器连接到 Azure SQL 托管实例，则需要在托管实例上启用公共终结点作为先决条件。 索引器通过公共终结点连接到托管实例。

本文提供了一些基本步骤，其中包括收集数据源配置所需的信息。 有关详细信息和方法，请参阅[在 Azure SQL 托管实例中配置公共终结点](../azure-sql/managed-instance/public-endpoint-configure.md)。

## <a name="enable-a-public-endpoint"></a>启用公共终结点

对于新的 SQL 托管实例，请创建资源并选择“启用公共终结点”选项。

   ![启用公共终结点](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "启用公共终结点")

或者，如果该实例已存在，可以在现有 SQL 托管实例上沿以下路径启用公共终结点：“安全性” > “虚拟网络” > “公共终结点” > “启用”   。

   ![使用托管实例 VNET 启用公共终结点](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "启用公共终结点")

## <a name="verify-nsg-rules"></a>验证 NSG 规则

检查网络安全组是否具有允许来自 Azure 服务的连接的正确的 **入站安全规则**。

   ![NSG 入站安全规则](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 入站安全规则")

## <a name="restrict-inbound-access-to-the-endpoint"></a>限制对终结点的入站访问

可以将当前规则 (`public_endpoint_inbound`) 替换为以下两个规则来限制对公共终结点的入站访问：

* 允许从 `AzureCognitiveSearch` [服务标记](../virtual-network/service-tags-overview.md#available-service-tags) ("SOURCE" = `AzureCognitiveSearch`, "NAME" = `cognitive_search_inbound`) 进行的入站访问

* 允许来自搜索服务的 IP 地址的入站访问，该 IP 地址可通过 ping 其完全限定的域名（例如 `<your-search-service-name>.search.windows.net`）来获取。 ("SOURCE" = `IP address`, "NAME" = `search_service_inbound`)

对于每项规则，请将“PORT”设置为 `3342`，“PROTOCOL”设置为 `TCP`，“DESTINATION”设置为 `Any`，“ACTION”设置为 `Allow`。

## <a name="get-public-endpoint-connection-string"></a>获取公共终结点连接字符串

复制要在搜索索引器的数据源连接中使用的连接字符串。 请确保复制公共终结点的连接字符串（端口 3342，而不是端口 1433）。

   ![公共终结点连接字符串](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "公共终结点连接字符串")

## <a name="next-steps"></a>后续步骤

完成配置后，现在可以将 [SQL 托管实例指定为索引器数据源](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。