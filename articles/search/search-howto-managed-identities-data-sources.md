---
title: 使用托管标识设置到数据源的连接
titleSuffix: Azure Cognitive Search
description: 了解如何使用托管标识设置到数据源的索引器连接
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: bd4d10c32f1c850adf6dc886672b16937b553222
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179396"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>使用托管标识设置到数据源的索引器连接

> [!IMPORTANT] 
> 免费 Azure 认知搜索层不支持使用托管标识设置到数据源的连接。

Azure 认知搜索中的[索引器](search-indexer-overview.md)是一种爬网程序，它提供了一种方法，用于将数据源中的数据提取到 Azure 认知搜索中。 索引器从你创建的数据源对象获取数据源连接。 数据源对象通常包含目标数据源的凭据。 例如，如果要为 blob 存储容器中的数据编制索引，数据源对象可能包含 Azure 存储帐户密钥。

在许多情况下，直接在数据源对象中提供凭据并不是问题，但可能会存在一些挑战：
* 如何在创建数据源对象的代码中确保凭据的安全性？
* 如果我的帐户密钥或密码遭到泄漏，我需要对其进行更改，那么我现在需要用新的帐户密钥或密码更新我的数据源对象，以便我的索引器可以再次连接到数据源。

可以通过使用托管标识设置连接来解决这些问题。

## <a name="using-managed-identities"></a>使用托管标识

[托管标识](../active-directory/managed-identities-azure-resources/overview.md)功能可在 Azure Active Directory (Azure AD) 中为应用程序提供一个自动托管标识。 可以在 Azure 认知搜索中使用此功能来创建一个具有不包含任何凭据的连接字符串的数据源对象。 这样，将通过基于 Azure 角色的访问控制 (Azure RBAC) 向搜索服务授予对数据源的访问权限。

使用托管标识设置数据源时，可以更改数据源凭据，而索引器仍可以连接到数据源。 还可以在代码中创建数据源对象，而无需包括帐户密钥，也无需使用 Key Vault 来检索帐户密钥。

有两种类型的托管标识。 Azure 认知搜索支持系统分配的托管标识和用户分配的托管标识。

### <a name="system-assigned-managed-identity"></a>系统分配的托管标识

[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)可以分配给单个 Azure 服务。 可以将系统分配的托管标识分配给单个 Azure 认知搜索服务，此标识与该搜索服务的生命周期相关联。

### <a name="user-assigned-managed-identity-preview"></a>用户分配的托管标识（预览版）

> [!IMPORTANT]
>根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，此功能为公共预览版。 REST API 版本 2021-04-30-Preview 和[管理 REST API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) 提供此功能。

[用户分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)是一种独立的 Azure 资源，可以分配给一个或多个 Azure 服务。 单个 Azure 认知搜索服务可以分配有一个或多个用户分配的托管标识。 单个用户分配的托管标识可以分配给多个搜索服务。

## <a name="limitations"></a>限制

以下数据源支持使用托管标识设置索引器连接。 

* [Azure Blob 存储、Azure Data Lake Storage Gen2（预览版）、Azure 表存储](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL 数据库](search-howto-managed-identities-sql.md)

以下功能当前不支持使用托管标识设置连接：
* 知识存储
* 自定义技能
 
## <a name="next-steps"></a>后续步骤

详细了解如何使用托管标识设置索引器连接：

* [Azure Blob 存储、Azure Data Lake Storage Gen2（预览版）、Azure 表存储](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL 数据库](search-howto-managed-identities-sql.md)