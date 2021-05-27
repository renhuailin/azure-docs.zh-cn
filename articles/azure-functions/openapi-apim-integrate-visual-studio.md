---
title: 使用 Azure Functions 和 API 管理在 Visual Studio 中创建无服务器 API
description: 使用 Visual Studio 创建 HTTP 触发的函数以及一个 OpenAPI 定义，该定义可启用 Azure API 管理集成，使其他应用和服务能够调用基于无服务器函数的 API。
ms.topic: tutorial
ms.date: 05/07/2021
ms.openlocfilehash: b8c6afc64f15df3124ab4052568bf36d209d4fde
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383709"
---
# <a name="create-serverless-apis-in-visual-studio-using-azure-functions-and-api-management-integration-preview"></a>使用 Azure Functions 和 API 管理集成（预览版）在 Visual Studio 中创建无服务器 API

通常使用 OpenAPI 定义描述 REST API。 此文件包含有关 API 中的操作以及 API 的请求和响应数据应采用哪种结构的信息。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Visual Studio 中创建无服务器函数项目
> * 使用内置的 OpenAPI 功能在本地测试函数 API
> * 使用 API 管理集成将项目发布到 Azure 中的函数应用 
> * 获取函数的访问密钥并在 API 管理中设置该密钥
> * 下载 OpenAPI 定义文件

创建的无服务器函数将提供一个 API，可让你确定风力涡轮机的紧急维修是否具有成本效益。 由于创建的函数应用和 API 管理实例都使用消耗计划，因此完成本教程的费用非常低。

> [!NOTE]
> 本文中所述的 OpenAPI 和 API 管理集成目前以预览版提供。 只有 C# 类库 (.NET Core 3.1) 函数支持这种公开无服务器 API 的方法。 所有其他语言运行时应[改用门户中的 Azure API 管理集成](functions-openapi-definition.md)。 

## <a name="prerequisites"></a>先决条件

