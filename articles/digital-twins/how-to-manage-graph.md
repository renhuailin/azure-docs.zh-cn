---
title: 管理孪生图和关系
titleSuffix: Azure Digital Twins
description: 了解如何使用关系将数字孪生体连接起来以管理数字孪生图。
author: baanders
ms.author: baanders
ms.date: 9/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5c02ad72ace0568f7be1062993adaf154bb31cbf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624304"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>使用关系管理数字孪生图

Azure 数字孪生的核心是代表整个环境的[孪生图](concepts-twins-graph.md)。 孪生图由通过关系连接的各个数字孪生组成。 

拥有有效的 [Azure 数字孪生实例](how-to-set-up-instance-portal.md)并在客户端应用中设置了[验证](how-to-authenticate-client.md)码后，就可在 Azure 数字孪生实例中创建、修改和删除数字孪生体及其关系。

本文重点介绍如何将关系和图作为一个整体进行管理；若要单独处理数字孪生体，请参阅[管理数字孪生体](how-to-manage-twin.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中显示了示例模型和孪生体。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::


## <a name="create-relationships"></a>创建关系

关系可描述不同数字孪生体是如何相互连接的（这是构成孪生图的基础）。

关系是使用 `CreateOrReplaceRelationshipAsync()` 调用创建的。 

若要创建关系，需要指定：
* 源孪生体 ID（以下代码示例中为 `srcId`）：关系起源的孪生体的 ID。
* 目标孪生体 ID（以下代码示例中为 `targetId`）：关系到达的孪生体的 ID。
* 关系名称（以下代码示例中为 `relName`）：关系的泛型类型，如 contains。
* 关系 ID（以下代码示例中为 `relId`）：此关系的特定名称，如 Relationship1。

关系 ID 在给定的源孪生体中必须是唯一的。 无需全局唯一。
例如，对于孪生体 Foo，每个特定的关系 ID 必须是唯一的。 但是，另一个孪生体 Bar 可以具有与 Foo 关系的相同 ID 匹配的传出关系。

下面的代码示例演示如何在 Azure 数字孪生实例中创建关系。 在可能出现在较大程序上下文中的自定义方法中，它使用 SDK 调用（已突出显示）。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

现在可调用此自定义函数来创建 contains 关系，方法如下： 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

如果希望创建多个关系，可重复调用同一方法，将不同的关系类型传递到参数。 

有关帮助程序类 `BasicRelationship` 的详细信息，请参阅 [Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md#serialization-helpers)。

### <a name="create-multiple-relationships-between-twins"></a>在孪生体之间创建多种关系

关系可以分为以下两类： 

* 传出关系：属于此孪生体的关系，此类关系指向外部以将孪生体与其他孪生体相连接。 `GetRelationshipsAsync()` 方法用于获取孪生体的传出关系。
* 传入关系：属于其他孪生体的关系，此类关系指向此孪生体以创建“传入”链接。 `GetIncomingRelationshipsAsync()` 方法用于获取孪生体的传入关系。

两个孪生体间的关系数量没有限制，你可根据需要在孪生体之间建立任意数量的关系。 

这意味着，可一次表达两个孪生体间的多种关系。 例如，孪生体 A 可与孪生体 B 既有 stored 关系又有 manufactured 关系 。

如果需要，甚至可在相同的两个孪生体之间创建具有同一种关系的多个实例。 在此示例中，孪生体 A 可与孪生体 B 具有两种不同的 stored 关系，只要这两种关系具有不同的关系 ID 即可。

## <a name="list-relationships"></a>列出关系

### <a name="list-properties-of-a-single-relationship"></a>列出单个关系的属性

始终可将关系数据反序列化为你选择的类型。 若要对关系进行基本访问，请使用类型 `BasicRelationship`。 使用 `BasicRelationship` 帮助程序类，还可通过 `IDictionary<string, object>` 访问关系上定义的属性。 若要列出属性，可使用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

### <a name="list-outgoing-relationships-from-a-digital-twin"></a>列出数字孪生体中传出的关系

若要访问图中给定孪生体的传出关系列表，可使用如下所示的 `GetRelationships()` 方法： 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

此方法会返回 `Azure.Pageable<T>` 或 `Azure.AsyncPageable<T>`，具体取决于你使用的是同步还是异步版本的调用。

以下为检索关系列表的示例。 在可能出现在较大程序上下文中的自定义方法中，它使用 SDK 调用（已突出显示）。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

现在可调用此自定义方法来查看孪生体的传出关系，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

可以使用检索到的关系导航到图中的其他孪生体，方法如下：从返回的关系中读取 `target` 字段，并将它用作下次调用 `GetDigitalTwin()` 的 ID。

### <a name="list-incoming-relationships-to-a-digital-twin"></a>列出传入数字孪生体的关系

Azure 数字孪生还提供了一个 SDK 调用，用于查找传入到给定孪生体的所有关系。 此 SDK 在反向导航或删除孪生体时通常很有用。

>[!NOTE]
> `IncomingRelationship` 调用不返回关系的完整正文。 有关 `IncomingRelationship` 类的更多信息，请参阅其[参考文档](/dotnet/api/azure.digitaltwins.core.incomingrelationship?view=azure-dotnet&preserve-view=true)。

上一节的代码示例重点介绍了如何查找孪生体的传出关系。 以下示例的结构类似，但用于查找孪生体的传入关系。 在可能出现在较大程序上下文中的自定义方法中，此示例还使用 SDK 调用（已突出显示）。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

现在可调用此自定义方法来查看孪生体的传入关系，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>列出所有的孪生体属性和关系

通过使用上述方法列出孪生体的传出和传入关系，可以创建一种方法来输出完整的孪生体信息，包括孪生体的属性以及这两种关系。 下面是一个自定义方法示例，展现了如何组合上述自定义方法来实现此目的。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

现在可调用此自定义函数，如下所示： 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>更新关系

使用 `UpdateRelationship` 方法更新关系。 

>[!NOTE]
>此方法用于更新关系的属性。 如果需要更改关系的源孪生体或目标孪生体，则需要[删除该关系](#delete-relationships)，然后使用新的孪生体[重新创建关系](#create-relationships)。

客户端调用所需的参数包括：
- 源孪生体（关系起源的孪生体）的 ID。
- 要更新的关系的 ID。
- 包含要更新的属性和新值的 [JSON 修补程序](http://jsonpatch.com/)文档。

下面是展现如何使用此方法的示例代码片段。 在可能出现在较大程序上下文中的自定义方法中，此示例使用 SDK 调用（已突出显示）。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

以下示例展现了如何调用此自定义方法，传入包含更新属性所需信息的 JSON 修补程序文档。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>删除关系

第一个参数指定源孪生体（关系起源的孪生体）。 另一个参数是关系 ID。 同时需要孪生体 ID 和关系 ID，因为关系 ID 只在孪生体范围内唯一。

下面是展现如何使用此方法的示例代码。 在可能出现在较大程序上下文中的自定义方法中，此示例使用 SDK 调用（已突出显示）。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

现在可调用此自定义方法来删除关系，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="create-graph-from-a-csv-file"></a>根据 CSV 文件创建关系图

在实际用例中，通常会根据其他数据库中存储的数据创建孪生体层次结构，也可能根据电子表格或 CSV 文件中的数据创建。 本部分说明如何从 CSV 文件中读取数据并根据这些数据创建孪生图。

请看以下数据表，其中描述了一组数字孪生体和关系。

|  模型 ID    | 孪生体 ID（必须唯一） | 关系名  | 目标孪生体 ID  | 孪生体初始化数据 |
| --- | --- | --- | --- | --- |
| dtmi:example:Floor;1    | 楼层 1 | 包含 | Room1 | |
| dtmi:example:Floor;1    | 楼层 0 | 包含 | Room0 | |
| dtmi:example:Room;1    | Room1 | | | {"Temperature": 80} |
| dtmi:example:Room;1    | Room0 | | | {"Temperature": 70} |

若要将此数据导入到 Azure 数字孪生，可以将表转换为 CSV 文件。 转换表后，可以编写代码来将文件解释为命令以创建孪生体和关系。 下面的代码示例演示如何从 CSV 文件读取数据并在 Azure 数字孪生中创建孪生图。

在下面的代码中，CSV 文件名为 data.csv，并且还有一个占位符，表示 Azure 数字孪生实例的主机名。 该示例还利用了多个包，你可将这些包添加到项目中来帮助完成此过程。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="runnable-twin-graph-sample"></a>可运行的孪生图示例

以下可运行代码片段使用本文中的关系操作，根据数字孪生体和关系创建孪生图。

### <a name="set-up-sample-project-files"></a>设置示例项目文件

该代码片段使用两个示例模型定义，[Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Room.json) 和 [Floor.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Floor.json)。 若要下载模型文件以便可在代码中使用，请使用以下链接直接转到 GitHub 中的这些文件。 然后，右键单击屏幕上的任意位置，在浏览器的右击菜单中选择“另存为”，并使用“另存为”窗口将文件另存为 Room.json 和 Floor.json  。

接下来，在 Visual Studio 或所选的编辑器中创建新的控制台应用项目。

然后，将可运行示例的以下代码复制到你的项目中：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

### <a name="configure-project"></a>配置项目

接下来，完成以下步骤来配置项目代码：
1. 将之前下载的 Room.json 和 Floor.json 文件添加到项目中，并替换代码中的 `<path-to>` 占位符，以告知程序可在何处找到它们 。
1. 将占位符 `<your-instance-hostname>` 替换为你的 Azure 数字孪生实例的主机名。
1. 将两个依赖项添加到你的项目，这是与 Azure 数字孪生结合使用所必需的。 第一个是[适用于 .NET 的 Azure 数字孪生 SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 的包，第二个提供工具来帮助向 Azure 进行身份验证。

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

如果要直接运行该示例，还需要设置本地凭据。 下一节将详细介绍这一过程。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>运行示例

现已完成设置，可以运行示例代码项目。

下面是该程序的控制台输出： 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="控制台输出的屏幕截图，其中显示孪生体的详细信息及其传入和传出关系。" lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> 孪生图是在孪生体之间创建关系的概念。 若要查看孪生图的可视化表示形式，请参阅本文的可视化效果部分。 

## <a name="next-steps"></a>后续步骤

了解如何查询 Azure 数字孪生孪生图：
* [查询语言](concepts-query-language.md)
* [查询孪生图](how-to-query-graph.md)