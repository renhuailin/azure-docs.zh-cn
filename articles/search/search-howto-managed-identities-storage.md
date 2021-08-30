---
title: 使用托管标识设置到存储帐户的连接
titleSuffix: Azure Cognitive Search
description: 了解如何使用托管标识设置到 Azure 存储帐户的索引器连接
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 7dd06e48d6d610b99f6c52affcd1d6101e04c9ba
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183983"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>使用托管标识设置到 Azure 存储帐户的连接

本页介绍如何使用托管标识设置到 Azure 存储帐户的索引器连接，而不是在数据源对象连接字符串中提供凭据。

可以使用系统分配的托管标识或用户分配的托管标识（预览版）。

在详细了解此功能之前，建议先了解什么是索引器以及如何为数据源设置索引器。 有关详细信息，请访问以下链接：
* [索引器概述](search-indexer-overview.md)
* [Azure Blob 索引器](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 索引器](search-howto-index-azure-data-lake-storage.md)
* [Azure 表索引器](search-howto-indexing-azure-tables.md)

## <a name="1---set-up-a-managed-identity"></a>1 - 设置托管标识

使用以下某个选项设置[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="option-1---turn-on-system-assigned-managed-identity"></a>选项 1 - 打开系统分配的托管标识

启用系统分配的托管标识后，Azure 将为搜索服务创建一个标识，该标识可用于对同一租户和订阅中的其他 Azure 服务进行身份验证。 然后，可以在基于角色的访问控制 (Azure RBAC) 分配中使用此标识，该分配允许在编制索引期间访问数据。

![打开系统分配的托管标识](./media/search-managed-identities/turn-on-system-assigned-identity.png "打开系统分配的托管标识")

选择“保存”后，将看到已分配给搜索服务的对象 ID。

![对象 ID](./media/search-managed-identities/system-assigned-identity-object-id.png "对象 ID")
 
### <a name="option-2---assign-a-user-assigned-managed-identity-to-the-search-service-preview"></a>选项 2 - 将用户分配的托管标识分配给搜索服务（预览版）

如果尚未创建用户分配的托管标识，则需要创建一个。 用户分配的托管标识是 Azure 上的一种资源。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 选择“+ 创建资源”。
1. 在“搜索服务和市场”搜索栏中，搜索“用户分配的托管标识”，然后选择“创建”。
1. 为标识提供描述性名称。

接下来，将用户分配的托管标识分配给搜索服务。 这可以使用 [2021-04-01-preview 管理 API](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) 来完成。

标识属性包含类型以及一个或多个完全限定的用户分配标识：

* 类型是标识类型。 如果要想同时使用两者，则有效值为“SystemAssigned”、“UserAssigned”或“SystemAssigned、UserAssigned”。 如果值为“None”，将从搜索服务中清除任何先前分配的标识。
* userAssignedIdentities 包括用户分配的托管标识的详细信息。
    * 用户分配的托管标识的格式： 
        * /subscriptions/订阅 ID/resourcegroups/资源组名称/providers/Microsoft.ManagedIdentity/userAssignedIdentities/托管标识名称  

关于如何将用户分配的托管标识分配给搜索服务的示例：

```http
PUT https://management.azure.com/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
Content-Type: application/json

{
  "location": "[region]",
  "sku": {
    "name": "[sku]"
  },
  "properties": {
    "replicaCount": [replica count],
    "partitionCount": [partition count],
    "hostingMode": "default"
  },
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[name of managed identity]": {}
    }
  }
} 
```

## <a name="2---add-a-role-assignment"></a>2 - 添加角色分配

在此步骤中，你将授予 Azure 认知搜索服务或用户分配的托管标识从存储帐户读取数据的权限。

1. 在 Azure 门户中，导航到包含要为其编制索引的数据的存储帐户。
2. 选择“访问控制(标识和访问管理)”
3. 依次选择“添加”和“添加角色分配” 

    ![添加角色分配](./media/search-managed-identities/add-role-assignment-storage.png "添加角色分配")

4. 根据要为其编制索引的存储帐户类型选择适当的角色：
    1. Azure Blob 存储要求将搜索服务添加到“存储 Blob 数据读取者”角色。
    1. Azure Data Lake Storage Gen2 要求将搜索服务添加到“存储 Blob 数据读取器”角色。
    1. Azure 表存储要求将搜索服务添加到“读取者和数据访问”角色。
5.  将“分配访问权限至”保留为“Azure AD 用户、组或服务主体” 
6.  如果使用的是系统分配的托管标识，请搜索你的搜索服务，然后选择它。 如果使用的是用户分配的托管标识，请搜索用户分配的托管标识的名称，然后选择它。 选择“保存”。

    关于使用系统分配的托管标识的 Azure Blob 存储和 Azure Data Lake Storage Gen2 的示例：

    ![添加存储 Blob 数据读取器角色分配](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "添加存储 Blob 数据读取器角色分配")

    关于使用系统分配的托管标识的 Azure 表存储的示例：

    ![添加读者和数据访问角色分配](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "添加读者和数据访问角色分配")

## <a name="3---create-the-data-source"></a>3 - 创建数据源

创建数据源并提供系统分配的托管标识或用户分配的托管标识（预览版）。 请注意，在以下步骤中不再使用管理 REST API。

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>选项 1 - 使用系统分配的托管标识创建数据源

[REST API](/rest/api/searchservice/create-data-source)、Azure 门户和 [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) 支持使用系统分配的托管标识。 下面的示例演示如何使用 [REST API](/rest/api/searchservice/create-data-source) 和托管标识连接字符串创建数据源以索引存储帐户的数据。 对于 REST API、.NET SDK 和 Azure 门户，托管标识连接字符串格式是相同的。

从存储帐户编制索引时，数据源必须具有以下必需属性：

* **name** 是搜索服务中数据源的唯一名称。
* type
    * Azure Blob 存储：`azureblob`
    * Azure 表存储：`azuretable`
    * Azure Data Lake Storage Gen2：`adlsgen2`
* **凭据**
    * 使用托管标识进行身份验证时，凭据格式与不使用托管标识时不同。 此时，需要提供没有帐户密钥或密码的 ResourceId。 ResourceId 必须包含存储帐户的订阅 ID、存储帐户的资源组和存储帐户名称。
    * 托管标识格式： 
        * ResourceId=/subscriptions/你的订阅 ID/resourceGroups/你的资源组名称/providers/Microsoft.Storage/storageAccounts/你的存储帐户名称/;**
* container 指定存储帐户中的容器或表名称。 默认情况下，容器中的所有 Blob 都可检索。 如果只想为特定虚拟目录中的 Blob 编制索引，可以使用可选的 **query** 参数指定该目录。

如何使用 [REST API](/rest/api/searchservice/create-data-source) 创建 Blob 数据源对象的示例：

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { 
        "connectionString" : "ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Storage/storageAccounts/[storage account name]/;" 
    },
    "container" : { 
        "name" : "my-container", "query" : "<optional-virtual-directory-name>" 
    }
}   
```

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>选项 2 - 使用用户分配的托管标识创建数据源

2021-04-30-preview REST API 支持用户分配的托管标识。 下面是一个示例，说明如何使用 [REST API](/rest/api/searchservice/create-data-source)、托管标识连接字符串和用户分配的托管标识创建数据源，以便为存储帐户中的数据编制索引。

从存储帐户编制索引时，数据源必须具有以下必需属性：

* **name** 是搜索服务中数据源的唯一名称。
* type
    * Azure Blob 存储：`azureblob`
    * Azure 表存储：`azuretable`
    * Azure Data Lake Storage Gen2：`adlsgen2`
* **凭据**
    * 使用托管标识进行身份验证时，凭据格式与不使用托管标识时不同。 此时，需要提供没有帐户密钥或密码的 ResourceId。 ResourceId 必须包含存储帐户的订阅 ID、存储帐户的资源组和存储帐户名称。
    * 托管标识格式： 
        * ResourceId=/subscriptions/你的订阅 ID/resourceGroups/你的资源组名称/providers/Microsoft.Storage/storageAccounts/你的存储帐户名称/;**
* container 指定存储帐户中的容器或表名称。 默认情况下，容器中的所有 Blob 都可检索。 如果只想为特定虚拟目录中的 Blob 编制索引，可以使用可选的 **query** 参数指定该目录。
* 标识包含用户分配的托管标识的集合。 创建数据源时，只应提供一个用户分配的托管标识。
    * userAssignedIdentities 包括用户分配的托管标识的详细信息。
        * 用户分配的托管标识的格式： 
            * /subscriptions/订阅 ID/resourcegroups/资源组名称/providers/Microsoft.ManagedIdentity/userAssignedIdentities/托管标识名称  

如何使用 [REST API](/rest/api/searchservice/create-data-source) 创建 Blob 数据源对象的示例：

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { 
        "connectionString" : "ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Storage/storageAccounts/[storage account name]/;" 
    },
    "container" : { 
        "name" : "my-container", "query" : "<optional-virtual-directory-name>" 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]" 
    }
}   
```

