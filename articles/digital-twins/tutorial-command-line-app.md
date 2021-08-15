---
title: 教程：在 Azure 数字孪生中创建图（客户端应用）
titleSuffix: Azure Digital Twins
description: 有关使用示例命令行应用程序生成 Azure 数字孪生方案的教程
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 1d4faa9ad8bce084fdd011fb9a79fd867f02aa27
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446900"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>教程：使用示例客户端应用创建 Azure 数字孪生图

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

在本教程中，你将使用模型、孪生和关系在 Azure 数字孪生中生成一个图。 本教程的工具是用于与 Azure 数字孪生实例交互的示例命令行客户端应用程序。 该客户端应用类似于[为客户端应用编写代码](tutorial-code.md)中编写的应用。

可以使用此示例执行基本的 Azure 数字孪生操作，例如上传模型、创建和修改孪生以及创建关系。 还可查看[示例的代码](https://github.com/Azure-Samples/digital-twins-samples/tree/master/)来了解 Azure 数字孪生 API，并按你想要的方式修改示例项目来练习实现你自己的命令。

在本教程中，你将...
> [!div class="checklist"]
> * 为环境建模
> * 创建数字孪生
> * 添加关系以构成图
> * 查询该图以回答问题

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>运行示例项目

设置应用和身份验证后，使用工具栏中的以下按钮运行该项目：

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Visual Studio 开始按钮的屏幕截图（SampleClientApp 项目）。" lightbox="media/tutorial-command-line/app/start-button-sample.png":::

控制台窗口随即打开，执行身份验证，然后等待命令。 

下面是项目控制台的外观的屏幕截图：

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="来自命令行应用的欢迎消息的屏幕截图。" lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> 有关在此项目中可以使用的所有可能命令的列表，请在项目控制台中输入 `help`，然后按回车键。

在确认该应用运行成功后，请关闭控制台窗口，暂时停止运行该应用。 在本文后面需再次运行它。

## <a name="model-a-physical-environment-with-dtdl"></a>使用 DTDL 为物理环境建模

设置 Azure 数字孪生实例和示例应用后，接下来可以开始生成方案图。 

创建 Azure 数字孪生解决方案的第一步是为环境定义孪生模型。 

模型类似于面向对象的编程语言中的类；它们为[数字孪生](concepts-twins-graph.md)提供了日后可遵循并实例化的用户定义的模板。 它们用类似于 JSON 的语言（称为数字孪生定义语言 (DTDL)）编写，并且可以定义孪生的属性、遥测、关系和组件   。

> [!NOTE]
> DTDL 还允许在数字孪生上定义命令。 但是，Azure 数字孪生服务当前不支持命令。

在打开了“AdtE2ESample”项目的 Visual Studio 窗口中，使用“解决方案资源管理器”窗格导航到“AdtSampleApp\SampleClientApp\Models”文件夹。  此文件夹包含示例模型。

选择 Room.json，在编辑窗口中将其打开，并通过以下方式更改：

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>将模型上传到 Azure 数字孪生

设计模型后，需要将其上传到 Azure 数字孪生实例。 这将使用你自己的自定义域词汇来配置 Azure 数字孪生服务实例。 上传模型后，可以创建使用这些模型的孪生实例。

1. 在编辑上一部分的 Room.json 文件后，再次开始运行控制台应用。

1. 在项目控制台窗口中，运行以下命令以上传更新后的 Room 模型和 Floor 模型，你将在下一节中使用这些模型创建不同类型的孪生 。

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    输出应指示模型已成功创建。

1. 通过运行命令 `GetModels true` 来验证模型是否已创建。 这将在 Azure 数字孪生实例中查询所有已上传的模型，并打印出其完整信息。 在结果中寻找经过编辑的 Room 模型：

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="GetModels 中的结果的屏幕截图，其中显示了已更新的 Room 模型。" lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>错误

该示例应用程序还处理服务中的错误。 

重新运行 `CreateModels` 命令，尝试再次重新上传刚刚上传的其中一个相同模型：

```cmd/sh
CreateModels Room
```

由于无法覆盖模型，此操作现在将返回服务错误。
有关如何删除现有模型的详细信息，请参阅[管理 DTDL 模型](how-to-manage-model.md)。
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>创建数字孪生

现在，一些模型已上传到 Azure 数字孪生实例，你可以根据模型定义创建数字孪生。 数字孪生表示业务环境中的实体，这类似于农场中的传感器、大楼中的房间或汽车上的灯。 

要创建数字孪生，请使用 `CreateDigitalTwin` 命令。 必须引用孪生所基于的模型，并且可以选择定义模型中任何属性的初始值。 在此阶段，无需传递任何关系信息。

1. 在先前更新的 Room 模型和另一个 Floor 模型的基础上，在正在运行的项目控制台中运行此代码，以创建多个孪生模型 。 回想一下，Room 具有三个属性，因此可以为这些属性提供具有初始值的参数。 （初始化属性值在一般情况下是可选操作，但本教程需要此操作。）

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    这些命令的输出应指示孪生已成功创建。 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="显示 CreateDigitalTwin 命令结果摘录的屏幕截图，其中包括 floor0、floor1、room0 和 room1。" lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. 可以运行 `Query` 命令来验证是否已创建孪生。 此命令在 Azure 数字孪生实例中查询其包含的所有数字孪生。 在结果中查找 room0、room1、floor0 和 floor1 孪生。

### <a name="modify-a-digital-twin"></a>修改数字孪生

你可以修改已创建的孪生的属性。 

> [!NOTE]
> 底层 REST API 使用 [JSON 补丁](http://jsonpatch.com/)格式定义对孪生的更新。 该命令行应用也使用此格式，以便用户更真实地体验底层 API 所需的内容。

1. 运行以下命令，将 room0 的 RoomName 从“Room0”更改为“PresidentialSuite”：
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    输出应指示模型已成功更新。

1. 可通过运行以下命令查看 *room0* 的信息，来验证更新是否成功：

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    输出应反映更新后的名称。


## <a name="create-a-graph-by-adding-relationships"></a>通过添加关系来创建关系图

接下来，你可以在这些孪生之间创建某种关系，以将它们连接到孪生图 。 孪生图用于表示整个环境。 

可由你在两个不同孪生之间创建的关系的类型，是在前面上传的[模型](#model-a-physical-environment-with-dtdl)中定义的。 [Floor 的模型定义](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)指定楼层可有一个名为 contains 的关系类型。 因此，可以创建从每个 Floor 孪生到它所包含的相应房间的 *contains* 类型的关系。

要添加关系，请使用 `CreateRelationship` 命令。 指定该关系的来源孪生、关系类型，以及该关系要连接到的孪生。 最后，为该关系指定唯一的 ID。

1. 运行以下代码，将来自之前创建的每个 Floor 孪生的“包含”关系添加到对应的 Room 孪生 。 关系的名称分别为 relationship0 和 relationship1。

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >在 [Floor 模型](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)中定义的 *contains* 关系也有两个字符串属性：`ownershipUser` 和 `ownershipDepartment`，因此，在创建关系时，也可以为参数提供这些属性的初始值。
    > 下面是上述命令的另一个版本，该版本将创建 relationship0，其中也会指定这些属性的初始值：
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    这些命令的输出确认关系已成功创建：
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="CreateRelationship 命令结果摘录的屏幕截图，其中包括 relationship0 和 relationship1。" lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. 可以使用以下任一命令来验证关系，这些命令将查询 Azure 数字孪生实例中的关系。
    * 若要查看源自每个楼层的所有关系（从一端查看关系）：
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * 若要查看到达每个房间的所有关系（从“另一”端查看关系）：
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * 若要按 ID 单独查找这些关系：
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

本教程中设置的孪生和关系形成以下概念图：

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="显示概念图的示意图。floor0 通过 relationship0 连接到 room0，floor1 通过 relationship1 连接到 room1。" border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>查询孪生图以回答环境问题

Azure 数字孪生的主要功能是能够轻松有效地[查询](concepts-query-language.md)孪生图，以解答有关环境的问题。 

在正在运行的项目控制台中运行以下命令，回答有关示例环境的一些问题。

1. **在我的环境中，用 Azure 数字孪生表示的所有实体都有哪些？** （查询所有）

    ```cmd/sh
    Query
    ```

    使用此命令可以一目了然地评估环境，确保在 Azure 数字孪生中以你希望的方式表示所有内容。 此命令的输出结果包含每个数字孪生及其详细信息。 下面是结果摘录：

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="显示孪生查询部分结果的屏幕截图，其中包括 room0 和 floor1。":::

    >[!NOTE]
    >在示例项目中，不带任何附加参数的命令 `Query` 相当于 `Query SELECT * FROM DIGITALTWINS`。 若要使用[查询 API](/rest/api/digital-twins/dataplane/query) 或 [CLI 命令](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)查询实例中的所有孪生体，请使用较长的（完整）查询。

1. **我的环境中的所有房间都有哪些？** （按模型查询）

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    可以将查询限制为某种类型的孪生，以获取有关所表示内容的更多具体信息。 此查询的结果显示 room0 和 room1，但不显示 floor0 或 floor1（因为它们是楼层，而不是房间）。
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="模型查询结果的屏幕截图，其中只显示了 room0 和 room1。":::

1. floor0 上的所有房间都有哪些？ （按关系查询）

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    可以根据图形中的关系进行查询，以获取有关如何连接孪生或如何将查询限制在特定区域的信息。 floor0 上只有 room0，因此它是结果中唯一的房间 。

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="关系查询结果的屏幕截图，其中显示了 room0。":::

1. **环境中温度高于 75 的所有孪生都有哪些？** （按属性查询）

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    可以根据属性查询图形，以回答各种问题，包括在环境中查找可能需要注意的离群值。 还支持其他比较运算符（<、>、= 或 !=   ）。 room1 出现在此处的结果中，因为它的温度为 80。

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="属性查询结果的屏幕截图，其中只显示了 room1。":::

1. floor0 上温度高于 75 的所有房间都有哪些？ （复合查询）

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    还可以像在 SQL 一样使用组合运算符（例如 `AND`、`OR` 和 `NOT`）合并之前的查询。 此查询使用 `AND` 使先前有关孪生温度的查询更具体。 现在，结果仅包括 floor0 上温度高于 75 的房间。在本例中，这些房间均不满足条件。 结果集为空。

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="复合查询结果的屏幕截图，其中未显示任何结果。" lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>清理资源

完成本教程后，可以选择要删除的资源，具体取决于接下来要执行的操作。

* 如果打算继续学习下一个教程，可保留在此处设置的资源，以便继续在下一个教程中使用此 Azure 数字孪生实例和已配置的示例应用

* 若要继续使用 Azure 数字孪生实例，但想要清除其所有模型、孪生和关系，可使用示例应用的 `DeleteAllTwins` 和 `DeleteAllModels` 命令分别清除实例中的孪生和模型。

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

你可能还需要从本地计算机中删除项目文件夹。

## <a name="next-steps"></a>后续步骤 

在本教程中，你已通过示例客户端应用程序在实例中生成一个图，开始使用了 Azure 数字孪生。 你创建了模型、数字孪生和关系以构成图。 你还对该图运行了一些查询，以大致了解 Azure 数字孪生可以解答的与环境有关的问题。

请继续学习下一篇教程，将 Azure 数字孪生与其他 Azure 服务结合使用来实现数据驱动的端到端方案：
> [!div class="nextstepaction"]
> [连接端到端解决方案](tutorial-end-to-end.md)