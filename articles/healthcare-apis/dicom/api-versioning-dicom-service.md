---
title: DICOM 服务的 API 版本控制 - Azure Healthcare APIs
description: 本指南概述了 DICOM 服务的 API 版本策略。
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: c07a963a9dfeaa8e05d79c43cff18ab934f44f1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778479"
---
# <a name="api-versioning-for-dicom-service"></a>DICOM 服务的 API 版本控制

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本参考指南概述了 DICOM 服务的 API 版本策略。 

所有版本的 DICOM API 将始终符合 DICOMweb™ 标准规范，但版本可能会根据 [DICOM 一致性语句](dicom-services-conformance-statement.md)公开不同的 API。

## <a name="specifying-version-of-rest-api-in-requests"></a>在请求中指定 REST API 版本

应在请求 URL 中显式指定 REST API 的版本，如以下示例所示：

`<service_url>/v<version>/studies`

目前仍支持没有版本的路由。 例如，`<service_url>/studies` 的行为与指定版本为 v1.0-prerelease 的行为相同。 但是，强烈建议通过 URL 在所有请求中指定版本。

## <a name="supported-versions"></a>支持的版本

当前支持的版本为：

* v1.0-prerelease

可在以下 URL 中找到支持版本的 OpenApi Doc：
 
`<service_url>/{version}/api.yaml`

## <a name="prerelease-versions"></a>预发行版本

带有标签“prerelease”的 API 版本指示版本尚未准备好用于生产，并且只应在测试环境中使用。 这些终结点可能会遇到中断性变更，恕不另行通知。

## <a name="how-versions-are-incremented"></a>如何递增版本

目前，每当发生中断性变更时，我们才递增主版本，这视为不向后兼容。 所有次要版本都默示为 0。 所有版本都采用 `Major.0` 格式。

下面是一些中断性变更示例（主版本递增）：

1. 重命名或删除终结点。
2. 删除参数或添加必需参数。
3. 更改状态代码。
4. 删除响应中的属性或完全更改响应类型，但可以向响应添加属性。
5. 更改属性的类型。
6. API 更改时的行为，比如业务逻辑变更，过去执行 foo，而现在执行 bar。

非中断性变更（版本不递增）：

1. 添加可为空或具有默认值的属性。
2. 向响应模型添加属性。
3. 更改属性顺序。

## <a name="header-in-response"></a>响应中的标头

ReportApiVersions 已打开，这意味着在适当情况下，我们将返回标头 api-supported-versions 和 api-deprecated-versions。

* api-supported-versions 将列出请求的 API 支持的版本。 它仅在调用带有 `ApiVersion("<someVersion>")` 批注的终结点时返回。

* api-deprecated-versions 将列出请求的 API 已弃用的版本。 它仅在调用带有 `ApiVersion("<someVersion>", Deprecated = true)` 批注的终结点时返回。

示例：

ApiVersion("1.0")

ApiVersion("1.0-prerelease", Deprecated = true)

[ ![API 支持的版本和弃用的版本。](media/api-supported-deprecated-versions.png) ](media/api-supported-deprecated-versions.png#lightbox)

