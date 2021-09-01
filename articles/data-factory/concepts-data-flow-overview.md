---
title: 映射数据流
description: Azure 数据工厂中的映射数据流的概述
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/26/2021
ms.openlocfilehash: 1d4e8ad4fd7d0b5690f506e9db7f1043c8f21045
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039082"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Azure 数据工厂中的映射数据流

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>映射数据流是什么？

映射数据流是 Azure 数据工厂中以可视方式设计的数据转换。 使用数据流，数据工程师可以开发数据转换逻辑，无需编写代码。 生成的数据流是使用横向扩展的 Apache Spark 群集作为活动在 Azure 数据工厂管道内执行的。 可以通过现有的 Azure 数据工厂计划、控制、流和监视功能，将数据流活动操作化。

映射数据流提供完全直观的体验，无需编码。 数据流在 ADF 托管的执行群集上运行，以进行横向扩展的数据处理。 Azure 数据工厂处理数据流作业的所有代码转换、路径优化以及执行。

## <a name="getting-started"></a>入门

数据流是从“工厂资源”窗格（如管道和数据集）中创建的。 若要创建数据流，请选择“工厂资源”旁边的加号，然后选择“数据流” 。 

![新建数据流](media/data-flow/new-data-flow.png)

此操作将转到数据流画布，你可在其中创建转换逻辑。 选择“添加源”，开始配置源转换。 有关详细信息，请参阅[源转换](data-flow-source.md)。

## <a name="authoring-data-flows"></a>创作数据流

映射数据流具有独特的创作画布，旨在简化生成转换逻辑。 数据流画布分为三个部分：顶部栏、图形和配置面板。 

![屏幕截图显示数据流画布，其中标记了顶部栏、图形和配置面板。](media/data-flow/canvas-1.png "画布")

### <a name="graph"></a>图形

图形显示转换流。 它显示源数据流入一个或多个接收器时的沿袭。 若要添加新源，请选择“添加源”。 若要添加新的转换，请选择现有转换右下方的加号。 详细了解如何[管理数据流图形](concepts-data-flow-manage-graph.md)。

