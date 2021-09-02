---
title: 使用 Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure Digital Twins Explorer 的功能
author: baanders
ms.author: baanders
ms.date: 4/20/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 22ad40dadf36b711bde4b7af7e8b24b09610ab89
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459497"
---
# <a name="use-azure-digital-twins-explorer-preview"></a>使用 Azure Digital Twins Explorer（预览版）

[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) 是一个用于可视化和使用 Azure 数字孪生的工具。 本文介绍 Azure Digital Twins Explorer 的功能，以及如何使用它们来管理 Azure 数字孪生实例中的数据。 

>[!NOTE]
>此工具目前提供公共预览版。

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

### <a name="switch-contexts-within-the-app"></a>在应用内切换上下文

进入应用程序后，还可以从顶部工具栏中选择实例名称，更改当前连接到 ADT Explorer 的实例。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png" alt-text="Azure Digital Twins Explorer 的屏幕截图。顶部工具栏中的实例名称突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png":::

这将打开“Azure 数字孪生 URL”模式，可以在“https://”后面输入另一个 Azure 数字孪生实例的主机名以连接到该实例。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png" alt-text="Azure Digital Twins Explorer 的屏幕截图。Azure 数字孪生 URL 模式显示一个包含 https:// 和主机名的可编辑框。" lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png":::

