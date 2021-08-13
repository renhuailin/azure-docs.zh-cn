---
title: 教程：在 Azure 数字孪生中创建图 (CLI)
titleSuffix: Azure Digital Twins
description: 有关使用 Azure CLI 生成 Azure 数字孪生方案的教程
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 93dbdb8384a37e2f104f1ab99caf1ab6d8464c06
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461895"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>教程：使用 Azure CLI 创建 Azure 数字孪生图

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

在本教程中，你将使用模型、孪生和关系在 Azure 数字孪生中生成一个图。 本教程使用的工具是[适用于 Azure CLI 的 Azure 数字孪生命令集](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)。

可以使用 CLI 命令执行基本的 Azure 数字孪生操作，例如上传模型、创建和修改孪生，以及创建关系。 还可查看 [az dt 命令集的参考文档](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)，查看完整的 CLI 命令集。

在本教程中，你将...
> [!div class="checklist"]
> * 为环境建模
> * 创建数字孪生
> * 添加关系以构成图
> * 查询该图以回答问题

## <a name="prerequisites"></a>必备条件

若要完成本教程中的步骤，需要先具备以下先决条件。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="download-the-sample-models"></a>下载示例模型

本教程使用两个预先编写的模型，这些模型是适用于 Azure 数字孪生的 C# [端到端示例项目](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)的一部分。 模型文件位于以下位置： 
* [Room.json](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [Floor.json](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

若要将这些文件获取到你的计算机上，请使用上面的导航链接，并将文件正文复制到计算机上同名的本地文件（*Room.json* 和 *Floor.json*）中。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>设置 Cloud Shell 会话
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>准备 Azure 数字孪生实例

若要在本文中使用 Azure 数字孪生，首先需要设置一个 Azure 数字孪生实例，还需具备使用它所必需的权限。 如果已有通过以前的工作设置的 Azure 数字孪生实例，则可以使用该实例。

如果没有，请按照[设置实例和身份验证](how-to-set-up-instance-cli.md)中的说明操作。 说明中还包含用于验证是否已成功完成每个步骤并准备好继续使用新实例的步骤。

设置 Azure 数字孪生实例后，请记下以下值，稍后需要这些值来连接到该实例：
* 实例的 **主机名**。
* 用于创建实例的 **Azure 订阅**。 

可在以下 Azure CLI 命令的输出中获取实例的这两个值： 

```azurecli-interactive
az dt show --dt-name <Azure-Digital-Twins-instance-name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Cloud Shell 浏览器窗口的屏幕截图，其中显示了 az dt show 命令的输出。突出显示了 hostName 字段和订阅 ID。":::

## <a name="model-a-physical-environment-with-dtdl"></a>使用 DTDL 为物理环境建模

设置 CLI 和 Azure 数字孪生实例后，接下来可以开始生成方案图。 

创建 Azure 数字孪生解决方案的第一步是为环境定义孪生模型。 

模型类似于面向对象的编程语言中的类；它们为[数字孪生](concepts-twins-graph.md)提供了日后可遵循并实例化的用户定义的模板。 它们用类似于 JSON 的语言（称为数字孪生定义语言 (DTDL)）编写，并且可以定义孪生的属性、遥测、关系和组件   。

> [!NOTE]
> DTDL 还允许在数字孪生上定义命令。 但是，Azure 数字孪生服务当前不支持命令。

在计算机上导航到在 [先决条件](#prerequisites)部分创建的 *Room.json* 文件。 在代码编辑器中打开该文件，然后通过以下方式对其进行更改：

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>将模型上传到 Azure 数字孪生

设计模型后，需要将其上传到 Azure 数字孪生实例。 这将使用你自己的自定义域词汇来配置 Azure 数字孪生服务实例。 上传模型后，可以创建使用这些模型的孪生实例。

1. 若要使用 Cloud Shell 添加模型，需将模型文件上传到 Cloud Shell 的存储，以便在运行使用这些文件的 Cloud Shell 命令时，这些文件可供使用。 为此，请选择“上传/下载文件”图标，然后选择“上传”。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell 浏览器窗口的屏幕截图，其中显示了选择“上传”图标。":::
    
    在计算机上导航到“Room.json”文件并选择“打开”。 然后针对 Floor.json 重复此步骤。

1. 接下来，按如下所示使用 [az dt model create](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_create) 命令将更新的 Room 模型上传到 Azure 数字孪生实例。 第二个命令上传另一个模型 Floor，在下一部分，还要使用该模型创建不同类型的孪生。

    ```azurecli-interactive
    az dt model create --dt-name <Azure-Digital-Twins-instance-name> --models Room.json
    az dt model create --dt-name <Azure-Digital-Twins-instance-name> --models Floor.json
    ```
    
    每个命令的输出将显示有关已成功上传的模型的信息。

    >[!TIP]
    >还可以使用 model create 命令的 `--from-directory` 选项，同时上传一个目录中的所有模型。 有关详细信息，请查看 [az dt model create 的可选参数](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_create-optional-parameters)。

1. 按如下所示使用 [az dt model list](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_list) 命令验证是否已创建模型。 此命令将输出已上传到 Azure 数字孪生实例的所有模型的列表及其完整信息。 

    ```azurecli-interactive
    az dt model list --dt-name <Azure-Digital-Twins-instance-name> --definition
    ```
    
    在结果中寻找经过编辑的 Room 模型：
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Cloud Shell 的屏幕截图，其中显示了 model list 命令的结果，结果中包括已更新的 Room 模型。" lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>错误

CLI 还会处理服务中发生的错误。 

重新运行 `az dt model create` 命令，尝试再次重新上传刚刚上传的其中一个相同模型：

```azurecli-interactive
az dt model create --dt-name <Azure-Digital-Twins-instance-name> --models Room.json
```

由于无法覆盖模型，此操作现在会返回 `ModelIdAlreadyExists` 错误代码。

## <a name="create-digital-twins"></a>创建数字孪生

现在，一些模型已上传到 Azure 数字孪生实例，你可以根据模型定义创建数字孪生。 数字孪生表示业务环境中的实体，这类似于农场中的传感器、大楼中的房间或汽车上的灯。 

若要创建数字孪生，请使用 [az dt twin create](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_create) 命令。 必须引用孪生所基于的模型，并且可以选择定义模型中任何属性的初始值。 在此阶段，无需传递任何关系信息。

1. 在 Cloud Shell 中运行此代码，以基于前面更新的 Room 模型以及另一个模型 Floor 创建多个孪生。 回想一下，Room 具有三个属性，因此可以为这些属性提供具有初始值的参数。 （初始化属性值在一般情况下是可选操作，但本教程需要此操作。）

    ```azurecli-interactive
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":80, "HumidityLevel":60}'
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > 如果在 PowerShell 环境中使用 Cloud Shell，可能需要将引号字符转义才能正确分析 `--properties` JSON 值。 按此方式进行编辑后，用于创建房间孪生的命令如下所示：
    >
    > ```azurecli-interactive
    > az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > 以下屏幕截图反映了结果。
    
    每个命令的输出将显示有关已成功创建的孪生的信息（包括随房间孪生一起初始化的房间孪生属性）。

1. 可按如下所示使用 [az dt twin query](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) 命令验证是否已创建孪生。 所示的查询将查找 Azure 数字孪生实例中的所有数字孪生。
    
    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DIGITALTWINS"
    ```
    
    在结果中查找 room0、room1、floor0 和 floor1 孪生。 下面是显示此查询结果的一部分的摘录。
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Cloud Shell 的屏幕截图，其中显示了孪生查询的部分结果，结果中包括 room0 和 room1。" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>修改数字孪生

你可以修改已创建的孪生的属性。 

1. 运行以下 [az dt twin update](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update) 命令，将 *room0* 的 RoomName 从 *Room0* 更改为 *PresidentialSuite*：

    ```azurecli-interactive
    az dt twin update --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > 如果在 PowerShell 环境中使用 Cloud Shell，可能需要将引号字符转义才能正确分析 `--json-patch` JSON 值。 按此方式进行编辑后，用于更新孪生的命令如下所示：
    >
    > ```azurecli-interactive
    > az dt twin update --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > 以下屏幕截图反映了结果。
    
    此命令的输出将显示孪生的当前信息，结果中应会显示 `RoomName` 的新值。

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Cloud Shell 屏幕截图，其中显示了 update 命令的结果，结果中包括 RoomName 的值 PresidentialSuite。" lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. 可以通过运行 [az dt twin show](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) 命令查看 room0 的信息，来验证更新是否成功：

    ```azurecli-interactive
    az dt twin show --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0
    ```
    
    输出应反映更新后的名称。

## <a name="create-a-graph-by-adding-relationships"></a>通过添加关系来创建关系图

接下来，你可以在这些孪生之间创建某种关系，以将它们连接到孪生图 。 孪生图用于表示整个环境。 

可由你在两个不同孪生之间创建的关系的类型，是在前面上传的[模型](#model-a-physical-environment-with-dtdl)中定义的。 [Floor 的模型定义](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)指定楼层可有一个名为 contains 的关系类型。 因此，可以创建从每个 Floor 孪生到它所包含的相应房间的 *contains* 类型的关系。

若要添加关系，请使用 [az dt twin relationship create](/cli/azure/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#az_dt_twin_relationship_create) 命令。 指定该关系的来源孪生、关系类型，以及该关系要连接到的孪生。 最后，为该关系指定唯一的 ID。 如果一个关系已定义为具有属性，则还可以在此命令中初始化关系属性。

1. 运行以下代码，将来自前面创建的每个 Floor 孪生的 *contains* 类型的关系添加到相应的 Room 孪生。 关系的名称分别为 relationship0 和 relationship1。

    ```azurecli-interactive
    az dt twin relationship create --dt-name <Azure-Digital-Twins-instance-name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create --dt-name <Azure-Digital-Twins-instance-name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >在 [Floor 模型](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)中定义的 *contains* 关系也有两个属性：`ownershipUser` 和 `ownershipDepartment`，因此，在创建关系时，也可以为参数提供这些属性的初始值。
    > 若要在这些属性已初始化的情况下创建关系，请将 `--properties` 选项添加到上述任一命令，如下所示：
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > 如果在 PowerShell 环境中使用 Cloud Shell，可能需要将引号字符转义才能正确分析 `--properties` JSON 值。
    
    每个命令的输出将显示有关已成功创建的关系的信息。

1. 可以使用以下任一命令来验证关系，这些命令将查询 Azure 数字孪生实例中的关系。
    * 若要查看源自每个楼层的所有关系（从一端查看关系）：
        ```azurecli-interactive
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor0
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor1
        ```
    * 若要查看到达每个房间的所有关系（从“另一”端查看关系）：
        ```azurecli-interactive
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0 --incoming
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id room1 --incoming
        ```
    * 若要按 ID 单独查找这些关系：
        ```azurecli-interactive
        az dt twin relationship show --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor1 --relationship-id relationship1
        ```

本教程中设置的孪生和关系形成以下概念图：

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="显示概念图的示意图。floor0 通过 relationship0 连接到 room0，floor1 通过 relationship1 连接到 room1。" border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>查询孪生图以回答环境问题

Azure 数字孪生的主要功能是能够轻松有效地[查询](concepts-query-language.md)孪生图，以解答有关环境的问题。 在 Azure CLI 中，这是使用 [az dt twin query](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) 命令来实现的。

在 Cloud Shell 中运行以下查询可以回答有关示例环境的一些问题。

1. **在我的环境中，用 Azure 数字孪生表示的所有实体都有哪些？** （查询所有）

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DIGITALTWINS"
    ```

    使用此命令可以一目了然地评估环境，确保在 Azure 数字孪生中以你希望的方式表示所有内容。 此命令的输出结果包含每个数字孪生及其详细信息。 下面是结果摘录：

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Cloud Shell 的屏幕截图，其中显示了孪生查询的部分结果，结果中包括 room0 和 room1。" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >你可能会认出，这是在前面的[创建数字孪生](#create-digital-twins)部分中用来查找实例中的所有 Azure 数字孪生的同一命令。

1. **我的环境中的所有房间都有哪些？** （按模型查询）

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    可以将查询限制为某种类型的孪生，以获取有关所表示内容的更多具体信息。 此查询的结果显示 room0 和 room1，但不显示 floor0 或 floor1（因为它们是楼层，而不是房间）。
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Cloud Shell 的屏幕截图，其中显示了模型查询的结果，结果中仅包括 room0 和 room1。" lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. floor0 上都有哪些房间？ （按关系查询）

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    可以根据图形中的关系进行查询，以获取有关如何连接孪生或如何将查询限制在特定区域的信息。 floor0 上只有 room0，因此它是结果中唯一的房间 。

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Cloud Shell 的屏幕截图，其中显示了关系查询的结果，结果中包括 room0。" lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > 请注意，已使用元数据字段 `$dtId` 查询了孪生的 ID（类似于上述查询中的 floor0）。 
    >
    >使用 Cloud Shell 针对类似于这样的以 `$` 开头的元数据字段运行查询时，应使用反撇号将 `$` 转义，让 Cloud Shell 知道它不是变量，应将其作为查询文本中的文本来使用。 以上屏幕截图反映了这种转义方式。

1. **环境中温度高于 75 的所有孪生都有哪些？** （按属性查询）

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    可以根据属性查询图形，以回答各种问题，包括在环境中查找可能需要注意的离群值。 还支持其他比较运算符（<、>、= 或 !=   ）。 room1 出现在此处的结果中，因为它的温度为 80。

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Cloud Shell 的屏幕截图，其中显示了属性查询的结果，结果中仅包括 room1。" lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. floor0 上温度高于 75 的所有房间都有哪些？ （复合查询）

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    还可以像在 SQL 一样使用组合运算符（例如 `AND`、`OR` 和 `NOT`）合并之前的查询。 此查询使用 `AND` 使先前有关孪生温度的查询更具体。 现在，结果仅包括 floor0 上温度高于 75 的房间。在本例中，这些房间均不满足条件。 结果集为空。

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Cloud Shell 的屏幕截图，其中显示了复合查询的结果，结果中不包括任何项。" lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>清理资源

完成本教程后，可以选择要删除的资源，具体取决于接下来要执行的操作。

* **如果你打算继续学习下一篇教程**，在此之前可以保留本教程中设置的资源而不要清除任何内容，并重复使用 Azure 数字孪生实例。

* 如果你要继续使用 Azure 数字孪生实例，但同时想要清除其所有模型、孪生和关系，可以分别使用 [az dt twin relationship delete](/cli/azure/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#az_dt_twin_relationship_delete)、[az dt twin delete](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_delete) 和 [az dt model delete](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_delete) 命令清除实例中的关系、孪生和模型。

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

你还可能想要删除在本地计算机上创建的模型文件。

## <a name="next-steps"></a>后续步骤 

在本教程中，你已通过 Azure CLI 在实例中生成一个图，开始使用了 Azure 数字孪生。 你创建了模型、数字孪生和关系以构成图。 你还对该图运行了一些查询，以大致了解 Azure 数字孪生可以解答的与环境有关的问题。

请继续学习下一篇教程，将 Azure 数字孪生与其他 Azure 服务结合使用来实现数据驱动的端到端方案：
> [!div class="nextstepaction"]
> [连接端到端解决方案](tutorial-end-to-end.md)