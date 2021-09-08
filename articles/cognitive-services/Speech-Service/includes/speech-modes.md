---
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 74ddfac57de8a45ea24a2ff0901cfa572e2f0021
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537817"
---
**交互式**
- 适用于命令和控制方案。
- 分段超时值为 X。
- 在一个识别的言语结束时，服务会停止处理来自该请求 ID 的音频，并结束该回合。 连接未关闭。
- 识别的最大限制为 20s。
- 要调用的典型 Carbon 调用是 `RecognizeOnceAsync`。

**聊天**
- 适用于长期识别。
- 分段超时值为 Y。(Y != X)
- 将处理多个完整的言语，而不结束该回合。
- 如果太过静默，将结束该回合。
- Carbon 将继续使用新的请求 ID 并根据需要重播音频。
- 语音识别 10 分钟后，该服务将强行断开连接。
- Carbon 会重新连接并重播未确认的音频。
- 在 Carbon 中用 `StartContinuousRecognition` 调用。

**听写**
- 允许用户通过说话来指定标点。
- 通过在 `SpeechConfig` 对象上指定 `EnableDictation` 在 Carbon 中调用，而不考虑启动识别的 API 调用。
- 第一方群集返回中间结果的 `speech.fragment` 消息，第三方返回 `speech.hypothesis` 消息<sup></sup><sup></sup>。