---
title: 数字孪生和孪生图
titleSuffix: Azure Digital Twins
description: 了解数字孪生体的概念，以及它们的关系如何成图。
author: baanders
ms.author: baanders
ms.date: 8/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 42cce83683df789aeaabe53ca170f17319ec3603
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224836"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>了解数字孪生体及其孪生图

在 Azure 数字孪生解决方案中，环境中的实体是由 **数字孪生体** 表示的。 数字孪生体是你自定义的[模型](concepts-models.md)之一的实例。 可以通过 **关系** 将其连接到其他数字孪生体以形成 **孪生图**：此孪生图是整个环境的表示形式。

> [!TIP]
> “Azure 数字孪生”是指作为一个整体的此 Azure 服务。 “数字孪生体”或“孪生体”是指此服务的实例中的单个孪生节点。

## <a name="digital-twins"></a>数字孪生

你需要将模型上传到此服务，然后才能在 Azure 数字孪生实例中创建数字孪生体。 模型描述特定孪生体可以具有的一组属性、遥测消息和关系，等等。 有关在模型中定义的信息的类型，请参阅[自定义模型](concepts-models.md)。

创建并上传模型后，客户端应用就可以创建该类型的实例了。 此实例是一个数字孪生体。 例如，在创建“Floor”的模型后，你可以创建使用此类型的一个或几个数字孪生体（例如，“Floor”类型的一个孪生体称为“GroundFloor”，另一个称为“Floor2”，等等）。

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

## <a name="relationships-a-graph-of-digital-twins"></a>关系：数字孪生体的图

孪生体通过其关系连接成为孪生图。 孪生体可以具有的关系定义为其模型的一部分。  

例如，模型 Floor 可以定义针对 Room 类型的孪生体的“包含”关系。 有了此定义，你就可以通过 Azure 数字孪生创建从任何 Floor 孪生体到任何 Room 孪生体（包括属于 Room 子类型的孪生体）的“包含”关系。 

此过程的结果是一组节点（数字孪生体），它们通过图中的边（它们的关系）连接在一起。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中显示了示例模型和孪生体。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

## <a name="create-with-the-apis"></a>使用 API 进行创建

本部分显示了从客户端应用程序创建数字孪生体和关系时的情况。 它包含使用 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) 的 .NET 代码示例，目的是提供在这些概念的每一个内发生的情况的更多上下文。

### <a name="create-digital-twins"></a>创建数字孪生

下面是客户端代码的一个片段，它使用 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) 来实例化 Room 类型的孪生体，并在实例化过程中定义 `twinId`。

你可以在创建孪生体时对其属性进行初始化，也可以稍后设置这些属性。 若要创建具有已初始化属性的孪生体，请创建一个提供必要初始化值的 JSON 文档。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

你还可以使用名为 `BasicDigitalTwin` 的帮助程序类更直接地将属性字段存储在“孪生体”对象中，这是一种替代字典的方法。 若要详细了解帮助程序类及其用法示例，请查看“如何：管理数字孪生”中的[创建数字孪生](how-to-manage-twin.md#create-a-digital-twin)部分。

>[!NOTE]
>虽然孪生体属性被视为可选属性，因此不需要初始化，但在创建孪生体时，需要设置孪生体上的所有[组件](concepts-models.md#elements-of-a-model)。 它们可以是空对象，但组件本身必须存在。

### <a name="create-relationships"></a>创建关系

下面是一些示例客户端代码，这些代码使用 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) 构建从一个数字孪生体（“源”孪生体）到另一个数字孪生体（“目标”孪生体）的关系。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

## <a name="json-representations-of-graph-elements"></a>图形元素的 JSON 表示形式

数字孪生体数据和关系数据均以 JSON 格式存储，这意味着，当你在 Azure 数字孪生实例中[查询孪生图](how-to-query-graph.md)时，结果将是你已创建的数字孪生体和关系的 JSON 表示形式。

### <a name="digital-twin-json-format"></a>数字孪生体 JSON 格式

当表示为 JSON 对象时，数字孪生体会显示以下字段：

| 字段名 | 说明 |
| --- | --- |
| `$dtId` | 用户提供的字符串，表示数字孪生体的 ID |
| `$etag` | 由 Web 服务器分配的标准 HTTP 字段 |
| `$conformance` | 包含此数字孪生体的一致性状态的枚举（conformant、non-conformant、unknown） |
| `<property-name>` | 属性的值，表示为 JSON 形式（`string`、数字类型或对象） |
| `$relationships` | 关系集合的路径的 URL。 如果数字孪生体没有传出的关系边，则此字段不存在。 |
| `$metadata.$model` | [可选] 将此数字孪生体特征化的模型接口的 ID |
| `$metadata.<property-name>.desiredValue` | [仅适用于可写属性] 指定的属性的所需值 |
| `$metadata.<property-name>.desiredVersion` | [仅适用于可写属性] 所需值的版本 |
| `$metadata.<property-name>.ackVersion` | 实现数字孪生体的设备应用认可的版本 |
| `$metadata.<property-name>.ackCode` | [仅适用于可写属性] 实现数字孪生体的设备应用返回的 `ack` 代码 |
| `$metadata.<property-name>.ackDescription` | [仅适用于可写属性] 实现数字孪生体的设备应用返回的 `ack` 说明 |
| `<component-name>` | 一个 JSON 对象，包含组件的属性值和元数据，类似于根对象的那些属性值和元数据。 即使组件没有属性，此对象也存在。 |
| `<component-name>.<property-name>` | 组件的属性的值，表示为 JSON 形式（`string`、数字类型或对象） |
| `<component-name>.$metadata` | 组件的元数据信息，类似于根级别 `$metadata` |

下面是一个格式设置为 JSON 对象的数字孪生体的示例：

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>关系 JSON 格式

当表示为 JSON 对象时，数字孪生体中的关系会显示以下字段：

| 字段名 | 说明 |
| --- | --- |
| `$relationshipId` | 用户提供的字符串，表示此关系的 ID。 此字符串在源数字孪生体的上下文中是唯一的，这也意味着 `sourceId` + `relationshipId` 在 Azure 数字孪生实例的上下文中是唯一的。 |
| `$etag` | 由 Web 服务器分配的标准 HTTP 字段 |
| `$sourceId` | 源数字孪生体的 ID |
| `$targetId` | 目标数字孪生体的 ID |
| `$relationshipName` | 关系的名称 |
| `<property-name>` | [可选] 此关系的属性的值，表示为 JSON 形式（`string`、数字类型或对象） |

下面是一个格式设置为 JSON 对象的关系的示例：

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>后续步骤

请参阅如何通过 Azure 数字孪生 API 管理图形元素：
* [管理数字孪生](how-to-manage-twin.md)
* [管理孪生图和关系](how-to-manage-graph.md)

或者，了解如何查询 Azure 数字孪生的孪生图来获取相关信息：
* [查询语言](concepts-query-language.md)