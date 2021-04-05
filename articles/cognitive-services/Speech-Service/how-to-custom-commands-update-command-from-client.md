---
title: 从客户端应用更新命令
titleSuffix: Azure Cognitive Services
description: 了解如何从客户端应用程序更新命令。
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560281"
---
# <a name="update-a-command-from-a-client-app"></a>从客户端应用更新命令

本文介绍如何从客户端应用程序更新正在运行的命令。

## <a name="prerequisites"></a>先决条件
> [!div class = "checklist"]
> * 之前[创建的自定义命令应用](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>更新命令的状态

如果客户端应用程序要求更新一个不使用语音输入的运行中命令的状态，你可以发送一个事件来更新该命令。

为了演示此方案，请发送以下事件活动来更新正在运行的命令 (`TurnOnOff`) 的状态： 

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

让我们看看此活动的关键属性：

| 属性 | 说明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| type | 该活动的类型为 `"event"`，该事件的名称需是 `"RemoteUpdate"`。 |
| **value** | 属性 `"value"` 包含更新当前命令所需的属性。 |
| **updatedCommand** | 属性 `"updatedCommand"` 包含命令的名称。 在该属性中，`"updatedParameters"` 是与参数的名称及其更新值建立的映射。 |
| **cancel** | 如果需要取消正在运行的命令，请将属性 `"cancel"` 设置为 `true`。 |
| **updatedGlobalParameters** | 与 `"updatedParameters"` 一样，属性 `"updatedGlobalParameters"` 也是一个映射，但它用于全局参数。 |
| **processTurn** | 如果需要在发送活动之后处理轮次，请将属性 `"processTurn"` 设置为 `true`。 |

可在自定义命令门户中测试此方案：

1. 打开前面创建的自定义命令应用程序。 
1. 依次选择“训练”、“测试”。 
1. 发送 `turn`。
1. 打开边侧面板并选择“活动编辑器”。
1. 键入并发送在上一部分指定的 `RemoteCommand` 事件。
    > [!div class="mx-imgBorder"]
    > ![显示用于远程命令的事件的屏幕截图。](media/custom-commands/send-remote-command-activity.png)

请注意参数 `"OnOff"` 的值是如何通过来自客户端的活动（而不是语音或文本）设置为 `"on"` 的。

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>更新命令的参数目录

为某个参数配置有效选项列表时，将为应用程序全局定义该参数的值。 

在本示例中，无论对话是什么，`SubjectDevice` 参数都具有固定的受支持值列表。

若要将新条目添加到每个对话的参数目录，可发送以下活动：

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
你已使用此活动将 `"stereo"` 的条目添加到 `"TurnOnOff"` 命令中 `"SubjectDevice"` 参数的目录。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="展示目录更新的屏幕截图。":::

请注意以下几点：
- 只需发送此活动一次（理想情况下，在启动连接后立即发送）。
- 发送此活动后，应等待事件 `ParameterCatalogsUpdated` 发回到客户端。

## <a name="add-more-context-from-the-client-application"></a>添加来自客户端应用程序的更多上下文

可为每个对话设置来自客户端应用程序的更多上下文，以后可以在自定义命令应用程序中使用该上下文。 

例如，假设你想要实现这样一种方案：发送已连接到自定义命令应用程序的设备的 ID 和名称。

若要测试此方案，让我们在当前应用程序中创建一个新命令：
1. 创建名为 `GetDeviceInfo` 的新命令。
1. 添加示例句子 `get device info`。
1. 在完成规则“完成”中，添加包含 `clientContext` 属性的“发送语音响应”操作。 
   ![显示用于通过上下文发送语音的响应的屏幕截图。](media/custom-commands/send-speech-response-context.png)
1. 保存、训练并测试你的应用程序。
1. 在测试窗口中，发送一个活动来更新客户端上下文。

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
   ![显示用于发送客户端上下文的活动的屏幕截图。](media/custom-commands/send-client-context-activity.png)

请注意以下几点：
- 只需发送此活动一次（理想情况下，在启动连接后立即发送）。
- 可对 `clientContext` 使用复杂对象。
- 可以在语音响应中使用 `clientContext` 来发送活动和调用 Web 终结点。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从 web 终结点更新命令](./how-to-custom-commands-update-command-from-web-endpoint.md)
