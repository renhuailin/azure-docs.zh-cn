---
title: 教程 - 使用请求跟踪在 Azure API 管理中调试 API
description: 请按照本教程的步骤在 Azure API 管理中启用跟踪和检查请求处理步骤。
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: danlep
ms.openlocfilehash: 59d095b45b10ad6ed093107cc8d4589e0915161b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643301"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>教程：使用请求跟踪调试 API

本教程介绍如何在 Azure API 管理中检查（跟踪）请求处理，帮助你调试 API 和对其进行故障排除。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 跟踪示例调用
> * 查看请求处理步骤

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API 检查器":::

## <a name="prerequisites"></a>先决条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
+ 完成以下教程：[导入和发布第一个 API](import-and-publish.md)。

## <a name="verify-allow-tracing-setting"></a>验证“允许跟踪”设置 

必须为用于 API 的订阅启用“允许跟踪”设置。 如果使用的是内置所有访问订阅，则默认启用该设置。 若要在门户中进行验证，请导航至 API 管理实例，然后选择“订阅”。

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="允许跟踪订阅":::

## <a name="trace-a-call"></a>跟踪调用

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到 API Management 实例。
1. 选择“API”。
1. 在 API 列表中选择“演示会议 API”。
1. 选择“测试”选项卡。
1. 选择“GetSpeakers”操作。
1. 确认 HTTP 请求头包含“Ocp-Apim-Trace:True”和“Ocp-Apim-Subscription-Key”的有效值。 如果不包含，请选择“+ 添加标头”以添加标头。
1. 选择“发送”以进行 API 调用。

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="配置 API 跟踪":::

> [!TIP]
> 如果 HTTP 请求中没有自动填充 Ocp-Apim-Subscription-Key，则可以在门户中检索它。 选择“订阅”，然后打开订阅的上下文菜单 (…) 。 选择“显示/隐藏密钥”。 也可以按需再生成密钥。 然后，将密钥添加到标头。

## <a name="review-trace-information"></a>查看跟踪信息

1. 调用结束后，转到“HTTP 响应”中的“跟踪”选项卡 。
1. 选择以下任何链接，跳至详细的跟踪信息：“入站”、“后端”和“出站”  。

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="查看响应跟踪":::

    * 入站 - 显示从调用方收到的原始请求 API 管理和应用于该请求的策略。 例如，如果在[教程：转换并保护 API](transform-api.md) 中添加了策略，它们将显示在此处。

    * 后端 - 显示 API 管理发送到 API 后端的请求以及收到的响应。

    * 出站 - 显示在将响应发送给调用方之前应用于该响应的策略。

    > [!TIP]
    > 每个步骤还显示了自 API 管理收到请求以来消逝的时间。

1. 在“消息”选项卡上，ocp-apim-trace-location 标头显示存储在 Azure blob 存储中的跟踪数据所在的位置 。 如果需要，请转到此位置以检索跟踪。

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Azure 存储中的跟踪位置":::
## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 跟踪示例调用
> * 查看请求处理步骤

转到下一教程：

> [!div class="nextstepaction"]
> [使用修订版](api-management-get-started-revise-api.md)
