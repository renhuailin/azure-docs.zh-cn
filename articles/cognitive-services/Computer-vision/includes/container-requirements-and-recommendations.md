---
title: 容器要求和建议
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 81e86eda454bdbd262f6c38a4766ee2a899d225d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336229"
---
> [!NOTE]
> 这些要求和建议基于这样的基准：每秒一个请求，使用包含 29 行和总共 803 个字符的经过扫描的业务信函的 523-KB 映像。 与最低配置相比，建议配置使响应速度提高了大约 2 倍。

下表显示了每个 Read OCR 容器的最小和建议资源分配。

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 读取 2.0-preview | 1 个内核，8 GB 内存 |    8 个内核，16 GB 内存 |
| Read 3.2 | 4 个内核，16 GB 内存 | 8 个内核，24 GB 内存 |

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。
