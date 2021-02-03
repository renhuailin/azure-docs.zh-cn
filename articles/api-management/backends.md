---
title: Azure API 管理后端 |Microsoft Docs
description: 了解 API 管理中的自定义后端
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500612"
---
# <a name="backends-in-api-management"></a>API 管理中的后端

API 管理中的 *后端* (或 *api 后端*) 是实现前端 API 及其操作的 HTTP 服务。

导入某些 Api 时，API 管理会自动配置 API 后端。 例如，API 管理在导入 [OpenAPI 规范](import-api-from-oas.md)、 [SOAP API](import-soap-api.md)或 AZURE 资源（如 HTTP 触发的 [azure Function App](import-function-app-as-api.md) 或 [逻辑应用](import-logic-app-as-api.md)）时配置后端。

API 管理还支持使用其他 Azure 资源，如 [Service Fabric 群集](how-to-configure-service-fabric-backend.md) 或自定义服务作为 API 后端。 使用这些自定义后端需要额外的配置，例如，向后端服务请求凭据并定义 API 操作。 可在 Azure 门户中配置和管理这些后端，或使用 Azure Api 或工具。

创建后端后，可以在 Api 中引用后端 URL。 使用 [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 策略将传入 API 请求重定向到自定义后端，而不是该 api 的默认后端。

## <a name="benefits-of-backends"></a>后端的优点

自定义后端有多个优点，包括：

* 提取有关后端服务的信息，提升 Api 的可重用性和改进的管理  
* 通过在现有 API 上配置转换策略轻松使用
* 利用 API 管理功能在 Azure Key Vault 中维护机密，前提是已为标头或查询参数身份验证配置了[命名值](api-management-howto-properties.md)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 门户设置 [Service Fabric 后端](how-to-configure-service-fabric-backend.md) 。
* 还可以使用 API 管理 [REST API](/rest/api/apimanagement)、 [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)或 [Azure 资源管理器模板](../service-fabric/service-fabric-tutorial-deploy-api-management.md)来配置后端。

