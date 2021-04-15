---
title: include 文件
description: include 文件
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92167640"
---
指定在[计算 Application Insights 的指标](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator)时要聚合多少个函数调用。 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|属性 |默认  | 说明 |
|---------|---------|---------| 
|batchSize|1000|要聚合的最大请求数。| 
|flushTimeout|00:00:30|要聚合的最大时间段。| 

达到两个限制中的第一个限制时，会聚合函数调用。
