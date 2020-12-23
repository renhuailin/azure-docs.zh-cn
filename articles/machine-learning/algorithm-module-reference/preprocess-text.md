---
title: 预处理文本：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的 "预处理文本" 模块来清理和简化文本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/16/2020
ms.openlocfilehash: 366b30df677a5b74bc7d70e1aea60e05b4df0152
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659269"
---
# <a name="preprocess-text"></a>预处理文本

本文介绍 Azure 机器学习设计器中的一个模块。

使用“预处理文本”模块来清理和简化文本。 它支持以下常见文本处理操作：

* 删除非索引字词
* 使用正则表达式搜索并替换特定目标字符串
* 词形还原，它将多个相关字词转换为同一个规范形式
* 大小写规范化
* 删除某些特定类别的字符，如数字、特殊字符和重复字符序列（如 "aaaa"）
* 标识和删除电子邮件和 URL

“预处理文本”模块当前仅支持英语。

## <a name="configure-text-preprocessing"></a>配置文本预处理  

1.  将“预处理文本”模块添加到 Azure 机器学习中的管道。 可以在“文本分析”下找到此模块。

1. 连接一个数据集，其中至少有一个列包含文本。

1. 从“语言”下拉列表中选择语言。

1. **要清理的文本列**：选择要预处理的列。

1. **删除非索引字词**：如果要将预定义的非索引字列表应用于文本列，请选择此选项。 

    非索引字列表与语言相关并可自定义。

1. **词形还原**：如果希望以规范形式表示字词，请选择此选项。 此选项可用于减少其他类似文本标记的唯一匹配项的数量。

    词形还原过程与语言高度相关。

1. **检测句子**：如果希望模块在执行分析时插入句子边界标记，请选择此选项。

    此模块使用三个管道字符 `|||` 系列来表示语句终止符。

1. 使用正则表达式执行可选的“查找替换”操作。 正则表达式将首先在所有其他内置选项之前处理。

    * **自定义正则表达式**：定义要搜索的文本。
    * **自定义替换字符串**：定义单个替换值。

1. **将大小写规范化为小写**：如果要将 ASCII 大写字符转换为小写形式，请选择此选项。

    如果不对字符执行规范化，系统会将同一单词的大写和小写形式视为两个不同的单词。

1. 还可以从已处理的输出文本中删除以下类型的字符或字符序列：

    * **删除数字**：选择此选项可以删除指定语言中的所有数字字符。 标识性数字与域和语言相关。 如果数字字符是已知单词的组成部分，可能不会删除该数字。 在 [技术说明](#technical-notes)中了解详细信息。
    
    * **删除特殊字符**：使用此选项可以删除任何非字母数字的特殊字符。
    
    * **删除重复字符**：选择此选项可以删除任何序列中重复次数超过两次的多余字符。 例如，"aaaaa"这样的序列可缩减为 "aa"。
    
    * **删除电子邮件地址**：选择此选项可以删除格式 `<string>@<string>`的任何序列。  
    * **删除 URL**：选择此选项可以删除任何包含以下 URL 前缀的序列：`http`、`https`、`ftp`、`www`
    
1. **展开谓词缩写形式**：此选项仅适用于使用谓词缩写形式的语言；目前仅限英语。 

    例如，选择此选项后，可以将短语 "wouldn't stay there"（不会留在那里）替换为 "would not stay there"。

1. **将反斜杠规范化为斜杠**：选择此选项可将 `\\` 的所有实例映射到 `/`。

1. **将标记拆分为特殊字符**：如果想将带有 `&`、`-` 等字符的词语与这些字符拆分开来，请选择此选项。 此选项还可以减少重复次数超过两次的特殊字符。 

    例如，字符串 `MS---WORD` 将拆分成三个标记，`MS`、`-` 和 `WORD`。

1. 提交管道。

## <a name="technical-notes"></a>技术说明

Studio 中的 " **预处理文本** " 模块 (经典) 和设计器都使用不同的语言模型。 设计器使用来自 [spaCy](https://spacy.io/models/en)的多任务 CNN 定型模型。 不同的模型会给出不同的标记器和词性标记，这会产生不同的结果。

以下是一些示例：

| 配置 | 输出结果 |
| --- | --- |
|选中所有选项后 </br> 解释： </br> 对于类似于 "WC.EXE-3 3test 4test" 中 "3test" 的情况，设计器将删除整个单词 "3test"，因为在此上下文中，语音标记部分将此标记 "3test" 指定为数字，并根据语音部分将其删除。| :::image type="content" source="./media/module/preprocess-text-all-options-selected.png" alt-text="选中所有选项后" border="True"::: |
|仅限 `Removing number` 选定 </br> 解释： </br> 对于类似于 "3test"、"4-EC" 的情况，设计器标记器剂量不会拆分这些事例，并将它们视为整个令牌。 因此，它不会删除这些词中的数字。| :::image type="content" source="./media/module/preprocess-text-removing-numbers-selected.png" alt-text="仅选择了 &quot;删除数字&quot;" border="True"::: |

还可以使用正则表达式输出自定义结果：

| 配置 | 输出结果 |
| --- | --- |
|选中所有选项后 </br> 自定义正则表达式： `(\s+)*(-|\d+)(\s+)*` </br> 自定义替换字符串： `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-all-options-selected.png" alt-text="选择了所有选项和正则表达式" border="True"::: |
|仅限 `Removing number` 选定 </br> 自定义正则表达式： `(\s+)*(-|\d+)(\s+)*` </br> 自定义替换字符串： `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-removing-numbers-selected.png" alt-text="删除选定数字和正则表达式" border="True"::: |


## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 