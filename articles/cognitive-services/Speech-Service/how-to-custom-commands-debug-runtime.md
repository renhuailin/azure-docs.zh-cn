---
title: 运行时自定义命令应用程序的故障排除指南
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在自定义命令应用程序中调试运行时错误。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 7577a1c2666c8b5d34920e3827a9f4549b69edd2
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455805"
---
# <a name="troubleshoot-a-custom-commands-application-at-runtime"></a>在运行时对自定义命令应用程序进行故障排除

本文介绍如何在运行自定义命令应用程序期间出现错误时进行调试。 

## <a name="connection-failed"></a>连接失败

如果从[客户端应用程序（具有语音 SDK）](./how-to-custom-commands-setup-speech-sdk.md)或 [Windows 语音助理客户端](./how-to-custom-commands-developer-flow-test.md)运行自定义命令应用程序，则可能会遇到如下所示的连接错误：

| 错误代码 | 详细信息 |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: WebSocket 升级失败并出现身份验证错误 |
| [1002](#error-1002) | 服务器返回了状态代码“404”，而状态代码应为“101”。 |

### <a name="error-401"></a>错误 401
- 客户端应用程序中指定的区域与自定义命令应用程序的区域不匹配

- 语音资源键无效
    
    请确保语音资源键正确。

### <a name="error-1002"></a>错误 1002 
- 自定义命令应用程序未发布
    
    在门户中发布应用程序。

- 自定义命令 applicationId 无效

    请确保自定义命令应用程序 ID 是正确的。
 自定义命令应用程序不在语音资源范围内

    请确保在语音资源下创建自定义命令应用程序。

有关排查连接问题的详细信息，请参阅 [Windows 语音助理客户端故障排除](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>对话已取消

运行自定义命令应用程序时，会在出现一些错误时取消对话。

- 如果在门户中测试应用程序，它会直接显示取消说明，并播放错误耳标。 

- 如果使用 [Windows 语音助理客户端](./how-to-custom-commands-developer-flow-test.md)运行应用程序，则会播放错误耳标。 可以在“活动日志”下找到“事件: CancelledDialog”。

- 如果你按照我们的客户端应用程序示例[客户端应用程序（具有语音 SDK）](./how-to-custom-commands-setup-speech-sdk.md)操作，则会播放错误耳标。 可以在“状态”下找到“事件: CancelledDialog”。

- 如果要构建自己的客户端应用程序，始终可以设计所需的逻辑来处理 CancelledDialog 事件。

CancelledDialog 事件包含取消代码和说明，如下所示：

| 取消代码 | 取消说明 |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | 在允许的最大回合数之后没有任何进展 |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | 超出识别器用量配额 |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | 与识别器的连接失败 |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | 无法通过当前订阅访问此应用程序 |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | 输入超出了识别器支持的最大长度 |
| [RecognizerNotFound](#recognizer-not-found) | 找不到识别器 |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | 识别器查询无效 |
| [RecognizerError](#recognizer-return-an-error) | 识别器返回一个错误 |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>在允许的最大回合数之后没有任何进展
当所需的槽在一定数量的回合后未成功更新时，将取消对话。 内置最大数量为 3。

### <a name="recognizer-usage-quota-exceeded"></a>超出识别器用量配额
语言理解 (LUIS) 对资源用量有限制。 导致“超出识别器用量配额错误”的常见原因有： 
- 你的 LUIS 创作超出限制

    将预测资源添加到自定义命令应用程序： 
    1. 转到“设置”、“LUIS 资源”
    1. 从“预测资源”中选择预测资源，或单击“创建新资源” 

- LUIS 预测资源超出限制

    如果你使用的是 F0 预测资源，则它的限制为每月 10000 次，每秒 5 次查询。

有关 LUIS 资源限制的详细信息，请参阅[语言理解资源用量和限制](../luis/luis-limits.md#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>与识别器的连接失败
通常这意味着与语言理解 (LUIS) 识别器的暂时性连接失败。 再试一次，应可以解决问题。

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>无法通过当前订阅访问此应用程序
你的订阅无权访问 LUIS 应用程序。 

### <a name="input-exceeds-the-maximum-supported-length"></a>输入超出支持的最大长度
输入超过 500 个字符。 对于输入言语，我们仅允许最多 500 个字符。

### <a name="invalid-query-for-the-recognizer"></a>识别器查询无效
输入超过 500 个字符。 对于输入言语，我们仅允许最多 500 个字符。

### <a name="recognizer-return-an-error"></a>识别器返回一个错误
LUIS 识别器在尝试识别输入时返回一个错误。

### <a name="recognizer-not-found"></a>找不到识别器
找不到在你的自定义命令对话框模型中指定的识别器类型。 目前，我们仅支持[语言理解 (LUIS) 识别器](https://www.luis.ai/)。

## <a name="other-common-errors"></a>其他常见错误
### <a name="unexpected-response"></a>意外响应
意外响应可能导致多个问题。 首先要检查以下内容：
- 示例句子中的是/否意向

    因为我们目前不支持是/否意向，除非使用确认功能。 不会检测到示例句子中定义的所有“是/否”意向。

- 命令中的类似意向和示例句子

    当两个命令有类似的意向和示例句子时，LUIS 识别准确性可能会受到影响。 可以尝试使命令功能和示例句子尽可能不同。

    若要获得提高识别准确性的最佳做法，请参阅 [LUIS 最佳做法](../luis/luis-concept-best-practices.md)。

- 对话已取消
    
    在上述部分中检查取消的原因。

### <a name="error-while-rendering-the-template"></a>呈现模板时出错
语音响应中使用了未定义的参数。 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>对象引用未设置为对象的实例
在“将活动发送到客户端”操作中定义的 JSON 有效负载中，有一个空参数。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看 GitHub 上的示例](https://aka.ms/speech/cc-samples)
