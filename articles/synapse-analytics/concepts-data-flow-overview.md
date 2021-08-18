---
title: 数据流
description: Azure Synapse Analytics 中的数据流概述
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 525fed571576f6d5fc49d68abbbe26e46cf3291e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862256"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的数据流

## <a name="what-are-data-flows"></a>什么是数据流？

数据流是 Azure Synapse Analytics 中以可视方式设计的数据转换。 使用数据流，数据工程师可以开发数据转换逻辑，无需编写代码。 生成的数据流是使用横向扩展的 Apache Spark 群集作为 Azure Synapse Analytics 管道中的活动执行的。 可以通过现有的 Azure Synapse Analytics 计划、控制、流和监视功能，将数据流活动操作化。

数据流提供完全可视化的体验，无需编码。 数据流在 Synapse 托管的执行群集上运行，以进行横向扩展的数据处理。 Azure Synapse Analytics 处理数据流作业的所有代码转换、路径优化以及执行。

## <a name="getting-started"></a>入门

数据流是通过 Synapse Studio 中的“开发”窗格创建的。 若要创建数据流，请选择“开发”旁边的加号，然后选择“数据流” 。 

![新建数据流](media/data-flow/new-data-flow.png)

此操作将转到数据流画布，你可在其中创建转换逻辑。 选择“添加源”，开始配置源转换。 有关详细信息，请参阅[源转换](../data-factory/data-flow-source.md?context=/azure/synapse-analytics/context/context)。

## <a name="authoring-data-flows"></a>创作数据流

数据流具有独特的创作画布，用于轻松构建转换逻辑。 数据流画布分为三个部分：顶部栏、图形和配置面板。 

![屏幕截图显示数据流画布，其中标记了顶部栏、图形和配置面板。](media/data-flow/canvas-1.png)

### <a name="graph"></a>图形

图形显示转换流。 它显示源数据流入一个或多个接收器时的沿袭。 若要添加新源，请选择“添加源”。 若要添加新的转换，请选择现有转换右下方的加号。 详细了解如何[管理数据流图形](../data-factory/concepts-data-flow-manage-graph.md?context=/azure/synapse-analytics/context/context)。

![显示画布的图形部分，其中包含“搜索”文本框。](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>配置面板

配置面板显示特定于当前所选转换的设置。 如果未选择任何转换，则会显示数据流。 在整个数据流配置中，可以通过“参数”选项卡来添加参数。有关详细信息，请参阅[数据流参数](../data-factory/parameters-data-flow.md?context=/azure/synapse-analytics/context/context)。

每个转换至少包含四个配置选项卡。

#### <a name="transformation-settings"></a>转换设置

每个转换的配置窗格中的第一个选项卡包含特定于该转换的设置。 有关详细信息，请参阅转换的文档页。

![源设置选项卡](media/data-flow/source-1.png)

#### <a name="optimize"></a>优化

“优化”选项卡包含用于配置分区方案的设置。 若要详细了解如何优化数据流，请参阅[映射数据流性能指南](../data-factory/concepts-data-flow-performance.md?context=/azure/synapse-analytics/context/context)。

![屏幕截图显示了“优化”选项卡](media/data-flow/optimize.png)

#### <a name="inspect"></a>检查

可以通过“检查”选项卡了解正在转换的数据流的元数据。 可以看到列计数、更改的列、添加的列、数据类型、列排序以及列引用。 “检查”视图是针对元数据的只读视图。 不需启用调试模式即可在“检查”窗格中查看元数据。

![“检查”选项卡](media/data-flow/inspect.png)

以转换方式更改数据的形状时，可以在“检查”窗格中查看元数据更改流。 如果源转换中没有定义的架构，则元数据将在“检查”窗格中不可见。 在架构偏差场景中，缺少元数据是很常见的。

#### <a name="data-preview"></a>数据预览

如果开启了调试模式，则“数据预览”选项卡将在每次转换时提供数据的交互式快照。 有关详细信息，请参阅[调试模式下的数据预览](../data-factory/concepts-data-flow-debug-mode.md?context=/azure/synapse-analytics/context/context#data-preview)。

### <a name="top-bar"></a>上栏

顶部栏包含影响整个数据流的操作，例如，验证和调试设置。 还可以查看转换逻辑的基础 JSON 代码和数据流脚本。

## <a name="available-transformations"></a>可用转换

查看[映射数据流转换概述](../data-factory/data-flow-transformation-overview.md?context=/azure/synapse-analytics/context/context)以获取可用转换的列表。

## <a name="data-flow-activity"></a>数据流活动

数据流是使用[数据流活动](../data-factory/control-flow-execute-data-flow-activity.md?context=/azure/synapse-analytics/context/context)在 Azure Synapse Analytics 管道内进行操作化的。 用户需要做的就是指定要使用的集成运行时并传入参数值。 有关详细信息，请参阅 [Azure 集成运行时](../data-factory/concepts-integration-runtime.md?context=/azure/synapse-analytics/context/context#azure-integration-runtime)。

## <a name="debug-mode"></a>调试模式

使用调试模式可以在生成和调试数据流时以交互方式查看每个转换步骤的结果。 生成数据流逻辑和使用数据流活动运行管道调试运行时，都可以使用调试会话。 若要了解详细信息，请参阅[调试模式文档](../data-factory/concepts-data-flow-debug-mode.md?context=/azure/synapse-analytics/context/context)。

## <a name="monitoring-data-flows"></a>监视数据流

数据流与现有的 Azure Synapse Analytics 监视功能集成。 若要了解如何掌握数据流监视输出，请参阅[监视映射数据流](../data-factory/concepts-data-flow-monitoring.md?context=/azure/synapse-analytics/context/context)。

Azure Synapse Analytics 团队已创建[性能优化指南](../data-factory/concepts-data-flow-performance.md?context=/azure/synapse-analytics/context/context)，可帮助你在生成业务逻辑后优化数据流的执行时间。

## <a name="next-steps"></a>后续步骤

* 了解如何创建[源转换](../data-factory/data-flow-source.md?context=/azure/synapse-analytics/context/context)。
* 了解如何在[调试模式](../data-factory/concepts-data-flow-debug-mode.md?context=/azure/synapse-analytics/context/context)下生成数据流。
