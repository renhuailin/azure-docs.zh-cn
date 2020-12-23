---
title: '在 .NET 中使用 Azure.Search.Documents (v11) '
titleSuffix: Azure Cognitive Search
description: '了解如何在 .NET 应用程序中使用 c # 和 Azure.Search.Documents (v11) 客户端库来创建和管理搜索对象。 代码片段演示了如何连接到服务、创建索引，以及如何查询。'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3ceead297ea726e256d806c08c22810b39296793
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917165"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>如何在 c # .NET 应用程序中使用 Azure.Search.Documents

本文介绍如何使用 c # 和 [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (版本 11) 客户端库来创建和管理搜索对象。

## <a name="about-version-11"></a>关于版本11

适用 [于 .net](/dotnet/api/overview/azure/search/client10)的 azure sdk 添加了一个来自 azure sdk 团队的新 [**Azure.Search.Documents**](/dotnet/api/overview/azure/search)客户端库，此库在功能上与 一些示例包括 [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) 密钥身份验证和 [System.Text.Js。JSON 序列化的序列化](/dotnet/api/system.text.json.serialization) 。

与以前的版本一样，你可以使用此库来执行以下操作：

+ 创建和管理搜索索引、数据源、索引器、技能集和同义词映射
+ 在索引中加载和管理搜索文档
+ 执行查询，无需处理 HTTP 和 JSON 的详细信息

