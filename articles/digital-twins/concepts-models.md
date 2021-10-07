---
title: DTDL 模型
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生如何使用自定义模型来描述环境中的实体。
author: baanders
ms.author: baanders
ms.date: 8/25/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: fasttrack-edit
ms.openlocfilehash: 792ddf3f3ca57b16112de78a6ecf2c2d887494ef
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535843"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>了解 Azure 数字孪生中的孪生模型

Azure 数字孪生的一个重要特征是能够定义你自己的词汇，并以你自定义的业务字词生成孪生图。 此功能是通过用户提供的 **模型** 提供的。 你可以将模型视为有关你的世界的说明中的名词。 

模型类似于面向对象的编程语言中的 **类**，用于定义真实工作环境中一个特定概念的数据形状。 模型具有名称（例如 *Room* 或 *TemperatureSensor*），并包含元素，例如属性、遥测/事件，以及用于描述此实体类型在你环境中的作用的命令。 稍后，你将使用这些模型来创建[数字孪生体](concepts-twins-graph.md)，这些孪生体代表符合此类型说明的特定实体。

Azure 数字孪生模型以基于 JSON-LD 的 **数字孪生定义语言 (DTDL)** 表示。  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>用于模型的数字孪生定义语言 (DTDL)

Azure 数字孪生的模型是使用数字孪生定义语言 (DTDL) 定义的。 

