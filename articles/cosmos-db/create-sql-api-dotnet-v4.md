---
title: 使用 .NET V4 SDK 管理 Azure Cosmos DB SQL API 资源
description: 参考本快速入门使用 .NET V4 SDK 生成控制台应用，以管理 Azure Cosmos DB SQL API 帐户资源。
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.openlocfilehash: 9fe921fff40a3bee7632317e5febe7cd84d3ee17
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112517192"
---
# <a name="quickstart-build-a-console-app-by-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>快速入门：使用 .NET V4 SDK（预览版）生成控制台应用以管理 Azure Cosmos DB SQL API 帐户资源
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3 连接器](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

开始使用适用于 .NET 的 Azure Cosmos DB SQL API 客户端库。 按照本文中的步骤安装 .NET V4 (Azure.Cosmos) 包并生成应用。 然后，尝试运行示例代码以针对 Azure Cosmos DB 中存储的数据执行基本的创建、读取、更新和删除 (CRUD) 操作。

> [!IMPORTANT]
> 用于 Azure Cosmos DB 的 .NET V4 SDK 当前提供公共预览版。 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 是 Microsoft 的快速 NoSQL 数据库，具有适合于任何规模的开放式 API。 使用 Azure Cosmos DB，可以快速创建和查询键/值、文档和图形数据库。 使用适用于 .NET 的 Azure Cosmos DB SQL API 客户端库完成以下操作：

* 创建 Azure Cosmos 数据库和容器。
* 向容器添加示例数据。
* 查询数据。 
* 删除数据库。

[库源代码](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [包 (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 [免费创建一个](https://azure.microsoft.com/free/)。 无需 Azure 订阅也可[试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，无需缴纳费用或承诺金。 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core)。 可以通过运行 `dotnet --version` 来验证你的环境中哪个版本可用。

## <a name="set-up"></a>设置

本部分指导你创建 Azure Cosmos 帐户，并设置使用适用于 .NET 的 Azure Cosmos DB SQL API 客户端库以管理资源的项目。 

本文所述的示例代码创建 `FamilyDatabase` 数据库，并在该数据库中创建家庭成员。 每个家庭成员是一个项，具有 `Id`、`FamilyName`、`FirstName`、`LastName`、`Parents`、`Children` 和 `Address` 等属性。 `LastName` 属性用作容器的分区键。 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> 创建 Azure Cosmos 帐户

如果使用[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 选项创建 Azure Cosmos 帐户，则必须创建 SQL API 类型的 Azure Cosmos 帐户。 已为你创建 Azure Cosmos 测试帐户。 无需显式创建帐户，因此可以跳过此部分并转到下一部分。

如果你有自己的 Azure 订阅或者免费创建了订阅，则应显式创建 Azure Cosmos 帐户。 以下代码将创建具有会话一致性的 Azure Cosmos 帐户。 该帐户在 `South Central US` 和 `North Central US` 中复制。  

你可以使用 Azure Cloud Shell 创建 Azure Cosmos 帐户。 Azure Cloud Shell 是一个用于管理 Azure 资源的、可通过浏览器访问的交互式经验证 shell。 它使用户能够灵活选择最适合自己工作方式的 shell 体验：无论是 Bash 还是 PowerShell。 

本快速入门中使用 Bash。 Azure Cloud Shell 还需要一个存储帐户。 可在系统提示时创建。

1. 选择以下代码旁边的“试用”按钮，依次选择“Bash”模式和“创建存储帐户”，然后登录到 Cloud Shell  。 

1. 将以下代码复制并粘贴到 Azure Cloud Shell，然后运行该代码。 Azure Cosmos 帐户名必须是全局唯一的，因此请确保在运行该命令之前更新 `mysqlapicosmosdb` 值。

   ```azurecli-interactive

   # Set variables for the new SQL API account, database, and container
   resourceGroupName='myResourceGroup'
   location='southcentralus'

   # The Azure Cosmos account name must be globally unique, so be sure to update the `mysqlapicosmosdb` value before you run the command
   accountName='mysqlapicosmosdb'

   # Create a resource group
   az group create \
       --name $resourceGroupName \
       --location $location

   # Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
   az cosmosdb create \
       --resource-group $resourceGroupName \
       --name $accountName \
       --kind GlobalDocumentDB \
       --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
       --default-consistency-level "Session" \
       --enable-multiple-write-locations true

   ```

创建 Azure Cosmos 帐户需要一段时间。 操作成功后，可以看到确认输出。 登录到 [Azure 门户](https://portal.azure.com/)，确认是否存在具有指定名称的 Azure Cosmos 帐户。 创建资源后，可以关闭 Azure Cloud Shell 窗口。 

### <a name="create-a-net-app"></a><a id="create-dotnet-core-app"></a>创建 .NET 应用

在你偏好的编辑器或 IDE 中创建一个 .NET 应用程序。 从本地计算机打开 Windows 命令提示符或终端窗口。 你将从命令提示符或终端运行接下来的部分中的所有命令。  

运行以下 `dotnet new` 命令创建名为 `todo` 的应用。 `--langVersion` 参数在创建的项目文件中设置 `LangVersion` 属性。

   ```bash
   dotnet new console --langVersion:8 -n todo
   ```

使用以下命令将目录切换到新建的应用文件夹，并生成应用程序：

   ```bash
   cd todo
   dotnet build
   ```

内部版本的预期输出应如下所示：

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>安装 Azure Cosmos DB 包

当你仍在应用程序目录中时，使用 `dotnet add package` 命令安装适用于 .NET Core 的 Azure Cosmos DB 客户端库：

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>从 Microsoft Azure 门户复制 Azure Cosmos 帐户凭据

此示例应用程序需对 Azure Cosmos 帐户进行身份验证。 为了进行身份验证，请将 Azure Cosmos 帐户凭据传递给应用程序。 按照以下步骤获取 Azure Cosmos 帐户凭据：

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 转到你的 Azure Cosmos 帐户。

1. 打开“密钥”窗格，复制帐户的“URI”和“主密钥”值  。 在下一个过程中，需要将这些 URI 和密钥值添加到环境变量。

## <a name="learn-the-object-model"></a><a id="object-model"></a>了解对象模型

在继续生成应用程序之前，先研究 Azure Cosmos DB 中资源的层次结构以及用于创建和访问这些资源的对象模型。 Azure Cosmos DB 按以下顺序创建资源：

* Azure Cosmos 帐户 
* 数据库 
* 容器 
* Items

若要详细了解实体的层次结构，请参阅 [Azure Cosmos DB 资源模型](account-databases-containers-items.md)一文。 使用以下 .NET 类与这些资源进行交互：

* `CosmosClient`. 此类为 Azure Cosmos DB 服务提供客户端逻辑表示。 此客户端对象用于对服务进行配置和执行请求。
* `CreateDatabaseIfNotExistsAsync`. 若数据库资源不存在，则此方法以异步操作的形式创建数据库资源；若数据库资源已存在，则此方法以异步操作的形式获取它。 
* `CreateContainerIfNotExistsAsync`. 若容器不存在，则此方法以异步操作的形式创建容器；若容器已存在，则此方法以异步操作的形式获取它。 可查看响应中的状态代码，确定是新创建了容器 (201) 还是返回了现有容器 (200)。 
* `CreateItemAsync`. 此方法在容器中创建项。
* `UpsertItemAsync`. 此方法在容器内创建一个项（如果该项尚不存在）或替换该项（如果该项已存在）。 
* `GetItemQueryIterator`. 此方法使用带有参数化值的 SQL 语句在 Azure Cosmos 数据库的容器下创建项查询。 
* `DeleteAsync`. 此方法从 Azure Cosmos 帐户中删除指定的数据库。

 ## <a name="configure-code-examples"></a><a id="code-examples"></a>配置代码示例

本文所述的示例代码在 Azure Cosmos DB 中创建家庭数据库。 家庭数据库包含家庭详细信息，例如名称、地址、位置、父母、子女和宠物。 

在为 Azure Cosmos 帐户填充数据之前，请定义家庭项的属性。 在示例应用程序的根级别创建一个名为 `Family.cs` 的新类，并向其中添加以下代码：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives-and-define-the-client-object"></a>添加 using 指令并定义客户端对象

在编辑器中打开项目目录中的 Program.cs 文件，并在应用程序顶部添加以下 `using` 指令：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


在 `Program` 类中，添加以下全局变量。 这些变量包含终结点和授权密钥、数据库的名称以及要创建的容器。 确保根据环境替换终结点和授权密钥值。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

最后，替换 `Main` 方法：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>创建数据库 

定义 `program.cs` 类中的 `CreateDatabaseAsync` 方法。 此方法创建 `FamilyDatabase` 数据库（如果尚不存在）。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>创建容器

定义 `Program` 类中的 `CreateContainerAsync` 方法。 此方法创建 `FamilyContainer` 容器（如果尚不存在）。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>创建项

通过使用以下代码添加 `AddItemsToContainerAsync` 方法来创建家庭项。 可以使用 `CreateItemAsync` 或 `UpsertItemAsync` 方法来创建项。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>查询项

插入项后，可以运行查询以获取 Andersen 家庭的详细信息。 以下代码显示如何直接使用 SQL 查询来执行查询。 获取 Anderson 家庭详细信息的 SQL 查询是 `SELECT * FROM c WHERE c.LastName = 'Andersen'`。 在 `Program` 类中定义 `QueryItemsAsync` 方法，并向其中添加以下代码：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>替换项 

通过使用以下代码添加 `ReplaceFamilyItemAsync` 方法来读取家庭项，然后对其更新：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>删除项 

通过使用以下代码添加 `DeleteFamilyItemAsync` 方法来删除家庭项：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>删除数据库 

可以通过使用以下代码添加 `DeleteDatabaseAndCleanupAsync` 方法来删除数据库：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

添加所有必需的方法后，保存 Program.cs 文件。 

## <a name="run-the-code"></a>运行代码

运行应用程序以创建 Azure Cosmos DB 资源：

   ```bash
   dotnet run
   ```

运行应用程序时生成以下输出：

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

通过登录到 Microsoft Azure 门户并查看 Azure Cosmos 帐户中的必需项，来验证是否已创建数据。 

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure Cosmos 帐户和相应资源组时，可以使用 Azure CLI 或 Azure PowerShell 将其删除。 以下命令显示如何使用 Azure CLI 删除资源组：

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何使用 .NET Core 应用创建 Azure Cosmos 帐户、数据库和容器。 现在可以使用以下文章中的说明将其他数据导入到 Azure Cosmos 帐户： 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)
