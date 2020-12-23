---
title: 包含文件
description: 包含文件
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 218e98e65c7c78272f32f75a0fdb93e4d87e6948
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167707"
---
控制 [Application Insights 中的采样功能](../articles/azure-functions/configure-monitoring.md#configure-sampling)。

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|isEnabled|true|启用或禁用采样。| 
|maxTelemetryItemsPerSecond|5|开始采样所要达到的阈值。| 
