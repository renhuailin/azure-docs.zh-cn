---
title: 管理数字孪生
titleSuffix: Azure Digital Twins
description: 了解如何检索、更新和删除各个孪生体和关系。
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b670c244c502049cc9eb419aa6570ad40e5aafa7
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114689934"
---
# <a name="manage-digital-twins"></a>管理数字孪生

环境中的实体由[数字孪生体](concepts-twins-graph.md)表示。 数字孪生体的管理操作可能包括创建、修改和删除。

本文重点介绍如何管理数字孪生体；若要将关系和[孪生图](concepts-twins-graph.md)作为一个整体进行处理，请参阅[管理孪生图和关系](how-to-manage-graph.md)。

> [!TIP]
> 所有 SDK 函数都提供同步和异步版本。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中显示了示例模型和孪生体。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

## <a name="create-a-digital-twin"></a>创建数字孪生体

若要创建孪生体，请在服务客户端上使用 `CreateOrReplaceDigitalTwinAsync()` 方法，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

若要创建数字孪生体，需要提供：
* 所需的数字孪生体的 ID（在此阶段定义）
* 要使用的[模型](concepts-models.md)

（可选）为数字孪生体的所有属性提供初始值。 属性视为可选项，可在以后设置，但在设置后才会显示为孪生体的一部分。