![显示画布的图形部分，其中包含“搜索”文本框。](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>配置面板

配置面板显示特定于当前所选转换的设置。 如果未选择任何转换，则会显示数据流。 在整个数据流配置中，可以通过“参数”选项卡来添加参数。有关详细信息，请参阅[映射数据流参数](parameters-data-flow.md)。

每个转换至少包含四个配置选项卡。

#### <a name="transformation-settings"></a>转换设置

每个转换的配置窗格中的第一个选项卡包含特定于该转换的设置。 有关详细信息，请参阅转换的文档页。

![源设置选项卡](media/data-flow/source1.png "源设置选项卡")

#### <a name="optimize"></a>优化

“优化”选项卡包含用于配置分区方案的设置。 若要详细了解如何优化数据流，请参阅[映射数据流性能指南](concepts-data-flow-performance.md)。

![屏幕截图显示“优化”选项卡，其中包含“分区选项”、“分区类型”和“分区数”。](media/data-flow/optimize.png)

#### <a name="inspect&quot;></a>检查

可以通过“检查”选项卡了解正在转换的数据流的元数据。 可以看到列计数、更改的列、添加的列、数据类型、列排序以及列引用。 “检查”视图是针对元数据的只读视图。 不需启用调试模式即可在“检查”窗格中查看元数据。

![检查](media/data-flow/inspect1.png &quot;检查")

以转换方式更改数据的形状时，可以在“检查”窗格中查看元数据更改流。 如果源转换中没有定义的架构，则元数据将在“检查”窗格中不可见。 在架构偏差场景中，缺少元数据是很常见的。

#### <a name="data-preview"></a>数据预览

如果开启了调试模式，则“数据预览”选项卡将在每次转换时提供数据的交互式快照。 有关详细信息，请参阅[调试模式下的数据预览](concepts-data-flow-debug-mode.md#data-preview)。

### <a name="top-bar"></a>上栏

顶部栏包含影响整个数据流的操作，如保存和验证。 还可以查看转换逻辑的基础 JSON 代码和数据流脚本。 有关详细信息，请参阅[数据流脚本](data-flow-script.md)。

## <a name="available-transformations"></a>可用转换

查看[映射数据流转换概述](data-flow-transformation-overview.md)以获取可用转换的列表。

## <a name="data-flow-data-types"></a>数据流数据类型

* array
* binary
* boolean
* complex
* decimal（包括精度）
* date
* FLOAT
* integer
* long
* map
* short
* 字符串
* timestamp

## <a name="data-flow-activity"></a>数据流活动

映射数据流是使用[数据流活动](control-flow-execute-data-flow-activity.md)在 ADF 管道内进行操作化的。 用户需要做的就是指定要使用的集成运行时并传入参数值。 有关详细信息，请参阅 [Azure 集成运行时](concepts-integration-runtime.md#azure-integration-runtime)。

## <a name="debug-mode"></a>调试模式

使用调试模式可以在生成和调试数据流时以交互方式查看每个转换步骤的结果。 生成数据流逻辑和使用数据流活动运行管道调试运行时，都可以使用调试会话。 若要了解详细信息，请参阅[调试模式文档](concepts-data-flow-debug-mode.md)。

## <a name="monitoring-data-flows"></a>监视数据流

映射数据流与现有的 Azure 数据工厂监视功能集成。 若要了解如何掌握数据流监视输出，请参阅[监视映射数据流](concepts-data-flow-monitoring.md)。

Azure 数据工厂团队已创建[性能优化指南](concepts-data-flow-performance.md)，可帮助你在生成业务逻辑后优化数据流的执行时间。


## <a name="available-regions"></a>可用区域

映射数据流在 ADF 的以下区域中可用：

| Azure 区域 | ADF 中的数据流 |
| ------------ | ----------------- |
| 澳大利亚中部 | |
| 澳大利亚中部 2 | |
| 澳大利亚东部 | ✓ |
| 澳大利亚东南部   | ✓ |
| 巴西南部  | ✓ |
| 加拿大中部 | ✓ |
| 印度中部 | ✓ |
| Central US    | ✓ |
| 中国东部 |      |
| 中国东部 2  |   |
| 中国（非区域性） | |
| 中国北部 | ✓ |
| 中国北部 2 | ✓ |
| 东亚 | ✓ |
| 美国东部   | ✓ |
| 美国东部 2 | ✓ |
| 法国中部 | ✓ |
| 法国南部  | |
| 德国中部（主权） | |
| 德国（非区域性）（主权） | |
| 德国北部（公共） | |
| 德国东北部（主权） | |
| 德国中西部（公共） |  |
| 日本东部 | ✓ |
| 日本西部 |  |
| 韩国中部 | ✓ |
| 韩国南部 | |
| 美国中北部  | ✓ |
| 北欧  | ✓ |
| 挪威东部 | ✓ |
| 挪威西部 | |
| 南非北部    | ✓ |
| 南非西部 |  |
| 美国中南部  | |
| 印度南部 | |
| 东南亚    | ✓ |
| 瑞士北部 | ✓ |
| 瑞士西部 | |
| 阿联酋中部 | |
| 阿拉伯联合酋长国北部 | ✓ |
| 英国南部  | ✓ |
| 英国西部 |     |
| US DoD 中部 | |
| US DoD 东部 | |
| US Gov 亚利桑那州 | ✓ |
| US Gov（非区域性） | |
| US Gov 德克萨斯州 | |
| US Gov 弗吉尼亚州 | ✓ |
| 美国中西部 |     |
| 西欧   | ✓ |
| 印度西部 | |
| 美国西部   | ✓ |
| 美国西部 2 | ✓ |

## <a name="next-steps"></a>后续步骤

* 了解如何创建[源转换](data-flow-source.md)。
* 了解如何在[调试模式](concepts-data-flow-debug-mode.md)下生成数据流。
