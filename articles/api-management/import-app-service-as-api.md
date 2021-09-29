---
title: 将 Azure Web 应用导入 Azure API 管理 | Microsoft 文档
description: 本文介绍如何使用 Azure API 管理导入 Azure 应用服务中托管的 Web API。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/27/2021
ms.author: danlep
ms.openlocfilehash: 8d98a6c115b7f3d06b80a938c090bfa1b9c9794e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585083"
---
# <a name="import-an-azure-web-app-as-an-api"></a>将 Azure Web 应用作为 API 导入

本文介绍如何使用 Azure 门户将 Azure Web 应用导入 Azure API 管理并测试已导入的 API。

> [!NOTE]
> 可以使用 Visual Studio Code 的 API 管理扩展来导入和管理 API。 按照 [API 管理扩展教程](visual-studio-code-tutorial.md)进行安装并开始使用。

在本文中，学习如何：

> [!div class="checklist"]
> * 导入应用服务中托管的 Web 应用
> * 在 Azure 门户中测试 API

## <a name="expose-web-app-with-api-management"></a>通过 API 管理公开 Web 应用

[Azure 应用服务](../app-service/overview.md)是一项基于 HTTP 的服务，用于托管 Web 应用程序、REST API 和移动后端。 API 开发人员可使用其首选技术堆栈和管道来开发 API，并在安全、可缩放的环境中将 API 后端发布为 Web 应用。 然后，使用 API 管理公开 Web 应用，在其生命周期中管理和保护 API，并将它们发布给使用者。

API 管理是推荐用来公开 Web 应用托管 API 的环境，原因如下：

* 将管理和保护向 API 使用者公开的前端与管理和监视后端 Web 应用分离开来
* 在与其他 API 相同的环境中管理作为 Web 应用托管的 Web API
* 应用[策略](api-management-policies.md)来更改 API 行为，例如调用速率限制
* 将 API 使用者定向到 API 管理的可自定义[开发人员门户](api-management-howto-developer-portal.md)，来发现和了解 API、请求访问权限并进行试用

有关详细信息，请查看[关于 API 管理](api-management-key-concepts.md)。

## <a name="openapi-specification-versus-wildcard-operations"></a>OpenAPI 规范与通配符操作

API 管理支持导入应用服务中托管的 Web 应用，后者包含 OpenAPI 规范（Swagger 定义）。 不过，OpenAPI 规范不是必需的。

* 如果 Web 应用已在 API 定义配置了 OpenAPI 规范，API 管理会创建直接映射到该定义的 API 操作，包括所需路径、参数和响应类型。 

  建议配置 OpenAPI 规范，因为如果将 API 高保真地导入 API 管理，你就能灵活地单独验证、管理、保护和更新每个操作的配置。

