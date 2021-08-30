---
title: API 版本
titleSuffix: Azure Cognitive Search
description: 适用于 .NET SDK 中的 Azure 认知搜索 REST API 和客户端库的版本策略。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: efb6431805d6f1e648ffad2e30087d7ab8f77d78
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437461"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure 认知搜索中的 API 版本

Azure 认知搜索会定期发布功能更新。 这些更新有时（但并非总是）需要发布 API 的新版本，以保持后向兼容性。 发布新版本使你可以控制何时以及如何在代码中集成搜索服务更新。

通常，Azure 认知搜索团队仅在必要时发布新版本，因为它可能会促使你必须升级代码才能使用新版本 API。 仅在 API 的某些方面以破坏后向兼容性的方式发生更改时，才需要新版本。 此类更改可能会因以下情况而发生：现有功能修复，或更改现有 API 外围功能的新功能。

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>不支持的版本

某些 API 版本不再受支持，将被搜索服务拒绝：

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-Preview**
+ **2014-10-20-Preview**

另外，早于 [3.0.0-rc](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) 的 Azure 认知搜索 .NET SDK 版本也将停用，因为它们面向这些 REST API 版本之一。

对上述版本的支持已于 2020 年 10 月 15 日终止。 如果你的代码使用了已停用的版本，可以[将现有代码迁移](search-api-migration.md)到更新的 [REST API 版本](/rest/api/searchservice/)或更新版本的 Azure SDK。

## <a name="rest-apis"></a>REST API

| REST API | 链接 |
|----------|------|
| 搜索服务（数据平面） | [API 版本](/rest/api/searchservice/search-service-api-versions) |
| 管理（控制平面） | [API 版本](/rest/api/searchmanagement/management-api-versions) |

## <a name="azure-sdk-for-net"></a>用于 .NET 的 Azure SDK

下表提供了最新 SDK 版本的链接。 

| SDK 版本 | 状态 | 描述 |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | Azure .NET SDK 中的新客户端库，最初于 2020 年 7 月发布。 |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | 2019 年 5 月发布。 这是 Microsoft.Azure.Search 包的最新版本。 它由 Azure.Search.Documents 继承。 |
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | 针对管理 REST api-version=2020-08-01。  |
| Microsoft.Azure.Management.Search 3.0.0 | Stable | 针对管理 REST api-version=2015-08-19。  |

## <a name="azure-sdk-for-java"></a>用于 Java 的 Azure SDK

| SDK 版本 | 状态 | 描述  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Azure Java SDK 中的新客户端库，2020 年 7 月发布。 面向搜索 REST api-version=2019-05-06。 |
| [Java 管理客户端 1.35.0](/java/api/overview/azure/search/management) | Stable | 针对管理 REST api-version=2015-08-19。 |

## <a name="azure-sdk-for-javascript"></a>用于 JavaScript 的 Azure SDK

| SDK 版本 | 状态 | 说明  |
|-------------|--------|------------------------------|
| [JavaScript @azure/search-documents 11.0](https://www.npmjs.com/package/@azure/search-documents) | Stable | Azure JavaScript 与 TypesScript SDK 中的新客户端库，2020 年 7 月发布。 面向搜索 REST api-version=2016-09-01。 |
| [JavaScript @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Stable | 针对管理 REST api-version=2015-08-19。 |

## <a name="azure-sdk-for-python"></a>用于 Python 的 Azure SDK

| SDK 版本 | 状态 | 描述  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://pypi.org/project/azure-search-documents/) | Stable | Azure Python SDK 中的新客户端库，2020 年 7 月发布。 面向搜索 REST api-version=2019-05-06。 |
| [Python azure-mgmt-search 8.0](https://pypi.org/project/azure-mgmt-search/) | Stable | 针对管理 REST api-version=2015-08-19。 |