## <a name="4---create-the-index"></a>4 - 创建索引

索引指定文档、属性和其他构造中可以塑造搜索体验的字段。

下面介绍了如何使用可搜索 `content` 字段创建索引，以存储从 Blob 中提取的文本：   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

有关创建索引的详细信息，请参阅[创建索引](/rest/api/searchservice/create-index)

## <a name="5---create-the-indexer"></a>5 - 创建索引器

索引器将数据源与目标搜索索引关联，并提供自动执行数据刷新的计划。

创建索引和数据源后，便可以准备创建索引器。

Blob 索引器的索引器定义示例：

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

此索引器每隔两小时运行一次（已将计划间隔设置为“PT2H”）。 若要每隔 30 分钟运行一次索引器，可将间隔设置为“PT30M”。 支持的最短间隔为 5 分钟。 计划是可选的 - 如果省略，则索引器在创建后只运行一次。 但是，可以随时根据需要运行索引器。   

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](/rest/api/searchservice/create-indexer)。

若要详细了解如何定义索引器计划，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

## <a name="accessing-secure-data-in-storage-accounts"></a>访问存储帐户中的安全数据

可使用防火墙和虚拟网络进一步保护 Azure 存储帐户。 如果要索引使用防火墙或虚拟网络保护的 Blob 存储帐户或 Data Lake Gen2 存储帐户的内容，请遵循[通过受信任的服务例外安全地访问存储帐户中的数据](search-indexer-howto-access-trusted-service-exception.md)中的说明。

## <a name="see-also"></a>另请参阅

详细了解 Azure 存储索引器：

* [Azure Blob 索引器](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 索引器](search-howto-index-azure-data-lake-storage.md)
* [Azure 表索引器](search-howto-indexing-azure-tables.md)