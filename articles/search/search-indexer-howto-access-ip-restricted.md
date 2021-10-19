---
title: 允许访问索引器 IP 范围
titleSuffix: Azure Cognitive Search
description: 配置 IP 防火墙规则以允许 Azure 认知搜索索引器访问数据。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: a91eddfba5c9e1973a938b0c58674cc528f11811
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858626"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-in-azure-cognitive-search"></a>在 Azure 认知搜索中配置 IP 防火墙规则以允许索引器连接

Azure 资源上的 IP 防火墙规则（例如存储帐户、Cosmos DB 帐户和 Azure SQL 服务器）仅允许源自特定 IP 范围的流量访问数据。

本文介绍如何通过 Azure 门户为存储帐户配置 IP 规则，以便 Azure 认知搜索索引器能够安全地访问数据。 虽然特定于 Azure 存储，但此方法也适用于使用 IP 防火墙规则保护对数据的访问的其他 Azure 资源。

> [!NOTE]
> 仅当存储帐户和搜索服务位于不同区域时，存储帐户的 IP 防火墙规则才有效。 如果安装程序不允许这样做，我们建议使用[受信任的服务异常选项](search-indexer-howto-access-trusted-service-exception.md)。

## <a name="get-the-ip-address-of-the-search-service"></a>获取搜索服务的 IP 地址

获取搜索服务的完全限定的域名 (FQDN)。 它将如下所示：`<search-service-name>.search.windows.net`。 可以通过在 Azure 门户上查找搜索服务来查找 FQDN。

   ![获取服务 FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "获取服务 FQDN")

通过对 FQDN 执行 `nslookup`（或 `ping`），可以获取搜索服务的 IP 地址。 在下面的示例中，将“150.0.0.1”添加到 Azure 存储防火墙的入站规则中。 更新防火墙设置之后，可能需要等待 15 分钟，之后搜索服务索引器才能访问 Azure 存储帐户。

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>获取“AzureCognitiveSearch”服务标记的 IP 地址范围

额外的 IP 地址用于来自索引器的[多租户执行环境](search-indexer-securing-resources.md#indexer-execution-environment)的请求。 可以从服务标记获取此 IP 地址范围。

可通过[发现 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) 或[可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)获取 `AzureCognitiveSearch` 服务标记的 IP 地址范围。

对于本演练，假设搜索服务是 Azure 公有云，应下载 [Azure 公共 JSON 文件](https://www.microsoft.com/download/details.aspx?id=56519)。

   ![下载 JSON 文件](media\search-indexer-howto-secure-access\service-tag.png "下载 JSON 文件")

在 JSON 文件中，假设搜索服务位于美国中西部，多租户索引器执行环境的 IP 地址列表如下。

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

对于 /32 IP 地址，请删除“/32”(52.253.133.74/32 -> 52.253.133.74)，其他可以逐字使用。

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>将 IP 地址范围添加到 IP 防火墙规则

向存储帐户的防火墙规则添加 IP 地址范围的最简单方法是通过 Azure 门户。 在门户中找到存储帐户，然后导航到“防火墙和虚拟网络”选项卡。

   ![防火墙和虚拟网络](media\search-indexer-howto-secure-access\storage-firewall.png "防火墙和虚拟网络")

在地址范围内添加之前获取的三个 IP 地址（1 个用于搜索服务 IP，2 个用于 `AzureCognitiveSearch` 服务标签），然后选择“保存”。

   ![防火墙 IP 规则](media\search-indexer-howto-secure-access\storage-firewall-ip.png "防火墙 IP 规则")

防火墙规则需要 5-10 分钟才能进行更新，之后索引器应能够访问存储帐户中的数据。

## <a name="next-steps"></a>后续步骤

- [配置 Azure 存储防火墙](../storage/common/storage-network-security.md)
- [配置 Cosmos DB 的 IP 防火墙](../cosmos-db/how-to-configure-firewall.md)
- [配置 Azure SQL Server 的 IP 防火墙](../azure-sql/database/firewall-configure.md)
