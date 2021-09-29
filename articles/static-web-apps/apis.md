---
title: Azure 静态 Web 应用中通过 Azure Functions 提供 API 支持
description: 了解 Azure 静态 Web 应用支持的 API 功能
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: cshoe
ms.openlocfilehash: d7716c6d91ac0f7c8c699d14f265e00f5535a00d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730410"
---
# <a name="api-support-in-azure-static-web-apps-with-azure-functions"></a>Azure 静态 Web 应用中通过 Azure Functions 提供 API 支持

Azure 静态 Web 应用通过 [Azure Functions](../azure-functions/functions-overview.md) 提供无服务器 API 终结点。 利用 Azure Functions，API 可根据需要动态缩放，并且其中包括以下功能：

- 集成安全性，可直接访问用户[身份验证和基于角色的授权](user-information.md)数据。

- 无缝路由，可向 Web 应用安全地提供 api 路由，而无需自定义 CORS 规则。

以下两种可能的配置支持 Azure Static Web Apps API：

- 托管函数：默认情况下，静态 Web 应用的 API 是与某些限制关联的 Azure Static Web Apps 所托管和部署的 Azure Functions 应用程序。

- 创建自己的函数：用户可选择针对任何计划类型[提供现有 Azure Functions 应用程序](functions-bring-your-own.md)，以及 Azure Functions 的所有功能。 使用此配置，用户需负责单独部署此 Functions 应用。

下表对比了使用托管函数和现有函数之间的差异。

| 功能 | 托管函数 | 创建自己的函数 |
| --- | --- | --- |
| 访问 Azure Functions [触发器](../azure-functions/functions-triggers-bindings.md#supported-bindings) | 仅 Http | 全部 |
| 支持 Azure Functions [运行时](../azure-functions/supported-languages.md#languages-by-runtime-version) | Node.js 12<br>.NET Core 3.1<br>Python 3.8 | 全部 |
| 支持 Azure Functions [托管计划](../azure-functions/functions-scale.md) | 消耗 | 消耗<br>高级<br>专用 |
| [集成安全性](user-information.md)，可直接访问用户身份验证和基于角色的授权数据 | ✔ | ✔ |
| [路由集成](./configuration.md?#routes)，可向 Web 应用安全地提供 api 路由，而无需自定义 CORS 规则。 | ✔ | ✔ |
| [Durable Functions](../azure-functions/durable/durable-functions-overview.md) 编程模型 | ✕ | ✔ |
| [托管的标识](../app-service/overview-managed-identity.md) | ✕ | ✔ |
| [Azure 应用服务身份验证和授权](../app-service/configure-authentication-provider-aad.md)令牌管理 | ✕ | ✔ |
| Azure Static Web Apps 外部的可用 API 函数 | ✕ | ✔ |
| Azure 密钥保管库中存储的应用程序设置 | ✕ | ✔ |

## <a name="configuration"></a>配置

API 终结点通过 api 路由向 Web 应用提供。

| 托管函数 | 创建自己的函数 |
| --- | --- |
| 虽然 api 路由已固定，但你可以控制托管函数应用的文件夹位置。 可以通过[编辑工作流 YAML 文件](build-configuration.md)来改变此位置，该文件位于存储库的 .github/workflows 文件夹中的。 | 对 api 路由的请求将发送到现有 Azure Functions 应用。 |

## <a name="troubleshooting-and-logs"></a>故障排除和日志

仅当添加 [Application Insights](monitor.md) 时，日志才可用。

| 托管函数 | 创建自己的函数 |
| --- | --- |
| 通过启用静态 Web 应用上的 Application Insights，启用日志记录。 | 通过启用 Azure Functions 应用上的 Application Insights，启用日志记录。 |

## <a name="constraints"></a>约束

- API 路由前缀必须是 api。
- API 函数的路由规则只支持[重定向](configuration.md#defining-routes)以及[使用角色保护路由](configuration.md#securing-routes-with-roles)。

| 托管函数 | 创建自己的函数 |
| --- | --- |
| <ul><li>触发器仅限于 [HTTP](../azure-functions/functions-bindings-http-webhook.md)。</li><li>Azure Functions 应用必须使用 Node.js 12、.NET Core 3.1 或 Python 3.8。</li><li>某些应用程序设置由服务管理，因此运行时已保留以下前缀：<ul><li>APPSETTING\_、AZUREBLOBSTORAGE\_、AZUREFILESSTORAGE\_、AZURE_FUNCTION\_、CONTAINER\_、DIAGNOSTICS\_、DOCKER\_、FUNCTIONS\_、IDENTITY\_、MACHINEKEY\_、MAINSITE\_、MSDEPLOY\_、SCMSITE\_、SCM\_、WEBSITES\_、WEBSITE\_、WEBSOCKET\_、AzureWeb</li></ul></li></ul> | <ul><li>用户负责管理 Functions 应用部署。</li></ul> |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