库以单个 [Azure.Search.DocArgumentable NuGet 包](https://www.nuget.org/packages/Azure.Search.Documents/)的形式分发，其中包括用于以编程方式访问搜索服务的所有 api。

客户端库会定义 `SearchIndex`、`SearchField` 和 `SearchDocument` 之类的类，还会定义 `SearchIndexClient` 和 `SearchClient` 类中的 `SearchIndexClient.CreateIndex` 和 `SearchClient.Search` 之类的操作。 这些类已组织成以下命名空间：

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (版本 11) 目标版本[ `2020-06-30` 的 Azure 认知搜索 REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30)。 

客户端库不提供 [服务管理操作](/rest/api/searchmanagement/)，例如创建和缩放搜索服务和管理 API 密钥。 如果需要从 .NET 应用程序管理搜索资源，请在 Azure SDK for .NET 中使用 [Microsoft. 管理](/dotnet/api/overview/azure/search/management) 库。

## <a name="upgrade-to-v11"></a>升级到 v11

如果你使用的是早期版本的 .NET SDK，并且想要升级到当前的已正式发布版本，请参阅 [升级到 Azure 认知搜索 .NET sdk 版本 11](search-dotnet-sdk-migration-version-11.md)

## <a name="sdk-requirements"></a>SDK 要求

+ Visual Studio 2019 或更高版本。

+ 有自己的 Azure 认知搜索服务。 要使用 SDK，需要服务的名称以及一个或多个 API 密钥。 [在门户中创建一个服务](search-create-service-portal.md) （如果没有）。

+ 使用 **工具** 在 Visual Studio 中 [Azure.Search.Documents package](https://www.nuget.org/packages/Azure.Search.Documents)  >  **NuGet Package Manager**  >  **管理用于解决方案的 nuget 包**，下载Azure.Search.Documents 包。 搜索包名称 `Azure.Search.Documents` 。

适用于 .NET 的 Azure SDK 符合 [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support)，这意味着 .NET Framework 4.6.1 和 .net Core 2.0 为最低要求。

## <a name="example-application"></a>示例应用程序

本文 "按示例讲授" 依赖于 GitHub 上的 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 代码示例来说明 Azure 认知搜索中的基本概念（具体而言，如何创建、加载和查询搜索索引）。

对于本文的其余部分，假设有一个名为 "酒店" 的新索引，其中填充了几个文档，其中有多个查询与结果匹配。

下面是主程序，显示总体流程：

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

接下来是输出的部分屏幕截图，假设使用有效服务名称和 API 密钥运行此应用程序：

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="从示例程序中的 WriteLine 输出":::

### <a name="client-types"></a>客户端类型

客户端库使用三种不同操作的客户端类型： [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) 创建、更新或删除索引、 [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) 加载或查询索引以及 [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) 使用索引器和技能集。 本文重点介绍前两个。 

至少，所有客户端都需要服务名称或终结点，以及一个 API 密钥。 通常在配置文件中提供此信息，这类似于在 `appsettings.json` [DotNetHowTo 示例应用程序](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)的文件中找到的内容。 若要从配置文件中读取，请将添加 `using Microsoft.Extensions.Configuration;` 到程序。

下面的语句创建用于创建、更新或删除索引的索引客户端。 它采用搜索终结点和管理 API 密钥。

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

下一条语句将创建用于加载文档或运行查询的搜索客户端。 `SearchClient` 需要索引。 你将需要一个管理 API 密钥来加载文档，但你可以使用查询 API 密钥来运行查询。 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> 如果为导入操作提供了无效的密钥 (例如，需要在其中管理密钥) 的查询密钥，则在 `SearchClient` `CloudException` 第一次调用操作方法时，将引发并带有错误消息 "已禁止"。 如果出现这种情况，请仔细检查 API 密钥。
>

### <a name="deleting-the-index"></a>删除索引

在开发的早期阶段，你可能希望包含 [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) 语句来删除正在进行的索引，以便可以使用更新的定义重新创建它。 Azure 认知搜索的示例代码通常包含一个删除步骤，以便你可以重新运行该示例。

以下行调用 `DeleteIndexIfExists` ：

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

此方法使用给定的 `SearchIndexClient` 来检查索引是否存在，如果存在，则将其删除：

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> 本文中的示例代码为了简单起见，使用了同步方法，但你应在自己的应用程序中使用异步方法，以使它们保持可伸缩和响应能力。 例如，在上面的方法中，可以使用 [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) 而不是 [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) 。
>

## <a name="create-an-index"></a>创建索引

您可以使用 [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) 创建索引。 

下面的方法创建一个新的对象，该 [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) 对象具有 [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) 定义新索引的架构的对象的列表。 每个字段都有名称、数据类型和数个属性（定义其搜索行为）。 

可以使用从模型类定义字段 [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) 。 `FieldBuilder` 类通过检查给定 `Hotel` 模型类的公共属性和特性，使用反射来为索引创建 `SearchField` 对象的列表。 我们会在以后详细地查看 `Hotel` 类。

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

除了字段以外，你还可以向索引中添加计分配置文件、建议器或 CORS 选项 (这些参数在示例中省略，以便于简洁) 。 可以在 [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) 属性列表以及 [REST API 引用](/rest/api/searchservice/)中找到有关 SearchIndex 对象及其构成部分的详细信息。

> [!NOTE]
> 如果需要，始终可以直接创建 `Field` 对象的列表，而不是使用 `FieldBuilder`。 例如，你可能不想使用模型类，或者可能需要使用不希望通过添加属性来进行修改的现有模型类。
>

### <a name="call-createindex-in-main"></a>在 Main ( 中调用 CreateIndex # A1

`Main` 通过调用上述方法创建新的 "宾馆" 索引：

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>将模型类用于数据表示

DotNetHowTo 示例使用用于 [旅馆](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs)、 [地址](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)和 [房间](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) 数据结构的模型类。 `Hotel` 引用 `Address` 、单个级别的复杂类型 (多部分字段) ， `Room` () 多部分字段的集合。

您可以使用这些类型来创建和加载索引，并构建查询的响应：

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

另一种方法是直接向索引添加字段。 下面的示例只显示了几个字段。

   ```csharp
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };
   ```

### <a name="field-definitions"></a>字段定义

.NET 中的数据模型及其相应的索引架构应支持你想要提供给最终用户的搜索体验。 .NET 中的每个顶层对象（如搜索索引中的搜索文档）对应于用户界面中显示的搜索结果。 例如，在酒店搜索应用程序中，最终用户可能想要按酒店名称、酒店特色或特定客房的特征进行搜索。 

在每个类中，字段是用确定其使用方式的数据类型和属性定义的。 每个类中的每个公共属性的名称将映射到索引定义中名称相同的字段。 

请看下面的代码片段，从旅馆类中提取多个字段定义。 请注意，Address 和聊天室是具有其自己的类定义的 c # 类型 (引用示例代码（如果要) 查看它们）。 两者都是复杂类型。 有关详细信息，请参阅[如何为复杂类型建模](search-howto-complex-data-types.md)。

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>选择字段类

定义字段时，可以使用基类 [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) ，也可以使用预配置的属性作为 "模板" 的派生帮助器模型。

索引中正好有一个字段必须充当文档键 (`IsKey = true`) 。 它必须为字符串，并且必须唯一标识每个文档。 还需要具有 `IsHidden = true` ，这意味着它不会在搜索结果中显示。

| 字段类型 | 说明和用法 |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | 基类，其中大多数属性设置为 null，只不过 `Name` 是必需的，并且 `AnalyzerName` 默认为标准 Lucene。 |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Helper 模型。 可以是任何数据类型，该数据类型始终是不可搜索的 (它对于全文搜索查询) 将被忽略，并且可检索 (它不会隐藏) 。 其他属性默认情况下处于关闭状态，但可以启用。 你可能会将 `SimpleField` 用于仅在筛选器、facet 或计分概要文件中使用的文档 ID 或字段。 如果是这样，请确保应用该方案所需的所有属性，例如为文档 ID 应用 `IsKey = true`。 有关详细信息，请参阅源代码中的 [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs)。 |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Helper 模型。 必须为字符串，并且始终可搜索和可检索。 其他属性默认情况下处于关闭状态，但可以启用。 因为此字段类型是可搜索的，所以它支持同义词和分析器属性的完整补集。 有关详细信息，请参阅源代码中的 [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs)。 |

无论使用基本 `SearchField` API 还是任一帮助程序模型，都必须显式启用筛选器、facet 和排序属性。 例如，[IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)、[IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) 和 [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) 必须进行显式属性化，如上例中所示。

#### <a name="adding-field-attributes"></a>添加字段属性

请注意，每个字段如何由、、和等属性修饰 `IsFilterable` `IsSortable` `IsKey` `AnalyzerName` 。 这些属性直接映射到 [Azure 认知搜索索引中的相应字段属性](/rest/api/searchservice/create-index)。 `FieldBuilder` 类使用这些属性来构造索引的字段定义。

#### <a name="field-type-mapping"></a>字段类型映射

这些属性的 .NET 类型映射到它们在索引定义中的等效字段类型。 例如，`Category` 字符串属性映射到 `Edm.String` 类型的 `category` 字段。 `bool?` 和 `Edm.Boolean`、 `DateTimeOffset?`和 `Edm.DateTimeOffset` 等之间存在类似的类型映射。 

你是否注意到了 `SmokingAllowed` 属性？

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

此属性的 `JsonIgnore` 特性告知 `FieldBuilder` 不要将其序列化为字段形式的索引。  这是创建可在应用程序中用作帮助器的客户端计算属性的极佳方法。  在这种情况下，`SmokingAllowed` 属性将反映 `Rooms` 集合中的任何 `Room` 是否允许吸烟。 如果全部为 false，则表示整个酒店不允许吸烟。

## <a name="load-an-index"></a>加载索引

下一步将 `Main` 填充新创建的 "酒店" 索引。 此索引填充操作是通过以下方法完成的：（为方便演示，某些代码已替换为“...”。 有关完整的数据填充代码，请参阅完整的示例解决方案。）

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                HotelName = "Secret Point Motel",
                ...
                Address = new Address()
                {
                    StreetAddress = "677 5th Ave",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Budget Room, 1 Queen Bed (Cityside)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (City View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                HotelName = "Twin Dome Motel",
                ...
                {
                    StreetAddress = "140 University Town Center Dr",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Suite, 2 Double Beds (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Standard Room, 1 Queen Bed (City View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Waterfront View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "3",
                HotelName = "Triple Landscape Hotel",
                ...
                Address = new Address()
                {
                    StreetAddress = "3393 Peachtree Rd",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Standard Room, 2 Queen Beds (Amenities)",
                        ...
                    },
                    new Room ()
                    {
                        Description = "Standard Room, 2 Double Beds (Waterfront View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (Cityside)",
                        ...
                    }
                }
            }
        };

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

此方法有四个部分。 第一个部分创建包含 3 个 `Hotel` 对象的数组，其中每个对象包含 3 个用作要上传到索引的输入数据的 `Room` 对象。 为简单起见，此数据是硬编码的。 在实际的应用程序中，数据可能来自外部数据源（如 SQL 数据库）。

第二部分创建 [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 包含文档的。 指定在创建批时要应用到批的操作，在此示例中，通过调用 [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) 。 然后，通过方法将该批上传到 Azure 认知搜索索引 [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) 。

> [!NOTE]
> 在本示例中，我们只需上传文档。 如果想要将更改合并到现有文档或删除文档，可以改为调用 `IndexDocumentsAction.Merge`、`IndexDocumentsAction.MergeOrUpload` 或 `IndexDocumentsAction.Delete` 创建 Batch。 还可以通过调用 `IndexBatch.New` 在单个批中混用不同操作，这会用到一个 `IndexDocumentsAction` 对象的集合，其中的每个对象都会指示 Azure 认知搜索对文档执行特定操作。 可以通过调用 `IndexDocumentsAction.Merge`、`IndexAction.Upload` 之类的相应方法，创建自带操作的每个 `IndexDocumentsAction`。
> 

此方法的第三部分是处理索引重要错误情况的 catch 块。 如果搜索服务无法为批处理中的某些文档编制索引， `IndexBatchException` 则会引发 `IndexDocuments` 。 如果在服务负载过大时为文档编制索引，可能会发生此异常。 **强烈建议在代码中显式处理这种情况。** 可以延迟为失败的文档编制索引，并重试，也可以像此示例一样记录并继续执行，还可以执行其他操作，具体取决于应用程序对数据一致性的要求。

最后，`UploadDocuments` 方法延迟了两秒钟。 索引在搜索服务中异步执行，因此，示例应用程序需要等待一小段时间，以确保文档可用于搜索。 此类延迟通常仅在演示、测试和示例应用程序中是必需的。

### <a name="call-uploaddocuments-in-main"></a>在 Main ( 中调用 UploadDocuments # A1

下面的代码段 [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) 使用 indexClient 的方法设置的实例 [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) 。 IndexClient 在其请求上使用管理 API 密钥，这是加载或刷新文档所必需的。

另一种方法是 `SearchClient` 直接调用，并在上传递管理 API 密钥 `AzureKeyCredential` 。

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>运行查询

首先，设置一个 `SearchClient` ，它从 **appsettings.js** 读取搜索终结点和查询 API 密钥：

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

其次，定义发送查询请求的方法。 

每次执行查询时，它都会创建一个新的 [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) 对象。 此对象用于为查询指定其他选项，如排序、筛选、分页和分面。 在此方法中，我们要 `Filter` `Select` 为不同的查询设置、和 `OrderBy` 属性。 有关搜索查询表达式语法的详细信息，请查看 [简单的查询语法](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)。

下一步是实际执行搜索查询。 使用 `SearchClient.Search` 方法运行搜索。 对于每个查询，传递要用作字符串的搜索文本 (或者， `"*"` 如果没有搜索文本) ，还将使用之前创建的搜索选项。 此外，我们指定 `Hotel` 作为 `SearchClient.Search` 的类型参数，这指示 SDK 将搜索结果中的文档反序列化为类型为 `Hotel` 的对象。

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

第三，定义写入响应的方法，将每个文档打印到控制台：

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>在 Main ( 中调用 RunQueries # A1

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>浏览查询构造

让我们依次仔细查看每个查询。 下面是用于执行第一个查询的代码：

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

在这种情况下，我们将在任何可搜索字段中搜索 "motel" 一词的整个索引，并且我们只想检索选项指定的酒店名称 `Select` 。 结果如下：

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

在第二个查询中，使用筛选器选择每夜低于 $100 的房间。 仅返回结果中的酒店 ID 和说明：

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

上面的查询使用 OData `$filter` 表达式 `Rooms/any(r: r/BaseRate lt 100)` 来筛选索引中的文档。 这会使用 [any 运算符](./search-query-odata-collection-operators.md)将“BaseRate lt 100”应用到 Rooms 集合中的每个项。 有关详细信息，请参阅 [OData 筛选器语法](./query-odata-filter-orderby-syntax.md)。

在第三个查询中，找到最近翻新的前两个酒店，并显示旅馆名称和上一次翻修日期。 代码如下： 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

在上一个查询中，查找与 "宾馆" 一词匹配的所有酒店名称：

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

本部分将介绍 .NET SDK 的这一简介，但不要在此处停止。 下一部分提供了其他资源，用于了解有关通过 Azure 认知搜索进行编程的详细信息。

## <a name="next-steps"></a>后续步骤

+ 浏览[Azure.Search.Documents](/dotnet/api/azure.search.documents)和[REST API](/rest/api/searchservice/)的 API 参考文档

+ 浏览基于 azure 中 Azure.Search.Documents 的其他代码示例 [-dotnet-示例](https://github.com/Azure-Samples/azure-search-dotnet-samples) 和 [搜索-入门-dotnet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ 查看 [命名约定](/rest/api/searchservice/Naming-rules) ，了解用于命名不同对象的规则

+ 查看 [支持的数据类型](/rest/api/searchservice/Supported-data-types)