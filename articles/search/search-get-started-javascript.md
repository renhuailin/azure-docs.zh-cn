---
title: 快速入门：在 JavaScript 中创建搜索索引
titleSuffix: Azure Cognitive Search
description: 在此 JavaScript 快速入门中，了解如何使用 JavaScript 对 Azure 认知搜索创建索引、加载数据和运行查询
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 07/08/2021
ms.custom: devx-track-js
ms.openlocfilehash: 8b4de14a55c46b9be66e54d96d9285b336cdc638
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113551571"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>快速入门：使用 JavaScript SDK 创建 Azure 认知搜索索引
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [门户](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)


使用[适用于 Azure 认知搜索 的 Javascript/Typscript SDK](/javascript/api/overview/azure/search-documents-readme) 在 JavaScript 中创建一个 Node.js 应用程序用于创建、加载和查询搜索索引。

本文演示如何逐步创建应用程序。 或者，可以[下载源代码和数据](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11)，并从命令行运行应用程序。

## <a name="prerequisites"></a>先决条件

开始之前，需具备以下工具和服务：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。

+ Azure 认知搜索服务。 [创建服务](search-create-service-portal.md)或[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。 

+ [Node.js](https://nodejs.org) 和 [NPM](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) 或其他 IDE


## <a name="set-up-your-project"></a>设置项目

首先获取搜索服务的终结点和密钥。 然后使用 NPM 创建一个新项目，如下所述。

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>复制密钥和终结点

对服务的调用要求每个请求都有一个 URL 终结点和一个访问密钥。 第一步，找到要添加到项目中的 API 密钥和 URL。 在稍后的步骤中创建客户端时，将指定这两个值。

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.windows.net`。

2. 在“设置” > “密钥”中，获取管理员密钥以获得针对服务的完全权限，需要该密钥才可创建或删除对象 。 有两个可互换的主要密钥和辅助密钥。 可以使用其中任意一个。

   ![获取 HTTP 终结点和访问密钥](media/search-get-started-rest/get-url-key.png "获取 HTTP 终结点和访问密钥")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

### <a name="create-a-new-npm-project"></a>创建新的 NPM 项目

首先打开 VS Code 及其[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)，或者打开其他终端，例如 Node.js 命令提示符。

1. 创建一个开发目录并将其命名为 `quickstart`：

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. 使用 NPM 初始化一个空项目，方法是运行 

    ```cmd
    npm init
    ```
     接受默认值，但“许可证”除外，其值应设置为“MIT”。 

3. 安装 `@azure/search-documents`，这是[适用于 Azure 认知搜索的 Javascript/Typscript SDK](/javascript/api/overview/azure/search-documents-readme)。

    ```cmd
    npm install @azure/search-documents
    ```

4. 安装 `dotenv`，它用于导入环境变量，例如我们的服务名称和 API 密钥。
    ```cmd
    npm install dotenv
    ```

5. 检查你的 package.json 文件是否与以下 json 类似，确认已配置项目及其依赖项：

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.2.0",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. 创建一个 .env 文件用来保存搜索服务参数：

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

请将 `<search-service-name>` 值替换为搜索服务的名称。 将 `<search-admin-key>` 替换为前面记下的密钥值。 

### <a name="create-indexjs-file"></a>创建 index.js 文件

接下来，创建一个index.js 文件，它是托管代码的主要文件。

在此文件的顶部导入 `@azure/search-documents` 库：

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

接下来，需要 `dotenv` 包来从 .env 文件中读取参数，如下所示：

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

在导入和环境变量设置完成后，就可定义 main 函数。

SDK 中的大部分功能都是异步的，因此我们将 main 函数设置为 `async`。 我们还在 main 函数下面包含一个 `main().catch()` 来捕获和记录遇到的所有错误：

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

完成后，即可创建索引。

## <a name="1---create-index"></a>1 - 创建索引 

创建文件 **hotels_quickstart_index.json**。  此文件定义 Azure 认知搜索如何处理要在下一步骤中加载的文档。 每个字段由 `name` 标识，采用指定的 `type`。 每个字段还包含一系列索引属性，这些属性指定 Azure 认知搜索是否可以根据字段进行搜索、筛选、排序和分面。 大多数字段采用简单数据类型，但有些字段（例如 `AddressType`）采用复杂类型，可让你在索引中创建丰富的数据结构。  可详细了解[支持的数据类型](/rest/api/searchservice/supported-data-types)，以及[创建索引 (REST)](/rest/api/searchservice/create-index) 中所述的索引属性。 

将以下内容添加到 **hotels_quickstart_index.json** 或 [下载文件](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json)。 

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
            "analyzerName": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzerName": "fr.lucene"
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

建立索引定义后，我们需要在 index.js 顶部导入 hotels_quickstart_index.json，使 main 函数能够访问索引定义 。

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

然后在 main 函数中，我们创建一个 `SearchIndexClient` 用来创建和管理 Azure 认知搜索索引。 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

接下来，我们要删除该索引（如果它已存在）。 这是测试/演示代码的常见做法。

为此，我们要定义一个简单函数，它会尝试删除索引。

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

为了运行该函数，我们将从索引定义中提取索引名称，并将 `indexName` 连同 `indexClient` 传递给 `deleteIndexIfExists()` 函数。

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

之后，我们就可用 `createIndex()` 方法创建索引了。

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>运行示例

此时就可运行示例了。 使用终端窗口运行以下命令：

```cmd
node index.js
```

如果[已下载源代码](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11)，但尚未安装所需的包，请先运行 `npm install`。

你应会看到一系列消息，其中描述了程序正在执行的操作。 

在 Azure 门户中打开搜索服务的“概述”。 选择“索引”选项卡。会看到下面这样的内容：

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Azure 门户、搜索服务概述、“索引”选项卡的屏幕截图" border="false":::

在下一步骤中，你要向索引添加数据。 

## <a name="2---load-documents"></a>2 - 加载文档 


在 Azure 认知搜索中，文档这一数据结构既是索引输入，也是查询输出。 可将此类数据推送到索引，或者使用[索引器](search-indexer-overview.md)。 在本例中，我们将以编程方式将文档推送到索引。

文档输入可以是数据库中的行、Blob 存储中的 Blob，或磁盘上的 JSON 文档（在本示例中为 JSON 文档）。 可以下载 [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json)，或创建包含以下内容的 **hotels.json** 文件：

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

与我们使用 indexDefinition 执行的操作类似，我们还需要在 index.js 顶部导入 `hotels.json`，以便可在我们的 main 函数中访问数据。

```javascript
const hotelData = require('./hotels.json');
```


为了将数据编入搜索索引中，现在需要创建 `SearchClient`。 虽然 `SearchIndexClient` 用于创建和管理索引，但 `SearchClient` 用于上传文档和查询索引。

创建 `SearchClient` 的方法有两种。 第一种方法是从头开始创建 `SearchClient`：

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

或者，你可使用 `SearchIndexClient` 的 `getSearchClient()` 方法来创建 `SearchClient`：

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

现在已定义了客户端，需将文档上传到搜索索引。 这样的话，我们使用 `mergeOrUploadDocuments()` 方法。该方法将上传文档；如果已存在具有相同密钥的文档，则它将这些文档与现有文档合并。

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

使用 `node index.js` 再次运行程序。 应会看到与步骤 1 中显示的消息略有不同的一系列消息。 这一次，索引确实存在，而且你会看到一条消息，它显示在应用创建新索引并向其发布数据之前删除此索引。 

在下一步中运行查询之前，请定义一个函数，使程序等待一秒钟。 仅出于测试/演示目的这样做，目的是确保索引完成且文档可在索引中用于查询。

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

若要让程序等待一秒钟，请调用 `sleep` 函数，如下所示：

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 - 搜索索引

创建索引并上传文档后，便可将查询发送到索引。 在本部分中，我们将向搜索索引发送 5 个不同的查询，以演示可供你使用的不同查询功能部分。

在 `sendQueries()` 函数中编写查询，我们将在 main 函数中调用此函数，如下所示：

```javascript
await sendQueries(searchClient);
```

使用 `searchClient` 的 `search()` 方法发送查询。 第一个参数是搜索文本，第二个参数是任何附加搜索选项。

第一个查询会搜索 `*`，这等效于搜索所有内容并选择索引中的三个字段。 最佳做法是通过 `select` 仅选择你需要的字段，因为回发不必要的数据可能会增加查询的延迟时间。

此查询的 `searchOptions` 还将 `includeTotalCount` 设置为 `true`，这将返回找到的匹配结果数。

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

还应将下述其余查询添加到 `sendQueries()` 函数。 为了方便阅读，此处将它们分开。

在下一个查询中，我们指定搜索词 `"wifi"`，还包括一个筛选器，以仅返回状态等于 `'FL'` 的结果。 还会按酒店的 `Rating` 对结果进行排序。

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

接下来，使用 `searchFields` 参数将搜索限制为单个可搜索字段。 如果你知道自己只对某些字段中的匹配感兴趣，则很适合使用该选项来提高查询的效率。 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

查询中包含的另一个常见选项是 `facets`。 通过 facet，可在 UI 上构建筛选器，使用户能够轻松地了解可筛选出的值。

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

最终查询使用 `searchClient` 的 `getDocument()` 方法。 这样，你就可通过文档的密钥有效地检索文档。 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>运行示例

使用 `node index.js` 运行程序。 现在，除上述步骤以外，还会发送查询并将结果写入控制台。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

在此 JavaScript 快速入门中，你完成了一系列任务：创建索引、使用文档加载索引，以及运行查询。 若要继续学习，请尝试将以下教程和示例作为“跳板”来尝试建议器（预先输入或自动完成查询）、筛选器和分面导航。 

+ [教程：向 Web 应用添加搜索](tutorial-csharp-overview.md)

+ [示例：构建 Azure 认知搜索的 React 前端](https://github.com/dereklegenzoff/azure-search-react-template)