* 如果未提供 OpenAPI 规范，API 管理会为常见的 HTTP 谓词（GET 和 PUT 等）生成[通配符操作](add-api-manually.md#add-and-test-a-wildcard-operation)。 将所需路径或参数追加到通配符操作可将 API 请求传递到后端 API。

  通过通配符操作，你仍可利用相同的 API 管理功能，但默认情况下，定义操作的详细信息级别不同。 在这两种情况下，你都可[编辑](edit-api.md)操作或将操作[添加](add-api-manually.md)到导入的 API。
 
### <a name="example"></a>示例
后端 Web 应用可能支持两种 GET 操作： 
*  `https://myappservice.azurewebsites.net/customer/{id}`
*  `https://myappservice.azurewebsites.net/customers`

通过 `https://contosoapi.azureapi.net/store` 等路径将 Web 应用导入 API 管理服务。 下表显示导入 API 管理的操作（无论是否具有 OpenAPI 规范）： 

| 类型 |导入的操作  |示例请求 |
|---------|---------|---------|
|OpenAPI 规范    | `GET  /customer/{id}`<br/><br/> `GET  /customers`         |  `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`       |
|通配符     | `GET  /*`         | `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`  |

通过通配符操作，可对后端服务发出与 OpenAPI 规范中的操作相同的请求。 不过，OpenAPI 指定的操作可在 API 管理中单独管理。 

## <a name="prerequisites"></a>先决条件

+ 完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
+ 确保订阅中已有应用服务。 有关详细信息，请查看[应用服务文档](../app-service/index.yml)。

  若要分步了解如何创建示例 web API 并发布为 Azure Web 应用，请查看：

    * [教程：使用 ASP.NET Core 创建 Web API](/aspnet/core/tutorials/first-web-api)
    * [使用 Visual Studio Code 将 ASP.NET Core 应用发布到 Azure](/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>导入和发布后端 API

> [!TIP]
> 以下步骤使用 Azure 门户中的 Azure API 管理来开始导入。 可选择应用的 API 菜单中的“API 管理”，直接从 Web 应用链接到 API 管理 。  

1. 在 Azure 门户中导航到 API 管理服务，然后从菜单中选择“API”。
1. 从列表中选择“应用服务”。

    :::image type="content" source="media/import-app-service-as-api/app-service.png" alt-text="在应用服务中创建":::
1. 选择“浏览”，查看订阅中的应用服务列表。
1. 选择一个应用服务。 如果 OpenAPI 定义与所选的 Web 应用关联，API 管理会提取该定义并将其导入。 

    如果未找到 OpenAPI 定义，API 管理会为常见的 HTTP 谓词生成通配符操作来公开 API。 
1. 添加 API URL 后缀。 后缀是用于在该 API 管理实例中标识此特定 API 的名称。 它在此 APIM 实例中必须唯一。
1. 通过关联 API 与产品来发布 API。 本例中使用了“无限制”产品  。 如果想要发布 API 并使其可供开发人员使用，请将其添加到产品中。 可在 API 创建期间执行此操作，或稍后进行设置。

    > [!NOTE]
    > 产品是一个或多个 API 的关联。 可包含许多 API，并通过开发人员门户将其提供给开发人员。 开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果创建了 APIM 实例，那么你已是管理员，因此默认情况下订阅了每个产品。
    >
    > 默认情况下，每个 API 管理实例附带两个示例产品：
    > * **入门**
    > * **不受限制**   
1. 输入其他 API 设置。 可以在创建过程中设置这些值，也可以稍后转到“设置”  选项卡来配置这些值。在[导入和发布第一个 API](import-and-publish.md#import-and-publish-a-backend-api) 教程中对这些设置进行了说明。
1. 选择“创建”  。
    :::image type="content" source="media/import-app-service-as-api/import-app-service.png" alt-text="在应用服务中创建 API":::

## <a name="test-the-new-api-in-the-azure-portal"></a>在 Azure 门户中测试新的 API

可直接从 Azure 门户调用操作，这样可以方便地查看和测试 API 的操作。 你还可在[开发人员门户](api-management-howto-developer-portal.md)中或使用自己的 REST 客户端工具来测试 API。

1. 选择上一步中创建的 API。
1. 选择“测试”选项卡。
1. 选择操作。

    该页将显示查询参数的字段和标头的字段。 其中一个标头是“Ocp-Apim-Subscription-Key”，用于提供和此 API 关联的产品订阅密钥。 如果创建了 API 管理实例，那么你已是管理员，因此会自动填充该密钥。 
1. 按“发送”。 

    如果测试成功，后端会响应并显示“200 正常”和一些数据。

### <a name="test-wildcard-operation-in-the-portal"></a>在 Azure 门户中测试通配符操作

生成通配符操作后，操作可能不会直接映射到后端 API。 例如，导入 API 管理中的通配符 GET 操作默认使用路径 `/`。 不过，后端 API 可能支持使用以下路径的 GET 操作：

`/api/TodoItems`

可按以下方式测试路径 `/api/TodoItems`。 

1. 选择已创建的 API 并选择操作。
1. 选择“测试”选项卡。
1. 在“模板参数”中，更新通配符 (*) 名称旁边的值。 例如，输入 `api/TodoItems`。 此值会追加到通配符操作的路径 `/`。

    :::image type="content" source="media/import-app-service-as-api/test-wildcard-operation.png" alt-text="测试通配符操作":::
1. 选择“发送”。 

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