>[!NOTE]
>尽管不需要初始化孪生体的属性，但在创建孪生体时，需要设置孪生体上的所有[组件](concepts-models.md#elements-of-a-model)。 它们可以是空对象，但组件本身必须存在。

模型和任何初始属性值都是通过 `initData` 参数提供的，该参数是一个包含相关数据的 JSON 字符串。 有关构造此对象的详细信息，请转到下一节。

> [!TIP]
> 创建或更新孪生体后，可能会有长达 10 秒的延迟才会在[查询](how-to-query-graph.md)中反映更改。 `GetDigitalTwin` API（见[后文](#get-data-for-a-digital-twin)）不会遇到这种延迟，因此，如果需要即时响应，请使用 API 调用而不是通过查询来查看新创建的孪生体。 

### <a name="initialize-model-and-properties"></a>初始化模型和属性

可在创建孪生体时初始化该孪生体的属性。 

孪生体创建 API 接受序列化为孪生体属性的有效 JSON 说明的对象。 请参阅[数字孪生体和孪生图](concepts-twins-graph.md)，获取孪生体的 JSON 格式的说明。 

首先，可以创建一个数据对象来表示孪生体及其属性数据。 可以手动创建参数对象，也可使用提供的帮助程序类创建参数对象。 下面举例说明每种方法。

#### <a name="create-twins-using-manually-created-data"></a>使用手动创建的数据创建孪生体

如果不使用任何自定义帮助程序类，则可在 `Dictionary<string, object>` 中表示孪生体的属性，其中 `string` 是属性的名称，`object` 是表示属性及其值的对象。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>用帮助程序类创建孪生体

使用帮助程序类 `BasicDigitalTwin` 可直接在“twin”对象中存储属性字段。 你可能希望仍使用 `Dictionary<string, object>` 生成属性列表，然后将属性直接添加到孪生体对象作为其 `CustomProperties`。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` 对象附带了一个 `Id` 字段。 可将此字段保留为空，但如果添加了一个 ID 值，则它需要与传递给 `CreateOrReplaceDigitalTwinAsync()` 调用的 ID 参数匹配。 例如：
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>获取数字孪生体的数据

可通过调用 `GetDigitalTwin()` 方法来访问任何数字孪生体的详细信息，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

此调用以强类型对象类型的形式返回孪生体的数据，如 `BasicDigitalTwin`。 `BasicDigitalTwin` 是 SDK 中随附的序列化帮助程序类，它将以预分析形式返回孪生体的核心元数据和属性。 始终可使用你选择的 JSON 库（如 `System.Text.Json` 或 `Newtonsoft.Json`）反序列化孪生数据。 然而，若要对孪生体进行基本访问，帮助程序类可使此操作更方便。

> [!NOTE]
> `BasicDigitalTwin` 使用 `System.Text.Json` 特性。 若要将 `BasicDigitalTwin` 用于 [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true)，必须使用默认构造函数初始化客户端，或者，如果要自定义序列化程序选项，请使用 [JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true)。

使用 `BasicDigitalTwin` 帮助程序类，还可通过 `Dictionary<string, object>` 访问孪生体上定义的属性。 若要列出孪生体的属性，可使用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

使用 `GetDigitalTwin()` 方法检索孪生体时，仅返回至少已设置一次的属性。

>[!TIP]
>孪生体的 `displayName` 是其模型元数据的一部分，因此在获取孪生体实例的数据时，它将不会显示。 若要查看此值，可[从模型中进行检索](how-to-manage-model.md#retrieve-models)。

若要使用单个 API 调用检索多个孪生体，请参阅[查询孪生图](how-to-query-graph.md)中的查询 API 示例。

请看下面定义月亮的模型（用[数字孪生体定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL) 编写）：

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

对月亮类型的孪生体调用 `object result = await client.GetDigitalTwinAsync("my-moon");` 的结果如下所示：

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

数字孪生体的已定义属性作为数字孪生体的顶级属性返回。 不属于 DTDL 定义的元数据或系统信息返回时将具有 `$` 前缀。 元数据属性包括以下值：
* `$dtId`：此 Azure 数字孪生实例中数字孪生体的 ID
* `$etag`：由 Web 服务器分配的标准 HTTP 字段。 每次更新孪生体时，此值将更新为新值，这对于确定自上次检查后是否已在服务器上更新了孪生体数据非常有用。 可以使用 `If-Match` 执行更新和删除，仅当实体的 etag 与提供的 etag 相匹配时，这两项操作才会完成。 有关这些操作的详细信息，请参阅 [DigitalTwins 更新](/rest/api/digital-twins/dataplane/twins/digitaltwins_update)和 [DigitalTwins 删除](/rest/api/digital-twins/dataplane/twins/digitaltwins_delete)的文档。
* `$metadata`：一组其他属性，包括：
  - 数字孪生体的模型的 DTMI。
  - 每个可写属性的同步状态。 这对设备最为有用，因为服务和设备的状态可能不同（例如当设备离线时）。 目前，此属性仅适用于连接到 IoT 中心的物理设备。 若使用元数据部分中的数据，可了解属性的完整状态以及上次修改的时间戳。 有关同步状态的详细信息，请参阅此 [IoT 中心教程](../iot-hub/tutorial-device-twins.md)，了解如何同步设备状态。
  - 服务特定的元数据，如来自 IoT 中心或 Azure 数字孪生的元数据。 

若要详细了解 `BasicDigitalTwin` 等序列化帮助程序类，可参阅 [Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md#serialization-helpers)。

## <a name="view-all-digital-twins"></a>查看所有数字孪生体

若要查看实例中的所有数字孪生体，请使用[查询](how-to-query-graph.md)。 可使用[查询 API](/rest/api/digital-twins/dataplane/query) 或 [CLI 命令](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)来运行查询。

下方是一个基本查询的正文，它将返回一个包含实例中所有数字孪生体的列表：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>更新数字孪生

若要更新数字孪生体的属性，请以 [JSON 修补程序](http://jsonpatch.com/)格式编写要替换的信息。 通过这种方式，可以一次替换多个属性。 然后将 JSON 修补程序文档传递到 `UpdateDigitalTwin()` 方法中：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

一个修补调用可根据需要更新单个孪生体上的任意数量的属性（甚至所有属性）。 如果需要更新多个孪生体上的属性，则需要对每个孪生体进行单独的更新调用。

> [!TIP]
> 创建或更新孪生体后，可能会有长达 10 秒的延迟才会在[查询](how-to-query-graph.md)中反映更改。 `GetDigitalTwin` API（见[前文](#get-data-for-a-digital-twin)）不会遇到这种延迟，因此，如果需要即时响应，请使用 API 调用而不是通过查询来查看新创建的孪生体。 

下面是 JSON 修补程序代码的示例。 此文档替换其应用到的数字孪生体的 mass 和 radius 属性值 。

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

若要更新对孪生和关系的调用，请使用 [JSON 修补程序](http://jsonpatch.com/)结构。 可使用 Azure .NET SDK 的 [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument?view=azure-dotnet&preserve-view=true) 创建修补程序。 示例如下。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-sub-properties-in-digital-twin-components"></a>更新数字孪生体组件中的子属性

请记住，模型可以包含组件，使其可由其他模型组成。 

若要修补数字孪生体组件中的属性，可在 JSON 修补程序中使用路径语法：

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-sub-properties-in-object-type-properties"></a>更新对象类型属性中的子属性

模型可能包含属于对象类型的属性。 这些对象可能具有自己的属性，并且你可能想要更新属于对象类型属性的其中一个子属性。 此过程类似于[更新组件中的子属性](#update-sub-properties-in-digital-twin-components)的过程，但可能需要执行一些额外的步骤。 

考虑具有对象类型属性 `ObjectProperty` 的模型。 `ObjectProperty` 具有一个名为 `StringSubProperty` 的字符串属性。

使用此模型创建孪生体时，无需实例化 `ObjectProperty`。 如果在创建孪生体的过程中未实例化对象属性，则不会为修补操作创建默认路径来访问 `ObjectProperty` 及其 `StringSubProperty`。 需要先添加 `ObjectProperty` 的路径，然后才能更新其属性。

这可以通过 JSON 修补 `add` 操作来完成，如下所示：

:::code language="json" source="~/digital-twins-docs-samples/models/patch-object-sub-property-1.json":::

>[!NOTE]
> 如果 `ObjectProperty` 具有多个属性，则应将所有属性都包含在此操作的 `value` 字段中，即使只更新一个属性也是如此：
> ```json
>... "value": {"StringSubProperty":"<string-value>", "Property2":"<property2-value>", ...}
>```


完成此操作后，将存在一个 `StringSubProperty` 路径，并且现在可以通过典型的 `replace` 操作直接更新它：

:::code language="json" source="~/digital-twins-docs-samples/models/patch-object-sub-property-2.json":::

虽然在创建孪生体时实例化了 `ObjectProperty` 的情况下不需要第一步，但建议在每次首次更新子属性时使用该步骤，因为你可能并不总是能够确定对象属性最初是否已实例化。

### <a name="update-a-digital-twins-model"></a>更新数字孪生体的模型

`UpdateDigitalTwin()` 函数还可用于将数字孪生体迁移到其他模型。 

例如，请看以下 JSON 修补程序文档，该文档将替换数字孪生体的元数据 `$model` 字段：

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

仅当修补程序修改的数字孪生体符合新模型时，此操作才会成功。 

请看下面的示例：
1. 假设有一个数字孪生体，其模型为 foo_old。 foo_old 定义必需属性 mass。
2. 新模型 foo_new 定义属性 mass 并添加新的必需属性 temperature。
3. 修补后，数字孪生体必须同时具有 mass 和 temperature 属性。 

这种情况的修补程序需要同时更新模型和孪生体的 temperature 属性，如下所示：

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>处理冲突的更新调用

Azure 数字孪生确保所有传入请求都会一个接一个地得到处理。 这意味着即使多个函数尝试同时更新一个孪生体的相同属性，也无需编写显式锁定代码来处理冲突。

此行为按孪生体发生。 

例如，假设这三个调用同时到达： 
*   在 Twin1 上写入属性 A
*   在 Twin1 上写入属性 B
*   在 Twin2 上写入属性 A

修改 Twin1 的两个调用相继执行，并为每个更改生成更改消息。 修改 Twin2 的调用在到达后，在没有冲突的情况下并发执行。

## <a name="delete-a-digital-twin"></a>删除数字孪生体

可使用 `DeleteDigitalTwin()` 方法删除孪生体。 但是，只能在孪生体没有关系时将其删除。 因此，请先删除孪生体的传入和传出关系。

下面是用于删除孪生体及其关系的代码示例。 `DeleteDigitalTwin` SDK 调用将突出显示，明确其在较为广泛的示例上下文中的位置。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>删除所有数字孪生体

有关如何一次删除所有孪生体的示例，请下载[使用示例客户端应用了解基础知识](tutorial-command-line-app.md)中使用的示例应用。 CommandLoop.cs 文件在 `CommandDeleteAllTwins()` 函数中执行此操作。

## <a name="runnable-digital-twin-code-sample"></a>可运行的数字孪生体代码示例

可使用下面的可运行代码示例创建一个孪生体，更新其详细信息，然后删除孪生体。 

### <a name="set-up-sample-project-files"></a>设置示例项目文件

代码片段使用示例模型定义 [Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Room.json)。 若要下载模型文件以便可以在代码中使用，请使用此链接直接转到 GitHub 中的该文件。 然后，右键单击屏幕上的任意位置，在浏览器的右击菜单中选择“另存为”，并使用“另存为”窗口将文件另存为 Room.json 。

接下来，在 Visual Studio 或所选的编辑器中创建新的控制台应用项目。

然后，将可运行示例的以下代码复制到你的项目中：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

### <a name="configure-project"></a>配置项目

接下来，完成以下步骤来配置项目代码：
1. 将之前下载的 Room.json 文件添加到项目中，并替换代码中的 `<path-to>` 占位符，以告知程序可在何处找到它。
2. 将占位符 `<your-instance-hostname>` 替换为你的 Azure 数字孪生实例的主机名。
3. 将两个依赖项添加到你的项目，这是与 Azure 数字孪生结合使用所必需的。 第一个是[适用于 .NET 的 Azure 数字孪生 SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 的包，第二个提供工具来帮助向 Azure 进行身份验证。

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

如果要直接运行该示例，还需要设置本地凭据。 下一节将详细介绍这一点。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>运行示例

现已完成设置，可以运行示例代码项目。

下面是上述程序的控制台输出： 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="显示已创建、更新和删除孪生体的控制台输出的屏幕截图。" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>后续步骤

了解如何创建和管理数字孪生体之间的关系：
* [管理孪生图和关系](how-to-manage-graph.md)