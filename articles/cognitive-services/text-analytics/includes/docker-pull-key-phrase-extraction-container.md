---
title: 适用于关键短语提取容器的 docker pull
titleSuffix: Azure Cognitive Services
description: 适用于关键短语提取容器的 docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906083"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>适用于关键短语提取容器的 docker pull

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器的可用标记的完整说明，请参阅 Docker Hub 上的[关键短语提取](https://go.microsoft.com/fwlink/?linkid=2018757)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
