---
title: 创建基于事件的触发器
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何在 Azure 数据工厂或 Azure Synapse Analytics 中创建运行管道的触发器来响应事件。
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 5c7cf8d02b3a1cf19b826db63ff5eaaac1ff20d1
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303227"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>创建运行管道的触发器来响应存储事件

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了可以在数据工厂或 Synapse 管道中创建的存储事件触发器。

事件驱动的体系结构 (EDA) 是一种常见数据集成模式，其中涉及到事件的生成、检测、消耗和响应。 数据集成方案通常要求客户根据存储帐户中发生的事件（如 Azure Blob 存储帐户中的文件到达或删除）触发管道。 数据工厂和 Synapse 管道以本机方式与 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)集成，后者可以根据事件触发管道。

有关此功能的 10 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> 本文中所介绍的集成依赖于 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)。 请确保订阅已注册事件网格资源提供程序。 有关详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。 你必须能够执行 Microsoft.EventGrid/eventSubscriptions/* 操作。 此操作属于 EventGrid EventSubscription 参与者内置角色。

## <a name="create-a-trigger-with-ui"></a>使用 UI 创建触发器

本部分介绍了如何在 Azure 数据工厂和 Synapse 管道用户界面中创建存储事件触发器。

1. 切换到数据工厂中的“编辑”选项卡或 Azure Synapse 中的“集成”选项卡 。

1. 在菜单上选择“触发器”，然后选择“新建/编辑” 。

1. 在“添加触发器”页上，选择“选择触发器...”，然后选择“+新建”  。

1. 选择触发器类型“存储事件”

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)
    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-1.png" alt-text="“创作”页（用于在数据工厂 UI 中创建新的存储事件触发器）的屏幕截图。":::
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-1-synapse.png" alt-text="用于在 Azure Synapse UI 中创建新的存储事件触发器的作者页的屏幕截图。":::

5. 从 Azure 订阅下拉列表中选择你的存储帐户，或使用其存储帐户资源 ID 手动选择。 选择希望事件在哪个容器中发生。 需选择容器，但请注意，选择所有容器可能会导致发生大量事件。

   > [!NOTE]
   > 存储事件触发器目前仅支持 Azure Data Lake Storage Gen2 和“常规用途”版本 2 存储帐户。 由于 Azure 事件网格的限制，对于每个存储帐户，Azure 数据工厂最多仅支持 500 个存储事件触发器。 如果达到此限制，请与支持人员联系以获取建议，并根据事件网格团队的评估提高限制。 

   > [!NOTE]
   > 若要新建存储事件触发器或修改现有存储事件触发器，用于登录到服务并发布存储事件触发器的 Azure 帐户必须对存储帐户具有相应的基于角色的访问控制 (Azure RBAC) 权限。 不需要其他权限：Azure 数据工厂和 Azure Synapse 的服务主体不需要拥有对存储帐户或事件网格的特殊权限。 有关访问控制的详细信息，请参阅[基于角色的访问控制](#role-based-access-control)部分。

1. 使用“Blob 路径开头为”和“Blob路径结尾为”属性，可以指定要为其接收事件的容器、文件夹和 Blob 的名称。  存储事件触发器要求至少定义其中的一个属性。 可以为“Blob 路径开头为”和“Blob路径结尾为”属性使用各种模式，如本文中后面的示例所示。

    * **Blob 路径开头为：** Blob 路径必须以文件夹路径开头。 有效值包括 `2018/` 和 `2018/april/shoes.csv`。 如果未选择容器，则无法选择此字段。
    * **Blob 路径结尾为：** Blob 路径必须以文件名或扩展名结尾。 有效值包括 `shoes.csv` 和 `.csv`。 指定容器和文件夹名称时，它们必须由 `/blobs/` 段分隔。 例如，名为“orders”的容器可以使用 `/orders/blobs/2018/april/shoes.csv` 值。 若要指定任何容器中的某个文件夹，请省略前导“/”字符。 例如，`april/shoes.csv` 将会针对任何容器中名为“april”的文件夹内名为 `shoes.csv` 的任何文件触发事件。
    * 请注意，只有“Blob 路径开头为”和“Blob 路径结尾为”是存储事件触发器中允许的模式匹配。 触发器类型不支持其他类型的通配符匹配。

1. 选择触发器是要响应“已创建 Blob”事件、“已删除 Blob”事件，还是同时响应这两者。  在指定的存储位置中，每个事件都将触发与触发器关联的数据工厂和 Synapse 管道。

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-2.png" alt-text="“存储事件触发器创建”页的屏幕截图。":::

1. 选择触发器是否忽略零字节的 Blob。

1. 配置触发器后，单击“下一步: 数据预览”。 此屏幕显示事件触发器配置匹配的现有 Blob。 请确保使用具体化的筛选器。 如果配置的筛选器过于宽泛，可能会匹配已创建/删除的大量文件，并可能会严重影响成本。 验证筛选条件后，单击“完成”。

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-3.png" alt-text="“存储事件触发器预览”页的屏幕截图。":::

1. 若要将管道附加到此触发器，请转到管道画布，然后单击“触发器”并选择“新建/编辑”。  出现边侧导航栏时，单击“选择触发器...”下拉列表，然后选择创建的触发器。 单击“下一步:数据预览”确认配置是否正确，然后单击“下一步”验证数据预览是否正确。

1. 如果管道具有参数，则你可以在触发器运行参数边侧导航栏中指定这些参数。 存储事件触发器将 Blob 的文件夹路径和文件名捕获到属性 `@triggerBody().folderPath` 和 `@triggerBody().fileName` 中。 若要在管道中使用这些属性的值，必须将这些属性映射至管道参数。 将这些属性映射至参数后，可以通过管道中的 `@pipeline().parameters.parameterName` 表达式访问由触发器捕获的值。 有关详细说明，请参阅[在管道中引用触发器元数据](how-to-use-trigger-parameterization.md)

   :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-4.png" alt-text="将属性映射到管道参数的存储事件触发器的屏幕截图。":::

   在前面的示例中，触发器配置为在容器 sample-data 中的文件夹 event-testing 内创建了以 .csv 结尾的 Blob 路径时触发。  **folderPath** 和 **fileName** 属性捕获新 Blob 的位置。 例如，将 MoviesDB.csv 添加到路径 sample-data/event-testing 时，`@triggerBody().folderPath` 的值为 `sample-data/event-testing`，`@triggerBody().fileName` 的值为 `moviesDB.csv`。 示例中的这些值将映射到管道参数 `sourceFolder` 和 `sourceFile`，这两个参数在整个管道中分别可以用作 `@pipeline().parameters.sourceFolder` 和 `@pipeline().parameters.sourceFile`。

   > [!NOTE]
   > 如果要在 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) 中创建管道和触发器，则必须使用 `@trigger().outputs.body.fileName` 和 `@trigger().outputs.body.folderPath` 作为参数。 这两个属性捕获 blob 信息。 使用这些属性，而不是使用 `@triggerBody().fileName` 和 `@triggerBody().folderPath`。

1. 完成后，单击“完成”。

## <a name="json-schema"></a>JSON 架构

下表概述了与存储事件触发器相关的架构元素：

| **JSON 元素** | **说明** | 类型 | **允许的值** | **必需** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **作用域** | 存储帐户的 Azure 资源管理器资源 ID。 | String | Azure 资源管理器 ID | 是 |
| **events** | 导致此触发器触发的事件的类型。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是的，这些值的任意组合。 |
| **blobPathBeginsWith** | blob 路径必须使用为要触发的触发器提供的模式开头。 例如，`/records/blobs/december/` 只会触发 `records` 容器下 `december` 文件夹中的 blob 触发器。 | 字符串   | | 必须为以下至少一个属性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **blobPathEndsWith** | blob 路径必须使用为要触发的触发器提供的模式结尾。 例如，`december/boxes.csv` 只会触发 `december` 文件夹中名为 `boxes` 的 blob 的触发器。 | String   | | 必须为其中至少一个属性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **ignoreEmptyBlobs** | 零字节 Blob 是否触发管道运行。 默认情况下，此元素设置为 true。 | 布尔 | true 或 false | 否 |

## <a name="examples-of-storage-event-triggers"></a>存储事件触发器的示例

本部分提供了存储事件触发器设置的示例。

> [!IMPORTANT]
> 每当指定容器和文件夹、容器和文件或容器、文件夹和文件时，都必须包含路径的 `/blobs/` 段，如以下示例所示。 对于 blobPathBeginsWith，UI 将自动在触发器 JSON 中的文件夹和容器名称之间添加 `/blobs/`。

| 属性 | 示例 | 说明 |
|---|---|---|
| **Blob 路径开头** | `/containername/` | 接收容器中任何 blob 事件。 |
| **Blob 路径开头** | `/containername/blobs/foldername/` | 接收 `containername` 容器和 `foldername` 文件夹中的任何 blob 事件。 |
| **Blob 路径开头** | `/containername/blobs/foldername/subfoldername/` | 此外可以引用一个子文件夹。 |
| **Blob 路径开头** | `/containername/blobs/foldername/file.txt` | 接收 `containername` 容器下的 `foldername` 文件夹中名为 `file.txt` 的 blob 事件。 |
| **Blob 路径结尾** | `file.txt` | 接收任何路径中名为 `file.txt` 的 blob 事件。 |
| **Blob 路径结尾** | `/containername/blobs/file.txt` | 接收容器 `containername` 下名为 `file.txt` 的 blob 事件。 |
| **Blob 路径结尾** | `foldername/file.txt` | 接收任何容器下 `foldername` 文件夹中名为 `file.txt` 的 blob 事件。 |

## <a name="role-based-access-control"></a>基于角色的访问控制

Azure 数据工厂和 Synapse 管道使用 Azure 基于角色的访问控制 (Azure RBAC) 确保严格禁止未经授权侦听 Blob 事件、订阅 Blob 事件中的更新以及触发链接到 Blob 事件的管道。

* 若要成功创建新的存储事件触发器或更新现有存储事件触发器，登录到服务的 Azure 帐户需要拥有对相关存储帐户的相应访问权限。 否则，操作会失败，并出现“访问被拒绝”错误消息。
* Azure 数据工厂和 Azure Synapse 不需要拥有对事件网格的特殊权限，并且无需为数据工厂或 Azure Synapse 服务主体分配特殊的 RBAC 权限来实现操作。

以下任何 RBAC 设置均适用于存储事件触发器：

* 存储帐户的所有者角色
* 存储帐户的参与者角色
* 对 _/subscriptions/####/resourceGroups/####/providers/Microsoft.Storage/storageAccounts/storageAccountName_ 存储帐户的 _Microsoft.EventGrid/EventSubscriptions/Write_ 权限

为了了解服务如何做出这两项承诺，让我们后退一步并在后台抢先了解。 下面是 Azure 数据工厂/Azure Synapse、存储和事件网格之间的集成的概要工作流。

### <a name="create-a-new-storage-event-trigger"></a>新建存储事件触发器

此概要工作流介绍 Azure 数据工厂如何与事件网格交互，以创建存储事件触发器。  对于 Azure Synapse，数据流相同，在下图中 Synapse 管道担任数据工厂的角色。

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="创建存储事件触发器的工作流。":::

工作流有两个值得注意之处：

* Azure 数据工厂和 Azure Synapse 不会直接与存储帐户联系。 创建订阅的请求将通过事件网格进行中继和处理。 因此，对于此步骤，服务不需要存储帐户的权限。

* 访问控制和权限检查发生在服务中。 在服务发送订阅存储事件的请求之前，它会检查用户的权限。 更具体地讲，它会检查已登录并在尝试创建存储事件触发器的 Azure 帐户是否具有访问相关存储帐户的相应权限。 如果权限检查失败，触发器创建也会失败。

### <a name="storage-event-trigger-pipeline-run"></a>存储事件触发管道运行

此概要工作流介绍存储事件如何通过事件网格触发管道运行。 对于 Azure Synapse，数据流相同，在下图中 Synapse 管道担任数据工厂的角色。

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="触发管道运行的存储事件的工作流。":::

在与服务中的事件触发管道相关的工作流中，有三个明显标注：

* 事件网格使用推送模型，当存储将消息放置到系统中时，它会尽快中继传递消息。 这与消息传送系统（例如使用了拉取系统的 Kafka）不同。
* 事件触发器充当传入消息的活动侦听器，并正确触发关联的管道。
* 存储事件触发器本身不与存储帐户建立直接联系

  * 也就是说，如果管道中有用于处理存储帐户中的数据的复制或其他活动，此服务将使用存储在链接服务中的凭据直接与存储联系。 确保正确设置了链接服务
  * 但是，如果不引用管道中的存储帐户，则无需授予服务访问存储帐户的权限

## <a name="next-steps"></a>后续步骤

* 有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md#trigger-execution)。
* 了解如何引用管道中的触发器元数据，详见[引用管道运行中的触发器元数据](how-to-use-trigger-parameterization.md)
