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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "97605053"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker 资源和知识库的语言支持

本文介绍了 QnA Maker 资源和知识库的语言支持选项。 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

服务的语言是你在资源中创建第一个知识库时选择的。 资源中的所有其他知识库必须采用相同的语言。 

语言决定了 QnA Maker 在响应用户查询时提供的结果的相关性。 QnA Maker 资源和该资源中的所有知识库都支持单一语言。 单一语言是为查询提供最佳答案结果所必需的。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

在 QnA Maker 托管中，你可以选择在单个知识库级别进行语言设置。 只能对服务的知识库启用此设置。 设置后，将无法更改服务的语言设置。 

如果你选择特定于知识库的语言设置，则可在服务本身中创建不同语言的知识库。 

---

## <a name="single-language-per-resource"></a>每个资源一种语言

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

考虑以下情况：

* QnA Maker 服务及其所有知识库仅支持一种语言。
* 语言是在创建服务的第一个知识库时显式设置的。
* 语言由创建知识库时添加的文件和 URL 确定。
* 不能为服务中的任何其他知识库更改语言。
* 认知搜索服务（排名程序 #1）和 QnA Maker 服务（排名程序 #2）使用该语言生成查询的最佳答案。

# <a name="qnamaker-managed-preview"></a>[QnAMaker 托管（预览版）](#tab/v2)
![QnA Maker 托管中的语言设置](../media/language-support/language-setting-managed.png)

如果你 **未选择相应复选框来启用单个知识库的语言设置**，请注意以下事项： 
* QnA Maker 服务及其所有知识库仅支持一种语言。
* 语言是在创建服务的第一个知识库时显式设置的
* 语言由创建知识库时添加的文件和 URL 确定
* 不能为服务中的任何其他知识库更改语言
* 认知搜索服务（排名程序 #1）和 QnA Maker 服务（排名程序 #2）使用该语言生成查询的最佳答案

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>一个 QnA Maker 资源中支持多种语言

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)
当前正式发布 (GA) 的稳定版本不支持此功能。 请查看 QnA Maker 托管来测试此功能。 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)
* 在你的服务中创建第一个知识库时，你可以选择启用单个知识库的语言设置。 选中复选框，以在一个服务中创建属于不同语言的知识库。
* 创建第一个知识库后，不能修改服务的语言设置选项。
* 如果你启用特定于每个知识库的语言设置，则每个知识库都会有一个测试索引，而不是每个服务都有一个测试索引。 

![QnA Maker 托管中的语言设置](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>在一个知识库中支持多种语言

如果你需要支持包含多种语言的知识库系统，可以执行以下操作：

* 在将问题发送到知识库之前，使用[翻译器服务](../../translator/translator-info-overview.md)将问题翻译为单一语言。 这使你能够集中精力来提高单一语言的质量以及备选问题和答案的质量。
* 创建一个 QnA Maker 资源，在该资源中为每种语言创建一个知识库。 这样你就可以为每种语言管理单独的、更细致的备选问题和答案文本。 这为你提供了大得多的灵活性，但当问题或答案在所有语言中有所不同时，这需要高得多的维护成本。


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
QnA Maker 依赖于 [Azure 认知搜索语言分析器](/rest/api/searchservice/language-support)来提供结果。

虽然 Azure 认知搜索功能在受支持的各种语言中不相上下，但 QnA Maker 还有基于 Azure 搜索结果的一个附加排名程序。 在此排名程序模型中，我们在以下语言中使用一些特殊语义和基于字词的特征。

|具有附加排名程序的语言|
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
|朝鲜语|
|波兰语|
|葡萄牙语|
|西班牙语|
|瑞典语|

此附加排名是 QnA Maker 的排名程序的一项内部工作。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [语言选择](../index.yml)