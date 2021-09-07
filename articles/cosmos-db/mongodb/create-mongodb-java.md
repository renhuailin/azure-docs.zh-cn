---
title: 快速入门：使用 Azure Cosmos DB API for Mongo DB 和 Java SDK 构建 Web 应用
description: 了解如何构建 Java 代码示例，可以参考该示例使用 Azure Cosmos DB 的 API for MongoDB 进行连接和查询。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 08/26/2021
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 51b6c1a6e480b8fdd383a970936e6b140aafd09d
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037678"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>快速入门：在 Azure Cosmos DB 中使用 Java 和 MongoDB API 创建控制台应用
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

在本快速入门中，你将通过 Azure 门户创建和管理 Azure Cosmos DB for MongoDB API 帐户，并使用从 GitHub 克隆的 Java SDK 应用来添加数据。 Azure Cosmos DB 是一种多模型数据库服务，你可以借助其全球分布和水平缩放功能快速创建和查询文档、表、键/值和图数据库。

## <a name="prerequisites"></a>先决条件
- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或者[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 而无需 Azure 订阅。 你还可以将 [Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator)与连接字符串 `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` 配合使用。
- [Java 开发工具包 (JDK) 版本 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)。 
- [Maven](https://maven.apache.org/download.cgi)。 或者运行 `apt-get install maven` 来安装 Maven。
- [Git](https://git-scm.com/downloads)。 

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [mongodb-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>添加集合

将新数据库命名为 **db**，将新集合命名为 **coll**。

[!INCLUDE [cosmos-db-create-collection](../includes/cosmos-db-mongodb-create-collection.md)] 

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 GitHub 克隆应用，设置连接字符串，然后运行该应用。 会看到以编程方式处理数据是多么容易。 

1. 打开命令提示符，新建一个名为“git-samples”的文件夹，然后关闭命令提示符。

    ```bash
    md "C:\git-samples"
    ```

2. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为要安装示例应用的新文件夹。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令，克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 然后在偏好的编辑器中打开代码。 

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何使用代码创建数据库资源，可以查看以下代码片段。 否则，可以直接跳转到[更新连接字符串](#update-your-connection-string)。 

以下代码片段全部摘自 *Program.java* 文件。

此控制台应用使用 [MongoDB Java 驱动程序](https://docs.mongodb.com/ecosystem/drivers/java/)。 

* 此时会对 DocumentClient 进行初始化。

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 会创建新数据库和集合。

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* 使用 `MongoCollection.insertOne` 插入一些文档

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* 使用 `MongoCollection.find` 执行一些查询

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 从你的 Azure Cosmos DB 帐户中，选择“快速启动”，选择“Java”，然后将连接字符串复制到剪贴板。

2. 打开 *Program.java* 文件，将 MongoClientURI 构造函数的参数替换为该连接字符串。 现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 
    
## <a name="run-the-console-app"></a>运行控制台应用

1. 在终端中运行 `mvn package`，安装所需的 npm 模块

2. 在终端中运行 `mvn exec:java -D exec.mainClass=GetStarted.Program`，启动 Java 应用程序。

现在可以使用 [Robomongo](connect-using-robomongo.md) / [Studio 3T](connect-using-mongochef.md) 来查询、修改和处理此新数据。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解了如何创建 Azure Cosmos DB API for Mongo DB 帐户，使用数据资源管理器创建数据库和容器，以及使用 Java 控制台应用来添加数据。 现在可以向你的 Cosmos 数据库导入更多数据。 

尝试为迁移到 Azure Cosmos DB 进行容量规划？ 可以使用有关现有数据库群集的信息进行容量规划。
* 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
* 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-capacity-planner.md)

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)