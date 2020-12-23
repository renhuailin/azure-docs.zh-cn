---
title: 快速入门：将 Cassandra API 与 Node.js 配合使用 - Azure Cosmos DB
description: 本快速入门介绍如何配合 Node.js 使用 Azure Cosmos DB Cassandra API 创建配置文件应用程序
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: b9e036df91eecadc701664a19905a92c142b7585
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591865"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>快速入门：使用 Node.js SDK 和 Azure Cosmos DB 构建 Cassandra 应用
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

在本快速入门中，你将创建一个 Azure Cosmos DB Cassandra API 帐户，并使用从 GitHub 克隆的 Cassandra Node.js 应用创建一个 Cassandra 数据库和一个容器。 Azure Cosmos DB 是一种多模型数据库服务，你可以借助其全球分布和水平缩放功能快速创建和查询文档、表、键/值和图数据库。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]或者，无需 Azure 订阅即可[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，也无需缴纳费用或承诺金。

此外，还需要：
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) 版本 v0.10.29 或更高版本
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>创建数据库帐户

在创建文档数据库之前，需通过 Azure Cosmos DB 创建 Cassandra 帐户。

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在从 GitHub 克隆 Cassandra API 应用，设置连接字符串，并运行应用。 会看到以编程方式处理数据是多么容易。 

1. 打开命令提示符。 创建名为 `git-samples` 的新文件夹。 然后，关闭命令提示符。

    ```bash
    md "C:\git-samples"
    ```

2. 打开 git 终端窗口（例如 git bash）。 使用 `cd` 命令更改为要安装示例应用的新文件夹。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何通过代码创建数据库资源，可以查看以下代码片段。 这些代码片段全部摘自 `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started` 文件夹中 `uprofile.js` 文件。 否则，可以直接跳转到[更新连接字符串](#update-your-connection-string)。 

* 用户名和密码值是使用 Azure 门户中的连接字符串页设置的。 `path\to\cert` 提供 X509 证书的路径。 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* 使用 contactPoint 信息初始化 `client`。 从 Azure 门户中检索 contactPoint。

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* `client` 连接到 Azure Cosmos DB Cassandra API。

    ```javascript
    client.connect(next);
    ```

* 创建新的键空间。

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* 创建新表。

   ```javascript
   function createTable(next) {
       var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* 插入键/值实体。

    ```javascript
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* 用于获取所有键值的查询。

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  

* 用于获取键-值的查询。

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        }
    ```  

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。 连接字符串使应用能与托管数据库进行通信。

1. 在 [Azure 门户](https://portal.azure.com/)的 Azure Cosmos DB 帐户中，选择“连接字符串”。 

1. 使用屏幕右侧的 :::image type="icon" source="./media/create-cassandra-nodejs/copy.png"::: 按钮复制最上面的值“联系点”。

    :::image type="content" source="./media/create-cassandra-nodejs/keys.png" alt-text="在 Azure 门户的连接字符串页中查看并复制“联系点”、“用户名”和“密码”":::

1. 打开 `config.js` 文件。 

1. 粘贴门户中的“联系点”值，并覆盖第 4 行中的 `<FillMEIN>`。

    第 4 行现在应如下所示 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

1. 复制并粘贴门户中的“用户名”值，并覆盖第 2 行中的 `<FillMEIN>`。

    第 2 行现在应如下所示 

    `config.username = 'cosmos-db-quickstart';`

1. 复制并粘贴门户中的“密码”值，并覆盖第 3 行中的 `<FillMEIN>`。

    第 3 行现在应如下所示

    `config.password = '2Ggkr662ifxz2Mg==';`

1. 保存 `config.js` 文件。

## <a name="use-the-x509-certificate"></a>使用 X509 证书

1. 从 [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt) 在本地下载 Baltimore CyberTrust 根证书。 使用文件扩展名 `.cer` 重命名该文件。

   证书的序列号为 `02:00:00:b9`，SHA1 指纹为 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`。

2. 打开 `uprofile.js` 并更改 `path\to\cert` 以指向新证书。

3. 保存 `uprofile.js`。

> [!NOTE]
> 如果在后面的步骤中遇到与证书相关的错误，并且是在 Windows 计算机上运行，请确保已按照相关过程将 .crt 文件正确转换为以下 Microsoft .cer 格式。
> 
> 双击 .crt 文件，将其打开到证书显示。 
>
> :::image type="content" source="./media/create-cassandra-nodejs/crtcer1.gif" alt-text="显示“证书”窗口的屏幕截图。":::
>
> 在证书向导中按“下一步”。 选择“Base-64 编码 X.509 (.CER)”，然后选择“下一步”。
>
> :::image type="content" source="./media/create-cassandra-nodejs/crtcer2.gif" alt-text="显示“Base-64 编码 X.509 (.CER)”选项的屏幕截图。":::
>
> 选择“浏览”（目的是查找目标），然后键入文件名。
> 完成后，选择“下一步”。
>
> 现在的 .cer 文件应该格式正确。 确保 `uprofile.js` 中的路径指向此文件。

## <a name="run-the-nodejs-app"></a>运行 Node.js 应用

1. 在 git 终端窗口中，确保你位于此前克隆的示例目录中：

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. 运行 `npm install`，安装所需的 npm 模块。

3. 运行 `node uprofile.js` 启动 node 应用程序。

4. 通过命令行验证结果是否符合预期。

    :::image type="content" source="./media/create-cassandra-nodejs/output.png" alt-text="查看并验证输出":::

    按 CTRL+C 停止执行程序并关闭控制台窗口。 

5. 在 Azure 门户中，打开数据资源管理器，以查询、修改和处理这些新数据。 

    :::image type="content" source="./media/create-cassandra-nodejs/data-explorer.png" alt-text="在数据资源管理器中查看数据"::: 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Cassandra API 创建 Azure Cosmos DB 帐户，以及如何运行用于创建 Cassandra 数据库和容器的 Cassandra Node.js 应用。 现在可以将其他数据导入 Azure Cosmos DB 帐户了。 

> [!div class="nextstepaction"]
> [将 Cassandra 数据导入 Azure Cosmos DB](cassandra-import-data.md)