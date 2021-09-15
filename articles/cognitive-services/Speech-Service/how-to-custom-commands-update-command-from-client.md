---
title: 从客户端应用更新命令参数
titleSuffix: Azure Cognitive Services
description: 了解如何从客户端应用程序更新命令。
services: cognitive-services
author: laujan
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: lajanuar
ms.openlocfilehash: 9ae8d43be88558469165e926994f994aa0fe5736
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455887"
---
# <a name="update-a-command-from-a-client-app"></a>从客户端应用更新命令

在本文中，你将学习如何从客户端应用程序更新正在进行的命令。

## <a name="prerequisites"></a>必备条件
> [!div class = "checklist"]
> * 之前[创建的自定义命令应用](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>更新命令的状态

如果客户端应用程序要求不使用语音输入来更新正在进行的命令的状态，则可以发送事件来更新命令。

为了说明此情况，请发送以下事件活动，以更新正在进行的命令 (`TurnOnOff`) 的状态： 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

让我们来看看此活动的关键属性：

| 属性 | 说明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| type | 活动的类型为 `"event"`。 |
| **name** | 事件的名称需为 `"RemoteUpdate"`。 |
| **value** | 属性 `"value"` 包含更新当前命令所需的属性。 |
| **updatedCommand** | 属性 `"updatedCommand"` 包含命令的名称。 在该属性中，`"updatedParameters"` 是一个映射，其中包含参数的名称及其更新后的值。 |
| **cancel** | 如果需要取消正在进行的命令，需要将属性 `"cancel"` 设置为 `true`。 |
| **updatedGlobalParameters** | 属性 `"updatedGlobalParameters"` 是类似于 `"updatedParameters"` 的映射，但用于全局参数。 |
| **processTurn** | 如果在发送活动后需要处理 turn，需要将属性 `"processTurn"` 设置为 `true`。 |

可以在自定义命令门户中测试此方案：

1. 打开之前创建的自定义命令应用程序。 
1. 选择“训练”，然后选择“测试” 。
1. 发送 `turn`。
1. 打开侧面板，并选择“活动编辑器”。
1. 键入并发送在上一部分中指定的 `RemoteCommand` 事件。
    > [!div class="mx-imgBorder"]
    > ![显示远程命令事件的屏幕截图。](media/custom-commands/send-remote-command-activity-no-mic.png)

注意参数 `"OnOff"` 的值是如何通过客户端（而不是语音或文本）中的活动设置为 `"on"` 的。

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>更新命令参数的目录

在配置参数的有效选项列表时，系统会为应用程序全局定义该参数的值。 

在我们的示例中，不管对话如何，`SubjectDevice` 参数都将具有一个固定的支持值列表。

如果要为每个对话的参数目录添加新条目，可以发送以下活动：

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
在此活动中，你将 `"stereo"` 的条目添加到命令 `"TurnOnOff"` 中参数 `"SubjectDevice"` 的目录中。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="显示目录更新的屏幕截图。":::

请注意以下几点：
- 只需发送一次此活动（理想情况下，在启动连接后立即发送）。
- 发送此活动后，应等待系统将事件 `ParameterCatalogsUpdated` 发送回客户端。

## <a name="add-more-context-from-the-client-application"></a>从客户端应用程序添加更多上下文

可以从客户端应用程序为每个对话设置以后可在自定义命令应用程序中使用的其他上下文。 

例如，请考虑这样一种情况：你希望发送连接到自定义命令应用程序的设备的 ID 和名称。

为了测试此情况，让我们在当前应用程序中创建一个新的命令：
1. 创建一个名为 `GetDeviceInfo` 的新命令。
1. 添加一个示例句子 `get device info`。
1. 在完成规则“已完成”中，添加包含 `clientContext` 属性的“发送语音响应”操作 。
   ![此屏幕截图显示发送包含上下文的语音的响应。](media/custom-commands/send-speech-response-context.png)
1. 保存、训练和测试应用程序。
1. 在测试窗口中，发送活动以更新客户端上下文。

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. 发送文本 `get device info`。
   ![显示发送客户端上下文的活动的屏幕截图。](media/custom-commands/send-client-context-activity-no-mic.png)

请注意以下几点：
- 只需发送一次此活动（理想情况下，在启动连接后立即发送）。
- 可以使用 `clientContext` 的复杂对象。
- 可以在语音响应中使用 `clientContext`，以便发送活动和调用 Web 终结点。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从 web 终结点更新命令](./how-to-custom-commands-update-command-from-web-endpoint.md)
