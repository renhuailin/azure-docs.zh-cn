---
title: 映射数据流中的列模式
titleSuffix: Azure Data Factory & Azure Synapse
description: 在 Azure 数据工厂或 Synapse Analytics 中使用映射数据流中的列模式创建通用数据转换模式。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 32d39c956121881da0073b53fe5b4196dbc179de
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828500"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>使用映射数据流中的列模式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通过多个映射数据流转换，你可以基于模式而不是硬编码列名称引用模板列。 这种匹配称为“列模式”。 你可以定义模式以根据名称、数据类型、流、源或位置来匹配列，而不是要求精确的字段名称。 有两种情况可以用到列模式：

* 如果传入的源字段经常更改，如在文本文件或 NoSQL 数据库中更改列的情况下。 这种情况称为[架构偏差](concepts-data-flow-schema-drift.md)。
* 如果要对一大组列执行常见操作， 例如，想要将列名称中具有“total”的每个列强制转换为双精度列。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>“派生列”和“聚合”中的列模式

若要在“派生列”、“聚合”或“窗口”转换中添加列模式，请单击列表上方的“添加”或现有派生列旁的加号图标。 选择“添加列模式”。

:::image type="content" source="media/data-flow/add-column-pattern.png" alt-text="屏幕截图显示“添加”列模式的加号图标。":::

使用[表达式生成器](concepts-data-flow-expression-builder.md)输入匹配条件。 基于列的 `name`、`type`、`stream`、`origin` 和 `position` 创建与列匹配的布尔表达式。 模式将影响条件返回 true 的任何列（偏差列或定义的列）。


:::image type="content" source="media/data-flow/edit-column-pattern.png" alt-text="屏幕截图显示派生列的“设置”选项卡。":::

上述列模式匹配双精度类型的每个列，并为每个匹配创建一个派生列。 通过将 `$$` 声明为列名称字段，每个匹配的列将使用相同的名称进行更新。 每列的值是现有值，四舍五入为两位小数。

若要验证匹配条件是否正确，可以在“检查”选项卡中验证已定义列的输出架构，或在“数据预览”选项卡中获取数据的快照 。 

:::image type="content" source="media/data-flow/columnpattern3.png" alt-text="屏幕截图显示“输出架构”选项卡。":::

### <a name="hierarchical-pattern-matching"></a>分层模式匹配

还可以在复杂的分层结构中生成模式匹配。 展开 `Each MoviesStruct that matches` 部分，该部分将提示数据流中的每个层次结构。 然后，你可以为所选层次结构中的属性生成匹配的模式。

:::image type="content" source="media/data-flow/patterns-hierarchy.png" alt-text="屏幕截图显示分层结构列模式。":::

## <a name="rule-based-mapping-in-select-and-sink"></a>“选择”和“接收器”转换中基于规则的映射

在“源”和“选择”转换中映射列时，可以添加固定映射或基于规则的映射。 基于列的 `name`、`type`、`stream`、`origin` 和 `position` 进行匹配。 可以具有固定映射和基于规则的映射的任意组合。 默认情况下，列数大于 50 的投影都将默认为基于规则的映射，其在每列上进行匹配并输出所输入的名称。 

若要添加基于规则的映射，请单击“添加映射”，然后选择“基于规则的映射” 。

:::image type="content" source="media/data-flow/rule2.png" alt-text="屏幕截图显示从“添加映射”中选择的“基于规则的映射”。":::

每个基于规则的映射需要两个输入：要匹配的条件以及每个映射列的名称。 这两个值都是通过[表达式生成器](concepts-data-flow-expression-builder.md)输入的。 在左侧表达式框中，输入布尔值匹配条件。 在右侧的表达式框中，指定要将匹配的列映射到的项。

:::image type="content" source="media/data-flow/rule-based-mapping.png" alt-text="屏幕截图显示映射。":::

使用 `$$` 语法来引用匹配列的输入名称。 以上图为例，假设用户想要在名称长度不到 6 个字符的所有字符串列上进行匹配。 如果一个传入列被命名为 `test`，则表达式 `$$ + '_short'` 会将列重命名为 `test_short`。 如果这是存在的唯一映射，则所有不符合该条件的列都将从输出的数据中删除。

模式同时匹配偏移列和已定义的列。 若要查看由规则映射的已定义的列，请单击规则旁边的眼镜图标。 使用数据预览来验证输出。

### <a name="regex-mapping"></a>正则表达式映射

如果单击向下的 V 形图标，可指定正则表达式映射条件。 对于与指定的正则表达式条件匹配的列名称，正则表达式映射条件与所有这些名称全部匹配。 这可与标准的基于规则的映射结合使用。

:::image type="content" source="media/data-flow/regex-matching.png" alt-text="屏幕截图显示具有层次结构级别和名称匹配的正则表达式映射条件。":::

上述示例与正则表达式模式 `(r)` 或任何包含小写 r 的列名匹配。 与基于规则的标准映射类似，所有匹配的列都使用 `$$` 语法由右侧的条件进行更改。

### <a name="rule-based-hierarchies"></a>基于规则的层次结构

如果定义的投影具有层次结构，则可使用基于规则的映射来映射层次结构子列。 指定匹配条件和要映射其子列的复杂列。 每个匹配的子列都将使用右侧指定的“名称为”规则进行输出。

:::image type="content" source="media/data-flow/rule-based-hierarchy.png" alt-text="屏幕截图显示对层次结构使用的基于规则的映射。":::

上面的示例与复杂列 `a` 的所有子列匹配。 `a` 包含两个子列 - `b` 和 `c`。 输出架构将包含 `b` 和 `c` 两个列，因为“名称为”条件是 `$$`。

## <a name="pattern-matching-expression-values"></a>模式匹配表达式值。

* `$$` 在运行时转换为每个匹配项的名称或值。 将 `$$` 视为等效于 `this`。
* `name` 表示每个传入列的名称
* `type` 表示每个传入列的数据类型。 在这里[此处](concepts-data-flow-overview.md#data-flow-data-types)找到数据流类型系统中的数据类型列表。
* `stream` 表示与流中的每个流式传输或转换相关联的名称
* `position` 是数据流中列的序号位置
* `origin` 是产生列或引起上次更新的转换

## <a name="next-steps"></a>后续步骤
* 详细了解用于数据转换的映射数据流[表达式语言](data-flow-expression-functions.md)
* 通过基于规则的映射在[“接收器”转换](data-flow-sink.md)和[“选择”转换](data-flow-select.md)中使用列模式
