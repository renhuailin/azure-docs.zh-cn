---
title: include 文件
description: include 文件
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: include
ms.custom: include file
ms.date: 11/17/2020
ms.openlocfilehash: c84a7291e342a1acc465732cfbc350c571bba74d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711285"
---
本文使用适用于 Azure 认知搜索 REST API 的 Visual Studio Code 扩展（预览版）。

> [!IMPORTANT] 
> 此功能目前处于公开预览状态。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

## <a name="prerequisites"></a>先决条件

本快速入门需要以下服务和工具。 

+ [Visual Studio Code](https://code.visualstudio.com/download)

+ [适用于 Visual Studio Code（预览版）的 Azure 认知搜索](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)

+ [创建 Azure 认知搜索服务](../search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。 

## <a name="copy-a-key-and-url"></a>复制密钥和 URL

REST 调用需要在每个请求中使用服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 认知搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥 。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](../media/search-get-started-rest/get-url-key.png "获取 HTTP 终结点和访问密钥")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="install-the-extension"></a>安装扩展

首先打开 [VS Code](https://code.visualstudio.com)。 选择活动栏上的“扩展”选项卡，然后搜索 Azure 认知搜索。 在搜索结果中找到扩展，然后选择“安装”。

![VS Code 扩展窗格](../media/search-get-started-rest/download-extension.png "下载 VS Code 扩展")

或者，可以在 Web 浏览器中从 VS Code 市场安装 [Azure 认知搜索扩展](https://aka.ms/vscode-search)。

如果尚未安装，活动栏上应会显示新的 Azure 选项卡。

![VS Code Azure 窗格](../media/search-get-started-rest/azure-pane.png "VS Code 中的 Azure 窗格")

## <a name="connect-to-your-subscription"></a>连接到订阅

选择“登录到 Azure...”然后登录到 Azure 帐户。

应会显示你的订阅。 选择订阅以查看订阅中的搜索服务的列表。

![VS Code Azure 订阅](../media/search-get-started-rest/subscriptions.png "VS Code 中的订阅")

若要限制显示的订阅，请打开命令面板（Ctrl+Shift+P 或 Cmd+Shift+P），搜索 Azure 或选择订阅 。 还有一些命令可用于登录和注销 Azure 帐户。

展开搜索服务时，你将看到每个认知搜索资源的树项：索引、数据源、索引器、技能集和同义词映射。

![VS Code Azure 搜索树](../media/search-get-started-rest/search-tree.png "VS Code Azure 搜索树")

可以扩展这些树项以显示搜索服务中的资源

## <a name="1---create-an-index"></a>1 - 创建索引

若要开始使用 Azure 认知搜索，首先需要创建搜索索引。 这是使用[创建索引 REST API](/rest/api/searchservice/create-index) 来完成的。 

使用 VS Code 扩展时，只需要考虑请求的正文。 在本快速入门中，我们提供了一个示例索引定义和相应的文档。

### <a name="index-definition"></a>索引定义

以下索引定义是虚构酒店的示例架构。

`fields` 集合定义搜索索引中文档的结构。 每个字段都有一个数据类型和多个其他属性，这些属性决定了如何使用该字段。

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

若要创建新索引，请右键单击“索引”然后选择“创建新索引” 。 将弹出一个名称类似于 `indexes-new-28c972f661.azsindex` 的编辑器。 

将上面的索引定义粘贴到窗口中。 如果想要更新索引，请保存文件并在出现提示时选择“上传”。 这将创建索引，并且该索引将在树状视图中可用。

![创建索引的 Gif](../media/search-get-started-rest/create-index.gif)

如果索引定义有问题，会弹出错误消息，说明该错误。

![创建索引错误消息](../media/search-get-started-rest/create-index-error.png)

如果发生这种情况，请修复问题并重新保存该文件。

## <a name="2---load-documents"></a>2 - 加载文档

创建索引和填充索引是分开的步骤。 在 Azure 认知搜索中，索引包含所有可搜索的数据。 在此场景中，数据以 JSON 文档的形式提供。 本任务将使用[添加、更新或删除文档 REST API](/rest/api/searchservice/addupdate-or-delete-documents)。 

若要在 VS Code 中添加新文档：

1. 展开创建的 `hotels-quickstart` 索引。 右键单击“文档”并选择“创建新文档” 。

    ![创建文档](../media/search-get-started-rest/create-document.png)

2. 这会打开一个已推断出索引架构的 JSON 编辑器。

    ![创建文档 JSON](../media/search-get-started-rest/create-document-2.png)

3. 粘贴下面的 JSON，并保存该文件。 将打开一个提示，要求确认更改。 选择“上传”以保存更改。

    ```json
    {
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
        } 
    }
    ```

4. 为其余三个文档重复此过程

    文档 2：
    ```json
    {
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
        } 
    }
    ```

    文档 3：
    ```json
    {
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        } 
    }
    ```

    文档 4：
    ```json
    {
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
        }
    }
    ```

此时，你应该在“文档”部分看到所有四个文档。

![上载所有文档后的状态](../media/search-get-started-rest/create-document-finish.png)

## <a name="3---search-an-index"></a>3 - 搜索索引

现在，索引和文档集已加载，可以使用[搜索文档 REST API](/rest/api/searchservice/search-documents) 针对它们发出查询了。

若要在 VS Code 执行此操作：

1. 右键单击要搜索的索引，然后选择“搜索索引”。 这会打开一个名称类似于 `sandbox-b946dcda48.azs` 的编辑器。

    ![扩展的搜索视图](../media/search-get-started-rest/search-vscode.png)

2. 会自动填充一个简单查询。 按“Ctrl+Alt+R”或“Cmd+Alt+R”以提交查询 。 你将在左侧的窗口中看到弹出的结果。

    ![扩展中的搜索结果](../media/search-get-started-rest/search-results.png)


### <a name="example-queries"></a>查询示例

尝试其他查询示例来了解语法。 下面有另外四个可尝试的查询。 可以将多个查询添加到同一个编辑器。 按“Ctrl+Alt+R”或“Cmd+Alt+R”时，光标所在的行决定了要提交的查询 。

![并行查询和结果](../media/search-get-started-rest/all-searches.png)

在第一个查询中，只搜索特定字段 `boutique` 和 `select`。 最佳做法是通过 `select` 仅选择你需要的字段，因为回发不必要的数据可能会增加查询的延迟时间。 该查询还将 `$count=true` 设置为搜索结果的返回总数。

```
// Query example 1 - Search `boutique` with select and return count
search=boutique&$count=true&$select=HotelId,HotelName,Rating,Category
```

在下一个查询中，我们指定搜索词 `wifi`，还包括一个筛选器，以仅返回状态等于 `'FL'` 的结果。 还会按酒店的 `Rating` 对结果进行排序。

```
// Query example 2 - Search with filter, orderBy, select, and count
search=wifi&$filter=Address/StateProvince eq 'FL'&$select=HotelId,HotelName,Rating&$orderby=Rating desc
```

接下来，使用 `searchFields` 参数将搜索限制为单个可搜索字段。 如果你知道自己只对某些字段中的匹配感兴趣，则很适合使用该选项来提高查询的效率。

```
// Query example 3 - Limit searchFields
search=submlime cliff&$select=HotelId,HotelName,Rating&searchFields=HotelName
```

查询中包含的另一个常见选项是 `facets`。 通过 facet，可在 UI 上构建筛选器，使用户能够轻松地了解可筛选出的值。

```
// Query example 4 - Take the top two results, and show only HotelName and Category in the results
search=*&$select=HotelId,HotelName,Rating&searchFields=HotelName&facet=Category
```

## <a name="open-index-in-the-portal"></a>在门户中打开索引

若要在门户中查看搜索服务，请右键单击搜索服务的名称，然后选择“在门户中打开”。 这会将你转到 Azure 门户中的搜索服务。