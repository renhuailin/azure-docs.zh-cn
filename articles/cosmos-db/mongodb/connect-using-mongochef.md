---
title: 使用 Studio 3T 连接到 Azure Cosmos DB 的 API for MongoDB
description: 了解如何使用 Studio 3T 连接到 Azure Cosmos DB 的 API for MongoDB。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: seodec18
ms.openlocfilehash: aab468bbc53eec0d6caacc02956b58080f430031
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029307"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>使用 Studio 3T 连接到 Azure Cosmos 帐户
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

若要使用 Studio 3T 连接到 Azure Cosmos DB 的 API for MongoDB，必须执行以下操作：

* 下载并安装 [Studio 3T](https://studio3t.com/)。
* 获取 Azure Cosmos 帐户的[连接字符串](connect-mongodb-account.md)信息。

## <a name="create-the-connection-in-studio-3t"></a>在 Studio 3T 中创建连接

若要将 Azure Cosmos 帐户添加到 Studio 3T 连接管理器，请执行以下步骤：

1. 按照[将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md) 一文中的说明，检索 Azure Cosmos DB 的 API for MongoDB 帐户的连接信息。

    :::image type="content" source="./media/connect-using-mongochef/connection-string-blade.png" alt-text="连接字符串页的屏幕截图":::

2. 单击“连接”  以打开“连接管理器”，并单击“新建连接” 

    :::image type="content" source="./media/connect-using-mongochef/connection-manager.png" alt-text="Studio 3T 连接管理器的屏幕截图，其中突出显示了“新建连接”按钮。":::
3. 在“新建连接”窗口中的“服务器”选项卡上，输入 Azure Cosmos 帐户的主机 (FQDN) 和端口   。

    :::image type="content" source="./media/connect-using-mongochef/connection-manager-server-tab.png" alt-text="Studio 3T 连接管理器服务器选项卡的屏幕截图":::
4. 在“新建连接”窗口中的“身份验证”选项卡上，选择“基本(MONGODB-CR 或 SCRAM-SHA-1)”身份验证模式，并输入用户名和密码。  接受默认的身份验证数据库（管理员），或提供自己的值。

    :::image type="content" source="./media/connect-using-mongochef/connection-manager-authentication-tab.png" alt-text="Studio 3T 连接管理器身份验证选项卡的屏幕截图":::
5. 在“新建连接”  窗口的“SSL”  选项卡上，选中“使用 SSL 协议进行连接”  复选框和“接受服务器自签名 SSL 证书”  单选按钮。

    :::image type="content" source="./media/connect-using-mongochef/connection-manager-ssl-tab.png" alt-text="Studio 3T 连接管理器 SSL 选项卡的屏幕截图":::
6. 单击“测试连接”  按钮以验证连接信息，单击“确定”  以返回到“新建连接”窗口，并单击“保存”  。

    :::image type="content" source="./media/connect-using-mongochef/test-connection-results.png" alt-text="Studio 3T 测试连接窗口的屏幕截图":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>使用 Studio 3T 创建数据库、集合和文档
若要使用 Studio 3T 创建数据库、集合和文档，请执行以下步骤：

1. 在“连接管理器”  中突出显示连接，并单击“连接”  。

    :::image type="content" source="./media/connect-using-mongochef/connect-account.png" alt-text="Studio 3T 连接管理器的屏幕截图":::
2. 右键单击主机，并选择“添加数据库”  。  提供数据库名称，并单击“确定”  。

    :::image type="content" source="./media/connect-using-mongochef/add-database.png" alt-text="Studio 3T“添加数据库”选项的屏幕截图":::
3. 右键单击数据库，并选择“添加集合”  。  提供集合名称，并单击“创建”  。

    :::image type="content" source="./media/connect-using-mongochef/add-collection.png" alt-text="Studio 3T“添加集合”选项的屏幕截图":::
4. 单击“集合”  菜单项，并单击“添加文档”  。

    :::image type="content" source="./media/connect-using-mongochef/add-document.png" alt-text="Studio 3T“添加文档”菜单项的屏幕截图":::
5. 在“添加文档”对话框中粘贴以下内容，并单击“添加文档”  。

    ```json
    {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
            { "firstName": "Thomas" },
            { "firstName": "Mary Kay"}
        ],
        "children": [
            {
                "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
                "pets": [{ "givenName": "Fluffy" }]
            }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }
    ```
    
6. 添加另一个文档，但这次使用以下内容：

    ```json
    {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                "givenName": "Lisa",
                "gender": "female",
                "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    }
    ```

7. 执行示例查询。 例如，搜索姓氏为“Andersen”的家庭，并返回父母和州/省字段。

    :::image type="content" source="./media/connect-using-mongochef/query-document.png" alt-text="Mongo Chef 查询结果的屏幕截图":::

## <a name="next-steps"></a>后续步骤

- 了解如何将 [Robo 3T](connect-using-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 通过 Azure Cosmos DB 的用于 MongoDB 的 API 来浏览 MongoDB [示例](nodejs-console-app.md)。
- 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量计划。
    - 若只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
    - 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-capacity-planner.md)