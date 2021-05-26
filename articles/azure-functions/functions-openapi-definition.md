---
title: 使用 Azure API 管理通过函数公开 API
description: 创建一个 OpenAPI 定义，使其他应用和服务可以在 Azure 中调用函数。
ms.topic: how-to
ms.date: 05/04/2021
ms.openlocfilehash: ec25b03b3d93fcd69297899653279fea86eabb02
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110375619"
---
# <a name="expose-serverless-apis-from-http-endpoints-using-azure-api-management"></a>使用 Azure API 管理从 HTTP 终结点公开无服务器 API

Azure Functions 在门户中与 Azure API 管理集成，允许你将 HTTP 触发器函数终结点公开为 REST API。 这些 API 是使用 OpenAPI 定义描述的。 此 JSON（或 YAML）文件包含 API 中提供的操作的相关信息。 其中包含有关应如何构造 API 的请求和响应数据的详细信息。 通过集成函数应用，你可以让 API 管理生成这些 OpenAPI 定义。  

本文介绍了如何将函数应用与 API 管理集成。 此集成适用于以任何[受支持的语言](supported-languages.md)开发的函数应用。 还可以[从 Azure API 管理导入函数应用](../api-management/import-function-app-as-api.md)。

对于 C# 类库函数，还可以[使用 Visual Studio](openapi-apim-integrate-visual-studio.md) 创建和发布与 API 管理集成的无服务器 API。  

## <a name="create-the-api-management-instance"></a>创建 API 管理实例

若要创建链接到函数应用的 API 管理实例，请执行以下操作：

1. 选择该函数应用，从左侧菜单中选择“API 管理”，然后在“API 管理”下选择“新建”。

    :::image type="content" source="media/functions-openapi-definitions/select-all-settings-openapi.png" alt-text="选择“API 管理”":::


1. 使用下表中指定的“API 管理”设置：

    | 设置      | 建议的值  | 说明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名称** | 全局唯一名称 | 根据函数应用的名称生成一个名称。 |
    | **订阅** | 订阅 | 在其下创建此新资源的订阅。 |  
    | **[资源组](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 与函数应用相同的资源，系统会为你设置。 |
    | **位置** | 服务的位置 | 请考虑选择与你的函数应用相同的位置。 |
    | 组织名称 | Contoso | 在开发人员门户中使用的组织名称，也用于电子邮件通知。 |
    | **管理员电子邮件** | 你的电子邮件 | 从 API 管理接收了系统通知的电子邮件。 |
    | **定价层** | 消耗 | 消耗层并非在所有区域中均可用。 如需完整的定价详细信息，请参阅 [API 管理定价页](https://azure.microsoft.com/pricing/details/api-management/) |

    ![创建新的 API 管理服务](media/functions-openapi-definitions/new-apim-service-openapi.png)

1. 选择“创建”以创建 API 管理实例，这可能需要几分钟的时间。

1. Azure 创建该实例后，它会在页面上启用“启用 Application Insights”选项。 选择该选项可将日志发送到函数应用程序所在的位置。

## <a name="import-functions"></a>导入函数

创建 API 管理实例后，你可以导入 HTTP 触发的函数终结点。 此示例导入名为 TurbineRepair 的终结点。   

1. 在“API 管理”页中，选择“链接 API”。

1. 此时会打开“导入 Azure Functions”，其中突出显示了“TurbineRepair”函数。  选择“选择”，继续操作。

    ![将 Azure Functions 导入 API 管理](media/functions-openapi-definitions/import-function-openapi.png)

1. 在“从函数应用创建”页中接受默认值，然后选择“创建”。

    :::image type="content" source="media/functions-openapi-definitions/create-function-openapi.png" alt-text="从函数应用创建":::

    Azure 会创建用于该函数的 API。

## <a name="download-the-openapi-definition"></a>下载 OpenAPI 定义

导入函数后，可以从 API 管理实例下载 OpenAPI 定义。

1. 选择页面顶部的“下载 OpenAPI 定义”。
   
   ![下载 OpenAPI 定义](media/functions-openapi-definitions/download-definition.png)

2. 保存下载的 JSON 文件，然后将其打开。 查看定义。

## <a name="next-steps"></a>后续步骤

现在，可以在门户上的“API 管理”中优化定义。 还可以[详细了解 API 管理](../api-management/api-management-key-concepts.md)。

> [!div class="nextstepaction"]
> [在 API 管理中编辑 OpenAPI 定义](../api-management/edit-api.md)
