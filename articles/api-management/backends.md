---
title: Azure API 管理后端 | Microsoft Docs
description: 了解 API 管理中的自定义后端
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a0ef3a2c1f2f1fc5cdf00737d1984f6cb13c40d0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813013"
---
# <a name="backends-in-api-management"></a>API 管理中的后端

API 管理中的后端（或 API 后端）是实现前端 API 及其操作的 HTTP 服务 。

在导入某些 API 时，API 管理会自动配置 API 后端。 例如，API 管理在导入 [OpenAPI 规范](import-api-from-oas.md)、[SOAP API](import-soap-api.md) 或 Azure 资源（如 HTTP 触发的 [Azure 函数应用](import-function-app-as-api.md)或[逻辑应用](import-logic-app-as-api.md)）时会配置后端。

API 管理还支持使用其他 Azure 资源（如 [Service Fabric 群集](how-to-configure-service-fabric-backend.md)或自定义服务）作为 API 后端。 使用这些自定义后端需要进行额外配置，例如，为针对后端服务的请求向凭据授权以及定义 API 操作。 请通过 Azure 门户或者 Azure API 或工具配置和管理这些后端。

在创建后端后，可以在 API 中引用后端 URL。 请使用 [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 策略将传入的 API 请求重定向到自定义后端，而不是该 API 的默认后端。

## <a name="benefits-of-backends"></a>后端的优点

自定义后端有多个优点，包括：

* 提取有关后端服务的信息，促进在 API 中的可重用性和改进治理  
* 由于可在现有 API 上配置转换策略，因而易于使用
* 如果已为标头或查询参数身份验证配置了[命名值](api-management-howto-properties.md)，则可利用 API 管理功能来维护 Azure Key Vault 中的机密

## <a name="next-steps"></a>后续步骤

* 使用 Azure 门户设置 [Service Fabric 后端](how-to-configure-service-fabric-backend.md)。
* 还可以使用 API 管理 [REST API](/rest/api/apimanagement)、[Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) 或 [Azure 资源管理器模板](../service-fabric/service-fabric-tutorial-deploy-api-management.md)来配置后端。

