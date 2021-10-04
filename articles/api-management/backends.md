---
title: Azure API 管理后端 | Microsoft Docs
description: 了解 API 管理中的自定义后端
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 09/21/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 008c5624f621f8598963a8f94261a9e369d0ca0e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647112"
---
# <a name="backends-in-api-management"></a>API 管理中的后端

API 管理中的后端（或 API 后端）是实现前端 API 及其操作的 HTTP 服务 。

在导入某些 API 时，API 管理会自动配置 API 后端。 例如，API 管理会在导入以下项时配置后端：
* [OpenAPI 规范](import-api-from-oas.md)。
* [SOAP API](import-soap-api.md)。
* Azure 资源，例如 HTTP 触发的 [Azure 函数应用](import-function-app-as-api.md)或[逻辑应用](import-logic-app-as-api.md)。

API 管理还支持使用其他 Azure 资源作为 API 后端，例如：
* [Service Fabric 群集](how-to-configure-service-fabric-backend.md)。
* 自定义服务。 

自定义后端需要额外的配置，以便为向后端服务发出的请求的凭据授权并定义 API 操作。 请通过 Azure 门户或者 Azure API 或工具配置和管理自定义后端。

在创建后端后，可以在 API 中引用后端 URL。 请使用 [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 策略将传入的 API 请求重定向到自定义后端，而不是该 API 的默认后端。

## <a name="benefits-of-backends"></a>后端的优点

自定义后端有多个优点，包括：

* 提取有关后端服务的信息，提高在各 API 中的可重用性并改进了治理。  
* 由于可在现有 API 上配置转换策略，因而易于使用。
* 如果已为标头或查询参数身份验证配置了[命名值](api-management-howto-properties.md)，则可利用 API 管理功能来维护 Azure Key Vault 中的机密。

## <a name="limitation"></a>限制

对于“开发人员”层级和“高级”层级，在[内部虚拟网络](api-management-using-with-internal-vnet.md)中部署的 API 管理实例可能会在网关终结点 URL 和后端 URL 相同时引发 HTTP 500 `BackendConnectionFailure` 错误。 如果遇到此限制，请按照技术社区博客中[内部虚拟网络模式下的自链式 API 管理请求限制](https://techcommunity.microsoft.com/t5/azure-paas-blog/self-chained-apim-request-limitation-in-internal-virtual-network/ba-p/1940417)一文的说明操作。 

## <a name="next-steps"></a>后续步骤

* 使用 Azure 门户设置 [Service Fabric 后端](how-to-configure-service-fabric-backend.md)。
* 还可以使用 API 管理 [REST API](/rest/api/apimanagement)、[Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) 或 [Azure 资源管理器模板](../service-fabric/service-fabric-tutorial-deploy-api-management.md)来配置后端。

