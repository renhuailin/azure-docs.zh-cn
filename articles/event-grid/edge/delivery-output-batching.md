---
title: Azure 事件网格 IoT Edge 中的输出批处理 | Microsoft Docs
description: IoT Edge 上的事件网格中的输出批处理。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0ae2261f8278c4d5e1944b01a9731afd293df20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171629"
---
# <a name="output-batching"></a>输出批处理

事件网格支持在单个传送请求中传送多个事件。 利用此功能，无需支付每个请求的 HTTP 开销，即可增加总体传送吞吐量。 默认情况下批处理处于关闭状态，但可以根据订阅打开。

> [!WARNING]
> 即使订阅者代码可能需要为每个批处理请求执行更多操作，处理每个传送请求所允许的最大持续时间也不变。 传送超时默认为 60 秒。

## <a name="batching-policy"></a>批处理策略

可以通过调整以下两个设置，为每个订阅者自定义事件网格的批处理行为：

* 每批最大事件数

  此设置用于设置可添加到批处理传送请求的事件数的上限。

* 首选批大小 (KB)

  此旋钮用于进一步控制可通过每个传送请求发送的最大 KB 数

## <a name="batching-behavior"></a>批处理行为

* 全或无

  事件网格使用“全或无”语义运行。 它不支持批处理传送部分成功。 订阅者应注意，只为每批请求他们可在 60 秒内合理处理的尽可能多的事件。

* 乐观批处理

  批处理策略设置对批处理行为的限制并不严格，应尽力遵守。 如果事件处理率较低，则会发现批大小小于每批请求的最大事件数。

* 默认设置为关闭

  默认情况下，事件网格仅向每个传送请求添加一个事件。 打开批处理的方法是在事件订阅 JSON 中设置本文前面提到的一个设置。

* 默认值

  创建事件订阅时，不必同时指定设置（每批最大事件数和近似批大小 (KB)）。 如果仅设置一项设置，事件网格将使用（可配置）默认值。 请参阅以下各节，了解默认值以及如何替代它们。

## <a name="turn-on-output-batching"></a>打开输出批处理

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>配置允许的最大值

以下部署时间设置用于控制创建事件订阅时允许的最大值。

| 属性名称 | 说明 |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | `PreferredBatchSizeInKilobytes` 旋钮允许的最大值。 默认为 `1033`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch` 旋钮允许的最大值。 默认为 `50`。

## <a name="configuring-runtime-default-values"></a>配置运行时默认值

以下部署时间设置用于控制每个旋钮在事件订阅中未指定时的运行时默认值。 重申一下，必须在事件订阅上设置至少一个旋钮才能打开批处理行为。

| 属性名称 | 说明 |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | 仅指定了 `MaxEventsPerBatch` 时的最大传送请求大小。 默认为 `1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 仅指定了 `MaxBatchSizeInBytes` 时要添加到批处理的最大事件数。 默认为 `10`。
