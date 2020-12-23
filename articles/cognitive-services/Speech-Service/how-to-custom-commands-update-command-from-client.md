---
title: 更新客户端应用程序中的命令
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
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560281"
---
# <a name="update-a-command-from-a-client-app"></a>更新客户端应用程序中的命令

本文介绍如何从客户端应用程序更新正在进行的命令。

## <a name="prerequisites"></a>先决条件
> [!div class = "checklist"]
> * 之前[创建的自定义命令应用](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>更新命令的状态

如果客户端应用程序要求在不使用语音输入的情况下更新正在进行的命令的状态，则可以发送事件以更新命令。

为了说明这种情况，请发送以下事件活动，以更新)  (正在进行的命令的状态 `TurnOnOff` ： 

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

我们来看看此活动的关键特性：

| Attribute | 说明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | 该活动的类型为 `"event"` ，并且该事件的名称需要为 `"RemoteUpdate"` 。 |
| **value** | 属性 `"value"` 包含更新当前命令所需的属性。 |
| **updatedCommand** | 属性 `"updatedCommand"` 包含命令的名称。 在该属性中， `"updatedParameters"` 是一个映射，其中包含参数的名称及其更新的值。 |
| **cancel** | 如果需要取消正在进行的命令，请将属性设置 `"cancel"` 为 `true` 。 |
| **updatedGlobalParameters** | 属性 `"updatedGlobalParameters"` 是类似于的映射 `"updatedParameters"` ，但它用于全局参数。 |
| **processTurn** | 如果在发送活动后需要处理 turn，请将属性设置 `"processTurn"` 为 `true` 。 |

可在自定义命令门户中测试此方案：

1. 打开前面创建的自定义命令应用程序。 
1. 选择 **定型** ，然后进行 **测试**。
1. 发送 `turn` 。
1. 打开侧边面板，然后选择 " **活动编辑器**"。
1. 键入并发送 `RemoteCommand` 上一部分中指定的事件。
    > [!div class="mx-imgBorder"]
    > ![显示远程命令的事件的屏幕截图。](media/custom-commands/send-remote-command-activity.png)

请注意，参数的值 `"OnOff"` 是如何 `"on"` 通过客户端（而不是语音或文本）的活动设置的。

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>更新命令的参数的目录

在配置参数的有效选项列表时，会为应用程序全局定义参数的值。 

在我们的示例中， `SubjectDevice` 参数将具有受支持的值的固定列表，而与会话无关。

如果要在每个会话中向参数的目录添加新条目，可以发送以下活动：

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
在此活动中，你在命令中为参数的目录添加了一个条目 `"stereo"` `"SubjectDevice"` `"TurnOnOff"` 。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="显示目录更新的屏幕截图。":::

请注意以下几点：
- 仅在 (理想情况下，才需要发送此活动一次，) 启动连接。
- 发送此活动后，应等待事件 `ParameterCatalogsUpdated` 发送回客户端。

## <a name="add-more-context-from-the-client-application"></a>添加来自客户端应用程序的更多上下文

可以从客户端应用程序为每个会话设置其他上下文，以后可在自定义命令应用程序中使用该上下文。 

例如，请考虑要将连接到自定义命令应用程序的设备的 ID 和名称发送到的方案。

若要测试此方案，让我们在当前应用程序中创建一个新命令：
1. 创建一个名为的新命令 `GetDeviceInfo` 。
1. 添加的示例句子 `get device info` 。
1. 完成规则 **完成后**，添加包含的属性的 " **发送语音响应** " 操作 `clientContext` 。
   ![显示使用上下文发送语音的响应的屏幕截图。](media/custom-commands/send-speech-response-context.png)
1. 保存、定型和测试您的应用程序。
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
1. 发送文本 `get device info` 。
   ![显示用于发送客户端上下文的活动的屏幕截图。](media/custom-commands/send-client-context-activity.png)

请注意以下几点：
- 仅在 (理想情况下，才需要发送此活动一次，) 启动连接。
- 您可以使用的复杂对象 `clientContext` 。
- 你可以 `clientContext` 在语音响应中使用，以便发送活动和调用 web 终结点。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从 web 终结点更新命令](./how-to-custom-commands-update-command-from-web-endpoint.md)