+ [Visual Studio 2019](https://azure.microsoft.com/downloads/) 版本 16.10 或更高版本。 请确保在安装过程中选择“Azure 开发”工作负载。 

+ 一个有效的 [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-a-functions-project"></a>创建 Functions 项目

Visual Studio 中的 Azure Functions 项目模板创建了一个项目，该项目可发布到 Azure 中的函数应用。 你还将创建 HTTP 触发的函数来支持生成 OpenAPI 定义文件（以前称为 Swagger 文件）。

1. 在 Visual Studio 菜单中，选择“文件” > “新建” > “项目”。

1. 在“创建新项目”中，在搜索框中输入“functions”，选择“Azure Functions”模板，然后选择“下一步”。

1. 在“配置新项目”中输入项目的项目名称（例如 `TurbineRepair`），然后选择“创建”  。 

1. 对于“创建新的 Azure Functions 应用程序”设置，请使用下表中的值：

    | 设置      | 值  | 说明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET 版本** | **.NET Core 3 (LTS)** | 此值创建使用 Azure Functions 版本 3.x 运行时的函数项目。 只有版本 3.x 的 Functions 运行时支持生成 OpenAPI 文件。 |
    | **函数模板** | **使用 OpenAPI 的 HTTP 触发器** | 此值创建 HTTP 请求触发的函数，该函数能够生成 OpenAPI 定义文件。  |
    | 存储帐户(AzureWebJobsStorage)  | 存储模拟器 | 可以使用仿真器在本地开发 HTTP 触发器函数。 由于 Azure 中的函数应用需要存储帐户，因此在将项目发布到 Azure 时会分配或创建一个存储帐户。 |
    | **授权级别** | 函数 | Azure 中运行的客户端在访问终结点时必须提供密钥。 有关密钥和授权的详细信息，请参阅[函数访问密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。 |
    
    ![Azure Functions 项目设置](./media/openapi-apim-integrate-vs/functions-project-settings.png)

1. 选择“创建”以创建支持 OpenAPI 的函数项目和 HTTP 触发器函数。 

Visual Studio 将创建一个项目，以及一个名为 `Function1` 的包含 HTTP 触发器函数类型样本代码的类。 接下来，请将此函数模板代码替换为你自己的自定义代码。 

## <a name="update-the-function-code"></a>更新函数代码

该函数使用的 HTTP 触发器采用以下两个参数：

| 参数名称 | 说明|
| ---- | ---- |
| *小时数* | 预计维修涡轮机所需的时间。 |
| *容量* | 涡轮机的容量，以千瓦为单位。 |

然后，该函数将计算维修成本，以及涡轮机在 24 小时内可产生的收入。 参数在查询字符串或者在 POST 请求的有效负载中提供。 

在 Function1.cs 项目文件中，请将生成的类库代码的内容替换为以下代码：

:::code language="csharp" source="~/functions-openapi-turbine-repair/TurbineRepair/Function1.cs":::

此函数代码会返回 `Yes` 或 `No` 消息，指示紧急修复是否具有成本效益。 它还返回涡轮机所代表的收入机会和修复涡轮机的成本。

## <a name="run-and-verify-the-api-locally"></a>在本地运行并验证 API

当你运行该函数时，OpenAPI 终结点会让使用生成的页面在本地试用该函数非常容易。 在本地运行时，无需提供函数访问密钥。

1. 按 F5 启动项目。 当 Functions 运行时在本地启动时，输出中会显示一组 OpenAPI 和 Swagger 终结点，以及函数终结点。  

1. 在浏览器中打开 RenderSwaggerUI 终结点，类似于 `http://localhost:7071/api/swagger/ui`。 此时将根据 OpenAPI 定义呈现一个页面。

1. 选择“POST” > “试用”，以查询参数的形式或者在 JSON 请求正文中输入 `hours` 和 `capacity` 的值，然后选择“执行”  。 

    :::image type="content" source="media/openapi-apim-integrate-vs/swagger-ui-post.png" alt-text="用于测试 TurbineRepair API 的 Swagger UI":::

1. 如果输入整数值（例如为 `hours` 输入 6，为 `capacity` 输入 2500），将获得如以下示例所示的 JSON 响应：
   
    :::image type="content" source="media/openapi-apim-integrate-vs/swagger-ui-response.png" alt-text="TurbineRepair 函数的响应 JSON 数据。":::

现在已经有了确定紧急修复是否经济高效的函数。 接下来，将项目和 API 定义发布到 Azure。

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

必须在 Azure 订阅中有一个函数应用，然后才能发布项目。 Visual Studio 发布将在你首次发布项目时创建一个函数应用。 它还可以创建一个与函数应用集成的 API 管理实例，以公开 TurbineRepair API。

1. 在解决方案资源管理器中，右键单击项目，选择“发布”，然后在“目标”中选择“Azure”，接着选择“下一步”    。

1. 对于“特定目标”，请选择“Azure 函数应用(Windows)”以创建一个在 Windows 上运行的函数应用，然后选择“下一步”  。

1. 在“函数实例”中，选择“+ 创建新的 Azure 函数…” 。

    :::image type="content" source="media/openapi-apim-integrate-vs/publish-new-resource.png" alt-text="创建新的函数应用实例":::

1. 使用下表中指定的值创建新的实例：

    | 设置      | 值  | 说明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名称** | 全局唯一名称 | 用于唯一标识新 Function App 的名称。 接受此名称或输入新名称。 有效的字符是 `a-z`、`0-9` 和 `-`。 |
    | **订阅** | 订阅 | 要使用的 Azure 订阅。 接受此订阅，或从下拉列表中选择一个新订阅。 |
    | **[资源组](../azure-resource-manager/management/overview.md)** | 资源组的名称 |  要在其中创建函数应用的资源组。 从下拉列表中选择现有资源组，或者选择“新建”来创建新的资源组。|
    | **[计划类型](functions-scale.md)** | 消耗 | 将项目发布到在[消耗计划](consumption-plan.md)中运行的函数应用时，只需为函数应用的执行付费。 其他托管计划会产生更高的成本。 |
    | **位置** | 服务的位置 | 在靠近你或者靠近函数访问的其他服务的 [区域](https://azure.microsoft.com/regions/)中选择一个 **位置**。 |
    | **[Azure 存储](storage-considerations.md)** | 常规用途存储帐户 | Functions 运行时需要 Azure 存储帐户。 选择“新建”即可配置常规用途存储帐户。 也可选择一个符合[存储帐户要求](storage-considerations.md#storage-account-requirements)的现有帐户。  |

    :::image type="content" source="media/openapi-apim-integrate-vs/create-function-app-with-storage.png" alt-text="在 Azure 中创建具有存储的新函数应用":::

1. 选择“创建”，在 Azure 中创建一个函数应用及其相关资源。 资源创建的状态如窗口左下角所示。 

1. 回到“函数实例”，确保已选中“从包文件运行” 。 启用[从包运行](run-functions-from-deployment-package.md)模式，函数应用将使用 [Zip 部署](functions-deployment-technologies.md#zip-deploy)进行部署。 建议对你的函数项目使用此部署方法，因为它可提高性能。 

1. 选择“下一步”，然后在“API 管理”页中选择“+ 创建 API 管理 API”  。

1.  使用下表中的值在 API 管理中创建一个 API：

    | 设置      | 值  | 说明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | API 名称 | TurbineRepair | API 的名称。 |
    | **订阅名称** | 订阅 | 要使用的 Azure 订阅。 接受此订阅，或从下拉列表中选择一个新订阅。 |
    | **资源组** | 资源组的名称 | 从下拉列表中选择你的函数应用所在的资源组。   |
    | API 管理服务 | 新实例 | 选择“新建”，在无服务器层中创建新的 API 管理实例。   |

    :::image type="content" source="media/openapi-apim-integrate-vs/create-api-management-api.png" alt-text="使用 API 创建 API 管理实例":::

1. 选择“创建”，以使用函数集成中的 TurbineRepair API 创建 API 管理实例。

1. 选择“完成”，确认“发布”页面上显示了“准备好发布”，然后选择“发布”，将包含项目文件的包部署到 Azure 中的新函数应用  。 

    部署完成后，Azure 中函数应用的根 URL 将显示在“发布”选项卡中。 

## <a name="get-the-function-access-key"></a>获取函数访问密钥

1. 在“发布”选项卡中，选择“托管”旁边的省略号图标 (...)，然后选择“在 Azure 门户中打开”   。 创建的函数应用将在默认浏览器中的 Azure 门户上打开。 

1. 在“函数”下，选择“函数” > “TurbineRepair”，然后选择“函数密钥”   。 

    :::image type="content" source="media/openapi-apim-integrate-vs/get-function-keys.png" alt-text="获取 TurbineRepair 函数的访问密钥":::

1. 在“函数密钥”下，选择“默认”并复制“值”  。 现在可以在 API 管理中设置此密钥，使其可以访问函数终结点。

## <a name="configure-api-management"></a>配置 API 管理

1. 在“发布”选项卡中，选择“托管”旁边的省略号图标 (...)，然后选择“在 Azure 门户中打开 API”   。 创建的 API 管理实例将在默认浏览器中的 Azure 门户上打开。 此 API 管理实例已链接到你的函数应用。 

1. 在“API”下选择“Azure Functions OpenAPI 扩展” > “测试” > “POST 运行”，然后在“入站策略”下选择“添加策略”     。

    :::image type="content" source="media/openapi-apim-integrate-vs/apim-add-policy.png" alt-text="将入站策略添加到 API":::

1. 在“添加入站策略”中，选择“设置查询参数”，为“名称”键入 `code`，选择“+值”，粘贴复制的函数密钥，然后选择“保存”    。 API 管理在将调用传递到函数终结点时会包含该函数密钥。 

设置函数密钥后，可以调用 API 来验证函数应用托管在 Azure 中时是否正常工作。

## <a name="verify-the-api-in-azure"></a>在 Azure 中验证 API

1. 在 API 中，依次选择“测试”选项卡、“POST 运行”，在“请求正文” > “原始”中输入以下代码，然后选择“发送”    ：

    ```json
    {
        "hours": "6",
        "capacity": "2500"
    }
    ```

    :::image type="content" source="media/openapi-apim-integrate-vs/api-management-test-function-api.png" alt-text="API 管理 API 中的 OpenAPI 测试页":::

    与前面一样，也可以提供相同的值作为查询参数。 

1. 选择“发送”，然后查看“HTTP 响应”以验证是否从 API 返回了相同的结果 。

## <a name="download-the-openapi-definition"></a>下载 OpenAPI 定义

如果 API 可按预期方式工作，则你可以下载 OpenAPI 定义。

1. 1. 在“API”下，依次选择“Azure Functions OpenAPI 扩展”、省略号图标 (...)、“导出”   。
   
   ![下载 OpenAPI 定义](media/openapi-apim-integrate-vs/download-definition.png)

2. 选择 API 导出方式，包括采用各种格式的 OpenAPI 文件。 也可以[将 API 从 Azure API 管理导出到 Power Platform](../api-management/export-api-power-platform.md)。 

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你在资源组中创建了 Azure 资源。 如果将来不再需要这些资源，可以通过删除资源组来删除它们。
 
从 Azure 门户菜单或“主页”页上，选择“资源组” 。 然后，在“资源组”页上选择创建的组。

在“myResourceGroup”页中，确保列出的资源是要删除的资源。

选择“删除资源组”，在文本框中键入该组的名称以确认，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

你已使用 Visual Studio 2019 创建了一个自编文档的（由于使用了 [OpenAPI 扩展](https://github.com/Azure/azure-functions-openapi-extension)）、与 API 管理集成的函数。 现在，可以在门户上的“API 管理”中优化定义。 还可以[详细了解 API 管理](../api-management/api-management-key-concepts.md)。

> [!div class="nextstepaction"]
> [在 API 管理中编辑 OpenAPI 定义](../api-management/edit-api.md)
