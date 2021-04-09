---
title: 从 web 终结点更新命令
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Web 终结点调用更新命令的状态。
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560264"
---
# <a name="update-a-command-from-a-web-endpoint"></a>从 web 终结点更新命令

如果客户端应用程序要求更新一个不使用语音输入的运行中命令的状态，你可以使用 Web 终结点调用来更新该命令。

本文介绍如何从 Web 终结点更新正在运行的命令。

## <a name="prerequisites"></a>先决条件
> [!div class = "checklist"]
> * 之前[创建的自定义命令应用](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>创建 Azure 函数 

对于此示例，需要一个由 HTTP 触发的、支持以下输入（或此输入的子集）的 [Azure 函数](../../azure-functions/index.yml)：

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

让我们看看此输入的关键属性：

| 属性 | 说明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 对话的唯一标识符。 请注意，可以从客户端应用生成此 ID。 |
| **currentCommand** | 对话中当前处于活动状态的命令。 |
| **name** | 命令的名称。 `parameters` 属性是与当前参数值建立的映射。 |
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

你可能认识此格式，因为它与[从客户端更新命令](./how-to-custom-commands-update-command-from-client.md)时使用的格式相同。 

现在创建一个基于 Node.js 的 Azure 函数。 复制/粘贴以下代码：

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

从自定义命令调用此 Azure 函数时，将发送当前对话值。 将返回所要更新的参数，或者确定是否要取消当前命令。

## <a name="update-the-existing-custom-commands-app"></a>更新现有的自定义命令应用

让我们将 Azure 函数挂接到现有的自定义命令应用：

1. 添加名为 `IncrementCounter` 的新命令。
1. 只添加一个包含 `increment` 值的示例句子。
1. 添加名为 `Counter`（与 Azure 函数中指定的名称相同）、类型为 `Number` 且包含默认值 `0` 的新参数。
1. 使用 Azure 函数的 URL 添加名为 `IncrementEndpoint` 的新 Web 终结点，其“远程更新”设置为“已启用”。 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="展示如何设置已启用远程更新的 Web 终结点的屏幕截图。":::
1. 创建名为 **IncrementRule** 的新交互规则，并添加“调用 Web 终结点”操作。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="展示如何创建交互规则的屏幕截图。":::
1. 在操作配置中选择 `IncrementEndpoint`。 将“成功时”配置为“发送语音响应”并显示 `Counter` 的值，将“失败时”配置为显示错误消息。  
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="展示如何设置一个用于调用 Web 终结点的增量计数器的屏幕截图。":::
1. 将规则的执行后状态设置为“等待用户输入”。

## <a name="test-it"></a>测试

1. 保存并训练应用。
1. 选择“测试”。
1. 发送 `increment`（`IncrementCounter` 命令的示例句子）几次。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="显示增量计数器示例的屏幕截图。":::

请注意 Azure 函数如何在每个轮次中递增 `Counter` 参数的值。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为自定义命令应用程序启用 CI/CD 过程](./how-to-custom-commands-deploy-cicd.md)