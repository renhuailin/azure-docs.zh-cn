---
title: 使用 Azure Cosmos DB 的用于 MongoDB 的 API 和 .NET SDK 生成 Web 应用
description: 演示一个 .NET 代码示例，可以参考该示例使用 Azure Cosmos DB 的用于 MongoDB 的 API 进行连接和查询。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 77e7b4feedc6d6d9fcc7dce589db3e75ce0bc180
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354904"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>快速入门：使用 Azure Cosmos DB 的 API for MongoDB 构建 .NET Web 应用 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB 是 Microsoft 的快速 NoSQL 数据库，具有适合于任何规模的开放式 API。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门演示如何使用 [Azure Cosmos DB 的用于 MongoDB 的 API](mongodb-introduction.md) 创建 Cosmos 帐户。 然后生成并部署通过 [MongoDB .NET 驱动程序](https://docs.mongodb.com/ecosystem/drivers/csharp/)生成的任务列表 Web 应用。

## <a name="prerequisites-to-run-the-sample-app"></a>运行示例应用的先决条件

* [Visual Studio](https://www.visualstudio.com/downloads/)
* 一个 Azure Cosmos DB 帐户。

如果没有 Visual Studio，请在使用安装程序安装 **ASP.NET 和 Web 开发** 工作负荷后，下载 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount-mongodb.md)]

本文中所述的示例与 MongoDB.Driver 版本 2.6.1 兼容。

## <a name="clone-the-sample-app"></a>克隆示例应用

在启用 GitHub 的命令窗口（如 [Git bash](https://git-scm.com/downloads)）中运行以下命令：

```bash
mkdir "C:\git-samples"
cd "C:\git-samples"
git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
```

前面的命令：

1. 为示例创建 C:\git-samples 目录。 选择适合你的操作系统的文件夹。
1. 将当前目录更改为 C:\git-samples 文件夹。
1. 将示例克隆到 C:\git-samples 文件夹中。

如果不想使用 git，也可以[下载 ZIP 文件格式的项目](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip)。

## <a name="review-the-code"></a>查看代码

1. 在 Visual Studio 中，右键单击解决方案资源管理器中的项目，并单击“管理 NuGet 包”。
1. 在 NuGet“浏览”框中，键入 *MongoDB.Driver*。
1. 从结果中安装“MongoDB.Driver”库。 这会安装 MongoDB.Driver 包以及所有依赖项。

可选择执行以下步骤。 如果有意了解如何使用代码创建数据库资源，请查看以下代码片段。 否则，可直接跳转到[更新连接字符串](#update-the-connection-string)。

以下代码片段摘自 DAL/Dal.cs 文件。

* 以下代码会初始化客户端：

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* 以下代码会检索数据库和集合：

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* 以下代码会检索所有文档：

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

以下代码会创建任务并将其插入到集合中：

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   同样，可以使用 [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) 和 [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) 方法更新和删除文档。

## <a name="update-the-connection-string"></a>更新连接字符串

从 Azure 门户复制连接字符串信息：

1. 在 [Azure 门户](https://portal.azure.com/)上选择 Cosmos 帐户，在左侧导航栏中单击“连接字符串”，然后单击“读写密钥” 。 使用屏幕右侧的复制按钮将用户名、密码和主机复制到下一步的 Dal.cs 文件中。

2. 打开 DAL/Dal.cs 文件。

3. 从门户中使用复制按钮复制“用户名”值，并在 Dal.cs 文件中将其设为“用户名”的值  。

4. 从门户复制“主机”值，并在 Dal.cs 文件中将其设为“主机”的值  。

5. 从门户复制“密码”值，并在 Dal.cs 文件中将其设为“密码”的值  。

<!-- TODO Store PW correctly-->
> [!WARNING]
> 切勿将密码或其他敏感数据签入源代码。

现在，你已在应用中更新了全部所需信息，它可以与 Cosmos DB 进行通信。

## <a name="run-the-web-app"></a>运行 Web 应用

1. 单击 Ctrl + F5 以运行应用。 默认浏览器随应用一起启动。 
1. 在浏览器中单击“创建”，并在“任务列表”应用中创建一些新任务。

<!-- 
## Deploy the app to Azure 
1. In VS, right click .. publish
2. This is so easy, why is this critical step missed?
-->
## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何创建 Cosmos 帐户、创建集合和运行控制台应用。 现在可以向你的 Cosmos 数据库导入更多数据。 

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
