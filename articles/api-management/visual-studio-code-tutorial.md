---
title: 教程 - 导入和管理 API - Azure API 管理和 Visual Studio Code | Microsoft Docs
description: 本教程将介绍如何使用 Visual Studio Code 的 Azure API 管理扩展来导入、测试和管理 API。
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: ecdb97ad62a9b75279546493bbbf185f92fd29f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625765"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>教程：使用 Visual Studio Code 的 API 管理扩展来导入和管理 API

本教程将介绍如何使用 Visual Studio Code 的 API 管理扩展来实现 API 管理中的常见操作。 使用熟悉的 Visual Studio Code 环境导入、更新、测试和管理 API。

你将学习如何执行以下操作：

> [!div class="checklist"]
> * 将 API 导入 API 管理
> * 编辑 API
> * 应用 API 管理策略
> * 测试 API


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API 管理扩展中的 API":::

有关其他 API 管理功能的介绍，请使用 [Azure 门户](import-and-publish.md)参阅“API 管理”教程。

## <a name="prerequisites"></a>先决条件
- 理解 [Azure API 管理的术语](api-management-terminology.md)
- 确保已安装 [Visual Studio Code](https://code.visualstudio.com/) 以及 [Visual Studio Code 的 Azure API 管理扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)最新版。
- [创建 API 管理实例](vscode-create-service-instance.md)

## <a name="import-an-api"></a>导入 API

以下示例将 JSON 格式的 OpenAPI 规范导入 API 管理。 Microsoft 提供本示例中使用的后端 API，并将其托管在 Azure 上 (`https://conferenceapi.azurewebsites.net?format=json`)。

1. 在 Visual Studio Code 中，选择“活动栏”中的 Azure 图标。
1. 在“资源管理器”窗格中，展开你创建的 API 管理实例。
1. 右键单击 API，然后选择“从 OpenAPI 链接导入” 。 
1. 出现提示时，输入以下值：
    1. JSON 格式的内容的“OpenAPI 链接”。 对于本示例： *https://conferenceapi.azurewebsites.net?format=json* 。
    此 URL 是实现示例 API 的服务。 API 管理将请求转发到此地址。
    1. 在 API 管理实例中唯一的 API 名称，如 demo-conference-api。 此名称只能包含字母、数字和连字符。 第一个字符和最后一个字符必须必须为字母数字字符。 将在调用 API 的路径中使用此名称。

成功导入 API 后，它将显示在“资源管理器”窗格中，可用的 API 操作显示在“操作”节点下。

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="资源管理器窗格中导入的 API":::

## <a name="edit-the-api"></a>编辑 API

可在 Visual Studio Code 中编辑 API。 例如，在编辑器窗口中编辑 API 的“资源管理器 JSON”说明，以删除用于访问 API 的 http 协议。 然后选择“文件” > “保存” 。

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="编辑 JSON 说明":::

若要编辑 OpenAPI 格式，请在“资源管理器”窗格中右键单击 API 名称，然后选择“编辑 OpenAPI”。 进行所需的更改，然后选择“文件” > “保存” 。

## <a name="apply-policies-to-the-api"></a>将策略应用到 API 

API 管理提供可为 API 配置的[策略](api-management-policies.md)。 策略是一组语句，在请求或 API 的响应时按顺序执行。 策略可以是全局的（适用于 API 管理实例中的所有 API），也可以将策略范围限定为特定 API 或 API 操作。

本部分介绍如何将一些常见的出站策略应用于转换 API 响应的 API。 此示例中的策略将更改响应标头并隐藏响应正文中显示的原始后端 URL。

1. 在“资源管理器”窗格中，在你导入的 demo-conference-api 下选择“策略”。 策略文件将在编辑器窗口中打开。 此文件将为 API 中的所有操作配置策略。 

1. 使用以下 `<outbound>` 元素中的内容更新文件：
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * 第一个 `set-header` 策略添加自定义响应标头，用于演示。
    * 第二个 `set-header` 策略删除 X-Powered-By 标头（如果存在）。 此标头可能披露 API 后端使用的应用程序框架，发布者通常会将其删除。
    * `redirect-content-urls` 策略重写（屏蔽）响应正文中的链接，使其通过 API 管理网关指向等效的链接。
    
1. 保存文件。 如果出现提示，请选择“上传”将文件上传到云中。

## <a name="test-the-api"></a>测试 API

### <a name="get-the-subscription-key"></a>获取订阅密钥

若要测试导入的 API 以及应用的策略，需要 API 管理实例的订阅密钥。

1. 在“资源管理器”窗格中，右键单击 API 管理实例的名称。
1. 选择“复制订阅密钥”。

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="复制订阅密钥":::

### <a name="test-an-api-operation"></a>测试 API 操作

1. 在“资源管理器”窗格中，展开你导入的 demo-conference-api 下的“操作”节点。
1. 选择一个操作，如“GetSpeakers”，然后右键单击该操作并选择“测试操作”。
1. 在编辑器窗口中的 Ocp-Apim-Subscription-Key 旁边，将 `{{SubscriptionKey}}` 替换为你复制的订阅密钥。
1. 选择“发送请求”。  

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="从 Visual Studio Code 发送 API 请求":::

如果请求成功，后端以“200 正常”和某些数据做出响应。

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API 测试操作":::

请注意响应中的以下详细信息：
* 会将 Custom 标头添加到响应中。
* 响应中不会出现 X-Powered-By 标头。
* API 后端的 URL 将重定向到 API 管理网关，在本例中为 `https://apim-hello-world.azure-api.net/demo-conference-api`。

### <a name="trace-the-api-operation"></a>跟踪 API 操作

若要获取详细跟踪信息以帮助调试 API 操作，请选择“Ocp-APIM-Trace-Location”旁边显示的链接。 

位于该位置的 JSON 文件包含入站、后端和出站跟踪信息，因此你可以确定发出请求后哪些地方出现了问题。

> [!TIP]
> 测试 API 操作时，API 管理扩展允许进行可选的[策略调试](api-management-debug-policies.md)（在开发人员服务层中可用）。

## <a name="clean-up-resources"></a>清理资源

当不再需要 API 管理实例和资源组时，请通过单击右键并选择“在门户中打开”来[删除 API 管理服务](get-started-create-service-instance.md#clean-up-resources)及其资源组，从而删除该实例和资源组。

另外，你还可以选择“删除 API 管理”，以仅删除 API 管理实例（此操作不会删除其资源组）。

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="从 VS Code 中删除 API 管理实例":::

## <a name="next-steps"></a>后续步骤

本教程介绍了 Visual Studio Code 的 API 管理扩展的多项功能，可用于导入和管理 API。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 将 API 导入 API 管理
> * 编辑 API
> * 应用 API 管理策略
> * 测试 API

API 管理扩展提供了用于处理 API 的额外功能。 例如[调试策略](api-management-debug-policies.md)（在开发人员服务层中可用），或创建和管理[命名值](api-management-howto-properties.md)。
