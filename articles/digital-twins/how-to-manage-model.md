---
title: 管理 DTDL 模型
titleSuffix: Azure Digital Twins
description: 了解如何在 Azure 数字孪生中创建、编辑和删除模型。
author: baanders
ms.author: baanders
ms.date: 4/07/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3c896e2aad7dae8d03b26e2a16ecb96224ab547b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303744"
---
# <a name="manage-azure-digital-twins-models"></a>管理 Azure 数字孪生模型

可使用 [DigitalTwinModels API](/rest/api/digital-twins/dataplane/models)、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) 或 [Azure 数字孪生 CLI](how-to-use-cli.md)来管理 Azure 数字孪生实例的[模型](concepts-models.md)。 

管理操作包括上传、验证、检索和删除模型。 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-models"></a>管理模型的方法

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-models"></a>创建模型

Azure 数字孪生的模型用 DTDL 编写，并保存为 .json 文件。 还有一个可用于 [Visual Studio Code](https://code.visualstudio.com/) 的 [DTDL 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)，该扩展提供语法验证和辅助编写 DTDL 文档的其他功能。

假设一家医院希望以数字方式表示房间。 每个房间都包含一个用于监视洗手情况的智能给皂器，以及用于监视房间人流量的传感器。

解决方案的第一步是创建模型来表示医院的各个方面。 此方案中的病房可以这样描述：

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> 这是一个 .json 文件的示例正文，其中定义并保存了模型，并且该模型作为客户端项目的一部分上传。 另一方面，REST API 调用采用类似于上面的模型定义数组（该数组在 .NET SDK 中映射到 `IEnumerable<string>`）。 因此，若要直接在 REST API 中使用此模型，请用括号括起来。

此模型定义了病房的名称和唯一 ID，并定义了表示访客人数和洗手状态的属性（这些计数器将根据运动传感器和智能给皂器进行更新，并将一起用于计算 handwash percentage 属性）。 该模型还定义了一个关系 hasDevices，用于将基于此 Room 模型的任何[数字孪生体](concepts-twins-graph.md)连接到实际设备 。

按照此方法，可继续为医院的病房、区域或医院本身定义模型。

### <a name="validate-syntax"></a>验证语法

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>上传模型

创建模型后，可将它们上传到 Azure 数字孪生实例。

准备好上传模型时，可使用以下代码片段：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

请注意，`CreateModels` 方法在单个事务中可接受多个文件。 下面用一个示例来说明：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

模型文件可以包含多个模型。 在这种情况下，需要以 JSON 数组形式放置模型。 例如：

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

上传时，模型文件由服务进行验证。

## <a name="retrieve-models"></a>检索模型

可列出和检索存储在 Azure 数字孪生实例上的模型。 

下面是可进行的操作：
* 检索单个模型
* 检索所有模型
* 检索模型的元数据和依赖项

下面是一些示例调用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

用于检索模型的 API 调用全都返回 `DigitalTwinsModelData` 对象。 `DigitalTwinsModelData` 包含有关存储在 Azure 数字孪生实例中的模型的元数据，例如模型的名称、DTMI 和创建日期。 `DigitalTwinsModelData` 对象也可以选择包含模型本身。 因此，根据参数，你可以使用检索调用仅检索元数据（在你希望显示可用工具的 UI 列表等情况下，这很有用），也可以检索整个模型。

`RetrieveModelWithDependencies` 调用不仅返回所请求的模型，还返回请求的模型所依赖的所有模型。

模型不一定完全按照上传时的文档形式返回。 Azure 数字孪生仅保证返回的形式在语义上是等效的。 

## <a name="update-models"></a>更新模型

模型上传到 Azure 数字孪生实例后，整个模型界面不可变。 这意味着不能对模型进行传统的“编辑”。 Azure 数字孪生也不允许重新上传同一模型。

但如果你要更改模型（例如更新 `displayName` 或 `description`），可以通过上传新版本的模型来实现。 

### <a name="model-versioning"></a>模型版本控制

若要创建现有模型的新版本，请从原始模型的 DTDL 开始。 更新、添加或删除要更改的字段。

接下来，更新模型的 `id` 字段，将其标记为模型的新版本。 模型 ID 的最后一个部分（`;` 后面的部分）表示模型编号。 若要指示这目前是此模型的最新版本，请将 `id` 值末尾的数字递增为大于当前版本号的任意数字。

例如，如果之前的模型 ID 如下所示：

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

此模型的版本 2 可能如下所示：

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

然后，将新版本的模型上传到实例。 

此版本的模型随后将在实例中提供，用于数字孪生体。 它不会覆盖早期版本的模型，因此在[删除早期版本](#remove-models)之前，模型的多个版本将在实例中共存。

### <a name="impact-on-twins"></a>对孪生体的影响

创建新的孪生体时，由于新模型版本和旧模型版本共存，因此新孪生体可使用新版本的模型，也可使用旧版本的模型。

这也意味着上传新版本的模型不会自动影响现有孪生体。 现有孪生体将只保留旧模型版本的实例。

通过修补现有孪生体可以将这些孪生体更新为新模型版本，如“操作指南：管理数字孪生体”的[更新数字孪生体的模型](how-to-manage-twin.md#update-a-digital-twins-model)部分所述 。 在同一修补程序中，必须同时更新模型 ID（更新为新版本）和孪生体上必须更改的任何字段以使其符合新模型 。

## <a name="remove-models"></a>删除模型

还可通过以下两种方式之一从服务中删除模型：
* **停止使用**：模型停止使用后，就不能再使用它来创建新的数字孪生体。 已使用此模型的现有数字孪生体不会受到影响，因此你仍可通过属性更改、添加或删除关系等操作对孪生体进行更新。
* **删除**：这将从解决方案中完全删除该模型。 使用此模型的任何孪生体不再与任何有效模型关联，因此它们被视为根本没有模型。 你仍可读取这些孪生体，但在将它们重新分配到另一个模型之前，不能对它们进行任何更新。

这些功能是独立的，尽管它们可以一起使用来逐步删除模型，但它们不会相互影响。 

### <a name="decommissioning"></a>停止使用

下面是停止使用模型的代码：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

模型的停止使用状态包含在模型检索 API 返回的 `ModelData` 记录中。

### <a name="deletion"></a>删除

可一次删除实例中的所有模型，也可逐个删除。

有关如何删除所有模型的示例，请下载[教程：使用示例客户端应用了解基础知识](tutorial-command-line-app.md)中使用的示例应用。 CommandLoop.cs 文件在 `CommandDeleteAllModels` 函数中执行此操作。

本节的其余部分将更详细地说明模型的删除，并演示如何针对单个模型执行此操作。

#### <a name="before-deletion-deletion-requirements"></a>删除前：删除的要求

通常，可随时删除模型。

但有一个例外情况，如果有其他模型依赖该模型（无论是通过 `extends` 关系还是以组件形式），则该模型不可删除。 例如，如果 ConferenceRoom 模型扩展了 Room 模型，并拥有组件形式的 ACUnit 模型，则在 ConferenceRoom 删除这些相应引用之前，不能删除 Room 或 ACUnit     。 

若要删除，可更新依赖的模型以删除依赖项，或者完全删除依赖的模型。

#### <a name="during-deletion-deletion-process"></a>删除期间：删除过程

即使模型满足立即删除的要求，你也可能需要先执行几个步骤，以避免留下的孪生体出现意外后果。 下面是可帮助你管理该过程的一些步骤：
1. 首先，停止使用该模型
2. 等待几分钟，确保服务已处理完最后发送的孪生体创建请求再停止使用
3. 按模型查询孪生体，查看正在使用现已停用的模型的所有孪生体
4. 如果不再需要这些孪生体，请将它们删除，或根据需要将它们修补为新模型。 还可选择不管它们，在这种情况下，在删除该模型后，这些模型就会变为无模型的孪生体。 有关此状态的含义，请参阅下一节。
5. 等待几分钟，确保更改已完成
6. 删除模型 

若要删除模型，请使用此调用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

#### <a name="after-deletion-twins-without-models"></a>删除后：无模型的孪生体

删除某一模型后，使用该模型的任何数字孪生体现在都被视为无模型。 请注意，没有任何查询可为你提供此状态的所有孪生体的列表，但你仍可通过已删除的模型来查询孪生体，以了解哪些孪生体受到影响。

下面概述了在孪生体无模型的情况下可执行和不可执行的操作。

可执行的操作：
* 查询孪生体
* 读取属性
* 读取传出关系
* 添加和删除传入关系（例如，其他孪生体仍可与此孪生体建立关系）
  - 关系定义中的 `target` 仍可反映已删除模型的 DTMI。 未定义目标的关系在此处也适用。      
* 删除关系
* 删除孪生体

不可执行的操作：
* 编辑传出关系（例如，此孪生体与其他孪生体建立的关系）
* 编辑属性

#### <a name="after-deletion-re-uploading-a-model"></a>删除后：重新上传模型

删除某一模型后，可决定稍后上传与删除的模型具有相同 ID 的新模型。 下面是在这种情况下会发生的事项。
* 从解决方案商店的角度来看，这等同于上传一个全新的模型。 该服务不记得这个旧模型曾经上传过。   
* 如果图中有任何引用已删除模型的剩余孪生体，则它们将不再是孤立的；此模型 ID 对于新定义仍然有效。 但是，如果模型的新定义不同于已删除的模型定义，则这些孪生体的属性和关系可能与已删除的定义相匹配，并且对于新定义是无效的。

Azure 数字孪生不会阻止此状态，因此请小心适当地修补孪生体，以确保它们通过模型定义切换后仍然有效。

## <a name="next-steps"></a>后续步骤

请参阅如何基于模型创建和管理数字孪生体：
* [操作指南：管理数字孪生体](how-to-manage-twin.md)