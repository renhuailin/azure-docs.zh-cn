---
title: 批量测试 TSV 格式 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 了解要批量测试的 TSV 格式
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132119"
---
# <a name="batch-testing-tsv-format"></a>批量测试 TSV 格式

通过[源代码](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)或[可下载的可执行文件压缩包](https://aka.ms/qna_btzip)可运行批量测试。 运行批量测试的命令格式为：

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|预期值|
|--|--|
|1|使用 [TSV 输入字段](#tsv-input-fields)进行格式设置的 tsv 文件的名称|
|2|终结点的 URI，YOUR-HOST 来自 QnA Maker 门户的“发布”页。|
|3|ENDPOINT-KEY，位于 QnA Maker 门户的“发布”页上。|
|4|批量测试作为结果创建的 tsv 文件的名称。|

请使用以下信息了解和实施 TSV 格式批量测试。 

## <a name="tsv-input-fields"></a>TSV 输入字段

|TSV 输入文件字段|说明|
|--|--|
|KBID|“发布”页上的 KB ID。|
|问题|用户可能输入的问题。|
|元数据标记|可选|
|Top 参数|可选| 
|预期的答案 ID|可选|

![要批量测试的 TSV 文件的输入格式。](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 输出字段 

|TSV 输出文件参数|备注|
|--|--|
|KBID|“发布”页上的 KB ID。|
|问题|输入文件中输入的问题。|
|答案|知识库中的最佳答案。|
|答案 ID|答案 ID|
|Score|答案的预测得分。 |
|元数据标记|与返回的答案相关|
|预期的答案 ID|可选（仅适用于给出了预期的答案 ID 时）|
|判断标签|可选，值可以是：正确或错误（仅适用于给出了预期答案时）|
