---
title: Azure API 管理中的版本 | Microsoft Docs
description: 了解 Azure API 管理中的版本的概念。
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 02/10/2021
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 6b055cb6771dc76557c61220a52120a5fbbd7940
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129811378"
---
# <a name="versions-in-azure-api-management"></a>Azure API 管理中的版本

通过版本可向开发人员显示相关 API 组。 你可以使用版本安全地处理 API 中的重大更改。 客户端可以选择在准备就绪时使用新的 API 版本，而现有客户端将继续使用较旧的版本。 版本通过版本标识符（即你选择的任何字符串值）进行区分，而版本控制方案使客户端能够标识要使用的 API 版本。

在大多数情况下，可以将每个 API 版本视为其自身的独立 API。 两个不同的 API 版本可能具有不同的操作集和不同的策略。

使用版本可执行以下操作：

- 同时发布 API 的多个版本。
- 使用路径、查询字符串或标头来区分版本。
- 使用想用来标识版本的任何字符串值，可以是数字、日期或名称。
- 在开发人员门户上显示组合在一起的 API 版本。
- 采用现有（非版本控制）API，并在不中断现有客户端的情况下创建新版本。

[按照我们的演练开始使用版本。](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>版本控制方案

不同 API 开发人员对版本控制的要求不同。 Azure API 管理不指定特定版本控制方法，而是提供多个选项。

### <a name="path-based-versioning"></a>基于路径的版本控制

使用路径版本控制方案时，任何 API 请求的 URL 路径中都需要包含版本标识符。

例如，`https://apis.contoso.com/products/v1` 和 `https://apis.contoso.com/products/v2` 可以引用相同的 `products` API，但需要分别引用版本 `v1` 和 `v2`。

使用基于路径的版本控制时，API 请求 URL 的格式为：`https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}`。

### <a name="header-based-versioning"></a>基于标头的版本控制

使用标头版本控制方案时，任何 API 请求的 HTTP 请求标头中都需要包含版本标识符。 可以指定 HTTP 请求头的名称。

例如，可以创建一个名为 `Api-Version` 的自定义标头，客户端可以在此标头的值中指定 `v1` 或 `v2`。

### <a name="query-string-based-versioning"></a>基于查询字符串的版本控制

使用查询字符串版本控制方案时，任何 API 请求的查询字符串参数中都需要包含版本标识符。 可以指定查询字符串参数的名称。

使用基于查询字符串的版本控制时，API 请求 URL 的格式为：`https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}`。

例如，`https://apis.contoso.com/products?api-version=v1` 和 `https://apis.contoso.com/products/api-version=v2` 可以引用相同的 `products` API，但需要分别引用版本 `v1` 和 `v2`。

## <a name="original-versions"></a>原始版本

如果为非版本控制 API 添加了版本，则会自动创建 `Original` 版本并在不指定版本标识符的情况下对默认 URL 进行响应。 `Original` 版本可以确保所有现有调用方不会被添加版本的过程中断。 如果在一开始就启用了版本的情况下创建新 API，则不会创建 `Original` 版本。

## <a name="how-versions-are-represented"></a>如何表示版本

Azure API 管理会维护名为“版本集”的资源，该资源代表单个逻辑 API 的一组版本。 版本集包含版本控制 API 的显示名称，以及用于将请求定向到指定版本的[版本控制方案](#versioning-schemes)。

API 的每个版本都作为其自身的 API 资源进行维护，并将资源与版本集相关联。 版本集可能包含具有不同操作或策略的 API。 你可能会在某个集中的版本之间进行重大更改。

Azure 门户可为你创建版本集。 你可以在 Azure 门户中修改版本集的名称和说明。

你可以使用 [Azure CLI](/cli/azure/apim/api/versionset)、[Azure PowerShell](/powershell/module/az.apimanagement/#api-management)、[资源管理器模板](/azure/templates/microsoft.apimanagement/service/apiversionsets)或 [Azure 资源管理器 API](/rest/api/apimanagement/2020-12-01/api-version-set) 直接查看和管理版本集。

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>将非版本控制 API 迁移到版本控制 API

使用 Azure 门户对现有 API 启用版本控制时，将对 API 管理资源进行以下更改：

 * 创建新版本集。
 * 保留现有版本并[配置为 `Original` API 版本](#original-versions)。 将该 API 链接到版本集，但不需要指定版本标识符。
 * 新版本创建为新的 API，并链接到版本集。 必须使用版本控制方案和标识符访问此新 API。

## <a name="versions-and-revisions"></a>版本和修订版

版本和修订版功能不同。 与非版本控制 API 一样，每个版本都可以有多个修订版。 你可以使用修订版而不使用版本，或反之亦然。 通常，版本用于分离 API 版本和重大更改，而修订版可用于对 API 进行次要和非重大的更改。

如果你发现修订版有重大更改，或者想要将其正式转换为 beta /测试版本，则可以从修订版中创建一个版本。 使用 Azure 门户，单击“修订版”选项卡上的修订版上下文菜单中的“从修订版创建版本”。

## <a name="developer-portal"></a>开发人员门户

[开发人员门户](./api-management-howto-developer-portal.md)分别列出了 API 的每个版本。

![显示版本控制 API 列表的 API 管理开发人员门户](media/api-management-versions/portal-list.png)

API 的详细信息还会显示此 API 的所有版本的列表。 会显示一个不带版本标识符的 `Original` 版本。

![显示 API 的详细信息和此 API 的版本列表的 API 管理开发人员门户](media/api-management-versions/portal-details.png)

> [!TIP]
> 需要将 API 版本添加到产品中，然后才能在开发人员门户上显示。
