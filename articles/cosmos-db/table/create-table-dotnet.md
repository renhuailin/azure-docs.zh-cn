---
title: 快速入门：将表 API 与 .NET 配合使用 - Azure Cosmos DB
description: 本快速入门演示如何使用 Azure.Data.Tables SDK 从 .NET 应用程序访问 Azure Cosmos DB 表 API
author: DavidCBerry13
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: csharp
ms.topic: quickstart
ms.date: 08/25/2021
ms.author: daberry
ms.custom: devx-track-csharp
ms.openlocfilehash: 66907bcaef5818990d5a4e855f4cbc98624e2b90
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081085"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>快速入门：使用 .NET SDK 和 Azure Cosmos DB 生成表 API 应用

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

本快速入门演示如何从 .NET 应用程序访问 Azure Cosmos DB [表 API](introduction.md)。  Cosmos DB 表 API 是一种无架构数据存储，允许应用程序在云中存储结构化 NoSQL 数据。  由于数据存储在无架构设计中，因此将具有新特性的对象添加到表中时，系统会自动向表中添加新属性（列）。

.NET 应用程序可以使用 [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) NuGet 包访问 Cosmos DB 表 API。  [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) 包是 [.NET Standard 2.0](/dotnet/standard/net-standard) 库，适用于 .NET Framework（4.7.2 及更高版本）和 .NET Core（2.0 及更高版本）应用程序。

## <a name="prerequisites"></a>先决条件

