---
title: 通过使用关系来管理孪生图
titleSuffix: Azure Digital Twins
description: 请参阅如何通过将数字孪生与关系连接起来来管理该图形。
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bc548d4cc728611387b36451d563be6ca0e21530
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388186"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>使用关系管理数字孪生图

Azure 数字孪生的核心是代表您的整个环境的克隆 [图](concepts-twins-graph.md) 。 "克隆" 图由通过 **关系** 连接的单个数字孪生组成。 

一旦拥有工作 [Azure 数字孪生实例](how-to-set-up-instance-portal.md) ，并在客户端应用程序中设置了 [身份验证](how-to-authenticate-client.md) 代码，就可以使用 [**DigitalTwins api**](/rest/api/digital-twins/dataplane/twins) 来创建、修改和删除 Azure 数字孪生实例中的数字孪生和它们之间的关系。 你还可以使用 [.net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)或 [AZURE 数字孪生 CLI](how-to-use-cli.md)。

本文重点介绍如何作为一个整体来管理关系和图形;若要使用单独的数字孪生，请参阅 [*操作方法：管理数字孪生*](how-to-manage-twin.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>图形管理方法

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

你还可以使用 Azure 数字孪生 (ADT) 资源管理器示例对图形进行更改，这将允许你可视化孪生和 graph，并在幕后使用 SDK。 下一部分将详细介绍此示例。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>创建关系

关系描述不同数字孪生彼此连接的方式，它们构成了双子图形的基础。

关系是使用调用创建的 `CreateOrReplaceRelationshipAsync()` 。 

若要创建关系，需要指定：
* `srcId`下面的代码示例中 (源克隆 id) ：该关系源自的位置的 id。
* `targetId`下面的代码示例中 (目标克隆 id) ：关系到达的位置的 id。
* `relName`下面的代码示例中 (关系名称) ：关系的泛型类型，如 _contains_。
* `relId`下面的代码示例中 (关系 ID) ：此关系的特定名称，类似于 _Relationship1_。

关系 ID 在给定的源克隆中必须是唯一的。 它不需要是全局唯一的。
例如，对于双子 *foo*，每个特定的关系 ID 都必须是唯一的。 但是，另一个克隆 *栏* 可以具有与 *foo* 关系的相同 ID 匹配的传出关系。

下面的代码示例演示如何在 Azure 数字孪生实例中创建关系。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod":::

在 main 方法中，你现在可以调用 `CreateRelationship()` 函数来创建 _包含_ 如下的关系： 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

如果希望创建多个关系，可以重复调用同一方法，将不同的关系类型传递到参数。 

有关 helper 类的详细信息 `BasicRelationship` ，请参阅 how [*To： Use The Azure 数字孪生 Api and sdk*](how-to-use-apis-sdks.md#serialization-helpers)。

### <a name="create-multiple-relationships-between-twins"></a>在孪生之间创建多个关系

关系可以分类为： 

* 传出关系：属于此克隆的关系，指向外部，以将其连接到其他孪生。 `GetRelationshipsAsync()`方法用于获取克隆的传出关系。
* 传入关系：属于其他孪生的关系，这些关系指向此克隆以创建 "传入" 链接。 `GetIncomingRelationshipsAsync()`方法用于获取克隆的传入关系。

对于在两个孪生之间可以具有的关系数没有限制，可以根据需要在孪生之间建立任意多的关系。 

这意味着，可以一次在两个孪生之间表示多个不同类型的关系。 例如，克隆 *A* 可以具有 *存储* 的 *关系和与**双子*

如果需要，您甚至可以在相同的两个孪生之间创建同一类型关系的多个实例。 在此示例中 *，只要* 关系具有不同的关系 id，就可以有两个不同的 *存储* 关系与 *双子 B*。

## <a name="list-relationships"></a>列出关系

若要访问图形中给定的克隆的 **传出** 关系列表，可以使用如下所示的 `GetRelationships()` 方法：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

这会返回 `Azure.Pageable<T>` 或 `Azure.AsyncPageable<T>` ，具体取决于你使用的是调用的同步还是异步版本。

下面是一个检索关系列表的示例：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

你现在可以调用此方法来查看孪生的传出关系，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

您可以使用检索到的关系导航到图形中的其他孪生。 为此，请 `target` 从返回的关系中读取字段，并将其用作对的下一次调用的 ID `GetDigitalTwin()` 。

### <a name="find-incoming-relationships-to-a-digital-twin"></a>查找到数字克隆的传入关系

Azure 数字孪生还提供了一个 API，用于查找到给定克隆的所有 **传入** 关系。 这通常适用于反向导航或删除克隆。

前面的代码示例重点介绍了从一次克隆查找传出关系的情况。 下面的示例在结构上类似，但却找到了到双子的 *传入* 关系。

请注意， `IncomingRelationship` 调用不返回关系的完整正文。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod":::

你现在可以调用此方法来查看孪生的传入关系，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>列出所有克隆的属性和关系

使用上述方法列出与克隆的传出和传入关系，你可以创建一种方法来打印完全不完整的信息，包括克隆的属性以及这两种类型的关系。 下面是一个名为的示例方法 `FetchAndPrintTwinAsync()` ，其中演示了如何执行此操作。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

你现在可以在 main 方法中调用此函数，如下所示： 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>更新关系

使用方法更新关系 `UpdateRelationship` 。 

>[!NOTE]
>此方法用于更新关系的 **属性** 。 如果需要更改关系的源克隆或目标克隆，则需要 [删除该关系](#delete-relationships) ，并使用新的孪生 [重新创建它](#create-relationships) 。

客户端调用所需的参数是 (源的源的 ID) ，要更新的关系的 ID 和包含要更新的属性和新值的 [JSON 修补](http://jsonpatch.com/) 文档。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod":::

下面是调用此方法的一个示例，其中传递了包含用于更新属性的信息的 JSON 修补文档。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>删除关系

第一个参数指定源的源位置 (关系源自的位置) 。 另一个参数是关系 ID。 由于关系 Id 只在克隆的作用域内是唯一的，因此您需要同时使用的 ID 和关系 ID。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod":::

你现在可以调用此方法删除关系，如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>可运行的克隆图形示例

以下可运行代码片段使用本文中的关系操作来创建数字孪生和关系的克隆图形。

### <a name="set-up-the-runnable-sample"></a>设置可运行示例

此代码片段使用教程中的模型定义 [*上的Room.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) 和 [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) [*：使用示例客户端应用浏览 Azure 数字孪生*](tutorial-command-line-app.md)。 你可以使用这些链接直接前往文件，或将其作为完整的端到端 [示例项目的](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)一部分进行下载。 

在运行该示例之前，请执行以下操作：
1. 下载模型文件，将其放在您的项目中，并替换 `<path-to>` 以下代码中的占位符，告诉您的程序在何处查找它们。
2. 将占位符替换 `<your-instance-hostname>` 为你的 Azure 数字孪生实例的主机名。
3. 向项目添加两个依赖于 Azure 数字孪生所需的依赖项。 第一个是[适用于 .NET 的 Azure 数字孪生 SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 的包，第二个提供工具来帮助向 Azure 进行身份验证。

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

如果要直接运行该示例，还需要设置本地凭据。 下一节将对此进行演练。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>运行示例

完成上述步骤后，可以直接运行以下示例代码。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

下面是上述程序的控制台输出： 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="显示孪生的克隆详细信息、传入和传出关系的控制台输出。" lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> 克隆图是在孪生之间创建关系的概念。 若要查看双子图形的可视化表示形式，请参阅本文的 " [*可视化*](how-to-manage-graph.md#visualization) " 部分。 

## <a name="create-graph-from-a-csv-file"></a>从 CSV 文件创建关系图

在实际使用案例中，会经常从存储在不同数据库中的数据创建克隆层次结构，也可以在电子表格或 CSV 文件中创建克隆层次结构。 本部分说明如何从 CSV 文件中读取数据并从中创建克隆图形。

请考虑以下数据表，其中描述一组数字孪生和关系。

|  模型 ID    | 克隆 ID (必须唯一)  | 关系名  | 目标克隆 ID  | 克隆初始化数据 |
| --- | --- | --- | --- | --- |
| dtmi：示例：楼层; 1    | 楼层 1 | 包含 | Room1 | |
| dtmi：示例：楼层; 1    | 楼层 0 | 包含 | Room0 | |
| dtmi：示例：房间; 1    | Room1 | | | {"温度"： 80} |
| dtmi：示例：房间; 1    | Room0 | | | {"温度"： 70} |

若要将此数据导入到 Azure 数字孪生，一种方法是将表转换为 CSV 文件，然后编写代码将文件解释为命令，以创建孪生和关系。 下面的代码示例演示如何从 CSV 文件中读取数据并在 Azure 数字孪生中创建一个克隆图形。

在下面的代码中，CSV 文件 *data.csv* 调用，并且存在一个占位符，表示 Azure 数字孪生实例的 **主机名** 。 该示例还利用了多个包，你可以将这些包添加到项目中来帮助执行此过程。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>后续步骤

了解如何查询 Azure 数字孪生克隆图形：
* [*概念：查询语言*](concepts-query-language.md)
* [*操作方法：查询双子图形*](how-to-query-graph.md)