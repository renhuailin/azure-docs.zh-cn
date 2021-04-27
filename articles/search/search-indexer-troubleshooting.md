---
title: 排查常见的搜索索引器问题
titleSuffix: Azure Cognitive Search
description: 修复 Azure 认知搜索中索引器的错误和常见问题，包括数据源连接、防火墙和丢失的文档。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740047"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>排查 Azure 认知搜索中的常见索引器问题

在将数据索引到 Azure 认知搜索中时，索引器可能会遇到许多问题。 故障的主要类别包括：

* [连接到数据源或其他资源](#connection-errors)
* [文档处理](#document-processing-errors)
* [目标为索引的文档引入](#index-errors)

## <a name="connection-errors"></a>连接错误

> [!NOTE]
> 索引器对访问 Azure 网络安全机制保护的数据源和其他资源提供有限的支持。 目前，索引器只能通过相应的 IP 地址范围限制机制或 NSG 规则（如果适用）访问数据源。 在下面可以找到有关访问每个受支持数据源的详细信息。
>
> 可以通过 ping 搜索服务的完全限定的域名（例如 `<your-search-service-name>.search.windows.net`）来查找其 IP 地址。
>
> 可以使用[可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)或通过[服务标记发现 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 找到 `AzureCognitiveSearch` [服务标记](../virtual-network/service-tags-overview.md#available-service-tags)的 IP 地址范围。 IP 地址范围每周更新一次。

### <a name="configure-firewall-rules"></a>配置防火墙规则

Azure 存储、CosmosDB 和 Azure SQL 提供可配置的防火墙。 防火墙启用后，没有具体的错误消息。 通常，防火墙错误是泛性的，类似于 `The remote server returned an error: (403) Forbidden` 或 `Credentials provided in the connection string are invalid or have expired`。

有 2 个选项可让索引器访问此类实例中的这些资源：

* 通过允许从 **所有网络** 进行访问（如果可行）来禁用防火墙。
* 或者，可以允许搜索服务的 IP 地址以及资源防火墙规则中 `AzureCognitiveSearch` [服务标记](../virtual-network/service-tags-overview.md#available-service-tags)的 IP 地址范围进行访问（IP 地址范围限制）。

在以下链接中可以找到有关对每种数据源类型配置 IP 地址范围限制的详细信息：

* [Azure 存储](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**限制**：如 Azure 存储的以上文档中所述，仅当搜索服务和存储帐户位于不同的区域时，IP 地址范围限制才起作用。

Azure Functions（可用作[自定义 Web API 技能](cognitive-search-custom-skill-web-api.md)）也支持 [IP 地址限制](../azure-functions/ip-addresses.md#ip-address-restrictions)。 要配置的 IP 地址列表是搜索服务的 IP 地址，以及 `AzureCognitiveSearch` 服务标记的 IP 地址范围。

[此文](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)提供了有关访问 Azure VM 上 SQL 服务器中的数据的详细信息

### <a name="configure-network-security-group-nsg-rules"></a>配置网络安全组 (NSG) 规则

访问 SQL 托管实例中的数据或者将 Azure VM 用作[自定义 Web API 技能](cognitive-search-custom-skill-web-api.md)的 Web 服务 URI 时，客户无需考虑特定的 IP 地址。

在这种情况下，可将 Azure VM 或 SQL 托管实例配置为驻留在虚拟网络中。 然后可以配置一个网络安全组，来筛选可流入和流出虚拟网络子网与网络接口的网络流量类型。

可以在入站 [NSG](../virtual-network/manage-network-security-group.md#work-with-security-rules) 规则中直接使用 `AzureCognitiveSearch` 服务标记，而无需查找其 IP 地址范围。

[此文](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)提供了有关访问 SQL 托管实例中的数据的更多详细信息

### <a name="cosmosdb-indexing-isnt-enabled"></a>未启用 CosmosDB“索引编制”

Azure 认知搜索对 Cosmos DB 索引存在隐式依赖。 如果在 Cosmos DB 中关闭自动索引，Azure 认知搜索会返回成功状态，但无法索引容器内容。 有关如何查看设置和启用索引功能的说明，请参阅[管理 Azure Cosmos DB 中的索引编制](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal)。

### <a name="sharepoint-online-conditional-access-policies"></a>SharePoint Online 条件访问策略

创建 SharePoint Online 索引器时，需要完成一个必需的步骤，即在提供设备代码后登录到 AAD 应用。 如果你收到一条消息，指明“登录已成功，但管理员要求请求访问的设备必须由以下人员管理”，说明索引器可能由于[条件访问](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview)策略而被阻止访问 SharePoint Online 文档库。

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

    `AzureCognitiveSearch` 服务标记的 IP 地址范围可通过[发现 API（预览版）](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview)或[可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)获取。

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

1. 返回到 Azure 门户的“条件访问”页，从左侧菜单中选择“命名位置”，然后选择“+ IP 范围位置”。 为新的命名位置提供一个名称，并为你在最后两个步骤中收集的搜索服务和索引器执行环境添加 IP 范围。
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
    1. 重新发送索引器创建请求。 使用新代码登录，并在成功登录后发送另一个索引器创建请求。

## <a name="document-processing-errors"></a>文档处理错误

### <a name="unprocessable-or-unsupported-documents"></a>文档无法处理或不受支持

[显式支持可记录格式的 Blob 索引器文档](search-howto-indexing-azure-blob-storage.md#SupportedFormats)。 有时候，Blob 存储容器包含不受支持的文档。 而另一些时候，可能存在有问题的文档。 可以通过[更改配置选项](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)来避免停止这些文档上的索引器：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>缺少文档内容

Blob 索引器可[查找并提取容器中 Blob 的文本](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs)。 提取文本时出现的一些问题包括：

* 文档仅包含扫描的图像。 包含扫描图像 (JPG) 之类的非文本内容的 PDF Blob 不会在标准 Blob 索引管道中生成结果。 如果图像内容包含文本元素，则可通过[认知搜索](cognitive-search-concept-image-scenarios.md)来查找并提取文本。
* Blob 索引器配置为仅索引元数据。 若要提取内容，必须将 Blob 索引器配置为[提取内容和元数据](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>索引错误

### <a name="missing-documents"></a>缺少文档

索引器从[数据源](/rest/api/searchservice/create-data-source)查找文档。 有时候，索引中似乎会缺失数据源中本应进行索引的文档。 之所以发生这些错误，有多种常见原因：

* 文档尚未进行索引。 查看门户中是否有成功的索引器运行。
* 检查[更改跟踪](/rest/api/searchservice/create-data-source#data-change-detection-policies)值。 如果高水印值是设置为将来时间的日期，则索引器将跳过任何日期小于此日期的文档。 可以使用[索引器状态](/rest/api/searchservice/get-indexer-status#indexer-execution-result)中的“initialTrackingState”和“finalTrackingState”字段来了解索引器的更改跟踪状态。
* 文档在索引器运行之后已更新。 如果索引器已在[计划](/rest/api/searchservice/create-indexer#indexer-schedule)之中，它最终会重新运行并选取该文档。
* 在数据源中指定的 [query](/rest/api/searchservice/create-data-source) 排除了该文档。 索引器不能索引不属于数据源的文档。
* [字段映射](/rest/api/searchservice/create-indexer#fieldmappings)或 [AI 扩充](./cognitive-search-concept-intro.md)已更改此文档，因此它看起来不同于预期。
* 使用[查找文档 API](/rest/api/searchservice/lookup-document) 来查找文档。