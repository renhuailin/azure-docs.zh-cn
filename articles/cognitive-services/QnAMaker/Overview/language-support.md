---
title: 语言支持 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 区域性列表（QnA Maker 支持的知识库的自然语言）。 请勿将多种语言混合在同一个知识库中。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 1edd5ffc2578a27a53c7e9a46a4a5f1cf61331ff
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605053"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker 资源和知识库的语言支持

本文介绍 QnA Maker 资源和知识库的语言支持选项。 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

在资源中创建第一个知识库时，将选择服务语言。 资源中的所有其他知识库必须采用相同的语言。 

该语言确定 QnA Maker 提供的结果与用户查询的相关关系。 此资源中的 QnA Maker 资源和所有知识库都支持一种语言。 需要使用一种语言来提供查询的最佳答案。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

在 QnA Maker 托管的中，你可以选择在单独的知识库级别上进行语言设置。 此设置只能在服务的知识库中启用。 设置后，将无法更改服务的语言设置。 

如果选择 "语言设置"，以使其特定于知识库，则允许您在服务中创建不同语言的知识库。 

---

## <a name="single-language-per-resource"></a>每个资源一种语言

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

考虑以下情况：

* QnA Maker 服务及其所有知识库仅支持一种语言。
* 创建服务的第一个知识库时，将显式设置语言。
* 语言由创建知识库时添加的文件和 Url 确定。
* 不能为服务中的任何其他知识库更改此语言。
* 语言由认知搜索服务使用 (ranker #1) ，QnA Maker 服务 (ranker #2) 来生成查询的最佳答案。

# <a name="qnamaker-managed-preview"></a>[QnAMaker managed (预览版) ](#tab/v2)
![QnA Maker 托管中的语言设置](../media/language-support/language-setting-managed.png)

如果 **未选中此复选框以启用每个知识库的语言设置**，请考虑以下事项： 
* QnA Maker 服务及其所有知识库仅支持一种语言。
* 当创建服务的第一个知识库时，将显式设置语言
* 语言由创建知识库时添加的文件和 Url 确定。
* 不能为服务中的任何其他知识库更改此语言
* 语言由认知搜索服务使用 (ranker #1) 和 QnA Maker 服务 (ranker #2) 来生成查询的最佳答案

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>在一个 QnA Maker 资源中支持多种语言

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)
当前已正式推出 (GA) 稳定版本中不支持此功能。 请查看 QnA Maker 托管以测试此功能。 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)
* 在您的服务中创建第一个知识库时，您可以选择启用每个知识库的语言设置。 选中该复选框，以在一个服务中创建属于不同语言的知识库。
* 创建第一个知识库后，不能修改该服务的语言设置选项。
* 如果启用特定于每个知识库的语言设置，而不是为服务创建一个测试索引，则每个知识库将有一个测试索引。 

![QnA Maker 托管中的语言设置](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>在一个知识库中支持多种语言

如果需要支持包含多种语言的知识库系统，可以执行以下操作：

* 使用 [转换器服务](../../translator/translator-info-overview.md) 将问题转换为一种语言，然后将问题发送到知识库。 这使您可以专注于一种语言的质量以及备用问题和答案的质量。
* 为每种语言创建一个 QnA Maker 资源和该资源中的知识库。 这使你可以管理单独的替代问题和答案文本，这些问题对于每种语言更加微妙。 这为你提供了更大的灵活性，但在所有语言中的问题或答案发生变化时需要更高的维护成本。


## <a name="languages-supported"></a>支持的语言

以下列表包含 QnA Maker 资源支持的语言。 

| 语言 |
|--|
| 阿拉伯语 |
| 亚美尼亚语 |
| Bangla |
| 巴斯克语 |
| 保加利亚语 |
| 加泰罗尼亚语 |
| 中文_简体 |
| 中文_繁体 |
| 克罗地亚语 |
| 捷克语 |
| 丹麦语 |
| 荷兰语 |
| 英语 |
| 爱沙尼亚语 |
| 芬兰语 |
| 法语 |
| 加利西亚语 |
| 德语 |
| 希腊语 |
| 古吉拉特语 |
| 希伯来语 |
| Hindi |
| 匈牙利语 |
| 冰岛语 |
| 印度尼西亚语 |
| 爱尔兰语 |
| 意大利语 |
| 日语 |
| 卡纳达语 |
| 韩语 |
| 拉脱维亚语 |
| 立陶宛语 |
| 马拉雅拉姆语 |
| 马来语 |
| 挪威语 |
| 波兰语 |
| 葡萄牙语 |
| 旁遮普语 |
| 罗马尼亚语 |
| 俄语 |
| 塞尔维亚语_西里尔文 |
| 塞尔维亚语_拉丁语 |
| 斯洛伐克语 |
| 斯洛文尼亚语 |
| 西班牙语 |
| 瑞典语 |
| 泰米尔语 |
| 泰卢固语 |
| 泰语 |
| 土耳其语 |
| 乌克兰语 |
| 乌尔都语 |
| 越南语 |

## <a name="query-matching-and-relevance"></a>查询匹配和相关性
QnA Maker 依赖于 [Azure 认知搜索语言分析器](/rest/api/searchservice/language-support) 来提供结果。

尽管 Azure 认知搜索功能对于支持的语言是相同的，但 QnA Maker 有一个位于 Azure 搜索结果之上的其他 ranker。 在此 ranker 模型中，使用以下语言的一些特殊语义和基于 word 的功能。

|具有其他 ranker 的语言|
|--|
|中文|
|捷克语|
|荷兰语|
|英语|
|法语|
|德语|
|匈牙利语|
|意大利语|
|日语|
|韩语|
|波兰语|
|葡萄牙语|
|西班牙语|
|瑞典语|

此附加排名是 QnA Maker 的 ranker 的内部工作。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [语言选择](../index.yml)