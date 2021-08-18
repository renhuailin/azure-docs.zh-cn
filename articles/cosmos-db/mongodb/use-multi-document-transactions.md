---
title: 在用于 MongoDB 的 Azure Cosmos DB API 中使用多文档事务
description: 了解如何创建 Mongo shell 应用示例，以在用于 MongoDB 4.0 的 Azure Cosmos DB API 中对固定集合执行多文档事务（全语义或无语义）。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: 547b305ed7c6a63b6512fb4d2bbf9d0a7d1b2012
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777864"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>在用于 MongoDB 的 Azure Cosmos DB API 中使用多文档事务
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

在本文中，你将创建 Mongo shell 应用，其可在用于服务器版本 4.0 的 MongoDB 的 Azure Cosmos DB API 中对固定集合执行多文档事务。

## <a name="what-are-multi-document-transactions"></a>什么是多文档事务？

在用于 MongoDB 的 Azure Cosmos DB API 中，对单个文档执行的操作是原子操作。 多文档事务使应用程序可以跨多个文档执行原子操作。 它为操作提供“全或无”语义。 提交时，会保留事务内所做的更改，如果事务失败，则会放弃事务中的所有更改。

多文档事务遵循 ACID 语义：

* 原子性：将所有操作视为一个操作
* 一致性：提交的数据有效
* 隔离：与其他操作隔离
* 持续性：客户端按指示保留事务数据

## <a name="requirements"></a>要求

版本 4.0 的 API 中的未分片集合支持多文档事务。 版本 4.0 中的跨集合或分片集合不支持多文档事务。 事务超时固定为 5 秒。

支持线路协议版本 4.0 或更高版本的所有驱动程序都将支持用于 MongoDB 的 Azure Cosmos DB API 的多文档事务。

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>在 MongoDB shell 中运行多文档事务
> [!Note]
> 此示例不适用于 MongoDB 指南针中嵌入的 MongoSH beta (shell)。

1. 打开命令提示符，转到安装了 Mongo shell 版本 4.0 和更高版本的目录：

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. 创建 mongo shell 脚本 connect_friends.js 并添加以下内容

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. 运行以下命令来执行多文档事务。 可以在 Azure 门户中找到主机、端口、用户和密钥。

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>后续步骤

探索[用于 MongoDB 4.0 的 Azure Cosmos DB API](feature-support-40.md) 中的新增功能
