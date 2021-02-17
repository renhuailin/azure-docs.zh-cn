---
title: 什么是 ontology？
titleSuffix: Azure Digital Twins
description: 了解在特定域中用于建模的 DTDL 行业本体论
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b53cac6e732fb568ba799534d9ad9168fd65eab1
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561286"
---
# <a name="what-is-an-ontology"></a>什么是 ontology？ 

Azure 数字孪生解决方案的词汇是使用模型定义的，这些 [模型](concepts-models.md)描述了环境中存在的实体类型。

有时，当您的解决方案与特定行业关联时，从已存在的一组模型开始，可以更方便、更有效地开始，而无需从头开始创作您自己的模型集。 这些预先存在的模型集称为 **本体论**。 

通常，ontology 是给定域的一组模型，如构建结构、IoT 系统、智能城市、能源网格、web 内容等。本体论通常用作知识库的架构，因为它们可以启用：
* 协调软件组件、文档、查询库等。
* 在概念建模和系统开发方面降低投资
* 更方便地在语义级别进行数据互操作性
* 最佳做法重用，而不是从头开始或 "变革轮"

本文介绍了在 Azure 数字孪生模型中使用本体论的原因和方式，以及它们目前可用的本体论和工具。

## <a name="using-ontologies-for-azure-digital-twins"></a>使用本体论 for Azure 数字孪生

本体论为数字克隆解决方案提供了一个很好的起点。 它们包含一组特定于域的模型和实体之间的关系，用于设计、创建和分析数字硬集关系图。 本体论使解决方案开发人员能够从经验证的起点开始使用数字孪生解决方案，并专注于解决业务问题。 Microsoft 提供的本体论也可以轻松地进行扩展，因此你可以为解决方案自定义它们。 

此外，在你的解决方案中使用这些本体论可以将它们设置为在不同合作伙伴与供应商之间实现更无缝的集成，因为本体论可以提供跨解决方案的通用词汇。

由于 Azure 数字孪生中的模型是以 [数字孪生定义语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)来表示的，因此用于 Azure 数字孪生的本体论也是用 DTDL 编写的。 

## <a name="strategies-for-integrating-ontologies"></a>集成本体论的策略

有三种可能的策略可将行业标准的本体论与 DTDL 集成。 你可以根据需要选择最适合的方法：

| 策略 | 说明 | 资源 |
| --- | --- | --- |
| **采用** | 你可以使用一个开源 DTDL ontology 来启动你的解决方案，该版本已采用广泛采用的行业标准构建。 您可以使用这些模型集，也可以使用您自己的自定义解决方案进行扩展。 | [*概念： &nbsp; 采用 &nbsp; 行业 &nbsp; 标准本体论*](concepts-ontologies-adopt.md)<br><br>[*概念： &nbsp; 扩展 &nbsp; 本体论*](concepts-ontologies-extend.md) |
| **转换** | 如果已经有以其他标准格式表示的现有模型，则可以将它们转换为 DTDL，以便将其与 Azure 数字孪生一起使用。 | [*概念： &nbsp; 转换 &nbsp; 本体论*](concepts-ontologies-convert.md)<br><br>[*概念： &nbsp; 扩展 &nbsp; 本体论*](concepts-ontologies-extend.md) |
| **作者** | 始终可以使用任何适用的行业标准作为灵感，随时开发您自己的自定义 DTDL 模型。 | [*概念： DTDL 模型*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>在模型开发路径中使用 ontology 策略

无论你选择将 ontology 集成到 Azure 数字孪生的策略，都可以遵循以下完整路径，指导你创建 ontology，并将其上传为 DTDL 模型。

1. 首先，查看并了解 [Azure 数字孪生中的 DTDL 建模](concepts-models.md)。
1. 继续执行所选的 ontology 集成策略：根据 ontology [**采用**](concepts-ontologies-adopt.md)、 [**转换**](concepts-ontologies-convert.md)或 [**创作**](concepts-models.md) 模型。
    1. 如有必要，请 [扩展](concepts-ontologies-extend.md) 你的 ontology，以根据需要对其进行自定义。
1. [验证](how-to-parse-models.md) 模型，验证它们是否正在使用 DTDL 文档。
1. 使用 [api](how-to-manage-model.md#upload-models) 或类似于 [azure 数字孪生模型上载](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader)程序的示例，将已完成的模型上传到 azure 数字孪生。

此后，你应该能够在 Azure 数字孪生实例中使用模型。 

可以使用 [Azure 数字孪生 (ADT) 资源管理器](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 或 [ADT 模型可视化工具](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)等示例对它们进行可视化，或继续使用它们来创建 [数字孪生](concepts-twins-graph.md)。

## <a name="next-steps"></a>后续步骤

阅读有关采用、转换和创作本体论的策略的详细信息：
* [*概念：采用行业标准本体论*](concepts-ontologies-adopt.md)
* [*概念：转换本体论*](concepts-ontologies-convert.md)
* [*如何：管理 DTDL 模型*](how-to-manage-model.md)

或者，了解如何使用模型来创建数字孪生： [*概念：数字孪生和克隆图*](concepts-twins-graph.md)。