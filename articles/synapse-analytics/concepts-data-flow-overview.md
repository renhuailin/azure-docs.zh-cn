---
title: 数据流
description: Azure Synapse 分析中的数据流概述
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592604"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Azure Synapse 分析中的数据流

## <a name="what-are-data-flows"></a>什么是数据流？

数据流是在 Azure Synapse 分析中以可视方式设计的数据转换。 数据流允许数据工程师无需编写代码即可开发数据转换逻辑。 生成的数据流将作为使用向外 Apache Spark 群集的 Azure Synapse 分析管道中的活动执行。 可以使用现有的 Azure Synapse Analytics 计划、控制、流和监视功能操作化数据流活动。

数据流提供完全直观的体验，无需编码。 数据流在 Synapse 托管的执行群集上运行，以便进行扩展的数据处理。 Azure Synapse Analytics 处理所有代码转换、路径优化和数据流作业的执行。

## <a name="getting-started"></a>开始使用

数据流是通过 Synapse studio 中的 "开发" 窗格创建的。 若要创建数据流，请选择 "**开发**" 旁边的加号，然后选择 "**数据流"。** 

![新建数据流](media/data-flow/new-data-flow.png)

此操作会将你转到 "数据流" 画布，你可以在其中创建转换逻辑。 选择 " **添加源** " 开始配置源转换。 有关详细信息，请参阅 [源转换](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="authoring-data-flows"></a>创作数据流

数据流具有独特的创作画布，旨在使生成转换逻辑变得简单。 数据流画布分为三部分：顶栏、图形和配置面板。 

![屏幕截图显示带有标记的顶部条形图、图形和配置面板的数据流画布。](media/data-flow/canvas-1.png)

### <a name="graph"></a>图形

关系图显示转换流。 它显示源数据流入一个或多个接收器时的沿袭。 若要添加新源，请选择 " **添加源**"。 若要添加新的转换，请选择现有转换右下方的加号。 了解更多有关如何 [管理数据流图形的](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)信息。

![屏幕截图使用 "搜索" 文本框显示画布的图形部分。](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>配置面板

"配置" 面板显示特定于当前选定转换的设置。 如果未选择任何转换，则会显示数据流。 在整个数据流配置中，可以通过 " **参数** " 选项卡添加参数。有关详细信息，请参阅 [数据流参数](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

每个转换至少包含四个配置选项卡。

#### <a name="transformation-settings"></a>转换设置

每个转换的配置窗格中的第一个选项卡包含特定于该转换的设置。 有关详细信息，请参阅该转换的文档页。

![源设置选项卡](media/data-flow/source-1.png)

#### <a name="optimize"></a>优化

" **优化** " 选项卡包含配置分区方案的设置。 若要了解有关如何优化数据流的详细信息，请参阅 [映射数据流性能指南](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

![屏幕截图显示 "优化" 选项卡](media/data-flow/optimize.png)

#### <a name="inspect"></a>检查

" **检查** " 选项卡可用于查看要转换的数据流的元数据。 您可以查看列计数、列更改、添加的列、数据类型、列顺序和列引用。 **检查** 是元数据的只读视图。 不需要启用调试模式即可在 " **检查** " 窗格中查看元数据。

![检查选项卡](media/data-flow/inspect.png)

通过转换更改数据形状时，" **检查** " 窗格中会显示元数据更改流。 如果源转换中没有定义的架构，则元数据将不会显示在 " **检查** " 窗格中。 缺少元数据在架构偏移方案中很常见。

#### <a name="data-preview"></a>数据预览

如果调试模式为打开状态，则 " **数据预览** " 选项卡将在每次转换时提供数据的交互式快照。 有关详细信息，请参阅 [调试模式下的数据预览](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview)。

### <a name="top-bar"></a>上栏

顶部栏包含影响整个数据流的操作，如验证和调试设置。 您还可以查看转换逻辑的基础 JSON 代码和数据流脚本。

## <a name="available-transformations"></a>可用转换

查看 [映射数据流转换概述](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 以获取可用转换的列表。

## <a name="data-flow-activity"></a>数据流活动

数据流在 Azure Synapse 分析管道中使用数据流 [活动](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)进行操作化。 用户需要做的就是指定要使用的集成运行时并传入参数值。 有关详细信息，请参阅 [Azure 集成运行时](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime)。

## <a name="debug-mode"></a>调试模式

调试模式允许您在生成和调试数据流时以交互方式查看每个转换步骤的结果。 生成数据流逻辑和运行包含数据流活动的管道调试运行时，可以在中使用调试会话。 若要了解详细信息，请参阅 [调试模式文档](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="monitoring-data-flows"></a>监视数据流

数据流与现有的 Azure Synapse 分析监视功能集成。 若要了解如何了解数据流监视输出，请参阅 [监视映射数据流](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

Azure Synapse Analytics 团队已经创建了 [性能优化指南](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ，可帮助您在生成业务逻辑之后优化数据流的执行时间。

## <a name="next-steps"></a>后续步骤

* 了解如何创建 [源转换](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* 了解如何在 [调试模式下](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)生成数据流。
