---
title: 管理 DTDL 模型
titleSuffix: Azure Digital Twins
description: 了解如何在 Azure 数字孪生中创建、编辑和删除模型。
author: baanders
ms.author: baanders
ms.date: 8/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c5614ce350a4690aa49268e8598bfc9eb2996d6a
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253196"
---
# <a name="manage-azure-digital-twins-models"></a>管理 Azure 数字孪生模型

本文介绍如何管理 Azure 数字孪生实例中的[模型](concepts-models.md)。 管理操作包括上传、验证、检索和删除模型。 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中显示了一张示例模型图。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

## <a name="create-models"></a>创建模型

Azure 数字孪生的模型用 DTDL 编写，并保存为 .json 文件。 还有一个可用于 [Visual Studio Code](https://code.visualstudio.com/) 的 [DTDL 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)，该扩展提供语法验证和便于编写 DTDL 文档的其他功能。

假设一家医院希望以数字方式表示房间。 每个房间都包含一个用于监视洗手情况的智能给皂器，以及用于监视房间人流量的传感器。

解决方案的第一步是创建模型来表示医院的各个方面。 此方案中的病房可以这样描述：

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> 这是一个 .json 文件的示例正文，其中定义并保存了模型，并且该模型作为客户端项目的一部分上传。 另一方面，REST API 调用采用类似于上面的模型定义数组（该数组在 .NET SDK 中映射到 `IEnumerable<string>`）。 因此，若要直接在 REST API 中使用此模型，请用括号括起来。

此模型定义了病房的名称和唯一 ID，并定义了表示访客人数和洗手状态的属性。 这些计数器将根据运动传感器和智能给皂器进行更新，并将一起用于计算“handwash percentage”属性。 该模型还定义了一个关系 hasDevices，用于将基于此 Room 模型的任何[数字孪生体](concepts-twins-graph.md)连接到实际设备。

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

您的选择包括：
* 检索单个模型
* 检索所有模型
* 检索模型的元数据和依赖项

下面是一些示例调用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

用于检索模型的 SDK 调用全都返回 `DigitalTwinsModelData` 对象。 `DigitalTwinsModelData` 包含有关存储在 Azure 数字孪生实例中的模型的元数据，例如模型的名称、DTMI 和创建日期。 `DigitalTwinsModelData` 对象也可以选择包含模型本身。 这意味着，根据参数，可以使用检索调用仅检索元数据（在希望显示可用工具的 UI 列表等情况下，这很有用），也可以检索整个模型。

`RetrieveModelWithDependencies` 调用不仅返回所请求的模型，还返回请求的模型所依赖的所有模型。

模型不一定完全按照上传时的文档形式返回。 Azure 数字孪生仅保证返回的形式在语义上是等效的。 

## <a name="update-models"></a>更新模型

本节介绍更新模型的注意事项和策略。

### <a name="before-updating-think-in-the-context-of-your-entire-solution"></a>更新前：考虑整个解决方案的上下文

在更新模型之前，建议从整体上考虑整个解决方案以及即将进行的模型更改的影响。 Azure 数字孪生解决方案中的模型通常是互连的，因此，必须注意级联更改，其中更新一个模型需要更新其他多个模型。 更新模型会影响使用模型的孪生体，还可能会影响流入量和处理代码、客户端应用程序和自动化报表。

下面是有助于顺利管理模型转换的一些建议：
* 请考虑在适当的时候改进整个模型集，使模型及其关系一起保持最新，而不是考虑单个模型。
* 将模型视为源代码，在源代码管理中管理它们。 对模型和模型更改应用与解决方案中应用于其他代码相同的严格性和关注。

准备好继续更新模型后，本节的其余部分将介绍可用于实现更新的策略。

### <a name="strategies-for-updating-models"></a>更新模型的策略

模型上传到 Azure 数字孪生实例后，模型界面不可变，这意味着没有传统的模型“编辑”。 实例中已存在匹配的模型时，Azure 数字孪生也不允许重新上传完全相同的模型。

但如果要更改模型（例如更新 `displayName` 或 `description`，或者添加和删除属性），则需要替换原始模型。

替换模型时，有两种策略可供选择：
* [策略 1：上传新模型版本](#strategy-1-upload-new-model-version)：上传具有新版本号的模型，并更新孪生体以使用该新模型。 新模型版本和旧模型版本的将同时存在于实例中，直至删除其中一个。
    - 如果只想更新使用该模型的一些孪生体，或者希望确保孪生体符合其模型并可通过模型转换进行写入，**请使用此策略**。
* [策略 2：删除旧模型并重新上传](#strategy-2-delete-old-model-and-reupload)：删除原始模型，并上传具有相同名称和 ID 的新模型（DTMI 值）替换。 使用新模型完全替换旧模型。 
    - 如果想同时更新使用此模型的所有孪生体，以及所有对模型做出反应的代码，**请使用此策略**。 如果模型更新包含对模型更新的中断性变更，则孪生体在从旧模型转换到新模型时，会在短时间内不符合它们的模型，这意味着在上传新模型并且孪生体符合新模型之前，它们将无法执行任何更新。

>[!NOTE]
> 在开发之外，不鼓励对模型进行中断性变更。

继续看以下几节，详细阅读每个策略选项的更多信息。

### <a name="strategy-1-upload-new-model-version"></a>策略 1：上传新模型版本

此选项涉及创建新模型版本并将其上传到实例。

此操作不会覆盖早期模型版本，因此在[删除早期版本](#remove-models)之前，模型的多个版本将在实例中共存。 由于新模型版本和旧模型版本共存，因此孪生体可以使用新模型版本，也可以使用旧模型版本，这意味着上传新模型版本不会自动影响现有孪生体。 现有孪生体将保留为旧模型版本的实例，可以通过修补这些孪生体将其更新为新模型版本。

若要使用此策略，请执行以下步骤。

#### <a name="1-create-and-upload-new-model-version"></a>1. 创建并上传新模型版本 

若要创建现有模型的新版本，请从原始模型的 DTDL 开始。 更新、添加或删除要更改的字段。

接下来，更新此模型的 `id` 字段，将模型标记为新模型版本。 模型 ID 的最后一个部分（`;` 后面的部分）表示模型编号。 若要指示此模型目前是最新版本，请将 `id` 值末尾的数字递增为大于当前版本号的任意数字。

例如，如果之前的模型 ID 如下所示：

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

此模型的版本 2 可能如下所示：

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

然后，将新模型版本[上传](#upload-models)到实例。 

此版本的模型随后将在实例中提供，用于数字孪生体。 它不会覆盖早期模型版本，因此模型的多个版本此时在实例中共存。

#### <a name="2-update-graph-elements-as-needed"></a>2. 根据需要更新图元素

接下来，更新实例中的“孪生体和关系”，以使用新模型版本，而不再使用旧模型版本。

可以使用以下说明[更新孪生体](how-to-manage-twin.md#update-a-digital-twins-model)和[更新关系](how-to-manage-graph.md#update-relationships)。 更新孪生体模型的修补操作如下所示：

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

>[!IMPORTANT]
>更新孪生体时，使用“同一补丁”来同时更新模型 ID（更新为新模型版本）和孪生体上必须更改的任何字段以使其符合新模型。

可能还需要更新实例中引用此模型的“关系”和其他“模型”，使其引用新模型版本。  需要执行另一个模型更新操作来实现此目的，因此请返回到本节的开头，并针对需要更新的其他任何模型重复此过程。

#### <a name="3-optional-decommission-or-delete-old-model-version"></a>3. （可选）停用或删除旧模型版本

如果不再使用旧模型版本，可以[停用](#decommissioning)旧模型。 此操作允许模型保留在实例中，但不能用于创建新的数字孪生体。

如果不想再在实例中使用旧模型，也可以将其完全[删除](#deletion)。

上面链接的几节包含有关停用和删除模型的示例代码和注意事项。

### <a name="strategy-2-delete-old-model-and-reupload"></a>策略 2：删除旧模型并重新上传

可以完全删除模型，并将编辑后的模型重新上传到实例，而不是递增模型的版本。

Azure 数字孪生不记得旧模型曾经上传过，因此此操作就像上传全新的模型一样。 使用模型的图中的孪生体在新定义可用后会自动切换到新定义。 根据新定义与旧定义的区别，这些孪生体的属性和关系可能与已删除的定义相匹配，并且对于新定义无效，因此可能需要修补它们以确保其保持有效。

若要使用此策略，请执行以下步骤。

### <a name="1-delete-old-model"></a>1. 删除旧模型

由于Azure 数字孪生不允许两个模型具有相同的 ID，因此首先从实例中删除原始模型。 

>[!NOTE]
> 如果有其他模型依赖于此模型（通过继承或组件），则需要先删除这些引用，然后才能删除该模型。 可以先更新这些依赖的模型以暂时删除引用，或删除依赖的模型并在后面的步骤中重新上传。

使用以下说明[删除原始模型](#deletion)。 此操作会暂时将使用该模型的孪生体“孤立”，因为它们现在使用的是不再存在的模型。 此状态将在下一步重新上传更新的模型时修复。

### <a name="2-create-and-upload-new-model"></a>2. 创建并上传新模型

从原始模型的 DTDL 开始。 更新、添加或删除要更改的字段。

然后，[上传模型](#upload-models)到实例，就像它是首次上传的新模型一样。

### <a name="3-update-graph-elements-as-needed"></a>3. 根据需要更新图元素

现在，新模型已上传并替换旧模型，一旦实例中的高速缓存过期并重置，图中的孪生体将自动开始使用新模型定义。 **此过程可能需要 10-15 分钟或更长时间**，具体取决于图的大小。 之后，应可访问模型的新属性和已更改的属性，而已删除的属性将不再可访问。

>[!NOTE]
> 如果之前为了删除原始模型而删除了其他依赖的模型，现在请在缓存重置后将其重新上传。 如果更新了依赖的模型以暂时删除对原始模型的引用，可以再次更新它们以放回引用。

接下来，更新实例中的“孪生体和关系”，以便其属性与新模型定义的属性匹配。 在此步骤完成之前，仍可以读取与模型不匹配的孪生体，但不能写入。 有关没有有效模型的孪生体状态的详细信息，请参阅[无模型的孪生体](#after-deletion-twins-without-models)。

有两种方法可以针对新模型更新孪生体和关系，以使其再次可写：
* 根据需要修补孪生体和关系，以使其适合新模型。 可以使用以下说明[更新孪生体](how-to-manage-twin.md#update-a-digital-twin)和[更新关系](how-to-manage-graph.md#update-relationships)。
    - 如果已添加属性：不需要更新孪生体和关系以具有新值，因为缺少新值的孪生体仍将是有效的孪生体。 但如果想要针对新属性添加值，可以修补它们。
    - 如果已删除属性：需要修补孪生体以删除现在对于新模型无效的属性。
    - 如果已更新属性：需要修补孪生体，以更新已更改属性的值，使其对于新模型有效。
* 删除使用模型的孪生体和关系，然后重新创建它们。 可以使用以下说明来[删除孪生体](how-to-manage-twin.md#delete-a-digital-twin)并[重新创建孪生体](how-to-manage-twin.md#create-a-digital-twin)，以及[删除关系](how-to-manage-graph.md#delete-relationships)并[重新创建关系](how-to-manage-graph.md#create-relationships)。
    - 如果要对模型进行许多更改，可能需要执行此操作，很难更新现有孪生体来匹配它。 但是，如果有许多孪生体由许多关系互连，则重新创建会很复杂。

## <a name="remove-models"></a>删除模型

可通过以下两种方式之一从服务中删除模型：
* **停止使用**：模型停止使用后，就不能再使用它来创建新的数字孪生体。 已使用此模型的现有数字孪生体不会受到影响，因此你仍可通过属性更改、添加或删除关系等操作对孪生体进行更新。
* **删除**：此操作将从解决方案中完全删除该模型。 使用此模型的任何孪生体不再与任何有效模型关联，因此它们被视为根本没有模型。 用户仍可读取这些孪生体，但在将它们重新分配到另一个模型之前，不能对它们进行任何更新。

这些操作是独立的功能，尽管它们可以一起使用来逐步删除模型，但它们不会相互影响。 

### <a name="decommissioning"></a>停止使用

若要停用一个模型，可以使用 SDK 中的 [DecommissionModel](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.decommissionmodel?view=azure-dotnet&preserve-view=true) 方法：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

还可使用 REST API 调用 [DigitalTwinModels Update](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_update) 来停用模型。 `decommissioned` 属性是唯一可以替换为此 API 调用的属性。 JSON 补丁文档将如下所示：

:::code language="json" source="~/digital-twins-docs-samples/models/patch-decommission-model.json":::

模型的停止使用状态包含在模型检索 API 返回的 `ModelData` 记录中。

### <a name="deletion"></a>删除

可一次删除实例中的所有模型，也可逐个删除。

有关如何同时删除所有模型的示例，请参阅 [Azure 数字孪生端到端示例](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/CommandLoop.cs) GitHub 存储库。 “CommandLoop.cs”文件包含 `CommandDeleteAllModels` 函数，具有用于删除实例中所有模型的代码。

若要删除单个模型，请按照本节其余内容中的说明和注意事项进行操作。

#### <a name="before-deletion-deletion-requirements"></a>删除前：删除的要求

通常，可随时删除模型。

但有一个例外情况，如果有其他模型依赖该模型（无论是通过 `extends` 关系还是以组件形式），则该模型不可删除。 例如，如果 ConferenceRoom 模型扩展了 Room 模型，并拥有组件形式的 ACUnit 模型，则在 ConferenceRoom 删除这些相应引用之前，不能删除 Room 或 ACUnit。 

若要删除，可更新依赖的模型以删除依赖项，或者完全删除依赖的模型。

#### <a name="during-deletion-deletion-process"></a>删除期间：删除过程

即使模型满足立即删除的要求，你也可能需要先执行几个步骤，以避免留下的孪生体出现意外后果。 下面是可帮助你管理该过程的一些步骤：
1. 首先，停止使用该模型
2. 等待几分钟，确保服务已处理完最后发送的孪生体创建请求再停止使用
3. 按模型查询孪生体，查看正在使用现已停用的模型的所有孪生体
4. 如果不再需要这些孪生体，请将它们删除，或根据需要将它们修补为新模型。 还可选择不管它们，在这种情况下，在删除该模型后，这些模型就会变为无模型的孪生体。 有关此状态的含义，请参阅下一节。
5. 等待几分钟，确保更改已完成
6. 删除模型 

若要删除模型，可以使用 [DeleteModel]/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.deletemodel?view=azure-dotnet&preserve-view=true) SDK 调用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

还可使用 [DigitalTwinModels Delete](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_delete) REST API 调用删除模型。

#### <a name="after-deletion-twins-without-models"></a>删除后：无模型的孪生体

删除某一模型后，使用该模型的任何数字孪生体现在都被视为无模型。 没有任何查询可提供此状态的所有孪生体的列表，但仍可通过已删除的模型来查询孪生体，以了解哪些孪生体受到影响。

下面概述了无模型孪生体可执行和不可执行的操作。

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

#### <a name="after-deletion-reuploading-a-model"></a>删除后：重新上传模型

删除某一模型后，可决定稍后上传与删除的模型具有相同 ID 的新模型。 下面是在这种情况下会发生的事项。
* 从解决方案商店的角度来看，此操作等同于上传一个全新的模型。 该服务不记得这个旧模型曾经上传过。   
* 如果图中有任何引用已删除模型的剩余孪生体，则它们将不再是孤立的；此模型 ID 对于新定义仍然有效。 但是，如果模型的新定义不同于已删除的模型定义，则这些孪生体的属性和关系可能与已删除的定义相匹配，并且对于新定义无效。

Azure 数字孪生不会阻止此状态，因此请小心适当地修补孪生体，以确保它们通过模型定义切换后仍然有效。

## <a name="next-steps"></a>后续步骤

请参阅如何基于模型创建和管理数字孪生体：
* [管理数字孪生](how-to-manage-twin.md)