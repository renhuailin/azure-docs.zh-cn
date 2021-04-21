---
title: 适用于情绪分析容器的 Docker pull
titleSuffix: Azure Cognitive Services
description: 适用于情绪分析容器的 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564532"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>适用于情绪分析 v3 容器的 Docker pull

情绪分析容器 v3 容器以多种语言提供。 若要下载英文版容器，请使用以下命令。 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

若要下载其他语言版的容器，请将 `en` 替换为以下语言代码之一。 

| 文本分析容器 | 语言代码 |
|--|--|
| 简体中文    |   `zh-hans`   |
| 繁体中文   |   `zh-hant`   |
| 荷兰语                 |     `nl`      |
| 英语               |     `en`      |
| 法语                |     `fr`      |
| 德语                |     `de`      |
| Hindi                 |    `hi`       |
| 意大利语               |     `it`      |
| 日语              |     `ja`      |
| 韩语                |     `ko`      |
| 挪威语(博克马尔语)   |     `no`      |
| 葡萄牙语（巴西）   |    `pt-BR`    |
| 葡萄牙语(葡萄牙) |    `pt-PT`    |
| 西班牙语               |     `es`      |
| 土耳其语               |     `tr`      |

有关文本分析容器可用标记的完整说明，请查阅 [Docker 中心](https://go.microsoft.com/fwlink/?linkid=2018654)。
