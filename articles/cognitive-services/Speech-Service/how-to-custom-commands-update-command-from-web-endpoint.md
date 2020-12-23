---
title: 从 web 终结点更新命令
titleSuffix: Azure Cognitive Services
description: 了解如何使用对 web 端点的调用更新命令的状态。
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560264"
---
# <a name="update-a-command-from-a-web-endpoint"></a>从 web 终结点更新命令

如果客户端应用程序需要对不使用语音输入的正在进行的命令的状态进行更新，则可以使用对 web 端点的调用来更新该命令。

在本文中，你将了解如何从 web 终结点更新正在进行的命令。

## <a name="prerequisites"></a>先决条件
> [!div class = "checklist"]
> * 之前[创建的自定义命令应用](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>创建 Azure 函数 

在此示例中，你将需要一个支持以下输入 (的 HTTP 触发的 [Azure 函数](../../azure-functions/index.yml) 或此输入) 的一个子集：

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

让我们查看一下此输入的关键特性：

| Attribute | 说明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 会话的唯一标识符。 请注意，此 ID 可以从客户端应用程序生成。 |
| **currentCommand** | 会话中当前活动的命令。 |
| **name** | 命令的名称。 `parameters`特性是一个映射，其中包含参数的当前值。 |
| **currentGlobalParameters** | 类似于的映射 `parameters` ，但用于全局参数。 |

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

你可能会识别此格式，因为它与你在 [更新客户端中的命令](./how-to-custom-commands-update-command-from-client.md)时使用的格式相同。 

现在，创建基于 Node.js 的 Azure 函数。 复制/粘贴以下代码：

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

从自定义命令调用此 Azure 函数时，将发送会话的当前值。 返回要更新的参数，或者如果要取消当前命令，则返回。

## <a name="update-the-existing-custom-commands-app"></a>更新现有的自定义命令应用

让我们将 Azure 函数与现有的自定义命令应用程序挂钩：

1. 添加一个名为的新命令 `IncrementCounter` 。
1. 只添加一个带有值的示例句子 `increment` 。
1. 添加一个名为的新参数 `Counter` (与 Azure function) 中指定的名称相同，其 `Number` 默认值为 `0` 。
1. 使用 Azure 函数的 URL 添加一个名为的新 web 终结点 `IncrementEndpoint` ，其中 " **远程更新** " 设置为 " **已启用**"。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="屏幕截图，显示如何设置具有远程更新的 web 终结点。":::
1. 创建名为 **IncrementRule** 的新交互规则并添加 **调用 web 终结点** 操作。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="显示交互规则创建的屏幕截图。":::
1. 在 "操作配置" 中，选择 `IncrementEndpoint` 。 **在成功** 时配置以使用的值 **发送语音响应** `Counter` ，并 **在失败** 时配置错误消息。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="屏幕截图，显示为调用 web 终结点设置增量计数器。":::
1. 设置规则的执行后状态，以 **等待用户的输入**。

## <a name="test-it"></a>测试

1. 保存并训练您的应用程序。
1. 选择“测试”。
1. 发送 `increment` 几次 (，这是命令) 的示例句子 `IncrementCounter` 。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="显示增量计数器示例的屏幕截图。":::

请注意，Azure 函数每次都会递增参数的值 `Counter` 。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为自定义命令应用程序启用 CI/CD 过程](./how-to-custom-commands-deploy-cicd.md)