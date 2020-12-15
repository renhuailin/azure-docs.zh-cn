---
title: 容器支持
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: d1add17c1c84d2a22d76aaa1f96aeca4db645ba7
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506357"
---
## <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源

1. 登录到<a href="https://portal.azure.com" target="_blank">Azure 门户 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
1. 选择 "<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">创建异常 <span class="docon docon-navigate-external x-hidden-focus"></span> 探测器</a>资源"。
1. 输入所有必需的设置：

    |设置|值|
    |--|--|
    |名称|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择附近任何可用的位置|
    |定价层|`F0` -100 每秒调用数，每月20K 事务数。 <br> 或：<br> `S0` -80 个调用/秒|
    |资源组|选择可用的资源组|

1. 单击“创建”并等待创建资源。 创建资源后，导航到“资源”页
1. 收集配置的 `endpoint` 和 API 密钥：

    |门户中的 "密钥和终结点" 选项卡|设置|“值”|
    |--|--|--|
    |**概述**|端点|复制终结点。 它看起来类似于 ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**“键”**|API 密钥|复制两个密钥中的 1 个。 它是一个由 32 个字母数字组成的字符串（不包含空格或短划线），即 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|



