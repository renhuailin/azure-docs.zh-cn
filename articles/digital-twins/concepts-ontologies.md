---
title: 什么是 ontology？
titleSuffix: Azure Digital Twins
description: 了解特定域中用于建模的 DTDL 行业 ontology
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d87f6a7a536f4dc9d15d87fe141f14760cab8aaa
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253753"
---
# <a name="what-is-an-ontology"></a>什么是 ontology？ 

Azure 数字孪生解决方案的词汇是使用[模型](concepts-models.md)定义的，这些模型描述了环境中存在的实体类型。

有时，当您的解决方案与特定行业关联时，从已存在的一组模型开始，可以更方便、更有效地开始，而无需从头开始创作您自己的模型集。 这些已存在的模型集则被称为 **ontology**。 

一般而言，ontology 是指特定领域的模型集，例如建筑结构、IoT 系统、智能城市、能源网、Web 内容等。ontology 通常用作孪生图架构，因为它们可以：
* 协调软件组件、文档、查询库等
* 减少概念建模和系统开发方面的投资
* 更方便在语义级别进行数据互操作
* 重复使用最佳做法，而不必从头开始或“浪费时间做无用功”

本文介绍为何以及如何对 Azure 数字孪生模型使用 ontology，以及目前哪些 ontology 和工具适用于它们。

## <a name="using-ontologies-for-azure-digital-twins"></a>对于 Azure 数字孪生使用 ontology

Ontology 为数字孪生解决方案提供了良好的起点。 它们包含一组特定于域的模型和实体之间的关系，用于设计、创建和分析数字孪生关系图。 通过 ontology，解决方案开发者可以从已经验证的起点开始制定数字孪生解决方案，并专注于解决业务问题。 Microsoft 提供的 ontology 也可以轻松进行扩展，因此你可以自定义适合自己解决方案的 ontology。 

此外，在解决方案中使用这些 ontology 有利于在不同合作伙伴和供应商之间实现无缝集成，因为 ontology 可以跨解决方案提供通用词汇。

由于 Azure 数字孪生中的模型以[数字孪生定义语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)表示，因此用于 Azure 数字孪生的 ontology 也将用 DTDL 编写。 

## <a name="strategies-for-integrating-ontologies"></a>集成 ontology 的策略

使用 DTDL 集成行业标准 ontology 可以采用三种策略。 你可以根据需求选择最适合的策略：

| 策略 | 说明 | 资源 |
| --- | --- | --- |
| **采用** | 你可以使用开源 DTDL ontology 启动自己的解决方案，这种 ontology 基于广泛采用的行业标准构建。 可以使用现成的模型集，也可以自行添加扩展，形成定制解决方案。 | [采用&nbsp;行业&nbsp;标准本体](concepts-ontologies-adopt.md)<br><br>[扩展&nbsp;本体](concepts-ontologies-extend.md) |
| **转换** | 如果目前已有以其他标准格式表示的模型，则可以将它们转换为 DTDL，以便将它们用于 Azure 数字孪生。 | [转换&nbsp;本体](concepts-ontologies-convert.md)<br><br>[扩展&nbsp;本体](concepts-ontologies-extend.md) |
| **作者** | 你随时都可以使用任何适用的行业标准作为灵感，从头开发自己的自定义 DTDL 模型。 | [DTDL 模型](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>在模型开发路径中使用 ontology 策略

无论选用哪种策略来将 ontology 集成到 Azure 数字孪生中，都可以遵循以下完整路径来指导创建 ontology，并将其上传为 DTDL 模型。

1. 首先，查看并了解 [Azure 数字孪生中的 DTDL 建模](concepts-models.md)。
1. 使用上面所选的 ontology 集成策略（[采用](concepts-ontologies-adopt.md)、[转换](concepts-ontologies-convert.md)或根据 ontology [创作](concepts-models.md)模型）继续执行操作。
    1. 如有必要，[扩展](concepts-ontologies-extend.md) ontology 以根据需求对其进行自定义。
1. [验证](how-to-parse-models.md)模型，以确认它们是有效的 DTDL 文档。
1. 使用 [API](how-to-manage-model.md#upload-models) 或类似于 [Azure 数字孪生模型上传工具](https://github.com/Azure/opendigitaltwins-tools/tree/master/ADTTools#uploadmodels)的示例，将完成的模型上传到 Azure 数字孪生。

然后，你应该能够在 Azure 数字孪生实例中使用自己的模型。 

>[!TIP]
> 可使用 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) 或 [Azure 数字孪生模型可视化工具](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)在 ontology 中直观呈现这些模型。

## <a name="next-steps"></a>后续步骤

阅读有关采用、转换和创作 ontology 策略的详细信息：
* [采用行业标准 ontology](concepts-ontologies-adopt.md)
* [转换 ontology](concepts-ontologies-convert.md)
* [管理 DTDL 模型](how-to-manage-model.md)

或者，了解如何使用模型来创建数字孪生体：[数字孪生体和孪生体关系图](concepts-twins-graph.md)。