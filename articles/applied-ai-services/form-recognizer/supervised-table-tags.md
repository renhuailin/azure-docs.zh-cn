---
title: 如何使用表标记训练自定义表单模型 - 表单识别器
titleSuffix: Azure Applied AI Services
description: 了解如何有效地使用监督表标记标签。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/23/2021
ms.author: lajanuar
ms.openlocfilehash: 5b27e3db548c54cec4e77ee169921e45ac700524
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326245"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>使用表标记训练自定义表单模型

本文介绍如何使用表标记（标签）训练自定义表单模型。 某些情况下需要更复杂的标签，而不只是对齐键值对。 此类场景包括从具有复杂层次结构的表单提取信息，或遇到服务无法自动检测和提取的项。 在这些情况下，可以使用表标记来训练自定义表单模型。

## <a name="when-should-i-use-table-tags"></a>应何时使用表标记？

下面是有关适合使用表标记场景的一些示例：

- 要提取的数据在表单中显示为表，表的结构是有意义的。 例如，表中的每一行表示一个项，行的每一列表示该项的一项特定功能。 在这种情况下，可以使用表标记，其中列表示功能，行表示每个功能的相关信息。
- 你希望提取的数据不会显示在特定表单域中，但从语义上讲，这些数据可以放入二维网格中。 例如，你的表单具有人员列表，其中包含名字、姓氏和电子邮件地址。 你希望提取此信息。 在这种情况下，可以使用表标记，其中以名字、姓氏和电子邮件地址作为列，并使用与列表中人员有关的信息填充每一行。

> [!NOTE]
> 表单识别器自动查找并提取文档中的所有表，无论表是否已标记。 因此，不必使用表标记来标记表单中的每个表，而且表标记无需复制在表单中找到的每个表的结构。 由表单识别器自动提取的表将包含在 JSON 输出的 pageResults 部分中。

## <a name="create-a-table-tag-with-the-form-recognizer-sample-labeling-tool"></a>使用表单识别器示例标记工具创建表格标记
<!-- markdownlint-disable MD004 -->
* 确定是需要动态还是固定大小的表标记。 如果行数与文档不同，则使用动态表标记。 如果文档中的行数保持一致，请使用固定大小的表标记。
* 如果表标记是动态的，则为每一列定义列名称以及数据类型和格式。
* 如果表标记是固定大小，则为每个标记定义列名称、行名称、数据类型和格式。
:::image type="content" source="./media/table-tag-configure.png" alt-text="配置表标记":::

## <a name="label-your-table-tag-data"></a>标记表标记数据

* 如果你的项目具有表标记，则可以打开“标签”面板并填充标记，就像标记键值字段一样。
:::image type="content" source="media/table-labeling.png" alt-text="使用表标记进行标记":::

## <a name="next-steps"></a>后续步骤

按照我们的快速入门来训练和使用自定义表单识别器模型：

> [!div class="nextstepaction"]
> [使用示例标签工具通过标签进行训练](label-tool.md)

## <a name="see-also"></a>请参阅

* [使用示例标记工具训练自定义模型](label-tool.md)
>
