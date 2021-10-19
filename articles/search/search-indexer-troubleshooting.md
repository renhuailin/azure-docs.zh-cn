---
title: 索引器故障排除指导
titleSuffix: Azure Cognitive Search
description: 本文针对服务搜索未返回错误消息的情况提供索引器问题和解决方案指南。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 4453660cb58a1b976488d1cc9e240768637a85b6
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857087"
---
# <a name="indexer-troubleshooting-guidance-for-azure-cognitive-search"></a>Azure 认知搜索的索引器故障排除指导

有时，索引器会出现问题，并且没有错误可帮助进行诊断。 本文介绍出现意外索引器结果而提供的信息有限这类问题及潜在行解决方案。 如果有错误要调查，请改为参阅[排查常见索引器错误和警告问题](cognitive-search-common-errors-warnings.md)。

## <a name="connection-errors"></a>连接错误

> [!NOTE]
> 索引器对访问 Azure 网络安全机制保护的数据源和其他资源提供有限的支持。 目前，索引器只能通过相应的 IP 地址范围限制机制或 NSG 规则（如果适用）访问数据源。 在下面可以找到有关访问每个受支持数据源的详细信息。
>
> 可以通过 ping 搜索服务的完全限定的域名（例如 `<your-search-service-name>.search.windows.net`）来查找其 IP 地址。
>
> 可以使用[可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)或通过[服务标记发现 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) 找到 `AzureCognitiveSearch` [服务标记](../virtual-network/service-tags-overview.md#available-service-tags)的 IP 地址范围。 IP 地址范围每周更新一次。

### <a name="firewall-rules"></a>防火墙规则

Azure 存储、Cosmos DB 和 Azure SQL 提供可配置的防火墙。 防火墙启用后，没有具体的错误消息。 通常，防火墙错误是泛性的，类似于 `The remote server returned an error: (403) Forbidden` 或 `Credentials provided in the connection string are invalid or have expired`。

有 2 个选项可让索引器访问此类实例中的这些资源：

* 通过允许从 **所有网络** 进行访问（如果可行）来禁用防火墙。

* 或者，可以允许搜索服务的 IP 地址以及资源防火墙规则中 `AzureCognitiveSearch` [服务标记](../virtual-network/service-tags-overview.md#available-service-tags)的 IP 地址范围进行访问（IP 地址范围限制）。

在以下链接中可以找到有关对每种数据源类型配置 IP 地址范围限制的详细信息：

* [Azure 存储](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**限制**：如 Azure 存储的以上文档中所述，仅当搜索服务和存储帐户位于不同的区域时，IP 地址范围限制才起作用。

Azure Functions（可用作[自定义 Web API 技能](cognitive-search-custom-skill-web-api.md)）也支持 [IP 地址限制](../azure-functions/ip-addresses.md#ip-address-restrictions)。 要配置的 IP 地址列表是搜索服务的 IP 地址，以及 `AzureCognitiveSearch` 服务标记的 IP 地址范围。

有关连接到虚拟机的详细信息，请参阅[在 Azure VM 上配置与 SQL Server 的连接](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>配置网络安全组 (NSG) 规则

访问 SQL 托管实例中的数据或者将 Azure VM 用作[自定义 Web API 技能](cognitive-search-custom-skill-web-api.md)的 Web 服务 URI 时，客户无需考虑特定的 IP 地址。

在这种情况下，可将 Azure VM 或 SQL 托管实例配置为驻留在虚拟网络中。 然后可以配置一个网络安全组，来筛选可流入和流出虚拟网络子网与网络接口的网络流量类型。

可以在入站 [NSG](../virtual-network/manage-network-security-group.md#work-with-security-rules) 规则中直接使用 `AzureCognitiveSearch` 服务标记，而无需查找其 IP 地址范围。

[此文](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)提供了有关访问 SQL 托管实例中的数据的更多详细信息

## <a name="azure-sql-database-serverless-indexing-error-code-40613"></a>Azure SQL 数据库无服务器索引（错误代码 40613）

如果 SQL 数据库在[无服务器计算层](../azure-sql/database/serverless-tier-overview.md)上，请确保在索引器连接到该数据库时，该数据库正在运行（未暂停）。

如果该数据库已暂停，则首次从搜索服务登录会自动恢复该数据库，但同时会返回错误，指出该数据库不可用，错误代码为 40613。 在数据库运行后，请重试登录以建立连接。

## <a name="sharepoint-online-conditional-access-policies"></a>SharePoint Online 条件访问策略

创建 SharePoint Online 索引器时，需要完成一个必需的步骤，即在提供设备代码后登录到 Azure AD 应用。 如果你收到一条消息，说明 `"Your sign-in was successful but your admin requires the device requesting access to be managed"`，则表明索引器可能因[条件访问](../active-directory/conditional-access/overview.md)策略受到阻止，不能访问 SharePoint Online 文档库。

若要更新策略以允许索引器访问文档库，请执行以下步骤：

1. 打开 Azure 门户并搜索“Azure AD 条件访问”，然后在左侧菜单中选择“策略”。 如果你没有查看此页的权限，则需要查找有访问权限或可获取访问权限的用户。

1. 确定阻止 SharePoint Online 索引器访问文档库的具体策略。 可能会阻止索引器的策略包括用于在“用户和组”部分的索引器创建步骤中进行身份验证的用户帐户。 此策略还可能具有以下条件：
    * 限制 Windows 平台。
    * 限制“移动应用和桌面客户端”。
    * 将“设备状态”配置为“是”。

1. 确认存在阻止索引器的策略后，接下来需要为索引器创建豁免。 检索搜索服务 IP 地址。

    1. 获取搜索服务的完全限定的域名 (FQDN)。 它将如下所示：`<search-service-name>.search.windows.net`。 可以通过在 Azure 门户上查找搜索服务来查找 FQDN。

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

1. 获取你所在区域的索引器执行环境的 IP 地址范围。

    额外的 IP 地址用于来自索引器的[多租户执行环境](search-indexer-securing-resources.md#indexer-execution-environment)的请求。 可以从服务标记获取此 IP 地址范围。

    `AzureCognitiveSearch` 服务标记的 IP 地址范围可通过[发现 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) 或[可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)获取。

    对于本演练，假设搜索服务是 Azure 公有云，应下载 [Azure 公共 JSON 文件](https://www.microsoft.com/download/details.aspx?id=56519)。

   ![下载 JSON 文件](media\search-indexer-troubleshooting\service-tag.png "下载 JSON 文件")

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

1. 返回到 Azure 门户的“条件访问”页，从左侧菜单中选择“命名位置”，然后选择“+ IP 范围位置” 。 为新的命名位置提供一个名称，并为你在最后两个步骤中收集的搜索服务和索引器执行环境添加 IP 范围。
    * 对于搜索服务 IP 地址，你可能需要将“/32”添加到 IP 地址的末尾，因为它只接受有效的 IP 范围。
    * 请注意，对于索引器执行环境 IP 范围，只需添加搜索服务所在区域的 IP 范围。

1. 从策略中排除新的命名位置。 
    1. 在左侧菜单上选择“策略”。 
    1. 选择阻止索引器的策略。
    1. 选择“条件”。
    1. 选择“位置”。
    1. 选择“排除”，然后添加新的命名位置。
    1. 保存更改。

1. 请等待几分钟，以使策略更新并强制执行新的策略规则。

1. 再次尝试创建索引器
    1. 为你创建的数据源对象发送更新请求。
    1. 重新发送索引器创建请求。 使用新代码登录，然后发送另一个索引器创建请求。

## <a name="indexing-unsupported-document-types"></a>索引不受支持的文档类型

如果要从 Azure Blob 存储中索引内容，并且该容器包含[不受支持的内容类型](search-howto-indexing-azure-blob-storage.md#SupportedFormats)的 blob，索引器将跳过该文档。 在其他情况下，单个文档可能会出现问题。 

可以[设置配置选项](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)，以允许在单个文档出现问题时继续执行索引器处理。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

## <a name="missing-documents"></a>缺少文档

索引器从外部[数据源](/rest/api/searchservice/create-data-source)中提取文档或行，并创建搜索文档，然后搜索服务对其编制索引。 有时，数据源中存在的文档无法在搜索索引中出现。 存在以下任一原因时，就可能出现这种意外结果：

* 运行索引器后更新了文档。 如果索引器已在[计划](/rest/api/searchservice/create-indexer#indexer-schedule)之中，它最终会重新运行并选取该文档。
* 索引器在可引入文档之前已超时。 存在[最大处理时间限制](search-limits-quotas-capacity.md#indexer-limits)，在此之后将不会处理任何文档。 可以在门户中或通过调用[获取索引器状态 (REST API)](/rest/api/searchservice/get-indexer-status) 来检查索引器状态。
* [字段映射](/rest/api/searchservice/create-indexer#fieldmappings)或 [AI 扩充](./cognitive-search-concept-intro.md)已更改文档，其在搜索索引中的清晰度与预期的不同。
* [更改跟踪](/rest/api/searchservice/create-data-source#data-change-detection-policies)值错误或缺少先决条件。 如果高水印值是设置为将来时间的日期，则索引器将跳过任何日期小于此日期的文档。 可以使用[索引器状态](/rest/api/searchservice/get-indexer-status#indexer-execution-result)中的“initialTrackingState”和“finalTrackingState”字段来了解索引器的更改跟踪状态。 Azure SQL 和 MySQL 的索引器必须在源表的高水位标记列上有索引，否则索引器使用的查询可能会超时。 

> [!TIP]
> 如果文档丢失，请检查正在使用的[查询](/rest/api/searchservice/search-documents)，确保其中未排除相关文档。 若要查询特定文档，请使用[查找文档 REST API](/rest/api/searchservice/lookup-document)。

## <a name="missing-content-from-blob-storage"></a>Blob 存储中缺少内容

Blob 索引器可[查找并提取容器中 Blob 的文本](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs)。 提取文本时出现的一些问题包括：

* 文档仅包含扫描的图像。 包含扫描图像 (JPG) 之类的非文本内容的 PDF Blob 不会在标准 Blob 索引管道中生成结果。 如果图像内容包含文本元素，则可通过[认知搜索](cognitive-search-concept-image-scenarios.md)来查找并提取文本。

* Blob 索引器配置为仅索引元数据。 若要提取内容，必须将 Blob 索引器配置为[提取内容和元数据](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="missing-content-from-cosmos-db"></a>Cosmos DB 中缺少内容

Azure 认知搜索对 Cosmos DB 索引存在隐式依赖。 如果在 Cosmos DB 中关闭自动索引，Azure 认知搜索会返回成功状态，但无法索引容器内容。 有关如何查看设置和启用索引功能的说明，请参阅[管理 Azure Cosmos DB 中的索引编制](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal)。

## <a name="see-also"></a>另请参阅

* [排查常见索引器错误和警告问题](cognitive-search-common-errors-warnings.md)
* [监视基于索引器的索引](search-howto-monitor-indexers.md)
