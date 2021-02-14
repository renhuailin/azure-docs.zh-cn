---
title: 快速入门 - 探索示例方案
titleSuffix: Azure Digital Twins
description: 快速入门 - 使用 ADT Explorer 示例直观显示和探索预生成方案。
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 9d09bca246938f972a212f7ee71f03a618e16ac4
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575666"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>快速入门 - 使用 ADT Explorer 探索示例 Azure 数字孪生方案

通过 Azure 数字孪生，可创建真实环境的实时模型并与之交互。 首先，将单个元素建模为“数字孪生”。 然后将它们连接到一个知识图，该图可以响应实时事件并查询信息。

在本快速入门中，你将通过名为 [Azure 数字孪生 (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 的示例应用程序来探索预生成的 Azure 数字孪生图形。 使用 ADT Explorer 可以执行以下操作：

- 上传环境的数字表示形式。
- 查看为表示 Azure 数字孪生中的环境而创建的孪生和图的可视图像。
- 通过基于浏览器的视觉体验执行其他管理活动。

本快速入门包含以下主要步骤：

1. 设置 Azure 数字孪生实例和 ADT Explorer。
1. 上传预生成的模型和图形数据来构造示例方案。
1. 探索已创建的方案图形。
1. 对图形进行更改。

你将使用的示例图形表示具有两个楼层和两个房间的建筑。 该图形将如下图像所示：

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="由箭头连接的 4 个圆形节点组成的图形视图。标记为“Floor1”的圆通过一个标记为“contains”的箭头连接到一个标记为“Room1”的圆。标记为“Floor0”的圆通过一个标记为“contains”的箭头连接到一个标记为“Room0”的圆。未连接“Floor1”和“Floor0”。":::

## <a name="prerequisites"></a>必备知识

要完成本快速入门，你需要一个 Azure 订阅。 如果还没有 Azure 订阅，可立即[免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

还需要在计算机上有 Node.js。 若要获取最新版本，请参阅 [Node.js](https://nodejs.org/)。

最后，还需要下载要在快速入门期间使用的示例. 示例应用程序是 ADT Explorer。 此示例包含快速入门中用于加载和探索 Azure 数字孪生方案的应用。 它还包含示例方案文件。 若要获取示例，请转到 [Azure 数字孪生 (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 选择“下载 ZIP”按钮，将此示例代码的 .zip 文件下载到计算机。 解压缩 Azure_Digital_Twins__ADT__explorer.zip 文件夹，并提取文件。

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>设置 Azure 数字孪生和 ADT 资源管理器

要使用 Azure 数字孪生，第一步是设置 Azure 数字孪生实例。 创建服务的实例并设置凭据以向 ADT Explorer 进行身份验证后，可以在 ADT Explorer 中连接到该实例，并且稍后可在快速入门中使用示例数据填充该实例。

本部分的其余部分将指导你完成这些步骤。

### <a name="set-up-an-azure-digital-twins-instance"></a>设置 Azure 数字孪生实例

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>设置本地 Azure 凭据

当你在本地计算机上运行示例时，ADT Explorer 应用程序使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet)（属于 `Azure.Identity` 库的一部分）对用户进行 Azure 数字孪生实例验证。 若要详细了解客户端应用可向 Azure 数字孪生进行身份验证的不同方法，请参阅[编写应用身份验证代码](how-to-authenticate-client.md)。

借助此身份验证类型，ADT Explorer 将在本地环境中搜索凭据，如本地 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 或 Visual Studio 或 Visual Studio Code 中的 Azure 登录名。 因此，你应该通过这些机制之一在本地登录 Azure，以便设置 ADT Explorer 应用的凭据。

如果已通过其中一种方式登录到 Azure，则可以跳到[下一部分](#run-and-configure-adt-explorer)。

否则，可以使用以下步骤安装本地 Azure CLI：

1. 按照[此安装链接](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)上的过程进行操作，完成与你的操作系统相匹配的安装。
1. 在计算机上打开控制台窗口。
1. 运行 `az login` 并按照身份验证提示操作以登录到 Azure 帐户。
1. 可能执行的最后一步：如果在此帐户下使用多个 Azure 订阅，请运行 `az account set --subscription "<your-subscription-name-or-ID>"` 来将身份验证上下文设置为包含 Azure 数字孪生实例的 Azure 订阅（订阅的名称或 ID 值也有效）。

登录后，在下一部分中运行 ADT Explorer 时，它会自动获取 Azure 凭据。

如果需要，可以关闭身份验证控制台窗口。 或者，可以使其保持打开状态以供下一步使用。

### <a name="run-and-configure-adt-explorer"></a>运行并配置 ADT 资源管理器

接下来，运行 ADT 资源管理器应用程序，并为 Azure 数字孪生实例配置该应用程序。

1. 转到已下载并解压缩的 Azure_Digital_Twins__ADT__explorer 文件夹。
在文件夹位置 Azure_Digital_Twins__ADT__explorer/client/src 打开控制台窗口。

1. 运行 `npm install`，下载所有必需的依赖项。

1. 运行 `npm run start` 启动该应用。

   几秒钟后，将打开一个浏览器窗口，应用将显示在浏览器中。

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="显示在 localhost:3000 运行的应用的浏览器窗口。此应用名为 ADT Explorer，其中包含查询资源管理器、模型视图、图形视图和属性资源管理器所对应的框。屏幕上尚无数据。" lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. 选择窗口右上角的“登录”按钮（如下图所示），以将 ADT Explorer 配置为使用已设置的实例。

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="在窗口顶部附近突出显示“登录”图标的 ADT 资源管理器。图标显示了一个简单的人员与密钥叠加的剪影。" lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. 输入之前在[设置 Azure 数字孪生实例](#set-up-an-azure-digital-twins-instance)部分中收集的 Azure 数字孪生实例 URL，格式为 https://{instance host name}。

>[!NOTE]
> 可随时重新访问或编辑此信息，方法是选择相同图标以再次打开“登录”框。 它将保留你传入的值。

> [!TIP]
> 如果在连接时显示 `SignalRService.subscribe` 错误消息，请确保 Azure 数字孪生 URL 以“https://”开头。

如果你看到来自 Microsoft 的“权限已请求”弹出窗口，请向此应用程序授予同意并接受以继续。

## <a name="add-the-sample-data"></a>添加示例数据

接下来，需要将把示例方案和图形导入到 ADT Explorer 中。 还可在你先前下载的 Azure_Digital_Twins__ADT__explorer 文件夹中找到该示例方案。

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

1. 在“模型视图”框中，选择“上传模型”图标 。

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="在“模型视图”框中，中间图标突出显示。它显示了一个指向云的箭头。" lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. 在出现的“文件选择器”框中，转到下载的存储库中的 Azure_Digital_Twins__ADT__explorer/client/examples 文件夹。
1. 选择“Room.json”和“Floor.json”，然后选择“确定”  。 可根据需要上传其他模型，但在本快速入门中不会用到它们。
1. 按照弹出对话框进行操作，该对话框要求你登录到 Azure 帐户。

>[!NOTE]
>如果出现下面的错误消息：:::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="弹出框显示“错误”：提取模型时出错:ClientAuthError:打开弹出窗口时出错。如果你使用的是 IE 或浏览器中阻止了弹出窗口，则可能会发生这种情况。”，底部带有“关闭”按钮。" border="false"::: 
> 请尝试禁用弹出窗口阻止程序或使用其他浏览器。

现在，ADT Explorer 会将这些模型文件上传到 Azure 数字孪生实例。 它们应在“模型视图”框中显示，并显示其友好名称和完整模型 ID。 可选择“查看模型”信息图标，查看其后面的 DTDL 代码。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="“模型视图”框的视图，其中列出了两个模型定义，分别是楼层 (dtmi:example:Floor;1) 和房间 (dtmi:example:Room;1)。“查看模型信息”图标，其中每个模型都突出显示了一个带圆圈的字母“i”。" lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>孪生和孪生图

现在，一些模型已上传到 Azure 数字孪生实例，你可添加按照模型定义的数字孪生。

数字孪生表示你的商业环境中的实际实体。 它们可以是诸如农场上的传感器、汽车上的灯，或者（在本快速入门中）建筑楼层上的房间等实体。 你可以创建任意给定模型类型的许多孪生，例如都使用“房间”模型的多个房间。 将它们与关系连接到表示完整环境的“孪生图形”。

在本部分，将上传预先创建的孪生，而这些孪生已连接到预先创建的图中。 该图包含两个楼层和两个房间，并通过下列布局连接：

* 楼层 0
    - 包含 Room0
* 楼层 1
    - 包含 Room1

#### <a name="import-the-graph"></a>导入图

请按照以下步骤导入图形。

1. 在“图形视图”框中，选择“导入图形”图标 。

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="在“图形视图”框中，一个图标突出显示。它显示了一个指向云的箭头。" lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. 在“文件选择器”框中，转到 Azure_Digital_Twins__ADT__explorer/client/examples 文件夹，然后选择 buildingScenario.xlsx 电子表格文件 。 此文件包含示例图的说明。 选择“确定”。

   几秒钟后，ADT Explorer 将打开“导入”视图，其中显示了要加载的图形的预览。

3. 若要确认图形上传，请选择“图形视图”框右上角的“保存”图标 。

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="在“图形预览”窗格中突出显示“保存”图标。" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. 现在，ADT Explorer 将使用上传的文件来创建所请求的孪生及其相互之间的关系。 完成后，将出现一个对话框。 选择“关闭”。

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="指示图形导入成功的对话框。它显示为“导入成功。已导入 4 对孪生。已导入 2 对关系。”" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. 该图现在已上传到 ADT 资源管理器。 若要查看该图，请在 ADT Explorer 窗口顶部附近的“Graph 浏览器”框中选择“运行查询”按钮 。

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="突出显示了窗口右上角的“运行查询”按钮。" lightbox="media/quickstart-adt-explorer/run-query.png":::

该操作将运行默认查询以选择并显示所有数字孪生。 ADT Explorer 将从服务中检索所有孪生和关系。 它将在“图形视图”框中绘制它们定义的图形。

## <a name="explore-the-graph"></a>浏览图

现在，你可看到已上传的示例方案图形。

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="“图形视图”框的视图，其中包含孪生图。标记为“floor1”的圆通过一个标记为“contains”的箭头连接到一个标记为“room1”的圆。标记为“floor0”的圆通过一个标记为“contains”的箭头连接到一个标记为“room0”的圆。":::

这些圆（图形“节点”）表示数字孪生。 这些线条表示关系。 Floor0 孪生包含 Room0，Floor1 孪生包含 Room1   。

如果使用的是鼠标，可以拖动图的各个部分来移动它们。

### <a name="view-twin-properties"></a>查看孪生属性

可选择一个孪生，在“属性资源管理器”框中查看其属性及其值的列表。

下面是房间 0 的属性：

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="显示 Room0 的属性的“属性资源管理器”框周围运用了突出显示，其中包括 Room0 的“$dtId”字段、“温度”字段 70 和“湿度”字段 30 等等。" lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 的温度为 70。

下面是房间 1 的属性：

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="显示 Room1 的属性的“属性资源管理器”框周围运用了突出显示，其中包括“Room1”的 $dtId 字段、“温度”字段 80 和“湿度”字段 60 等等。" lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 的温度为 80。

### <a name="query-the-graph"></a>查询图形

Azure 数字孪生的主要功能是能够轻松有效地[查询](concepts-query-language.md)孪生图，以解答有关环境的问题。

查询图中孪生的一种方法是按其属性进行查询。 基于属性的查询可以帮助回答各种问题。 例如，可以在你的环境中找到需要注意的离群值。

在本部分中，你将运行一个查询来回答你的环境中有多少孪生的温度高于 75。

若要查看答案，请在“查询资源管理器”框中运行以下查询。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

回想一下之前查看过的孪生属性，Room0 的温度为 70，Room1 的温度为 80。 因此，此处的结果中仅显示 Room1。
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="属性查询的结果，其中仅显示 Room1。" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> 之前的查询还支持其他比较运算符（<、>、=，或 !=）。 你可尝试将这些运算符、不同的值或不同的孪生属性插入到查询中来试着回答自己的问题。

## <a name="edit-data-in-the-graph"></a>编辑图中的数据

可使用 ADT 资源管理器来编辑图中表示的孪生的属性。 在本部分中，我们将 Room0 的温度调高到 76。

首先，请选择“Room0”，然后在“属性资源管理器”框中调出其属性列表 。

此列表中的属性是可编辑的。 选择温度值 70 来输入新值。 输入 76，然后选择“保存”图标，将温度更新为 76  。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="显示 Room0 的属性的“属性资源管理器”框。温度值为一个显示 76 的可编辑框，“保存”图标周围突出显示。" lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

现在，你将看到“补丁信息”窗口，其中显示了在后台用于 Azure 数字孪生 [API](how-to-use-apis-sdks.md) 进行更新的补丁代码。 选择“关闭”。

### <a name="query-to-see-the-result"></a>查询以查看结果

若要验证该图是否已成功将更新内容注册为 Room0 的温度，请重新运行先前进行的查询，获取环境中温度高于 75 的所有孪生。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

现在，房间 0 的温度已从 70 更改为 76，因此两个孪生都应出现在结果中 。

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="属性查询的结果，其中同时显示了 Room0 和 Room1。" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>回顾所学内容并将其置于上下文中进行理解

在本快速入门中，你创建了一个 Azure 数字孪生实例、将它连接到了 ADT 资源管理器，并使用示例方案对其进行了填充。

然后，你探索了图，方法是：

* 使用查询以回答有关方案的问题。
* 编辑数字孪生上的属性。
* 再次运行查询，查看答案如何因更新而发生变化。

本练习的目的是演示如何使用 Azure 数字孪生图回答有关环境的问题（即使在环境持续变化时）。

在本快速入门中，你手动更新了温度。 在 Azure 数字孪生中，经常会将数字孪生连接到实际的 IoT 设备，以便它们能够根据遥测数据自动接收更新。 通过这种方式，你可以构建始终反映环境真实状态的实时图。 你可以使用查询来实时获取环境中正在发生的事情的信息。

## <a name="clean-up-resources"></a>清理资源

若要结束本快速入门的工作，请先结束正在运行的控制台应用。 此操作将关闭与浏览器中 ADT Explorer 应用的连接。 你将无法在浏览器中查看实时数据。 你可关闭浏览器标签页。

然后，可选择想要删除的资源，这具体取决于你接下来要执行哪些操作。

* 如果你计划继续学习 Azure 数字孪生教程，可将本快速入门中使用的实例重复用于那些文章，无需将它们删除。

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

你可能还需要从本地计算机中删除项目文件夹。

## <a name="next-steps"></a>后续步骤

接下来，请继续学习 Azure 数字孪生教程，生成你自己的 Azure 数字孪生方案和交互工具。

> [!div class="nextstepaction"]
> [教程：为客户端应用编写代码](tutorial-code.md)