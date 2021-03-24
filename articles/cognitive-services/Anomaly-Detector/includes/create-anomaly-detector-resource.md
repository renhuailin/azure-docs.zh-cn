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
ms.openlocfilehash: feb79d047a6c3b25176a13dcc3c3afd53a51459e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444935"
---
## <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源

1. 登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。
1. 选择<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">创建异常检测器 </a> 资源。
1. 输入所有必需的设置：

    |设置|值|
    |--|--|
    |名称|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择附近任何可用的位置|
    |定价层|`F0` - 每秒 10 次调用，每月 20,000 个事务。 <br> 或：<br> `S0` - 每秒 80 次调用|
    |资源组|选择可用的资源组|

1. 单击“创建”并等待创建资源。 创建资源后，导航到“资源”页
1. 收集配置的 `endpoint` 和 API 密钥：

    |门户中的“密钥和终结点”选项卡|设置|“值”|
    |--|--|--|
    |**概述**|端点|复制终结点。 它看起来类似于 ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**“键”**|API 密钥|复制两个密钥中的 1 个。 它是一个由 32 个字母数字组成的字符串（不包含空格或短划线），即 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|



