---
title: Azure Cosmos DB 中的 Azure 表存储支持
description: 了解 Azure Cosmos DB 表 API 和 Azure 存储表如何通过共享相同的表数据模型和操作来协同工作
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 01/08/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: c25a18516fb654dc211f532d7f9f735d0b739bc8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778352"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>使用 Azure 表存储 API 和 Azure Cosmos DB 进行开发
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Azure Cosmos DB 表 API 和 Azure 表存储通过其 SDK 共享相同的表数据模型，并公开相同的创建、删除、更新和查询操作。

> [!NOTE]
> [无服务器容量模式](../serverless.md)现在在 Azure Cosmos DB 的表 API 上可用。

[!INCLUDE [storage-table-cosmos-comparison](../../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>使用 Azure Cosmos DB 表 API 进行开发

当前，[Azure Cosmos DB 表 API](introduction.md) 具有四个可用于进行开发的 SDK： 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. 此库面向 .NET Standard，不仅与公共 [Microsoft Azure 存储 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 具有相同的类和方法签名，而且还可以使用表 API 连接到 Azure Cosmos DB 帐户。 建议 .NET Framework 库 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) 的用户升级到 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)，因为它处于维护模式并且很快将被弃用。

* [Python SDK](python-sdk.md)：新的 Azure Cosmos DB Python SDK 是在 Python 中唯一支持 Azure 表存储的 SDK。 此 SDK 与 Azure 表存储和 Azure Cosmos DB 表 API 连接。

* [Java SDK](java-sdk.md)：此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。

* [Node.js SDK](nodejs-sdk.md)：此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。


有关使用表 API 的其他信息可在[常见问题解答：使用表 API 进行开发](table-api-faq.yml)一文中找到。

## <a name="developing-with-azure-table-storage"></a>使用 Azure 表存储进行开发

Azure 表存储的以下 SDK 可用于开发：

- 通过 [Microsoft.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)[Microsoft.Azure.Storage.File](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)[Microsoft.Azure.Storage.Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/) 和 [Microsoft.Azure.Storage.Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) 库可以使用 Azure 表存储服务。 若要 Azure Cosmos DB 中使用表 API，则可以改为使用 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) 库。
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python)。 用于 Python 的 Azure Cosmos DB 表 SDK 支持表存储服务（由于 Azure 表存储和 Cosmos DB 的表 API 共享相同的特性和功能，因此为了分解我们的 SDK 开发工作，我们建议使用此 SDK）。
- [用于 Java 的 Azure 存储 SDK](https://github.com/azure/azure-storage-java)。 此 Azure 存储 SDK 提供了一个 Java 客户端库来使用 Azure 表存储。
- [Node.js SDK](https://github.com/Azure/azure-storage-node)。 此 SDK 提供了一个 Node.js 包和一个浏览器兼容的 JavaScript 客户端库来使用存储表服务。
- [AzureRmStorageTable PowerShell 模块](https://www.powershellgallery.com/packages/AzureRmStorageTable)。 此 PowerShell 模块包含 cmdlet 以使用存储表。
- [适用于 C++ 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-cpp/)。 此库使你能够针对 Azure 存储构建应用程序。
- [适用于 Ruby 的 Azure 存储表客户端库](https://github.com/azure/azure-storage-ruby/tree/master/table)。 此项目提供了一个 Ruby 包，使用该包可轻松访问 Azure 存储表服务。
- [Azure 存储表 PHP 客户端库](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)。 此项目提供了一个 PHP 客户端库，使用该库可轻松访问 Azure 存储表服务。


   