可以在 GitHub 中查看 DTDL 的完整语言规范：[数字孪生定义语言 (DTDL) - 版本 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。

DTDL 基于 JSON-LD，独立于编程语言。 DTDL 并非专用于 Azure 数字孪生，它还可用于表示 [IoT 即插即用](../iot-develop/overview-iot-plug-and-play.md)等其他 IoT 服务中的设备数据。 Azure 数字孪生使用 DTDL **版本 2**（现在不建议在 Azure 数字孪生中使用 DTDL 版本 1）。 

本文的余下内容总结了如何在 Azure 数字孪生中使用该语言。

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure 数字孪生 DTDL 实现细节

并非使用 DTDL 的所有服务都实现完全相同的 DTDL 功能。 例如，IoT 即插即用不使用适用于图的 DTDL 功能，而 Azure 数字孪生目前不实现 DTDL 命令。 

要使 DTDL 模型与 Azure 数字孪生兼容，必须满足以下要求：

* 模型中的所有顶级 DTDL 元素必须是接口类型。 提出此项要求的原因是，Azure 数字孪生模型 API 可以接收表示某个接口或接口数组的 JSON 对象。 因此，不允许在最高级别使用其他 DTDL 元素类型。
* 用于 Azure 数字孪生的 DTDL 不得定义任何命令。
* Azure 数字孪生只允许单个级别的组件嵌套，这意味着，用作组件的接口本身不能包含任何组件。 
* 不能在其他 DTDL 接口中以内联方式定义接口；接口必须定义为具有自身 ID 的独立顶级实体。 然后，当另一个接口想要以组件形式或通过继承包含该接口时，这另一个接口可以引用该接口的 ID。

此外，Azure 数字孪生不关注属性或关系上的 `writable` 特性。 尽管可以根据 DTDL 规范设置此特性，但 Azure 数字孪生不会使用该值。 这些特性始终被视为可供对 Azure 数字孪生服务拥有常规写入权限的外部客户端写入。

## <a name="model-overview"></a>模型概述

### <a name="elements-of-a-model"></a>模型的元素

在模型定义中，顶级代码项是一个 **接口**。 此类型封装整个模型，模型的其余部分在接口中定义。 

一个 DTDL 模型接口可以包含以下每种字段的零个、一个或多个字段：
* **属性** - 属性是表示实体状态的数据字段（类似于许多面向对象的编程语言中的属性）。 属性具有后备存储，随时可供读取。 有关详细信息，请参阅下面的[属性和遥测](#properties-and-telemetry)。
* **遥测** - 遥测字段表示度量结果或事件，通常用于描述设备传感器读数。 与属性不同，遥测数据不会存储在数字孪生体上；它是一系列有时间限制的数据事件，在这些事件发生时就需要对其进行处理。 有关详细信息，请参阅下面的[属性和遥测](#properties-and-telemetry)。
* **关系** - 使用关系可以表示一个数字孪生与其他数字孪生的关连方式。 关系可以表示不同的语义含义，例如 contains ("floor contains room")、cools ("hvac cools room")、isBilledTo ("compressor is billed to user")，等等  。 解决方案可以使用关系来提供相互关联的实体的图。 关系还可具有其自己的属性。 有关详细信息，请参阅下面的[关系](#relationships)。
* **组件** - 如果需要，可以使用组件生成你的模型接口作为其他接口的程序集。 组件的一个示例是 *frontCamera* 接口（和另一个组件接口 *backCamera*），在定义 *phone* 的模型时可以使用这些接口。 先定义 frontCamera 的接口，就像它是其自己的模型一样，然后在定义 Phone 时引用该接口 。

    使用组件可以描述这样的对象：它属于解决方案不可或缺的一部分，但不需要单独的标识，且不需要在孪生图中单独创建、删除或重新排列。 如果你希望实体在孪生体图中独立存在，请将其表示为通过关系连接的不同模型的单独数字孪生体。
    
    >[!TIP] 
    >组件还可用于进行组织，以便在模型接口中将相关属性集分组到一起。 在这种情况下，可以将每个组件视为该接口中的命名空间或“文件夹”。

    有关详细信息，请参阅下面的[组件](#components)。


> [!NOTE]
> [DTDL 规范](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)还定义了 **命令**。命令是可以在数字孪生上执行的方法（例如重置命令，或者打开或关闭风扇的命令）。 但是，Azure 数字孪生中目前不支持命令。

### <a name="model-code"></a>模型代码

可以在任何文本编辑器中编写孪生类型模型。 DTDL 语言遵循 JSON 语法，因此你应使用扩展名 .json 存储模型。 使用 JSON 扩展名可使许多编程文本编辑器在你的 DTDL 文档中提供基本语法检查和突出显示。 还有一个适用于 [Visual Studio Code](https://code.visualstudio.com/) 的 [DTDL 扩展名](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)。

模型的字段为：

| 字段 | 说明 |
| --- | --- |
| `@id` | 模型的标识符。 必须采用 `dtmi:<domain>:<unique-model-identifier>;<model-version-number>` 格式。 |
| `@type` | 标识所要描述的信息类型。 对于接口，类型为 *Interface*。 |
| `@context` | 设置 JSON 文档的[上下文](https://niem.github.io/json/reference/json-ld/context/)。 模型应使用 `dtmi:dtdl:context;2`。 |
| `displayName` | [可选] 提供用于定义模型的易记名称的选项。 |
| `contents` | 所有剩余的接口数据将作为特性定义数组放在此处。 每个特性必须提供 `@type`（属性、遥测、命令、关系或组件），用于标识它所描述的接口信息种类，然后必须提供一组属性用于定义实际特性（例如，提供 `name` 和 `schema` 来定义某个属性）     。 |

#### <a name="example-model"></a>示例模型

本部分包含一个以 DTDL 接口形式编写的基本模型示例。 

此模型描述了一个住宅，其中每个 ID 都有一个属性。 住宅模型还定义了与楼层模型的关系，用于指示住宅孪生体连接到了特定的楼层孪生体。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/IHome.json":::

## <a name="properties-and-telemetry"></a>属性和遥测

本部分详细介绍了 DTDL 模型中的属性和遥测 。

有关可能出现在属性中的字段的完整列表，请参阅 [DTDL v2 规范中的“属性”](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)。有关可能出现在遥测中的字段的完整列表，请参阅 [DTDL v2 规范中的“遥测”](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#telemetry)。

### <a name="difference-between-properties-and-telemetry"></a>属性和遥测之间的差异

下面是有关在概念上区分 Azure 数字孪生中 DTDL 属性和遥测数据的一些指导 。
* “属性”应具有后备存储，这意味着，你可以随时读取属性并检索其值。 如果属性是可写的，则你还可在该属性中存储值。  
* **遥测** 更像是事件流；它是一组生存期较短的数据消息。 如果不设置侦听事件以及该事件发生时要执行的操作，则以后不会跟踪该事件。 以后你无法返回并读取该事件。 
  - 在 C# 术语中，遥测类似于 C# 事件。 
  - 在 IoT 术语中，遥测通常是设备发送的单个度量结果。

“遥测”通常用于 IoT 设备，因为许多设备无法或者不需要存储它们生成的度量值。 它们只是将这些值作为“遥测”事件流发出。 在这种情况下，你将无法随时在设备上查询遥测字段的最新值。 需要侦听设备发出的消息，并在消息抵达时执行操作。 

因此，在 Azure 数字孪生中设计模型时，在大多数情况下你可能会使用属性来为孪生建模。 这样就可以获得后备存储，并可以读取和查询数据字段。

遥测和属性通常协同工作来处理从设备流入的数据。 由于流入 Azure 数字孪生的所有数据都是通过 [API](concepts-apis-sdks.md) 传输的，因此，你通常会使用 ingress 函数读取来自设备的遥测或属性事件，并在 Azure 数字孪生的响应中设置一个属性。 

你还可以通过 Azure 数字孪生 API 发布遥测事件。 与其他遥测一样，该事件是一个生存期较短的事件，需要通过一个侦听器对其进行处理。

### <a name="schema"></a>架构

根据 DTDL，属性和遥测特性的架构可以是标准基元类型（`integer`、`double`、`string` 和 `boolean`），也可以是 `dateTime` 和 `duration` 等其他类型 。 

除基元类型外，属性和遥测字段还可以使用以下[复杂类型](#complex-object-type-example)：
* `Object`
* `Map`
* `Enum`
* （仅限遥测）`Array`

它们也可以是[语义类型](#semantic-type-example)，这样便可批注带单位的值。

### <a name="basic-property-and-telemetry-examples"></a>基本属性和遥测示例

下面是 DTDL 模型中属性的基本示例。 此示例显示住宅的 ID 属性。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/IHome.json" highlight="7-11":::

下面是 DTDL 模型中遥测字段的基本示例。 此示例显示传感器上的温度遥测。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/ISensor.json" highlight="7-11":::

### <a name="complex-object-type-example"></a>复杂（对象）类型示例

属性和遥测可以是复杂类型，包括 `Object` 类型。

下面的示例演示住宅模型的另一个版本，其地址具有一个属性。 `address` 是一个对象，有其自己的街道、城市、省/市/自治区和邮政编码字段。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IHome.json" highlight="8-31":::

### <a name="semantic-type-example"></a>语义类型示例

语义类型可表示带有单位的值。 可使用 DTDL 支持的任何语义类型来表示属性和遥测。 要详细了解 DTDL 中的语义类型以及支持的值，请参阅 [DTDL v2 规范中的语义类型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#semantic-types)。

以下示例演示了一个传感器模型，该模型具有温度的语义类型遥测和湿度的语义类型属性。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/ISensor.json" highlight="7-18":::

## <a name="relationships"></a>关系

本部分详细介绍 DTDL 模型中的关系。

有关可能出现在关系中的字段的完整列表，请参阅 [DTDL v2 规范中的“关系”](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#relationship)。

### <a name="basic-relationship-example"></a>基本关系示例

下面是 DTDL 模型中关系的基本示例。 此示例展示了住宅模型上的关系，通过这种关系，住宅模型可以连接到楼层模型。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/IHome.json" highlight="12-18":::

>[!NOTE]
>对于关系，`@id` 是一个可选字段。 如果未提供 `@id`，则数字孪生体接口处理器会分配一个。

### <a name="targeted-and-non-targeted-relationships"></a>目标和非目标关系

可定义含目标或不含目标的关系。 目标指定关系可以涉及的孪生体类型。 例如，可以包含一个目标，指定住宅模型只能与楼层孪生体之间具有 rel_has_floors 关系。 

有时最好定义不含特定目标的关系，以便关系可以连接到许多不同类型的孪生体。

下面是 DTDL 模型上不含目标的关系示例。 在此例中，关系用于定义房间可能具有哪些传感器，关系可以连接到任意类型。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IRoom.json" range="2-27" highlight="20-25":::

### <a name="properties-of-relationships"></a>关系的属性

DTDL 还允许关系具有其自己的属性。 在 DTDL 模型中定义关系时，关系可以有它自己的 `properties` 字段，你可以在其中定义自定义属性来描述特定于关系的状态。

下面的示例演示了住宅模型的另一个版本，其中 `rel_has_floors` 关系具有一个属性，用于表示相关楼层上次被占用的时间。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IHome.json" highlight="39-45":::

## <a name="components"></a>组件

本部分详细介绍 DTDL 模型中的组件。

有关可能出现在组件中的字段的完整列表，请参阅 [DTDL v2 规范中的“组件”](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component)。

### <a name="basic-component-example"></a>基本组件示例

下面是 DTDL 模型中组件的基本示例。 此示例演示使用恒温器模型作为组件的 Room 模型。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IRoom.json" highlight="15-19, 28-41":::

如果此解决方案中的其他模型也应该包含恒温器，那么这些模型可以在自己的定义中引用相同的恒温器模型作为组件，就像 Room 所做的那样。

> [!IMPORTANT]
> 组件接口（上面示例中的恒温器）必须与任何使用它的接口（上面示例中的 Room）定义在同一个数组中，以便找到组件引用。

## <a name="model-inheritance"></a>模型继承

有时你可能想要进一步将模型专用化。 例如，使用一个泛型模型 Room 以及专用化变体 ConferenceRoom 和 Gym 可能会很有用。 为了表示专用化，DTDL 支持继承。 接口可从一个或多个接口继承。 为此可将一个 `extends` 字段添加到模型。

`extends` 节是接口名称或接口名称的数组（允许扩展接口从多个父模型继承）。 单个父模型可以充当多个扩展接口的基础模型。

以下示例将前面 DTDL 示例中的住宅模型重新假设为更大的“核心”模型的子类型。 首先定义父模型（核心），然后使用 `extends` 在父模型的基础之上构建子模型（住宅）。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/ICore.json":::

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IHome.json" range="1-8" highlight="6":::

在这种情况下，核心模型向住宅模型提供 ID 和名称。 其他模型也可以扩展核心模型以同样获取这些属性。 下面是扩展同一父接口的住宅模型：

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IRoom.json" range="2-9" highlight="6":::

应用继承后，扩展接口会公开整个继承链中的所有属性。

扩展接口不能更改父接口的任何定义；只能在父接口中添加定义。 此外，扩展接口不能重新定义已在其任何父接口中定义的功能（即使定义的功能相同）。 例如，如果父接口定义了 `double` 属性 mass，则扩展接口不能包含 mass 的声明，即使该声明也是 `double` 。

## <a name="modeling-best-practices"></a>建模的最佳做法

在设计模型以反映环境中的实体时，提前考虑你的设计的[查询](concepts-query-language.md)影响。 你可能想要以一种可避免图遍历后生成较大结果集的方式设计属性。 我们还建议你将需要在单个查询中解答的关系建模为单层关系。

### <a name="validating-models"></a>验证模型

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="modeling-tools"></a>建模工具

我们提供了多个示例来进一步方便你处理模型和本体。 这些示例位于以下存储库中：[数字孪生定义语言 (DTDL) 的工具](https://github.com/Azure/opendigitaltwins-tools)。

本部分更详细地介绍了当前的示例集。

### <a name="model-uploader"></a>模型上传程序 

完成创建、扩展或选择模型后，可以将其上传到 Azure 数字孪生实例，使其可在解决方案中使用。 为此，可以按照[管理 DTDL 模型](how-to-manage-model.md#upload-models)中所述使用 [Azure 数字孪生 API](concepts-apis-sdks.md)。

但如果你要上传很多模型，或者这些模型存在很多相互依赖关系，导致各个模型的上传顺序变得复杂，那么，你可以使用 [Azure 数字孪生模型上传程序示例](https://github.com/Azure/opendigitaltwins-tools/tree/master/ADTTools#uploadmodels)一次上传很多模型。 按照示例附带的说明，配置并使用此项目将模型上传到你自己的实例中。

### <a name="model-visualizer"></a>模型可视化工具 

将模型上传到 Azure 数字孪生实例后，可以使用 [Azure 数字孪生模型可视化工具](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)查看 Azure 数字孪生实例中的模型，包括任何继承和模型关系。 此示例当前处于草稿状态。 我们鼓励数字孪生开发社区扩展该示例并为其贡献内容。 

## <a name="next-steps"></a>后续步骤

* 了解如何基于行业标准本体来创建模型：[什么是本体？](concepts-ontologies.md)

* 更深入地了解如何通过 API 操作来管理模型：[管理 DTDL 模型](how-to-manage-model.md)

* 了解如何使用模型来创建数字孪生体：[数字孪生体和孪生体关系图](concepts-twins-graph.md)

