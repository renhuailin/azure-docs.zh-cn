---
title: Azure Cosmos DB 中的 Azure 表存储支持
description: 了解 Azure Cosmos DB 表 API 和 Azure 存储表如何通过共享相同的表数据模型和操作来协同工作
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 08/25/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: f2d337f7154e2ae9412930aab9720a47bde4768e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124762030"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>使用 Azure 表存储 API 和 Azure Cosmos DB 进行开发
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Azure Cosmos DB 表 API 和 Azure 表存储通过其 SDK 共享相同的表数据模型，并公开相同的创建、删除、更新和查询操作。

> [!NOTE]
> [无服务器容量模式](../serverless.md)现在在 Azure Cosmos DB 的表 API 上可用。

[!INCLUDE [storage-table-cosmos-comparison](../../../includes/storage-table-cosmos-comparison.md)]

## <a name="azure-sdks"></a>Azure SDK

### <a name="current-release"></a>当前版本

以下 SDK 包同时用于 Azure Cosmos 表 API 和 Azure 表存储。

* .NET - 使用 NuGet 上提供的 [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/)。

* Python - 使用 PyPi 中的 [azure-data-tables](https://pypi.org/project/azure-data-tables/)。

* JavaScript/TypeScript - 使用 npm.js 中的 [@azure/data-tables](https://www.npmjs.com/package/@azure/data-tables) 包。  

* Java - 使用 Maven 中的 [azure-data-tables](https://mvnrepository.com/artifact/com.azure/azure-data-tables/12.0.0) 包。

### <a name="prior-releases"></a>以前的版本

以下 SDK 包仅适用于 Azure Cosmos DB 表 API。

* NuGet 上的 .NET - [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)。  此库仅适用于 Cosmos DB 表 API。

* PyPi 中的 Python - [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/)。 此 SDK 与 Azure 表存储和 Azure Cosmos DB 表 API 连接。

* npm.js 中的 JavaScript/TypeScript - [azure-storage](https://www.npmjs.com/package/azure-storage) 包。 此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。

* **Java**  -  Maven 上的[用于 Java 的 Microsoft Azure 存储客户端 SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)。 此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。

* **C++**   -  [用于 C++ 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-cpp/)。 此库使你能够针对 Azure 存储构建应用程序。

* **Ruby**  -  [用于 Ruby 的 Azure 存储表客户端](https://github.com/azure/azure-storage-ruby/tree/master/table)。 此项目提供了一个 Ruby 包，使用该包可轻松访问 Azure 存储表服务。

* **PHP** - [Azure 存储表 PHP 客户端库](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)。 此项目提供了一个 PHP 客户端库，使用该库可轻松访问 Azure 存储表服务。

* **PowerShell** - [AzureRmStorageTable PowerShell 模块](https://www.powershellgallery.com/packages/AzureRmStorageTable)。 此 PowerShell 模块包含 cmdlet 以使用存储表。
