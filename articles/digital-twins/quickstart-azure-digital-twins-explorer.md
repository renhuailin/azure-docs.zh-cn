---
title: 快速入门 - Azure Digital Twins Explorer 入门
titleSuffix: Azure Digital Twins
description: 快速入门 - 使用 Azure Digital Twins Explorer 示例直观显示和探索预生成方案。
author: baanders
ms.author: baanders
ms.date: 4/27/2021
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 92b1eeb5317f5a7535406d1b3781a4b0b66cad45
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272446"
---
# <a name="quickstart---get-started-with-a-sample-scenario-in-azure-digital-twins-explorer"></a>快速入门 - 开始使用 Azure Digital Twins Explorer 中的示例场景

通过 Azure 数字孪生，可创建真实环境的实时模型并与之交互。 首先，将单个元素建模为“数字孪生”。 然后将它们连接到一个知识图，该图可以响应实时事件并查询信息。

在本快速入门中，你将使用 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) 来探索预生成的 Azure 数字孪生图形。 通过此工具，你可在 Azure 门户中将 Azure 数字孪生数据可视化并与之交互。 

你将完成以下步骤：

1. 创建一个 Azure 数字孪生实例，并在 Azure Digital Twins Explorer 中连接到该实例。
1. 上传预生成的模型和图形数据来构造示例方案。
1. 探索已创建的方案图形。
1. 对图形进行更改。
1. 回顾你从体验中学到的知识。

你将使用的示例图形表示具有两个楼层和两个房间的建筑。 Floor0 包含 Room0，Floor1 包含 Room1。 该图形将如下图像所示：

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="四个圆形节点组成的图形的屏幕截图，这些节点在 Azure Digital Twins Explorer 中由箭头连接。":::

>[!NOTE]
>本快速入门介绍如何浏览预生成的图形，以了解 Azure 数字孪生表示数据的方式。 为简单起见，本快速入门不介绍如何设置 IoT 中心设备与其图形表示形式之间的连接。 若要为图形设置连接的端到端流，请继续阅读教程：[连接端到端解决方案](tutorial-end-to-end.md)。

## <a name="prerequisites"></a>必备知识