>[!NOTE]
>此时，在应用内切换上下文的功能对于个人 Microsoft 帐户 (MSA) 不适用。 MSA 用户需要从 Azure 门户中的正确实例访问 ADT Explorer，或者可以通过[指向环境的直接链接](#link-to-your-environment)连接到特定实例。


## <a name="query-your-digital-twin-graph"></a>查询数字孪生体图

可以使用“查询资源管理器”面板对图执行[查询](concepts-query-language.md)。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png" alt-text="Azure Digital Twins Explorer 的屏幕截图。“查询资源管理器”面板突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png":::

输入要运行的查询，然后选择“运行查询”按钮。 这会在“孪生体图”面板中加载查询结果。

>[!NOTE]
> 包含关系的查询结果只有当结果中包含至少一个孪生体时，才能在“孪生体图”面板中呈现。 虽然在 Azure 数字孪生中可进行只返回关系的查询，但只能在 Azure Digital Twins Explorer 中使用[输出面板](#advanced-settings)查看它们。

### <a name="overlay-query-results"></a>覆盖查询结果

如果你希望结果在当前显示于“孪生体图”面板中的内容中突出显示，而不是将面板的内容完全替换为新的查询结果，则可在运行查询之前选中“覆盖结果”框 。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png" alt-text="Azure Digital Twins Explorer 的“查询资源管理器”面板屏幕截图。已选中“覆盖结果”框，并在“孪生体图”面板中显示的较大图中突出显示了两个孪生体。" lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png":::

如果查询结果包含“孪生体图”面板中当前未显示的内容，则该元素将添加到现有视图。

### <a name="save-and-rerun-queries"></a>保存和重新运行查询

查询可以保存在浏览器中的本地存储中，使其易于选择和重新运行。

>[!NOTE]
>本地浏览器存储意味着保存的查询将无法在除了保存它们的浏览器之外的其他浏览器中使用，并且它们将无限期地保留在浏览器存储中，直到本地存储被清除。

若要保存查询，请在查询框中输入该查询，然后选择面板右边的“保存”图标。 出现提示时，输入已保存查询的名称。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png" alt-text="Azure Digital Twins Explorer 的“查询资源管理器”面板屏幕截图。“保存”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png":::

保存查询后，可从“保存的查询”下拉菜单中选择该查询，轻松地再次运行它。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png" alt-text="Azure Digital Twins Explorer 的“查询资源管理器”面板的屏幕截图。“保存的查询”下拉菜单突出显示并显示两个示例查询。" lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png":::

若要删除已保存的查询，请在“保存的查询”下拉菜单打开时选择查询名称旁边的“X”图标 。

>[!TIP]
>对于大型图形，建议仅查询有限的子集，然后根据需要加载图形的其余部分。 可以在“孪生体图”面板中双击某个孪生体，以检索其他相关节点。

## <a name="explore-the-twin-graph"></a>探索孪生体图

使用“孪生体图”面板，可以探索实例中的孪生体和关系。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png" alt-text="Azure Digital Twins Explorer 的屏幕截图。“孪生体图”面板突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png":::

可以使用此面板[查看孪生体和关系](#view-twins-and-relationships)。

“孪生体图”面板还提供多种功能来自定义图形查看体验：
* [编辑孪生体图布局](#edit-twin-graph-layout)
* [控制孪生体图扩展](#control-twin-graph-expansion)
* [显示和隐藏孪生体图元素](#show-and-hide-twin-graph-elements)
* [筛选和突出显示孪生体图元素](#filter-and-highlight-twin-graph-elements)

### <a name="view-twins-and-relationships"></a>查看孪生体和关系

使用[查询资源管理器](#query-your-digital-twin-graph)运行一个查询，查看在“孪生体图”面板显示的查询结果中的孪生体和关系。

>[!TIP]
>用于显示所有孪生体和关系的查询为 `SELECT * FROM digitaltwins`。

#### <a name="view-twin-and-relationship-properties"></a>查看孪生体和关系属性

若要查看孪生体或关系的属性值，请在“孪生体图”中选择该孪生体或关系，并使用“切换属性检查器”按钮展开“属性”面板  。 此面板将显示与该元素关联的所有属性及其值。 它还包括尚未设置的属性的默认值。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。选择了 FactoryA 孪生体，并展开了“属性”面板，其中显示该孪生体的属性。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

属性通常以白色文本显示，但也可能以下列颜色显示，以指示其他信息：

* 模型的红色文本：表示找不到孪生体的模型。 如果在创建孪生体后删除了模型，则可能会发生这种情况。

    :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/properties-color-red.png" alt-text="Azure Digital Twins Explorer 的“属性”面板屏幕截图，显示示例孪生体的属性。 $model 字段及其值以红色文本显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/properties-color-red.png":::

* 属性的黄色文本：表示该属性不是孪生体正在使用的模型定义的一部分。 如果属性创建后孪生体的模型已被替换或更改，并且该属性不再存在于模型的最新版本中，则可能会发生这种情况。 具有过期属性的孪生体无法更新，除非更新还会更正或删除过期属性。

    :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/properties-color-yellow.png" alt-text="Azure Digital Twins Explorer 的“属性”面板屏幕截图，显示示例孪生体的属性。几个属性名称以黄色文本显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/properties-color-yellow.png":::

#### <a name="view-a-twins-relationships"></a>查看孪生体的关系

你还可以快速查看涉及特定孪生体的所有关系（包括传入和传出关系）的代码。

为此，请右键单击图中的孪生体，然后选择“获取关系”。 这将打开一个“关系信息”模式，显示所有传入和传出关系的 [JSON 表示形式](concepts-twins-graph.md#relationship-json-format)。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。屏幕中央显示一个“关系信息”模式，显示传入和传出关系。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png":::

### <a name="edit-twin-graph-layout"></a>编辑孪生体图布局

可通过在“孪生体图”屏幕上单击并拖动孪生体，将它们重新排列成不同的配置。

还可以从“运行布局”菜单中的选项将多种布局算法之一应用于图形。 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。“运行布局”按钮突出显示，显示的菜单包含布局选项 Cola、Dagre、fCoSE 和 Klay。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png":::

### <a name="control-twin-graph-expansion"></a>控制孪生体图扩展

在“孪生体图”面板中查看查询结果时，可以双击一个孪生体，让该图提取其关系和相关孪生体，并显示它们（如果它们尚未出现在视图中）。 可以通过设置大小和方向来确定要提取的孪生体数量，以自定义此扩展。

若要设置要扩展的层数，请使用“扩展级别”选项。 此数字表示要从所选孪生体中获取多少层关系。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。“扩展级别”按钮突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png":::

若要指示扩展时要遵循的关系类型，请使用“扩展模式”按钮。 这样，你可从“仅传入”、“仅传出”或“传入和传出”关系进行选择。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-mode.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。“扩展模式”按钮突出显示，显示的菜单包含“传入”、“传出”和“传入/传出”选项。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-mode.png":::

### <a name="show-and-hide-twin-graph-elements"></a>显示和隐藏孪生体图元素

可以在图形视图中切换隐藏孪生体或关系的选项。 

若要隐藏孪生体或关系，请在“孪生体图”窗口中右键单击它。 这将打开一个菜单，其中包含用于隐藏该元素或其他相关元素的选项。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。已选中 FactoryA 孪生体，并且有一个菜单包含以下选项：隐藏选定项、隐藏选定项 + 子项、隐藏所有其他项和隐藏非子项。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png":::

还可以通过使用 CTRL/CMD 或 SHIFT 键在图形中多选多个相同类型的元素，一次隐藏多个孪生体或关系。 在此处，按照相同的右键单击过程查看隐藏选项。

若要在隐藏某些孪生体后返回显示所有孪生体，请使用“全部显示”按钮。 若要返回显示所有关系，请使用“显示所有关系”按钮。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。“全部显示”和“显示所有关系”按钮突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png":::

### <a name="filter-and-highlight-twin-graph-elements"></a>筛选和突出显示孪生体图元素

可以通过选择以下“筛选器”图标，按文本筛选图中显示的孪生体和关系：

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。文本筛选器图标已选中，显示了可在里面输入搜索词的“筛选器”选项卡。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png":::

还可以通过选择以下“突出显示”图标，按文本突出显示图中显示的孪生体和图形：

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。文本筛选器图标已选中，显示了可在里面输入搜索词的“突出显示”选项卡。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png":::

## <a name="manage-twins-and-graph"></a>管理孪生体和图形

Azure Digital Twins Explorer 提供了多种方法来管理实例中的[孪生体](concepts-twins-graph.md#digital-twins)和[关系](concepts-twins-graph.md#relationships-a-graph-of-digital-twins)。

本部分介绍如何执行以下管理活动：
* [创建孪生体](#create-twins)，孪生体带有或不带初始属性
* 在孪生体间[创建关系](#create-relationships)
* [编辑孪生体和关系](#edit-twins-and-relationships)
* [删除孪生体和关系](#delete-twins-and-relationships)

有关孪生体和关系的查看体验的信息，请参阅[探索孪生体和孪生体图](#explore-the-twin-graph)。

### <a name="create-twins"></a>创建孪生体

可以在“模型”面板中根据模型定义创建新的数字孪生体。

若要根据模型创建孪生体，请在列表中找到该模型，然后选择模型名称旁边的“创建孪生体”图标。 系统会要求输入新孪生体的“名称”，该名称必须是唯一的。 然后保存孪生体，这会将其添加到图形中。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png" alt-text="Azure Digital Twins Explorer 的“模型”面板屏幕截图。单个模型的“创建孪生体”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

若要向孪生体添加属性值，请参阅[编辑孪生体和关系](#edit-twins-and-relationships)。

### <a name="create-relationships"></a>创建关系

若要在两个孪生体之间创建关系，请首先在“孪生体图”窗口中选择关系的源孪生体。 接下来，按住 CTRL/CMD 或 SHIFT 键，同时选择第二个孪生体作为关系的目标。

同时选择两个孪生体后，右键单击其中一个孪生体。 这将打开一个菜单，其中包含在二者之间添加关系的选项。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。已选择 FactoryA 和 Consumer 孪生体，且菜单显示添加关系的选项。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png":::

此时将打开“创建关系”对话框，其中显示关系的源孪生体和目标孪生体，后跟一个“关系”下拉菜单，其中包含源孪生体可以具有的关系类型（在其 DTDL 模型中定义） 。 为关系类型选择一个选项，并“保存”新的关系。

### <a name="edit-twins-and-relationships"></a>编辑孪生体和关系

若要查看孪生体或关系的属性值，请在“孪生体图”中选择该元素，并使用“切换属性检查器”按钮展开“属性”面板  。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。选择了 FactoryA 孪生体，并展开了“属性”面板，其中显示该孪生体的属性。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

可以使用此面板直接编辑可写属性。 请以内联方式更新它们的值，然后单击面板顶部的“修补孪生体”（保存）按钮以保存更改。 保存更新后，屏幕会显示一个模式窗口，其中显示[更新 API](/rest/api/azure-digitaltwins/) 应用的 JSON 修补操作。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。屏幕中央显示一个“路径信息”模式，其中显示 JSON 修补代码。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png":::

### <a name="delete-twins-and-relationships"></a>删除孪生体和关系

若要删除孪生体或关系，请在“孪生体图”窗口中右键单击它。 这将打开一个菜单，其中包含删除该元素的选项。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。已选择 FactoryA 孪生体，并有一个菜单包含删除孪生体的选项。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png":::

可以通过使用 CTRL/CMD 或 SHIFT 键在图形中多选多个相同类型的元素，一次删除多个孪生体或多个关系。 在此处，按照相同的右键单击过程删除元素。

还可以选择使用顶部工具栏中的“删除所有孪生体”按钮同时删除实例中的所有孪生体。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png" alt-text="Azure Digital Twins Explorer 的屏幕截图。“删除所有孪生体”图标已选中。" lightbox="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png":::

## <a name="explore-models-and-the-model-graph"></a>探索模型和模型图

可在以下两个位置查看模型：Azure Digital Twins Explorer 屏幕左侧的“模型”面板中，或屏幕中间的“模型图”面板中 。

“模型”面板：:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Azure Digital Twins Explorer 的屏幕截图。“模型”面板突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::

“模型图”面板：:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="Azure Digital Twins Explorer 的屏幕截图。“模型图”面板突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

可以使用这些面板来[查看模型](#view-models)。

“模型图”面板还提供多种功能来自定义图形查看体验：
* [编辑模型图布局](#edit-model-graph-layout)
* [筛选和突出显示模型图元素](#filter-and-highlight-model-graph-elements)
* [上传模型图像](#upload-model-images)以表示图中的模型

### <a name="view-models"></a>查看模型

可以在“模型”面板中查看实例中模型的简单列表。 可使用面板中的“搜索”栏搜索此列表。

可以使用“模型图”面板来查看实例中模型的图形化表示形式，以及将它们彼此连接起来的关系、继承和组件。

#### <a name="view-model-definition"></a>查看模型定义

若要查看模型的完整定义，请在“模型”窗格中找到该模型，然后选择模型名称旁边的“查看模型”图标 。 这会显示“模型信息”模式，其中显示模型的原始 DTDL 定义。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png" alt-text="Azure Digital Twins Explorer 的“模型”面板屏幕截图。单个模型的“查看模型”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

还可以通过在“模型图”中选择模型，并使用“切换模型详细信息”按钮展开“模型详细信息”面板来查看该模型的完整定义  。 此面板还将显示模型的完整 DTDL 代码。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png" alt-text="Azure Digital Twins Explorer 的“模型图”面板屏幕截图。已选择 Floor 模型，并展开“模型详细信息”面板，其中显示了模型的 DTDL 代码。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png":::

### <a name="edit-model-graph-layout"></a>编辑模型图布局

可通过在“模型图”屏幕上单击并拖动模型，将它们重新排列成不同的配置。

还可以从“运行布局”菜单中的选项将多种布局算法之一应用于模型图。 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png" alt-text="Azure Digital Twins Explorer 的“模型图”面板屏幕截图。“运行布局”按钮突出显示，显示的菜单包含布局选项 Cola、Dagre、fCoSE、Klay 和 d3Force。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png":::

### <a name="filter-and-highlight-model-graph-elements"></a>筛选和突出显示模型图元素

可以筛选出现在模型图中的连接类型。 通过此菜单中的开关关闭一种连接类型可以使该连接类型不再显示在图中。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png" alt-text="Azure Digital Twins Explorer 的“模型图”面板屏幕截图。关系、继承和组件的“筛选器”菜单已突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png":::

还可以通过选择以下“筛选器”图标，按文本筛选图中显示的模型和连接：

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png" alt-text="Azure Digital Twins Explorer 的“模型图”面板屏幕截图。文本筛选器图标已选中，显示了可在里面输入搜索词的“筛选器”选项卡。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png":::

可以通过选择以下“突出显示”图标，按文本突出显示图中显示的模型和连接：

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png" alt-text="Azure Digital Twins Explorer 的“模型图”面板屏幕截图。文本筛选器图标已选中，显示了可在里面输入搜索词的“突出显示”选项卡。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png":::

### <a name="upload-model-images"></a>上传模型图像

可以上传自定义图像，以在模型图和[孪生体图](#explore-the-twin-graph)视图中表示不同的模型。 可以为单个模型或一次为多个模型上传图像。

>[!NOTE]
>这些图像存储在本地浏览器存储中。 这意味着图像将无法在除了保存它们的浏览器之外的其他浏览器中使用，并且它们将无限期地保留在浏览器存储中，直到本地存储被清除。

若要为单个模型上传图像，请在“模型”面板中找到该模型，然后选择模型名称旁边的“上传模型图像”图标 。 在出现的文件选择器框中，在计算机上导航到要为该模型上传的图像文件。 选择“打开”，将其上传。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png" alt-text="Azure Digital Twins Explorer 的“模型”面板屏幕截图。单个模型的“上传模型图像”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

还可以批量上传模型图像。

首先，在上传前使用以下说明设置图像文件名。 这使 Azure Digital Twins Explorer 能够在图像上传后自动将图像分配给正确的模型。 
1. 从模型 ID 值开始（例如，`dtmi:example:Floor;1`）
1. 将“:”的实例替换为“_”（示例变为 `dtmi_example_Floor;1`）
1. 将“;”的实例替换为“-”（示例变为 `dtmi_example_Floor-1`）
1. 请确保该文件具有图像扩展名（示例变为类似 `dtmi_example_Floor-1.png`）

> [!NOTE]
> 如果尝试使用上述条件上传未映射到任何现有模型的图像，则不会上传或存储该图像。

然后，若要同时上传图像，请使用“模型”面板顶部的“上传模型图像”图标。 在文件选择器框中，选择要上传的图像文件。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png" alt-text="Azure Digital Twins Explorer 的“模型”面板屏幕截图。“上传模型图像”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-models"></a>管理模型

可以使用 Azure Digital Twins Explorer 屏幕左侧的“模型”面板，对整个模型集或各个模型执行管理活动。 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Azure Digital Twins Explorer 的屏幕截图。“模型”面板突出显示" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::。

在此面板中，可以完成以下模型管理活动：
* [上传模型](#upload-models)到 Azure 数字孪生实例
* 从实例中[删除模型](#delete-models)
* [刷新模型](#refresh-models)以将所有模型的列表重新加载到此面板中

有关模型的查看体验的信息，请参阅[探索模型和模型图](#explore-models-and-the-model-graph)。

### <a name="upload-models"></a>上传模型

可以通过单独选择各模型或一次上传整个模型文件夹，从计算机上传这些模型。

若要上传单独选择的一个或多个模型，请选择“上传模型”图标，该图标显示一个向上指向云的箭头。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png" alt-text="Azure Digital Twins Explorer 的“模型”面板屏幕截图。“上传模型”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

在出现的文件选择器框中，在计算机上导航到要上传的模型。 可以选择一个或多个 JSON 模型文件，然后选择“打开”将其上传。

若要上传模型的文件夹，包括文件夹中的所有内容，请选择“上传模型目录”图标，该图标显示一个文件夹。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png" alt-text="Azure Digital Twins Explorer 的“模型”面板屏幕截图。“上传模型目录”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

在出现的文件选择器框中，在计算机上导航到包含 JSON 模型文件的文件夹。 选择“打开”，上传顶层文件夹及其所有内容。

>[!IMPORTANT]
>如果模型在其定义中引用了另一个模型（例如在定义关系或组件时），被引用的模型需要存在于实例中，以便上传使用它的模型。 如果逐个上传模型，则应该先上传被引用的模型，然后再上传使用它的任何模型。 如果要批量上传模型，则可以在同一导入中同时选择它们，Azure 数字孪生将推断它们的上传顺序。

### <a name="delete-models"></a>删除模型

可以使用“模型”面板删除实例中的单个模型或同时删除所有模型。

若要删除单个模型，请在列表中找到该模型，然后选择模型名称旁边的“删除模型”图标。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png" alt-text="Azure Digital Twins Explorer 的“模型”面板屏幕截图。单个模型的“删除模型”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

若要同时删除实例中的所有模型，请选择“模型”面板顶部的“删除所有模型”图标。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png" alt-text="Azure Digital Twins Explorer 的“模型”面板屏幕截图。“删除所有模型”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="refresh-models"></a>刷新模型

打开 Azure Digital Twins Explorer 时，“模型”面板应会自动显示环境中所有可用的模型。 

不过，你可以随时手动刷新面板，以重新加载 Azure 数字孪生实例中所有模型的列表。 为此，请选择“刷新模型”图标（向下指向云外的箭头）。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png" alt-text="Azure Digital Twins Explorer 的“模型”面板屏幕截图。“刷新模型”图标突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="importexport-graph"></a>导入/导出图形

从“孪生体图”面板中，可以选择[导入](#import-graph)和[导出](#export-graph-and-models)图形的功能。

### <a name="import-graph"></a>导入图形

可以使用导入功能将孪生体、关系和模型添加到实例。 这对于同时创建多个孪生体、关系和/或模型非常有用。

#### <a name="create-import-file"></a>创建导入文件

导入图形的第一步是创建一个文件，表示要添加的孪生体和关系。

导入文件可采用以下两种格式之一：
* 本部分其余内容中描述的“基于 Excel 的自定义格式”。 通过该格式，可以上传孪生体和关系。
* 在[图形导出](#export-graph-and-models)时生成的“基于 JSON 的格式”。 该格式可以包含孪生体、关系和/或模型。

若要在 Excel 中创建自定义图形，请使用以下格式。

每行代表一个要创建的元素：孪生体、关系或孪生体和对应关系的组合。
使用以下列来构建孪生体或关系数据的结构。 列名称可自定义，但应保持以下顺序。

| ModelID | ID | 关系（源） | 关系名称 | 初始化数据 |
| --- | --- | --- | --- | --- |
| *可选*<br>应创建的孪生体的 DTMI 模型 ID。<br><br>如果希望该行仅创建关系（不创建孪生体），则可将该列保留为空。 | *必需*<br>孪生体的唯一 ID。<br><br>如果要在此行中创建新的孪生体，这将是新孪生体的 ID。<br>如果行中存在关系信息，则此 ID 将用作关系的目标。 | *可选*<br>应为新关系的源孪生体的孪生体 ID。<br><br>如果希望该行仅创建孪生体（不创建关系），则可将该列保留为空。 | *可选*<br>要创建的新关系的名称。 关系方向是从 C 列中的孪生体到 B 列中的孪生体 。 | *可选*<br>包含要创建的孪生体的属性设置的 JSON 字符串。 这些属性必须与列 A 中的模型所定义的属性相匹配。 |

下面是一个示例 .xlsx 文件，创建了两个 floor 和两个 room 的小图形。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/import-example.png" alt-text="Excel 中图形数据的屏幕截图。列标题按顺序对应于上述字段，行包含相应的数据值。" lightbox="media/how-to-use-azure-digital-twins-explorer/import-example.png":::

可以在 GitHub 上的 [Azure Digital Twins Explorer 存储库](https://github.com/Azure-Samples/digital-twins-explorer/tree/main/client/examples)中查看此文件和其他 .xlsx 图形示例。

>[!NOTE]
>.xlsx 中所述的属性和关系必须与相关孪生体的模型定义中定义的内容匹配。

#### <a name="import-file-to-azure-digital-twins-explorer"></a>将文件导入到 Azure Digital Twins Explorer

计算机上有了可供导入的文件后，选择“孪生体图”面板中的“导入图形”图标。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。“导入图形”按钮突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png":::

在出现的文件选择器框中，在计算机上导航到要上传的图形文件（.xlsx 或 .json），然后选择“打开”将其上传。

Azure 数字孪生将打开一个“导入”面板，显示要导入的图形的预览。 若要确认，请选择面板右上角的“保存”图标。

如果导入成功，一个模式窗口将显示已上传的模型、孪生体和关系的数目。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。屏幕中央显示“导入成功”模式，显示已导入 4 个孪生体和 2 个关系。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png":::

### <a name="export-graph-and-models"></a>导出图形和模型

可以使用导出功能导出部分或完整图形，包括模型、孪生体和关系。 导出将最近查询结果中的孪生体和关系以及实例中的所有模型序列化为基于 JSON 的格式（可以将其下载到计算机上）。

首先，请使用[查询资源管理器](#query-your-digital-twin-graph)面板运行一个查询，该查询选择要下载的孪生体和关系。 这会在“孪生体图”面板中填充它们。

>[!TIP]
>用于显示所有孪生体和关系的查询为 `SELECT * FROM digitaltwins`。

在“孪生体图”面板显示要下载的图形部分后，请选择“导出图形”图标。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png" alt-text="Azure Digital Twins Explorer 的“孪生体图”面板屏幕截图。“导出图形”按钮突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png":::

此操作启用“孪生体图”框中的“下载”链接。 选择它可将查询结果的基于 JSON 的表示形式和实例中的所有模型下载到计算机。

>[!TIP]
>可以通过[导入](#import-graph)功能对此文件进行编辑和/或将其重新上传到 Azure 数字孪生。

## <a name="link-to-your-environment"></a>链接到环境

可以与他人共享 Azure Digital Twins Explorer 环境，以便在工作上进行协作。 本部分介绍如何将 Azure Digital Twins Explorer 环境发送给其他人，并验证他们是否具有访问它的权限。

若要共享环境，可以向收件人发送一个链接，该链接将打开连接到你的实例的 Azure Digital Twins Explorer 窗口。 使用下面的链接，并替换 Azure 数字孪生实例的“租户 ID”和“主机名”的占位符 。 

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>`

>[!NOTE]
> 此处，主机名占位符的值前面没有“https://”。

下面是填写了占位符值的 URL 示例：

`https://explorer.digitaltwins.azure.net/?tid=00a000aa-00a0-00aa-0a0aa000aa00&eid=ADT-instance.api.wcus.digitaltwins.azure.net`

对于要在生成的 Azure Digital Twins Explorer 窗口中查看实例的收件人，必须登录 Azure 帐户，并拥有对实例的“Azure 数字孪生数据读取者”访问权限（有关 Azure 数字孪生角色的详细信息，可参阅[安全](concepts-security.md)）。 对于要对图形和数据进行更改的收件人，他们必须对实例拥有“Azure 数字孪生数据所有者”角色。

### <a name="link-with-a-query"></a>与查询链接

你可能希望共享一个环境并指定一个在登陆时执行的查询，以便为团队成员突出显示子图或自定义视图。 为此，请从环境的 URL 开始，将查询文本作为 querystring 参数添加到 URL：

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>&query=<query-text>`

查询文本应进行 URL 编码。 

>[!TIP]
>可以从“查询资源管理器”窗口复制查询文本，并将其粘贴到 URL 窗口中 URL 末尾的正确位置。 提交此 URL 应将查询文本转换为使用正确的 URL 编码。
>
> 还可以使用独立的 URL 编码器来转换查询文本。

下面是查询“SELECT * FROM digitaltwins”的参数示例：

`...&query=SELECT%20*%20FROM%20digitaltwins`

然后，便可共享完成的 URL。

## <a name="advanced-settings"></a>高级设置

可以为 Azure Digital Twins Explorer 启用多个高级设置选项。

单击右上角的设置齿轮可以配置以下高级功能：
* 预先加载：可通过顶部工具栏中的“设置”齿轮进行访问。 当查询返回与其他孪生体有关系的孪生体，而这些其他孪生体未包含在查询结果中时，此功能将在呈现图形之前加载“缺失的”孪生体。
* 高速缓存：可通过顶部工具栏中的“设置”齿轮进行访问。 启用此功能后，Azure Digital Twins Explorer 将在内存中保留关系和模型的本地缓存，以提高查询性能。 在执行对相关元素的任何写入操作时以及浏览器刷新时，这些缓存会被清除。
* 控制台：可通过顶部工具栏中的“设置”齿轮进行访问。 此功能支持显示控制台窗口，该窗口能够使用简单 shell 函数来处理图形。
* 输出：可通过顶部工具栏中的“设置”齿轮进行访问。 此功能支持显示输出窗口，其中显示了操作的诊断跟踪。
* 自定义面板布局：可编辑组成 Azure Digital Twins Explorer 的面板（查询资源管理器、模型、孪生体图、模型图）的位置。 若要将面板移动到不同的位置，请单击并按住面板名称，然后将其拖动到新的所需位置。

    :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/panels.png" alt-text="Azure Digital Twins Explorer 的屏幕截图。“查询资源管理器”、“模型”、“孪生体图”和“模型图”面板突出显示。" lightbox="media/how-to-use-azure-digital-twins-explorer/panels.png":::

    刷新浏览器窗口时，面板位置将被重置。

## <a name="next-steps"></a>后续步骤 

了解如何为 Azure 数字孪生的孪生体图编写查询： 
* [查询语言](concepts-query-language.md)
* [查询孪生图](how-to-query-graph.md)