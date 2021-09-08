---
title: 使用 Robo 3T 连接到 Azure Cosmos DB
description: 了解如何使用 Robo 3T 和 Azure Cosmos DB 的用于 MongoDB 的 API 连接到 Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: bb7de61fc81a39fc6000f4adfac71a62c93aec41
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039613"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>将 Robo 3T 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

若要使用 Robo 3T 连接到 Cosmos 帐户，必须：

* 下载并安装 [Robo 3T](https://robomongo.org/)
* 具有 Cosmos DB [连接字符串](connect-mongodb-account.md)信息

> [!NOTE]
> 目前，Cosmos DB 的 API for MongoDB 支持 Robo 3T v1.2 及更低版本。

## <a name="connect-using-robo-3t"></a>使用 Robo 3T 进行连接

若要将你的 Cosmos 帐户添加到 Robo 3T 连接管理器，请执行以下步骤：

1. 按照[此处](connect-mongodb-account.md)的说明，检索使用 Azure Cosmos DB 的 API MongoDB 配置的 Cosmos 帐户的连接信息。

    :::image type="content" source="./media/connect-using-robomongo/connectionstringblade.png" alt-text="连接字符串边栏选项卡的屏幕截图":::
2. 运行 Robomongo  应用程序。

3. 单击“文件”  下的“连接”按钮以管理连接。 然后，在“MongoDB 连接”  窗口中单击“创建”  ，这会打开“连接设置”  窗口。

4. 在“连接设置”  窗口中，选择名称。 然后，从步骤 1 的连接信息中找到 **主机** 和 **端口**，并将其分别输入到“地址”  和“端口”  中。

    :::image type="content" source="./media/connect-using-robomongo/manageconnections.png" alt-text="Robomongo 管理连接的屏幕截图":::
5. 在“身份验证”  选项卡上，单击“执行身份验证”  。 然后，输入数据库（默认值为 Admin  ）、**用户名** 和 **密码**。
**用户名** 和 **密码** 可以在步骤 1 的连接信息中找到。

    :::image type="content" source="./media/connect-using-robomongo/authentication.png" alt-text="Robomongo 身份验证选项卡的屏幕截图":::
6. 在“SSL”  选项卡上，选中“使用 SSL 协议”  ，并将“身份验证方法”  更改为“自签名证书”  。

    :::image type="content" source="./media/connect-using-robomongo/ssl.png" alt-text="Robomongo SSL 选项卡的屏幕截图":::
7. 最后，单击“测试”  验证是否能够连接，并单击“保存”  。

## <a name="next-steps"></a>后续步骤

- 了解如何将 [Studio 3T](connect-using-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 通过 Azure Cosmos DB 的用于 MongoDB 的 API 来浏览 MongoDB [示例](nodejs-console-app.md)。
- 尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
    - 如果只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
    - 如果你知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量规划工具估算请求单位](estimate-ru-capacity-planner.md)