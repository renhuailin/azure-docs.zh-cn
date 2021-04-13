---
title: 句子配对和对齐 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 在执行训练过程中，会将并行文档中的句子配对或对齐。 自定义翻译每次学习一个句子的翻译，并通过读取另一个句子来获取此句子的翻译。 然后，它将这两个句子中的单词和短语相互对齐。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 0c33d766bfd3dff47ddb151e8ce4ea7b25c37548
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897945"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>并行文档中的句子配对和对齐

上传文档后，并行文档中的句子将配对或对齐。 自定义翻译会报告它能够配对为每个数据集中“已对齐句子”的句子数。

## <a name="pairing-and-alignment-process"></a>配对和对齐过程

自定义翻译每次学习一个句子的翻译。 它从源文本读取句子，然后从目标文本读取此句子的翻译。 然后，它将这两个句子中的单词和短语相互对齐。 此过程可让自定义翻译将一个句子中的单词和短语映射到此句子的翻译中的对应单词和短语。 对齐的目的是尽量确保基于相互翻译的句子训练系统。

## <a name="pre-aligned-documents"></a>预先对齐的文档

如果你知道自己可以提供并行文档，则可以通过提供预先对齐的文本文件来替代句子对齐。 可将两个文档中的所有句子提取到该文本文件，按每行一个句子的方式进行组织，然后使用 `.align` 扩展上传。 `.align` 扩展告知自定义翻译应该跳过句子对齐。

为获得最佳结果，请尽量确保在文件中每行放置一个句子。 不要在句子中插入换行符，否则会导致对齐结果不佳。

## <a name="suggested-minimum-number-of-sentences"></a>建议的最小句子数

为了成功完成训练，下表显示了每种文档类型所需的最小句子数。 此限制是一种安全保障，可确保并行句子包含足够的独特词汇来成功训练翻译模型。 一般准则是，具有更多人工翻译质量的域内并行句子应该生成更高质量的模型。

| 文档类型   | 建议的最小句子计数 | 最大句子计数 |
|------------|--------------------------------------------|--------------------------------|
| 培训   | 10,000                                     | 没有上限                 |
| 优化     | 500                                      | 2,500       |
| 测试    | 500                                      | 2,500  |
| 字典 | 0                                          | 没有上限                 |

> [!NOTE]
> - 如果不满足“训练”的 10,000 最小句子计数，训练将不会开始，并且将失败。 
> - “优化”和“测试”是可选的。 如果不提供它们，系统将从“训练”中删除适当的百分比以用于验证和测试。 
> - 可以仅使用字典数据来训练模型。 请参阅[什么是字典](./what-is-dictionary.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何在自定义翻译中使用[字典](what-is-dictionary.md)。
