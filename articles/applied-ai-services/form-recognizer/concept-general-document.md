---
title: 表单识别器常规文档模型 | 预览版
titleSuffix: Azure Applied AI Services
description: 有关使用预生成常规文档预览版模型进行数据提取和分析的概念
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 49ee6ed3ff8f23cb819a901aba3f370b95901fa9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716259"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-general-document-model--preview"></a>表单识别器常规文档模型 | 预览版

常规文档预览版模型将强大的光学字符识别 (OCR) 功能与深度学习模型相结合，从文档中提取键值对和实体。 仅预览版 (v3.0) API 提供常规文档。  有关使用预览版 (v3.0) API 的详细信息，请参阅[迁移指南](v3-migration-guide.md)。

* 常规文档 API 支持大多数表单类型，可以分析文档，并将它所发现的值关联到键、条目关联到表。 它非常适合用于从文档中提取通用键值对。 可以使用常规文档模型作为[训练不带标签的自定义模型](compose-custom-models.md#train-without-labels)的替代方法。

## <a name="try-form-recognizer-studio-preview"></a>试用表单识别器工作室（预览版）

* 预览版 (v3.0) API 提供表单识别器工作室和常规文档模型。

* 使用表单识别器工作室常规文档功能从表单和文档中提取表、值和实体：

##### <a name="sample-document-processed-in-the-form-recognizer-studio"></a>在[表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)中处理的示例文档：

:::image type="content" source="media/general-document-analyze.png" alt-text="屏幕截图：表单识别器工作室中的常规文档分析。":::

> [!div class="nextstepaction"]
> [试用表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)

## <a name="general-document-features"></a>常规文档功能

* 无需训练自定义模型来提取键值对。

* 单个 API 用于从文档中提取键值对、实体、文本、表和结构。

* 它是一个预先训练的模型，将定期采用新数据进行训练，以提高覆盖率和准确性。

* 常规文档模型支持结构化、半结构化和非结构化数据。

## <a name="key-value-pairs"></a>键值对

键值对是文档中的特定范围，用于标识标签或键及其关联的响应或值。 在结构化表单中，这可能是字段标签和用户输入的相应值，或者在非结构化文档中，它可能是基于段落文本执行合同的日期。  AI 模型经过训练，可基于各种文档类型、格式和结构提取可识别的键和值。

当模型检测到有键但无关联的值，或模型处理可选字段时，键也可以单独存在。 例如，在某些实例中，窗体上的中间名字段可能留空。 键值对始终是文档中包含的文本范围，如果文档中采用不同方式描述了相同的值（例如客户或用户），则关联的键将是客户或用户，具体取决于文档所包含的内容。 

## <a name="entities"></a>实体

自然语言处理模型可以识别语音的各个部分，并对每个标记或单词进行分类。 命名实体识别模型能够识别人员、位置和日期等实体，以提供更丰富的体验。 通过识别实体可以区分客户类型，例如个人或组织。
键值对提取模型和实体标识模型对整个文档并行运行，而不只是对提取的键值对的值运行。 这可确保无法识别键的复杂结构仍可通过标识引用的实体进行扩充。 仍可根据所标识范围的偏移将键或值与实体匹配。

* 常规文档是一个已预先训练的模型，可以直接通过 REST API 调用。 

* 常规文档模型支持多个实体类别的命名实体识别 (NER)。 NER 是指识别文本中不同实体，并将其分类为预定义的类或类型（例如：人员、位置、事件、产品和组织）的功能。 如果要验证所提取的值，提取实体可能很有帮助。 实体是从整个内容中提取的，而不只是从提取的值中提取。

## <a name="general-document-model-data-extraction"></a>常规文档模型数据提取

| **Model**   | 文本提取 |键值对 |选择标记   | **表**   |**实体** |
| --- | :---: |:---:| :---: | :---: |:---: |
|常规文档  | ✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="input-requirements"></a>输入要求

* 为获得最佳结果，请针对每个文档提供一张清晰的照片或高质量的扫描件。
* 支持的文件格式：JPEG、PNG、BMP、TIFF 和 PDF（文本嵌入或扫描式）。 最好是使用文本嵌入式 PDF，这可以避免在提取和定位字符时出错。
* 对于 PDF 和 TIFF，最多可以处理 2000 页（对于免费层订阅，仅处理前两页）。
* 文件大小必须小于 50 MB。
* 图像尺寸必须介于 50 x 50 像素与 10000 x 10000 像素之间。
* PDF 尺寸最大为 17 x 17 英寸，相当于法律专用纸或 A3 纸张尺寸或更小。
* 训练数据的总大小不得超过 500 页。
* 如果 PDF 是密码锁定的文件，则必须先删除锁，然后才能提交它们。
* 对于非监督式学习（没有已标记的数据）：
  * 数据必须包含键和值。
  * 键必须显示在值的上方或左侧，而不能显示在下方或右侧。

## <a name="supported-languages-and-locales"></a>支持的语言和区域设置

| 型号 | 语言 - 区域设置代码 | 默认 |
|--------|:----------------------|:---------|
|常规文档| <ul><li>英语（美国）—en-US</li></ul>| 英语（美国）—en-US|

### <a name="named-entity-recognition-ner-categories"></a>命名实体识别 (NER) 类别

| 类别 | 类型 | 说明 |
|-----------|-------|--------------------|
| 人员 | string | 人员的部分姓名或全名。 |
|PersonType | string | 人员的职位类型或角色。  |
| 位置 | string | 自然地标和人造地标、结构、地理特征和地缘政治实体 |
| 组织 | string | 公司、政治团体、乐队、体育俱乐部、政府机构和公共组织。 |
| 事件 | string | 历史事件、社会事件和自然发生的事件。 |
| 产品 | string |各种类别的物理对象。 |
| 技能 | string | 能力、技能或专长。 |
| 地址 | 字符串 | 完整的邮寄地址。 |
| 电话号码 | string| 电话号码。 | 
电子邮件 | 字符串 | 电子邮件地址。 |
| URL | string| 网站 URL 和链接|
| IP 地址 | string| 网络 IP 地址。 |
| DateTime | string| 某天的日期和时间。 |
| 数量 | string | 数值度量和单位。 |

## <a name="considerations"></a>注意事项

* 如果要验证所提取的值，提取实体可能很有帮助。 实体是在文档的全部内容中提取的，而不只是在提取的值中提取。

* 键是从文档中提取的文本的范围，对于半结构化文档，可能需要将键映射到现有的键字典。

* 可能会看到有键无值的键值对。 例如，用户在表单上选择不提供电子邮件地址。

## <a name="next-steps"></a>后续步骤

* 请参阅[表单识别器 v3.0 迁移指南](v3-migration-guide.md)，了解如何在应用程序和工作流中使用预览版。

* 请探索 [REST API（预览版）](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)，了解有关预览版和新功能的更多信息。

> [!div class="nextstepaction"]
> [试用表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio)
