---
title: DTDL 模型
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生如何使用自定义模型来描述环境中的实体。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b3f0dd599f982e19fee7febc3b85d46f91a55b35
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589289"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>了解 Azure 数字孪生中的孪生模型

Azure 数字孪生的一个重要特征是能够定义你自己的词汇，并以你自定义的业务字词生成孪生图。 此功能是通过用户提供的 **模型** 提供的。 你可以将模型视为有关你的世界的说明中的名词。 

模型类似于面向对象的编程语言中的 **类**，用于定义真实工作环境中一个特定概念的数据形状。 模型具有名称（例如 *Room* 或 *TemperatureSensor*），并包含元素，例如属性、遥测/事件，以及用于描述此实体类型在你环境中的作用的命令。 稍后，你将使用这些模型来创建 [**数字孪生**](concepts-twins-graph.md)，这些孪生代表符合此类型说明的特定实体。

Azure 数字孪生模型以基于 JSON-LD 的 **数字孪生定义语言 (DTDL)** 表示。  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>用于模型的数字孪生定义语言 (DTDL)

Azure 数字孪生的模型是使用数字孪生定义语言 (DTDL) 定义的。 

可以在 GitHub 中查看 DTDL 的完整语言规范：[**数字孪生定义语言 (DTDL) - 版本 2**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。

DTDL 基于 JSON-LD，独立于编程语言。 DTDL 并非专用于 Azure 数字孪生，它还可用于表示 [IoT 即插即用](../iot-pnp/overview-iot-plug-and-play.md)等其他 IoT 服务中的设备数据。 Azure 数字孪生使用 DTDL **版本 2**（现在不建议在 Azure 数字孪生中使用 DTDL 版本 1）。 

本文的余下内容总结了如何在 Azure 数字孪生中使用该语言。

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure 数字孪生 DTDL 实现细节

并非使用 DTDL 的所有服务都实现完全相同的 DTDL 功能。 例如，IoT 即插即用不使用适用于图的 DTDL 功能，而 Azure 数字孪生目前不实现 DTDL 命令。 

要使 DTDL 模型与 Azure 数字孪生兼容，必须满足以下要求：

* 模型中的所有顶级 DTDL 元素必须是接口类型。 这是因为，Azure 数字孪生模型 API 可以接收表示某个接口或接口数组的 JSON 对象。 因此，不允许在最高级别使用其他 DTDL 元素类型。
* 用于 Azure 数字孪生的 DTDL 不得定义任何命令。
* Azure 数字孪生只允许单个级别的组件嵌套。 这意味着，用作组件的接口本身不能包含任何组件。 
* 不能在其他 DTDL 接口中以内联方式定义接口；接口必须定义为具有自身 ID 的独立顶级实体。 然后，当另一个接口想要以组件形式或通过继承包含该接口时，这另一个接口可以引用该接口的 ID。

此外，Azure 数字孪生不关注属性或关系中的 `writable` 特性。 尽管可以根据 DTDL 规范设置此值，但 Azure 数字孪生不会使用此值。 这些值始终被视为可供对 Azure 数字孪生服务拥有常规写入权限的外部客户端写入。

## <a name="elements-of-a-model"></a>模型的元素

在模型定义中，顶级代码项是一个 **接口**。 这将封装整个模型，并在接口中定义模型的其余部分。 

一个 DTDL 模型接口可以包含以下每种字段的零个、一个或多个字段：
* **属性** - 属性是表示实体状态的数据字段（类似于许多面向对象的编程语言中的属性）。 属性具有后备存储，随时可供读取。
* **遥测** - 遥测字段表示度量结果或事件，通常用于描述设备传感器读数。 与属性不同，遥测数据不会存储在数字孪生上；它是一系列有时间限制的数据事件，在这些事件发生时就需要对其进行处理。 有关属性和遥测之间的差异的详细信息，请参阅下面的[属性与遥测](#properties-vs-telemetry)部分。
* **组件** - 如果需要，可以使用组件生成你的模型接口作为其他接口的程序集。 组件的一个示例是 *frontCamera* 接口（和另一个组件接口 *backCamera*），在定义 *phone* 的模型时可以使用这些接口。 必须先定义 *frontCamera* 的接口，就像它是其自己的模型一样，然后在定义 *Phone* 时可以引用该接口。

    使用组件可以描述这样的对象：它属于解决方案不可或缺的一部分，但不需要单独的标识，且不需要在孪生图中单独创建、删除或重新排列。 如果你希望实体在孪生图中独立存在，请将其表示为通过关系连接的不同模型的单独数字孪生（参阅下一条带项目符号的要点）。
    
    >[!TIP] 
    >组件还可用于进行组织，以便在模型接口中将相关属性集分组到一起。 在这种情况下，可以将每个组件视为该接口中的命名空间或“文件夹”。
* **关系** - 使用关系可以表示一个数字孪生与其他数字孪生的关连方式。 关系可以表示不同的语义含义，例如 contains ("floor contains room")、cools ("hvac cools room")、isBilledTo ("compressor is billed to user")，等等。解决方案可以使用关系来提供相互关联的实体的图。 关系还可具有其自己的[属性](#properties-of-relationships)。

> [!NOTE]
> [DTDL 规范](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)还定义了 **命令**。命令是可以在数字孪生上执行的方法（例如重置命令，或者打开或关闭风扇的命令）。 但是，Azure 数字孪生中目前不支持命令。

### <a name="properties-vs-telemetry"></a>属性与遥测

下面是有关区分 Azure 数字孪生中 DTDL **属性** 和 **遥测** 字段的一些附加指导。

Azure 数字孪生模型的属性和遥测的区别如下：
* **属性** 预期具有后备存储。 这意味着，你可以随时读取属性并检索其值。 如果属性是可写的，则你还可以在该属性中存储值。  
* **遥测** 更像是事件流；它是一组生存期较短的数据消息。 如果不设置侦听事件以及该事件发生时要执行的操作，则以后不会该跟踪事件。 以后你无法返回并读取该事件。 
  - 在 C# 术语中，遥测类似于 C# 事件。 
  - 在 IoT 术语中，遥测通常是设备发送的单个度量结果。

**遥测** 通常用于 IoT 设备，因为许多设备无法存储它们生成的度量值，或者不注重此类操作。 它们只是将这些值作为“遥测”事件流发出。 在这种情况下，你将无法随时在设备上查询遥测字段的最新值。 而是需要侦听设备发出的消息，并在消息抵达时执行操作。 

因此，在 Azure 数字孪生中设计模型时，你在大多数情况下可能会使用 **属性** 来为孪生建模。 这样你就可以获得后备存储，并可以读取和查询数据字段。

遥测和属性通常协同工作来处理从设备流入的数据。 由于流入 Azure 数字孪生的所有数据都是通过 [API](how-to-use-apis-sdks.md) 传输的，因此，你通常会使用 ingress 函数读取来自设备的遥测或属性事件，并在 Azure 数字孪生的响应中设置一个属性。 

你还可以通过 Azure 数字孪生 API 发布遥测事件。 与其他遥测一样，该事件是一个生存期较短的事件，需要通过一个侦听器对其进行处理。

#### <a name="properties-of-relationships"></a>关系的属性

DTDL 还允许关系具有其自己的属性。 在 DTDL 模型中定义关系时，关系可以有它自己的 `properties` 字段，你可以在其中定义自定义属性来描述特定于关系的状态。

## <a name="model-inheritance"></a>模型继承

有时你可能想要进一步将模型专用化。 例如，使用一个泛型模型 *Room* 以及专用化变体 *ConferenceRoom* 和 *Gym* 可能会很有用。 为了表达专用化，DTDL 支持继承：接口可以从一个或多个其他接口继承。 

以下示例将前面的 DTDL 示例中的 *Planet* 模型重新假设为更大 *CelestialBody* 模型的子类型。 首先定义“父”模型，然后，“子”模型将使用字段 `extends` 构建在“父”模型的基础之上。

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

在此示例中，*CelestialBody* 向 *Planet* 提供名称、质量和温度。 `extends` 节是接口名称或接口名称的数组（允许扩展接口以根据需要从多个父模型继承）。

应用继承后，扩展接口会公开整个继承链中的所有属性。

扩展接口不能更改父接口的任何定义；只能在父接口中添加定义。 此外，扩展接口不能重新定义已在该接口的父接口中定义的功能（即使定义的功能相同）。 例如，如果父接口定义了 `double` 属性 *mass*，则扩展接口不能包含 *mass* 的声明，即使该声明也是 `double`。

## <a name="model-code"></a>模型代码

可以在任何文本编辑器中编写孪生类型模型。 DTDL 语言遵循 JSON 语法，因此你应使用扩展名 *.json* 存储模型。 使用 JSON 扩展名可使许多编程文本编辑器在你的 DTDL 文档中提供基本语法检查和突出显示。 还有一个适用于 [Visual Studio Code](https://code.visualstudio.com/) 的 [DTDL 扩展名](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)。

### <a name="possible-schemas"></a>可能的架构

根据 DTDL，属性和遥测特性的架构可以是标准基元类型（`integer`、`double`、`string` 和 `Boolean`），也可以是 `DateTime` 和 `Duration` 等其他类型。  

除基元类型外，属性和遥测字段还可以使用以下复杂类型： 
* `Object`
* `Map`
* `Enum`

遥测字段还支持 `Array`。

### <a name="example-model"></a>示例模型

本部分包含一个以 DTDL 接口形式编写的典型模型示例。 该模型描述 **行星**，每个行星具有名称、质量和温度。
 
假设行星还可能与它们的卫星 **月球** 交互，并可能包含 **陨石坑**。 在以下示例中，`Planet` 模型通过引用两个外部模型（`Moon` 和 `Crater`）来表达与其他这些实体的连接。 以下示例代码中也定义了这些模型，但其代码非常简单，以免分散读者对主要 `Planet` 示例的注意力。

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

模型的字段为：

| 字段 | 说明 |
| --- | --- |
| `@id` | 模型的标识符。 必须采用 `dtmi:<domain>:<unique model identifier>;<model version number>` 格式。 |
| `@type` | 标识所要描述的信息类型。 对于接口，类型为 *Interface*。 |
| `@context` | 设置 JSON 文档的[上下文](https://niem.github.io/json/reference/json-ld/context/)。 模型应使用 `dtmi:dtdl:context;2`。 |
| `displayName` | [可选] 允许根据需要指定模型的易记名称。 |
| `contents` | 所有剩余的接口数据将作为特性定义数组放在此处。 每个特性必须提供 `@type`（属性、遥测、命令、关系或组件）以标识它所描述的接口信息种类，然后必须提供一组属性用于定义实际特性（例如，提供 `name` 和 `schema` 来定义某个属性）。      |

> [!NOTE]
> 请注意，组件接口（本示例中为 *Crater*）是在使用该接口的接口 (*Planet*) 所在的同一数组中定义的。 必须以这种方式在 API 调用中定义组件才能找到该接口。

## <a name="best-practices-for-designing-models"></a>有关设计模型的最佳做法

在设计模型以反映环境中的实体时，提前考虑你的设计的[查询](concepts-query-language.md)影响。 你可能想要以一种可避免图遍历后生成较大结果集的方式设计属性。 你还可能想要为在单个查询中作为单级别关系解答的关系建模。

### <a name="validating-models"></a>验证模型

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>模型工具 

我们提供了多个示例来进一步方便你处理模型和本体。 这些示例位于以下存储库中：[数字孪生定义语言 (DTDL) 的工具](https://github.com/Azure/opendigitaltwins-tools)。

本部分更详细地介绍了当前的示例集。

### <a name="model-uploader"></a>模型上传程序 

**用于将模型上传到 Azure 数字孪生**

完成创建、扩展或选择模型后，可以将其上传到 Azure 数字孪生实例，使其可在解决方案中使用。 可以根据[操作指南：管理 DTDL 模型](how-to-manage-model.md#upload-models)中所述，使用 [Azure 数字孪生 API](how-to-use-apis-sdks.md) 来执行此操作。

但是，如果你要上传很多模型，或者这些模型存在很多相互依赖关系，导致各个模型的上传顺序变得复杂，那么，你可以使用此示例一次上传很多模型：[**Azure 数字孪生模型上传程序**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader)。 按照示例附带的说明，配置并使用此项目将模型上传到你自己的实例中。

### <a name="model-visualizer"></a>模型可视化工具 

**用于将模型可视化**

将模型上传到 Azure 数字孪生实例后，可以使用 [**Azure 数字孪生模型可视化工具**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)查看 Azure 数字孪生实例中的模型，包括任何继承和模型关系。 此示例当前处于草稿状态。 我们鼓励数字孪生开发社区扩展该示例并为其贡献内容。 

## <a name="next-steps"></a>后续步骤

* 了解如何基于行业标准本体创建模型：[概念：什么是本体？](concepts-ontologies.md)

* 深入了解如何通过 API 操作管理模型：[*操作指南：管理 DTDL 模型*](how-to-manage-model.md)

* 了解如何使用模型来创建数字孪生：[*概念：数字孪生和孪生图*](concepts-twins-graph.md)。