示例应用程序采用 [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet/3.1) 进行编写，不过原则同时适用于 .NET Framework 和 .NET Core 应用程序。  可以使用 [Visual Studio](https://www.visualstudio.com/downloads/)[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) 或 [Visual Studio Code](https://code.visualstudio.com/) 作为 IDE。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="sample-application"></a>示例应用程序

本教程的示例应用程序可以从存储库 [https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet](https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet) 进行克隆或下载。  入门应用和完整应用都包含在示例存储库中。

```bash
git clone https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet
```

示例应用程序使用天气数据作为示例来演示表 API 的功能。 表示天气观察值的对象使用表 API 进行存储和检索，包括存储具有附加属性的对象以演示表 API 的无架构功能。

:::image type="content" source="./media/create-table-dotnet/table-api-app-finished-application-720px.png" alt-text="已完成应用程序的屏幕截图，其中显示了使用表 API 存储在 Cosmos DB 中的数据。" lightbox="./media/create-table-dotnet/table-api-app-finished-application.png":::

## <a name="1---create-an-azure-cosmos-db-account"></a>1 - 创建 Azure Cosmos DB 帐户

首先需要创建将包含在应用程序中使用的表的 Cosmos DB 表 API 帐户。  这可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 来完成。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

登录 [Azure 门户](https://portal.azure.com/)，并按照以下步骤创建 Cosmos DB 帐户。

| 说明    | 屏幕快照 |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1-240px.png" alt-text="显示如何使用顶部工具栏中的搜索框在 Azure 中查找 Cosmos DB 帐户的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2-240px.png" alt-text="显示 Azure 中 Cosmos DB 帐户页面上的“创建”按钮位置的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-3.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3-240px.png" alt-text="将“Azure 表”选项显示为要选择的正确选项的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3.png":::           |
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-4.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4-240px.png" alt-text="显示如何在 Cosmos DB 帐户创建页面上填写字段的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cosmos DB 帐户使用 [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) 命令来创建。 必须包含 `--capabilities EnableTable` 选项才能在 Cosmos DB 中启用表存储。  由于所有 Azure 资源都必须包含在资源组中，因此以下代码片段还会为 Cosmos DB 帐户创建资源组。

Cosmos DB 帐户名称的长度必须介于 3 到 44 个字符之间，并且只能包含小写字母、数字和连字符 (-) 字符。  Cosmos DB 帐户名称还必须在 Azure 中是唯一的。

Azure CLI 命令可以在 [Azure Cloud Shell](https://shell.azure.com) 中或是[安装了 Azure CLI](/cli/azure/install-azure-cli) 的工作站上运行。

Cosmos DB 帐户创建过程通常需要几分钟才能完成。

```azurecli
LOCATION='eastus'
RESOURCE_GROUP_NAME='rg-msdocs-tables-sdk-demo'
COSMOS_ACCOUNT_NAME='cosmos-msdocs-tables-sdk-demo-123'    # change 123 to a unique set of characters for a unique name
COSMOS_TABLE_NAME='WeatherData'

az group create \
    --location $LOCATION \
    --name $RESOURCE_GROUP_NAME

az cosmosdb create \
    --name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --capabilities EnableTable
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure Cosmos DB 帐户使用 [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) cmdlet 来创建。 必须包含 `-ApiKind "Table"` 选项才能在 Cosmos DB 中启用表存储。  由于所有 Azure 资源都必须包含在资源组中，因此以下代码片段还会为 Azure Cosmos DB 帐户创建资源组。

Azure Cosmos DB 帐户名称的长度必须介于 3 到 44 个字符之间，并且只能包含小写字母、数字和连字符 (-) 字符。  Azure Cosmos DB 帐户名称还必须在 Azure 中是唯一的。

Azure PowerShell 命令可以在 [Azure Cloud Shell](https://shell.azure.com) 中或是[安装了 Azure PowerShell](/powershell/azure/install-az-ps) 的工作站上运行。

Cosmos DB 帐户创建过程通常需要几分钟才能完成。

```azurepowershell
$location = 'eastus'
$resourceGroupName = 'rg-msdocs-tables-sdk-demo'
$cosmosAccountName = 'cosmos-msdocs-tables-sdk-demo-123'  # change 123 to a unique set of characters for a unique name

# Create a resource group
New-AzResourceGroup `
    -Location $location `
    -Name $resourceGroupName

# Create an Azure Cosmos DB 
New-AzCosmosDBAccount `
    -Name $cosmosAccountName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -ApiKind "Table"
```

---

## <a name="2---create-a-table"></a>2 - 创建表

接下来，需要在 Cosmos DB 帐户中创建表，以供应用程序使用。  与传统数据库不同，只需指定表的名称，无需指定表中的属性（列）。  将数据加载到表中时，属性（列）会根据需要自动创建。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

在 [Azure 门户](https://portal.azure.com/)中，完成以下步骤，以在 Cosmos DB 帐户中创建表。

| 说明    | 屏幕快照 |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db table step 1](<./includes/create-table-dotnet/create-cosmos-table-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1-240px.png" alt-text="显示如何使用顶部工具栏中的搜索框查找 Cosmos DB 帐户的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db table step 2](<./includes/create-table-dotnet/create-cosmos-table-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2-240px.png" alt-text="显示“添加表”按钮位置的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db table step 3](<./includes/create-table-dotnet/create-cosmos-table-3.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3-240px.png" alt-text="显示 Cosmos DB 表的“新建表”对话框的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cosmos DB 中的表使用 [az cosmosdb table create](/cli/azure/cosmosdb/table#az_cosmosdb_table_create) 命令来创建。

```azurecli
COSMOS_TABLE_NAME='WeatherData'

az cosmosdb table create \
    --account-name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_TABLE_NAME \
    --throughput 400
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Cosmos DB 中的表使用 [New-AzCosmosDBTable](/powershell/module/az.cosmosdb/new-azcosmosdbtable) cmdlet 来创建。

```azurepowershell
$cosmosTableName = 'WeatherData'

# Create the table for the application to use
New-AzCosmosDBTable `
    -Name $cosmosTableName `
    -AccountName $cosmosAccountName `
    -ResourceGroupName $resourceGroupName
```

---

## <a name="3---get-cosmos-db-connection-string"></a>3 - 获取 Cosmos DB 连接字符串

若要在 Cosmos DB 中访问表，应用需要 CosmosDB 存储帐户的表连接字符串。  可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 检索该连接字符串。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

| 说明    | 屏幕快照 |
|:----------------|-----------:|
| [!INCLUDE [Get cosmos db table connection string step 1](<./includes/create-table-dotnet/get-cosmos-connection-string-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1-240px.png" alt-text="显示 Cosmos DB 页面上的连接字符串链接位置的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1.png":::           |
| [!INCLUDE [Get cosmos db table connection string step 2](<./includes/create-table-dotnet/get-cosmos-connection-string-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2-240px.png" alt-text="显示在应用程序中选择和使用的连接字符串的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 获取主表存储连接字符串，请使用 [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) 命令及选项 `--type connection-strings`。  此命令使用 [JMESPath 查询](https://jmespath.org/)，以便仅显示主表连接字符串。

```azurecli
# This gets the primary Table connection string
az cosmosdb keys list \
    --type connection-strings \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_ACCOUNT_NAME \
    --query "connectionStrings[?description=='Primary Table Connection String'].connectionString" \
    --output tsv
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 获取主表存储连接字符串，请使用 [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) cmdlet。

```azurepowershell
# This gets the primary Table connection string  
 $(Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $cosmosAccountName `
    -Type "ConnectionStrings")."Primary Table Connection String"
```

---

Cosmos DB 帐户的连接字符串被视为应用机密，必须如同任何其他应用机密或密码一样进行保护。  此示例使用[机密管理器工具](/aspnet/core/security/app-secrets#secret-manager)在开发过程中存储连接字符串，并提供给应用程序使用。  可以从 Visual Studio 或 .NET CLI 访问机密管理器工具。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

若要从 Visual Studio 打开机密管理器工具，请右键单击项目，然后从上下文菜单中选择“管理用户机密”。  这会打开项目的 secrets.json 文件。  将该文件的内容替换为下面的 JSON，从而替换为 Cosmos DB 表连接字符串。

```json
{
  "ConnectionStrings": {
    "CosmosTableApi": "<cosmos db table connection string>"
  }  
}
```

### <a name="net-cli"></a>[.NET CLI](#tab/netcore-cli)

若要使用机密管理器，必须先使用 `dotnet user-secrets init` 命令为项目初始化它。

```dotnetcli
dotnet user-secrets init
```

然后，使用 `dotnet user-secrets set` 命令将 Cosmos DB 表连接字符串添加为机密。

```dotnetcli
dotnet user-secrets set "ConnectionStrings:CosmosTableApi" "<cosmos db table connection string>"
```

---

## <a name="4---install-azuredatatables-nuget-package"></a>4 - 安装 Azure.Data.Tables NuGet 包

若要从 .NET 应用程序访问 Cosmos DB 表 API，请安装 [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables) NuGet 包。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```PowerShell
Install-Package Azure.Data.Tables
```

### <a name="net-cli"></a>[.NET CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Azure.Data.Tables
```

---

## <a name="5---configure-the-table-client-in-startupcs"></a>5 - 在 Startup.cs 中配置表客户端

Azure SDK 使用客户端对象与 Azure 通信，以对 Azure 执行不同的操作。  [TableClient](/dotnet/api/azure.data.tables.tableclient) 对象是用于与 Cosmos DB 表 API 通信的对象。

应用程序通常会为每个表创建单个 [TableClient](/dotnet/api/azure.data.tables.tableclient) 对象，以在整个应用程序中使用。  建议使用依赖项注入 (DI) 并将 [TableClient](/dotnet/api/azure.data.tables.tableclient) 对象注册为单一实例来实现此目的。  有关将 DI 与 Azure SDK 一起使用的详细信息，请参阅[依赖项 注入与用于 .NET 的 Azure SDK](/dotnet/azure/sdk/dependency-injection)。

在应用程序的 `Startup.cs` 文件中，编辑 ConfigureServices() 方法以匹配以下代码片段：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddMvcOptions(options =>
        {
            options.Filters.Add(new ValidationFilter());
        });
    
    var connectionString = Configuration.GetConnectionString("CosmosTableApi");
    services.AddSingleton<TableClient>(new TableClient(connectionString, "WeatherData"));
    
    services.AddSingleton<TablesService>();
}
```

还需要将下面的 using 语句添加到 Startup.cs 文件顶部。

```csharp
using Azure.Data.Tables;
```

## <a name="6---implement-cosmos-db-table-operations"></a>6 - 实现 Cosmos DB 表操作

示例应用的所有 Cosmos DB 表操作都在位于 Services 目录中的 `TableService` 类中实现。  需要在此文件顶部导入 `Azure` 和 `Azure.Data.Tables` 命名空间，以使用 `Azure.Data.Tables` SDK 包中的对象。

```csharp
using Azure;
using Azure.Data.Tables;
```

在 `TableService` 类的开头，添加用于 [TableClient](/dotnet/api/azure.data.tables.tableclient) 对象的成员变量以及一个构造函数，以允许将 [TableClient](/dotnet/api/azure.data.tables.tableclient) 对象注入该类。

```csharp
private TableClient _tableClient;

public TablesService(TableClient tableClient)
{
    _tableClient = tableClient;
}
```

### <a name="get-rows-from-a-table"></a>从表中获取行

[TableClient](/dotnet/api/azure.data.tables.tableclient) 类包含名为 [Query](/dotnet/api/azure.data.tables.tableclient.query) 的方法，可用于从表中选择行。  在此示例中，由于未向该方法传递任何参数，因此会从表中选择所有行。

该方法还采用类型为 [ITableEntity](/dotnet/api/azure.data.tables.itableentity) 的泛型参数，该参数指定返回的模型类数据。 在此例中，将使用内置类 [TableEntity](/dotnet/api/azure.data.tables.itableentity)，这意味着 `Query` 方法会返回 `Pageable\<TableEntity\>` 集合作为其结果。

```csharp
public IEnumerable<WeatherDataModel> GetAllRows()
{
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>();

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

`Azure.Data.Tables` 包中定义的 [TableEntity](/dotnet/api/azure.data.tables.itableentity) 类具有适用于表中的分区键和行键值的属性。  这两个值共同表示表中行的唯一键。  在此示例应用程序中，气象站的名称（城市）存储在分区键中，观测日期/时间存储在行键中。  所有其他属性（温度、湿度、风速）存储在 `TableEntity` 对象中的字典内。

常见做法是将 [TableEntity](/dotnet/api/azure.data.tables.tableentity) 对象映射到具有你自己定义的对象。  示例应用程序在 Models 目录中定义了一个类 `WeatherDataModel` 来实现此目的。  此类具有分区键和行键将映射到的气象站名称和观察日期属性，可为这些值提供更有意义的属性名称。  它随后使用字典将所有其他属性存储在对象中。  这是使用表存储时的常见模式，因为行可以具有任意数量的任意属性，并且我们希望模型对象能够捕获所有这些属性。  此类还包含列出类中的属性的方法。

```csharp
public class WeatherDataModel 
{
    // Captures all of the weather data properties -- temp, humidity, wind speed, etc
    private Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public DateTimeOffset? Timestamp { get; set; }

    public string Etag { get; set; }

    public object this[string name] 
    { 
        get => ( ContainsProperty(name)) ? _properties[name] : null; 
        set => _properties[name] = value; 
    }
    
    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);       
}
```

`MapTableEntityToWeatherDataModel` 方法用于将 [TableEntity](/dotnet/api/azure.data.tables.itableentity) 对象映射到 `WeatherDataModel` 对象。  [TableEntity](/dotnet/api/azure.data.tables.itableentity) 对象包含一个 [Keys](/dotnet/api/azure.data.tables.tableentity.keys) 属性，用于获取对象的表中包含的所有属性名称（实际上是表中此行的列名称）。  `MapTableEntityToWeatherDataModel` 方法直接映射 `PartitionKey`、`RowKey`、`Timestamp` 和 `Etag` 属性，然后使用 `Keys` 属性循环访问 `TableEntity` 对象中的其他属性，并将这些属性映射到 `WeatherDataModel` 对象（不包括已直接映射的属性）。

编辑 `MapTableEntityToWeatherDataModel` 方法中的代码以匹配以下代码块。

```csharp
public WeatherDataModel MapTableEntityToWeatherDataModel(TableEntity entity)
{
    WeatherDataModel observation = new WeatherDataModel();
    observation.StationName = entity.PartitionKey;
    observation.ObservationDate = entity.RowKey;
    observation.Timestamp = entity.Timestamp;
    observation.Etag = entity.ETag.ToString();

    var measurements = entity.Keys.Where(key => !EXCLUDE_TABLE_ENTITY_KEYS.Contains(key));
    foreach (var key in measurements)
    {
        observation[key] = entity[key];
    }
    return observation;            
}
```

### <a name="filter-rows-returned-from-a-table"></a>筛选从表返回的行

若要筛选从表返回的行，可以将 OData 样式筛选器字符串传递到 [Query](/dotnet/api/azure.data.tables.tableclient.query) 方法。 例如，如果要获取 2021 年 7 月 1 日午夜到 2021 年 7 月 2 日午夜（含）之间的所有芝加哥天气读数，则传入以下筛选器字符串。

```odata
PartitionKey eq 'Chicago' and RowKey ge '2021-07-01 12:00 AM' and RowKey le '2021-07-02 12:00 AM'
```

可以在[筛选器系统查询选项](https://www.odata.org/documentation/odata-version-2-0/uri-conventions/)部分中查看 OData 网站上的所有 OData 筛选器运算符。

在示例应用程序中，`FilterResultsInputModel` 对象旨在捕获用户提供的任何筛选条件。

```csharp
public class FilterResultsInputModel : IValidatableObject
{
    public string PartitionKey { get; set; }
    public string RowKeyDateStart { get; set; }
    public string RowKeyTimeStart { get; set; }
    public string RowKeyDateEnd { get; set; }
    public string RowKeyTimeEnd { get; set; }
    [Range(-100, +200)]
    public double? MinTemperature { get; set; }
    [Range(-100,200)]
    public double? MaxTemperature { get; set; }
    [Range(0, 300)]
    public double? MinPrecipitation { get; set; }
    [Range(0,300)]
    public double? MaxPrecipitation { get; set; }
}
```

将此对象传递到 `TableService` 类中的 `GetFilteredRows` 方法时，它会为每个非 null 属性值创建一个筛选器字符串。  它随后会使用“and”子句将所有值联接在一起，以创建合并筛选器字符串。  此合并筛选器字符串会传递到 [TableClient](/dotnet/api/azure.data.tables.tableclient) 对象上的 [Query](/dotnet/api/azure.data.tables.tableclient.query) 方法，仅返回与筛选器字符串匹配的行。  可以在代码中使用类似方法，根据应用程序的要求构造合适的筛选器字符串。

```csharp
public IEnumerable<WeatherDataModel> GetFilteredRows(FilterResultsInputModel inputModel)
{
    List<string> filters = new List<string>();

    if (!String.IsNullOrEmpty(inputModel.PartitionKey))
        filters.Add($"PartitionKey eq '{inputModel.PartitionKey}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateStart) && !String.IsNullOrEmpty(inputModel.RowKeyTimeStart))
        filters.Add($"RowKey ge '{inputModel.RowKeyDateStart} {inputModel.RowKeyTimeStart}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateEnd) && !String.IsNullOrEmpty(inputModel.RowKeyTimeEnd))
        filters.Add($"RowKey le '{inputModel.RowKeyDateEnd} {inputModel.RowKeyTimeEnd}'");
    if (inputModel.MinTemperature.HasValue)
        filters.Add($"Temperature ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxTemperature.HasValue)
        filters.Add($"Temperature le {inputModel.MaxTemperature.Value}");
    if (inputModel.MinPrecipitation.HasValue)
        filters.Add($"Precipitation ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxPrecipitation.HasValue)
        filters.Add($"Precipitation le {inputModel.MaxTemperature.Value}");

    string filter = String.Join(" and ", filters);
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>(filter);

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

### <a name="insert-data-using-a-tableentity-object"></a>使用 TableEntity 对象插入数据

将数据添加到表的最简单方法是使用 [TableEntity](/dotnet/api/azure.data.tables.itableentity) 对象。  在此示例中，数据会从输入模型对象映射到 [TableEntity](/dotnet/api/azure.data.tables.itableentity) 对象。  输入对象中表示气象站名称和观察日期/时间的属性分别映射到 [PartitionKey](/dotnet/api/azure.data.tables.tableentity.partitionkey) 和 [RowKey](/dotnet/api/azure.data.tables.tableentity.rowkey) 属性，这些属性共同构成表中行的唯一键。  输入模型对象上的其他属性随后会映射到 TableEntity 对象上的字典属性。  最后，[TableClient](/dotnet/api/azure.data.tables.tableclient) 对象上的 [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) 方法用于将数据插入表中。

修改示例应用程序中的 `InsertTableEntity` 类，以包含以下代码。

```csharp
public void InsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.AddEntity(entity);
}
```

### <a name="upsert-data-using-a-tableentity-object"></a>使用 TableEntity 对象更新插入数据

如果尝试向表中插入的行具有该表中已存在的分区键/行键组合，则会收到错误。  因此，在向表添加行时，通常最好使用 [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) 而不是 AddEntity 方法。  如果表中已存在给定分区键/行键组合，则 [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) 方法会更新现有行。  否则，行会添加到表中。

```csharp
public void UpsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.UpsertEntity(entity);
}
```

### <a name="insert-or-upsert-data-with-variable-properties"></a>使用变量属性插入或更新插入数据

使用 Cosmos DB 表 API 的优点之一是，如果要加载到表的对象包含任何新属性，则这些属性会自动添加到表中并且值存储在 Cosmos DB 中。  无需如同传统数据库中一样，运行 ALTER TABLE 等 DDL 语句来添加列。

在处理可能会随着时间推移添加或修改需要捕获的数据的数据源时，或者在不同的输入向应用程序提供不同的数据时，此模型可使应用程序具有灵活性。 在示例应用程序中，我们可以模拟一个不仅发送基本天气数据，而且还发送一些附加值的气象站。 首次将具有这些新属性的对象存储在表中时，对应属性（列）会自动添加到表中。

在示例应用程序中，`ExpandableWeatherObject` 类围绕内部字典进行构建，以支持对象上的任何属性集。  此类表示对象需要包含任意属性集时的典型模式。

```csharp
public class ExpandableWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

若要使用表 API 插入或更新插入 此类对象，请将可扩充对象的属性映射到 [TableEntity](/dotnet/api/azure.data.tables.tableentity) 对象，并根据需要对 [TableClient](/dotnet/api/azure.data.tables.tableclient) 对象使用 [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) 或 [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) 方法。

```csharp
public void InsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.AddEntity(entity);
}

        
public void UpsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.UpsertEntity(entity);
}

```
  
### <a name="update-an-entity"></a>更新条目

可以通过对 [TableClient](/dotnet/api/azure.data.tables.tableclient) 对象调用 [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) 方法来更新实体。  由于使用表 API 存储的实体（行）可以包含任意属性集，因此创建基于字典对象的更新对象（类似于前面讨论的 `ExpandableWeatherObject`）通常很有用。  在此情况下，唯一的区别是添加 `Etag` 属性，该属性用于在更新期间进行并发控制。

```csharp
public class UpdateWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }
    public string ObservationDate { get; set; }
    public string Etag { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

在示例应用中，此对象会传递到 `TableService` 类中的 `UpdateEntity` 方法。  此方法首先对 [TableClient](/dotnet/api/azure.data.tables.tableclient) 使用 [GetEntity](/dotnet/api/azure.data.tables.tableclient.getentity) 方法，从表 API 加载现有实体。  它随后更新该实体对象，并使用 `UpdateEntity` 方法将更新保存到数据库。  请注意 [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) 方法如何获取对象的当前 Etag，以确保对象自最初加载以来未发生更改。  如果希望无论如何都更新实体，则可以将 `Etag.Any` 值传递到 `UpdateEntity` 方法。

```csharp
public void UpdateEntity(UpdateWeatherObject weatherObject)
{
    string partitionKey = weatherObject.StationName;
    string rowKey = weatherObject.ObservationDate;

    // Use the partition key and row key to get the entity
    TableEntity entity = _tableClient.GetEntity<TableEntity>(partitionKey, rowKey).Value;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }

    _tableClient.UpdateEntity(entity, new ETag(weatherObject.Etag));
}
```

### <a name="remove-an-entity"></a>删除实体

若要从表中删除实体，请使用对象的分区键和行键对 [TableClient](/dotnet/api/azure.data.tables.tableclient) 对象调用 [DeleteEntity](/dotnet/api/azure.data.tables.tableclient.deleteentity) 方法。

```csharp
public void RemoveEntity(string partitionKey, string rowKey)
{
    _tableClient.DeleteEntity(partitionKey, rowKey);           
}
```

## <a name="7---run-the-code"></a>7 - 运行代码

运行示例应用程序以与 Cosmos DB 表 API 交互。  首次运行应用程序时没有数据，因为表为空。  使用应用程序顶部的任何按钮将数据添加到表。

:::image type="content" source="./media/create-table-dotnet/table-api-app-data-insert-buttons-480px.png" alt-text="应用程序的屏幕截图，其中显示用于通过表 API 将数据插入到 Cosmos DB 中的按钮位置。" lightbox="./media/create-table-dotnet/table-api-app-data-insert-buttons.png":::

选择“使用表实体插入”按钮会打开一个对话框，使你可以使用 `TableEntity` 对象插入或更新插入新行。

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-table-entity-480px.png" alt-text="应用程序的屏幕截图，其中显示用于通过 TableEntity 对象插入数据的对话框。" lightbox="./media/create-table-dotnet/table-api-app-insert-table-entity.png":::

选择“使用可扩充数据插入”按钮会打开一个对话框，使你可以使用自定义属性插入对象，演示了 Cosmos DB 表 API 如何在需要时自动向表添加属性（列）。  使用“添加自定义字段”按钮添加一个或多个新属性并演示此功能。

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-expandable-entity-480px.png" alt-text="应用程序的屏幕截图，其中显示用于通过带有自定义字段的对象插入数据的对话框。" lightbox="./media/create-table-dotnet/table-api-app-insert-expandable-entity.png":::

使用“插入示例数据”按钮将一些示例数据加载到 Cosmos DB 表中。

:::image type="content" source="./media/create-table-dotnet/table-api-app-sample-data-insert-480px.png" alt-text="应用程序的屏幕截图，其中显示示例数据插入按钮的位置。" lightbox="./media/create-table-dotnet/table-api-app-sample-data-insert.png":::

选择顶部菜单中的“筛选结果”项以进入“筛选结果”页面。  在此页面上，填写筛选条件以演示如何构建筛选子句并传递到 Cosmos DB 表 API。

:::image type="content" source="./media/create-table-dotnet/table-api-app-filter-data-480px.png" alt-text="应用程序的屏幕截图，其中显示筛选结果页面，并突出显示了用于导航到该页面的菜单项。" lightbox="./media/create-table-dotnet/table-api-app-filter-data.png":::

## <a name="clean-up-resources"></a>清理资源

完成示例应用程序后，应从 Azure 帐户中删除与本文相关的所有 Azure 资源。  可以通过删除资源组来实现此目的。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

可以通过执行以下操作，使用 [Azure 门户](https://portal.azure.com/)删除资源组。

| 说明    | 屏幕快照 |
|:----------------|-----------:|
| [!INCLUDE [Delete resource group step 1](<./includes/create-table-dotnet/remove-resource-group-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-1-240px.png" alt-text="显示如何搜索资源组的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-1.png"::: |
| [!INCLUDE [Delete resource group step 2](<./includes/create-table-dotnet/remove-resource-group-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-2-240px.png" alt-text="显示“删除资源组”按钮位置的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-2.png"::: |
| [!INCLUDE [Delete resource group step 3](<./includes/create-table-dotnet/remove-resource-group-3.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-3-240px.png" alt-text="显示用于删除资源组的确认对话框的屏幕截图。" lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-3.png"::: |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 删除资源组，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令以及要删除的资源组的名称。  删除某个资源组还会删除该资源组中包含的所有 Azure 资源。

```azurecli
az group delete --name $RESOURCE_GROUP_NAME
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 删除资源组，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令以及要删除的资源组的名称。  删除某个资源组还会删除该资源组中包含的所有 Azure 资源。

```azurepowershell
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建表和运行应用。  现在可以使用表 API 进行数据查询了。  

> [!div class="nextstepaction"]
> [将表数据导入表 API](table-import.md)
