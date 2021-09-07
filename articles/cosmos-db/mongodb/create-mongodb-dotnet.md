---
title: 使用 Azure Cosmos DB API for MongoDB 和 .NET SDK 生成 Web API
description: 演示一个 .NET 代码示例，可以参考该示例使用 Azure Cosmos DB 的用于 MongoDB 的 API 进行连接和查询。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 8/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2aadb79f306165543c7b8e7c6935b9ff066681e4
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123307761"
---
# <a name="quickstart-build-a-net-web-api-using-azure-cosmos-dbs-api-for-mongodb"></a>快速入门：使用 Azure Cosmos DB API for MongoDB 生成 .NET Web API
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

本快速入门演示如何：
1. 创建 [Azure Cosmos DB API for MongoDB 帐户](mongodb-introduction.md) 
2. 使用 [MongoDB .NET 驱动程序](https://docs.mongodb.com/ecosystem/drivers/csharp/)生成产品目录 Web API
3. 导入示例数据

## <a name="prerequisites-to-run-the-sample-app"></a>运行示例应用的先决条件

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)
* 具有活动订阅的 Azure 帐户。 [免费创建 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 无需 Azure 订阅也可[试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，无需缴纳费用或承诺金。

如果没有 Visual Studio，请在使用安装程序安装 **ASP.NET 和 Web 开发** 工作负荷后，下载 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。

<a id="create-account"></a>
## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="learn-the-object-model"></a>了解对象模型

在继续生成应用程序之前，先研究 API for MongoDB 中资源的层次结构以及用于创建和访问这些资源的对象模型。 API for MongoDB 按以下顺序创建资源：

* Azure Cosmos DB API for MongoDB 帐户
* 数据库 
* 集合 
* 文档

若要详细了解实体的层次结构，请参阅 [Azure Cosmos DB 资源模型](../account-databases-containers-items.md)一文。

## <a name="install-the-sample-app-template"></a>安装示例应用模板

本示例是一个 dotnet 项目模板，可以安装该模板来创建本地副本。 在命令窗口中运行以下命令：

```bash
mkdir "C:\cosmos-samples"
cd "C:\cosmos-samples"
dotnet new -i Microsoft.Azure.Cosmos.Templates
dotnet new cosmosmongo-webapi
```

前面的命令：

1. 为示例创建 C:\cosmos-samples 目录。 选择适合你的操作系统的文件夹。
1. 将当前目录更改为 C:\cosmos-samples 文件夹。
1. 安装项目模板，使其从 dotnet CLI 全局可用。
1. 使用项目模板创建本地示例应用。

如果不想使用 dotnet CLI，也可以[下载 ZIP 文件格式的项目模板](https://github.com/Azure/azure-cosmos-dotnet-templates)。 此示例位于 `Templates/APIForMongoDBQuickstart-WebAPI` 文件夹中。

## <a name="review-the-code"></a>查看代码

可选择执行以下步骤。 如果有意了解如何使用代码创建数据库资源，请查看以下代码片段。 否则，请跳到[更新应用程序设置](#update-the-application-settings)。

### <a name="setup-connection"></a>设置连接

以下代码片段来自 Services/MongoService.cs 文件。

* 以下类表示客户端，并由 .NET Framework 注入到使用它的服务中：

    ```cs
        public class MongoService
        {
            private static MongoClient _client;

            public MongoService(IDatabaseSettings settings)
            {
                _client = new MongoClient(settings.MongoConnectionString);
            }

            public MongoClient GetClient()
            {
                return _client;
            }
        }
    ```

### <a name="setup-product-catalog-data-service"></a>设置产品目录数据服务

以下代码片段来自 Services/ProductService.cs 文件。

* 以下代码将检索数据库和集合，如果不存在，则进行创建：

    ```csharp
    private readonly IMongoCollection<Product> _products;        

    public ProductService(MongoService mongo, IDatabaseSettings settings)
    {
        var db = mongo.GetClient().GetDatabase(settings.DatabaseName);
        _products = db.GetCollection<Product>(settings.ProductCollectionName);
    }
    ```

* 以下代码将按唯一产品标识符 SKU 检索文档：

    ```csharp
    public Task<Product> GetBySkuAsync(string sku)
    {
        return _products.Find(p => p.Sku == sku).FirstOrDefaultAsync();
    }
    ```

* 以下代码将创建产品并将其插入到集合中：

    ```csharp
    public Task CreateAsync(Product product)
    {
        _products.InsertOneAsync(product);
    }
    ```

* 以下代码将查找并更新产品：

    ```csharp
    public Task<Product> UpdateAsync(Product update)
    {
        return _products.FindOneAndReplaceAsync(
            Builders<Product>.Filter.Eq(p => p.Sku, update.Sku), 
            update, 
            new FindOneAndReplaceOptions<Product> { ReturnDocument = ReturnDocument.After });
    }
    ```

    同样，可以使用 [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) 方法来删除文档。

## <a name="update-the-application-settings"></a>更新应用程序设置

从 Azure 门户复制连接字符串信息：

1. 在 [Azure 门户](https://portal.azure.com/)中选择 Cosmos DB 帐户，在左侧导航栏中，选择“连接字符串”，然后选择“读写密钥” 。 在后续步骤中，你将使用屏幕右侧的复制按钮将主连接字符串复制到 appsettings.json 文件中。

2. 打开 *appsettings.json* 文件。

3. 从门户中复制主连接字符串值（使用复制按钮），并将其设置为 appsettings.json 文件中 DatabaseSettings.MongoConnectionString 属性的值。

4. 查看 appsettings.json 文件的 DatabaseSettings.DatabaseName 属性中的“数据库名称”值。

5. 查看 appsettings.json 文件的 DatabaseSettings.ProductCollectionName 属性中的“集合名称”值。

> [!WARNING]
> 切勿将密码或其他敏感数据签入源代码。

现在，你已在应用中更新了全部所需信息，它可以与 Cosmos DB 进行通信。

## <a name="load-sample-data"></a>加载示例数据

[下载](https://www.mongodb.com/try/download/database-tools) [mongoimport](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport)，这是一种 CLI 工具，可轻松导入少量 JSON、CSV 或 TSV 数据。 我们将使用 mongoimport 加载此项目的 `Data` 文件夹中提供的示例产品数据。

从 Azure 门户中复制连接信息，并在下面的命令中输入该信息： 

```bash
mongoimport --host <HOST>:<PORT> -u <USERNAME> -p <PASSWORD> --db cosmicworks --collection products --ssl --jsonArray --writeConcern="{w:0}" --file Data/products.json
```

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure Cosmos DB API for MongoDB 帐户，在左侧导航栏中，选择“连接字符串”，然后选择“读写密钥” 。 

1. 从门户中复制“主机”值（使用复制按钮）并输入它来代替 **\<HOST\>** 。

1. 从门户中复制“端口”值（使用复制按钮）并输入它来代替 **\<PORT\>** 。

1. 从门户中复制“用户名”值（使用复制按钮）并输入它来代替 **\<USERNAME\>** 。

1. 从门户中复制“密码”值（使用复制按钮）并输入它来代替 **\<PASSWORD\>** 。

1. 查看“数据库名称”值，如果你创建的不是 `cosmicworks`，则更新该值。

1. 查看“集合名称”值，如果你创建的不是 `products`，则更新该值。

> [!Note]
> 若要跳过此步骤，可以使用作为此 Web API 项目的一部分提供的 POST 终结点创建具有正确架构的文档。

## <a name="run-the-app"></a>运行应用

在 Visual Studio 中，选择 CTRL + F5 运行应用。 默认浏览器随应用一起启动。

如果你喜欢使用 CLI，请在命令窗口中运行以下命令，以启动示例应用。 此命令还将安装项目依赖项并生成项目，但不会自动启动浏览器。

```bash
dotnet run
```

应用程序运行后，导航到 `https://localhost:5001/swagger/index.html`，以查看 Web API 的 [Swagger 文档](https://swagger.io/)并提交示例请求。

选择要测试的 API，然后选择“试用”。

:::image type="content" source="./media/create-mongodb-dotnet/try-swagger.png" alt-text="Web API Swagger UI 试用 API 终结点页面的屏幕截图。":::

输入任何必要的参数并选择“执行”。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 API for MongoDB 帐户、使用代码创建数据库和集合，以及运行 Web API 应用。 你现在可以向数据库导入更多数据。 

正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量计划。
* 如果你只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
* 如果你知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB Capacity Planner 估算请求单位](estimate-ru-capacity-planner.md)

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