要完成本快速入门，你需要一个 Azure 订阅。 如果还没有 Azure 订阅，可立即[免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

对于在快速入门中使用的示例图形，你还需要下载相关资料。 按照以下链接和说明，从 [GitHub 存储库 digital-twins-explorer](https://github.com/Azure-Samples/digital-twins-explorer) 中下载 3 个必需文件。
* [Room.js](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Room.json)：导航到链接，右键单击屏幕上的任意位置，然后在浏览器的右键单击菜单中选择“另存为”。 使用下面的“另存为”窗口将文件保存在计算机上的某个位置，文件名为“Room.json”。
* [Floor.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Floor.json)：导航到链接，右键单击屏幕上的任意位置，然后在浏览器的右键单击菜单中选择“另存为”。 使用下面的“另存为”窗口将文件保存在 Room.json 所在的位置，文件名为“Floor.json” 。
* [buildingScenario.xlsx](https://github.com/Azure-Samples/digital-twins-explorer/blob/main/client/examples/buildingScenario.xlsx)：导航到链接，然后选择“下载”按钮。 这会将该文件下载到默认下载位置。

    :::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png" alt-text="GitHub 中的 digital-twins-explorer/client/examples/buildingScenario.xlsx 文件的屏幕截图。其中的“下载”按钮突出显示。" lightbox="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png":::

## <a name="set-up-azure-digital-twins"></a>设置 Azure 数字孪生

要使用 Azure 数字孪生，第一步是创建一个 Azure 数字孪生实例。 创建服务的实例后，可在 Azure Digital Twins Explorer 中连接到该实例。在本快速入门中，从前到后你都要使用 Azure Digital Twins Explorer 来操作该实例。

本部分的其余部分将指导你完成这些步骤。

### <a name="create-an-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

[!INCLUDE [digital-twins-setup-portal.md](../../includes/digital-twins-setup-portal.md)]

3. 填写安装程序的“基本信息”选项卡上的字段，包括订阅、资源组、位置和新实例的资源名称。 选中“分配 Azure 数字孪生数据所有者角色”框，以便为你自己授予管理实例中数据的权限。

    >[!NOTE]
    > 如果“分配 Azure 数字孪生数据所有者角色”框灰显，则表示你在 Azure 订阅中无权管理用户对资源的访问权限。 你可以根据本部分所述继续创建实例，然后应在完成本快速入门的其余部分之前，让某个拥有所需权限的人员[在该实例上为你分配此角色](how-to-set-up-instance-portal.md#assign-the-role-using-azure-identity-management-iam)。
    >
    > 满足此要求的常见角色包括“所有者”、“帐户管理员”或“用户访问管理员”和“参与者”的组合。  

4. 选择“查看 + 创建”以完成实例创建。

    :::image type="content" source= "media/quickstart-azure-digital-twins-explorer/create-azure-digital-twins-basics.png" alt-text="Azure 门户中 Azure 数字孪生的“创建资源”过程的屏幕截图。已填写所描述的值。":::
    
5. 你将看到一个摘要页，其中显示了输入的详细信息。 确认这些信息，然后选择“创建”以创建实例。

随后你会转到“概述”页，可在其中跟踪实例的部署状态。

### <a name="open-instance-in-azure-digital-twins-explorer"></a>在 Azure Digital Twins Explorer 中打开实例

部署完实例后，使用“转到资源”按钮导航到门户中该实例的“概述”页。

:::image type="content" source= "media/quickstart-azure-digital-twins-explorer/deployment-complete.png" alt-text="Azure 门户中 Azure 数字孪生的部署页的屏幕截图。页面中指示部署已完成。":::

接下来，选择“打开 Azure Digital Twins Explorer (预览版)”按钮。

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="显示 Azure 数字孪生实例概述页面的 Azure 门户的屏幕截图。其中的“打开 Azure Digital Twins Explorer (预览版)”按钮突出显示。" lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

这将打开连接到实例的 Azure Digital Twins Explorer 窗口。

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Internet 浏览器中 Azure Digital Twins Explorer 的屏幕截图。" lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

## <a name="add-the-sample-data"></a>添加示例数据

接下来，需要将把示例方案和图形导入到 Azure Digital Twins Explorer 中。 可在[先决条件](#prerequisites)部分下载的“digital-twins-explorer-main”文件夹中找到示例方案。

### <a name="models"></a>模型

Azure 数字孪生解决方案的第一步是为你的环境定义词汇。 你需要创建自定义[模型](concepts-models.md)，这些模型用于描述环境中存在的实体类型。

每个模型都是用 JSON-LD 这样的语言编写的，这种语言称为数字孪生定义语言 (DTDL)。 每个模型根据属性、遥测、关系和组件来描述单个类型的实体   。 稍后，你将使用这些模型作为表示这些类型的特定实例的数字孪生的基础。

通常，在创建模型时，需要完成 3 个步骤：

1. 编写模型定义。 在本快速入门中，此步骤已作为示例解决方案的一部分完成。
1. 验证它以确保语法正确。 在本快速入门中，此步骤已作为示例解决方案的一部分完成。
1. 将它上传到 Azure 数字孪生实例。
 
本快速入门已为你编写和验证了模型文件。 它们包含在你下载的解决方案中。 在本部分中，你需要将两个预先编写的模型上传到实例，以定义构建环境的这些组件：

* Floor
* 会议室

#### <a name="upload-models"></a>上传模型

按照以下步骤上传模型。

1. 在“模型”面板中，选择“上传模型”图标，该图标显示一个指向云的箭头 。

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中突出显示“模型”面板和“上传模型”图标。" lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. 在显示的“打开”窗口中，导航到包含之前下载的 Room.json 和 Floor.json 文件的文件夹 。
1. 选择 Room.json 和 Floor.json，然后选择“打开”来将其上传  。 

Azure Digital Twins Explorer 会将这些模型文件上传到 Azure 数字孪生实例。 它们应在“模型”面板中显示，并显示其易记名称和完整的模型 ID。 可选择“查看模型”信息图标，查看其后面的 DTDL 代码。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="显示“模型”面板的 Azure Digital Twins Explorer 的屏幕截图，其中列出“楼层”和“房间”这两个模型的定义。" lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>孪生和孪生图

现在，一些模型已上传到 Azure 数字孪生实例，你可添加按照模型定义的数字孪生。

数字孪生表示你的商业环境中的实际实体。 它们可以是诸如农场上的传感器、汽车上的灯，或者（在本快速入门中）建筑楼层上的房间等实体。 你可以创建任意给定模型类型的许多孪生，例如都使用“房间”模型的多个房间。 将它们与关系连接到表示完整环境的“孪生图形”。

在本部分，将上传预先创建的孪生体，这些孪生体已连接到预先创建的图中。 该图包含两个楼层和两个房间，并通过下列布局连接：

* 楼层 0
    - 包含 Room0
* 楼层 1
    - 包含 Room1

#### <a name="import-the-graph"></a>导入图

请按照以下步骤导入图形。

1. 在“孪生图”面板中，选择“导入图形”图标，该图标显示一个指向云的箭头 。

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="显示“图形视图”面板的 Azure Digital Twins Explorer 的屏幕截图，其中突出显示“导入图形”图标。" lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. 在“打开”窗口中，导航到之前下载的 buildingScenario.xlsx 文件。 此文件包含示例图的说明。 选择“打开”  。

   几秒钟后，Azure Digital Twins Explorer 将打开“导入”视图，其中显示了要加载的图形的预览。

3. 若要确认图形上传，请选择图形预览面板右上角的“保存”图标。

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="在“图形预览”窗格中突出显示“保存”图标的 Azure Digital Twins Explorer 的屏幕截图。" lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Azure Digital Twins Explorer 将使用上传的文件来创建所请求的孪生体及其相互关系。 完成后，将出现一个对话框。 选择“关闭”。

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中有一个对话框指示图形导入成功。" lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. 该图现在已上传到 Azure Digital Twins Explorer。 切换回“孪生图”面板。
 
   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png" alt-text="突出显示“孪生图”选项卡的 Azure Digital Twins Explorer 的屏幕截图。" lightbox="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png"::: 
 
6. 若要查看该图，请在 Azure Digital Twins Explorer 窗口顶部附近的“查询资源管理器”面板中选择“运行查询”按钮 。

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="突出显示窗口右上角的“运行查询”按钮的 Azure Digital Twins Explorer 的屏幕截图。" lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

该操作将运行默认查询以选择并显示所有数字孪生。 Azure Digital Twins Explorer 将从服务中检索所有孪生和关系。 它将在“孪生图”面板中绘制它们定义的图形。

## <a name="explore-the-graph"></a>浏览图

现在，你可看到已上传的示例方案图形。

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中显示包含孪生图的“图形视图”面板。":::

这些圆（图形“节点”）表示数字孪生。 这些线条表示关系。 Floor0 孪生包含 Room0，Floor1 孪生包含 Room1   。

如果使用的是鼠标，可以拖动图的各个部分来移动它们。

### <a name="view-twin-properties"></a>查看孪生属性

可选择一个孪生体，在“属性”面板中查看其属性及其值的列表。

下面是房间 0 的属性：

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="突出显示“属性”窗格的 Azure Digital Twins Explorer 的屏幕截图，其中显示 Room0 的 $dtId、Temperature 和 Humidity 属性。" lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 的温度为 70。

下面是房间 1 的属性：

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="突出显示“属性”窗格的 Azure Digital Twins Explorer 的屏幕截图，其中显示 Room1 的 $dtId、Temperature 和 Humidity 属性。" lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 的温度为 80。

### <a name="query-the-graph"></a>查询图形

Azure 数字孪生的主要功能是能够轻松有效地[查询](concepts-query-language.md)孪生图，以解答有关环境的问题。

查询图中孪生的一种方法是按其属性进行查询。 基于属性的查询可以帮助回答各种问题。 例如，可以在你的环境中找到需要注意的离群值。

在本部分中，你将运行一个查询来回答你的环境中有多少孪生的温度高于 75。

若要查看答案，请在“查询资源管理器”面板中运行以下查询。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

回想一下之前查看过的孪生属性，Room0 的温度为 70，Room1 的温度为 80。 因此，此处的结果中仅显示 Room1。
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="显示属性查询结果的 Azure Digital Twins Explorer 的屏幕截图，其中只显示 Room1。" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> 之前的查询还支持其他比较运算符（<、>、=，或 !=）。 你可尝试将这些运算符、不同的值或不同的孪生属性插入到查询中来试着回答自己的问题。

## <a name="edit-data-in-the-graph"></a>编辑图中的数据

可使用 Azure Digital Twins Explorer 来编辑图中表示的孪生的属性。 在本部分中，我们将 Room0 的温度调高到 76。

首先，重新运行以下查询以选择所有数字孪生体。 这将在“孪生图”面板中再次显示完整的图。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

选择“Room0”，在“属性”面板中调出其属性列表 。

此列表中的属性是可编辑的。 选择温度值 70 来输入新值。 输入 76，然后选择“保存”图标，将温度更新为 76  。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中突出显示“属性”面板上有可为 Room0 编辑的属性。" lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

现在，你将看到“补丁信息”窗口，其中显示了在后台用于 Azure 数字孪生 [API](concepts-apis-sdks.md) 进行更新的补丁代码。 选择“关闭”。

### <a name="query-to-see-the-result"></a>查询以查看结果

若要验证该图是否已成功将更新内容注册为 Room0 的温度，请重新运行先前进行的查询，获取环境中温度高于 75 的所有孪生。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

现在，房间 0 的温度已从 70 更改为 76，因此两个孪生都应出现在结果中 。

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="显示属性查询结果的 Azure Digital Twins Explorer 的屏幕截图，其中显示 Room0 和 Room1。" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>回顾所学内容并将其置于上下文中进行理解

在本快速入门中，你创建了一个 Azure 数字孪生实例，并在 Azure Digital Twins Explorer 中使用示例方案对其进行了填充。

然后，你探索了图，方法是：

* 使用查询以回答有关方案的问题。
* 编辑数字孪生上的属性。
* 再次运行查询，查看答案如何因更新而发生变化。

本练习的目的是演示如何使用 Azure 数字孪生图回答有关环境的问题（即使在环境持续变化时）。

在本快速入门中，你手动更新了温度。 在 Azure 数字孪生中，经常会将数字孪生连接到实际的 IoT 设备，以便它们能够根据遥测数据自动接收更新。 通过这种方式，你可以构建始终反映环境真实状态的实时图。 你可以使用查询来实时获取环境中正在发生的事情的信息。

## <a name="clean-up-resources"></a>清理资源

若要在本快速入门完成后进行清理，请根据接下来要执行的操作选择要删除的资源。

* 如果你计划继续学习 Azure 数字孪生教程，可将本快速入门中使用的实例重复用于那些文章，无需将它们删除。

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

你可能还需要从本地计算机中删除示例项目文件夹。

## <a name="next-steps"></a>后续步骤

接下来，请继续学习 Azure 数字孪生教程，生成你自己的 Azure 数字孪生方案和交互工具。

> [!div class="nextstepaction"]
> [为客户端应用编写代码](tutorial-code.md)
