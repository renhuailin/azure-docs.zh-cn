---
title: 从 web 终结点更新命令
titleSuffix: Azure Cognitive Services
description: 了解如何通过调用 web 终结点更新命令的状态。
services: cognitive-services
author: laujan
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: lajanuar
ms.openlocfilehash: 9900d873de5efac89db13b80c0cc37167a0cee05
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122066610"
---
# <a name="update-a-command-from-a-web-endpoint"></a>从 web 终结点更新命令

如果客户端应用程序要求不使用语音输入来更新正在进行的命令的状态，则可以通过调用 web 终结点来更新命令。

在本文中，你将学习如何从 web 终结点更新正在进行的命令。

## <a name="prerequisites"></a>先决条件
> [!div class = "checklist"]
> * 之前[创建的自定义命令应用](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>创建 Azure 函数 

对于本例，你需要一个 HTTP 触发的 [Azure 函数](../../azure-functions/index.yml)，以支持以下输入（或此输入的一个子集）：

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

让我们来看此输入的关键属性：

| 属性 | 说明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 对话的唯一标识符。 请注意，此 ID 可以从客户端应用生成。 |
| **currentCommand** | 对话中当前启用的命令。 |
| **name** | 命令的名称。 `parameters` 属性是一个包含参数当前值的映射。 |
| **currentGlobalParameters** | 类似于 `parameters` 的映射，但用于全局参数。 |

Azure 函数的输出需要支持以下格式：

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

这种格式就是[从客户端更新命令](./how-to-custom-commands-update-command-from-client.md)时使用的格式，你可能认识。 

现在，基于 Node.js 创建一个 Azure 函数。 复制/粘贴以下代码：

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

从自定义命令调用此 Azure 函数时，将发送对话的当前值。 返回要更新的参数或是否要取消当前命令。

## <a name="update-the-existing-custom-commands-app"></a>更新现有的自定义命令应用

让我们将 Azure 函数与现有的自定义命令应用关联起来：

1. 添加一个名为 `IncrementCounter` 的新命令。
1. 只添加一个示例语句，值为 `increment`。
1. 添加一个名为 `Counter`（与 Azure 函数中指定的名称相同）的新参数，类型为 `Number`，默认值为 `0`。
1. 创建一个带有 Azure 函数 URL 的新 web 终结点 `IncrementEndpoint`，并将“远程更新”设置为“已启用”。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="显示通过远程更新设置 web 终结点的屏幕截图。":::
1. 创建一个名为 IncrementRule 的新交互规则，并添加一个“调用 web 终结点”操作。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="显示创建交互规则的屏幕截图。":::
1. 在“操作配置”中，选择 `IncrementEndpoint`。 将“成功时”配置为“发送语音响应”（值为 `Counter`），并为“失败时”配置一条错误消息。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="显示为调用 web 终结点设置增量计数器的屏幕截图。":::
1. 将该规则的执行后状态设置为“等待用户输入”。

## <a name="test-it"></a>测试

1. 保存并训练你的应用。
1. 选择“测试”。
1. 发送几次 `increment`（`IncrementCounter` 命令的示例语句）。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example-no-mic.png" alt-text="显示增量计数器示例的屏幕截图。":::

请注意 Azure 函数每次怎样递增 `Counter` 参数的值。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为自定义命令应用程序启用 CI/CD 过程](./how-to-custom-commands-deploy-cicd.md)