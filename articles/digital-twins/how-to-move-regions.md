---
title: 将实例移动到不同的 Azure 区域
titleSuffix: Azure Digital Twins
description: 请参阅如何将 Azure 数字孪生实例从一个 Azure 区域移到另一个。
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e268cca87479625af023b5970bb27c56721f6d39
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049842"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>将 Azure 数字孪生实例移动到不同的 Azure 区域

如果需要将 Azure 数字孪生实例从一个区域移到另一个区域，当前过程是在新区域中重新创建资源，然后删除原始资源。 在此过程结束时，你将使用与第一个相同的新 Azure 数字孪生实例，但更新的位置除外。

本文提供了有关如何执行完整的移动和复制以使新实例与原始实例相匹配的指导。

此过程包括下列步骤：

1. 准备：下载原始模型、孪生和图形。
1. 移动：在新区域中创建新的 Azure 数字孪生实例。
1. 移动：重新填充新的 Azure 数字孪生实例。
    - 上传原始模型、孪生和 graph。
    - 重新创建终结点和路由。
    - 重新链接连接的资源。
1. 清理源资源：删除原始实例。

## <a name="prerequisites"></a>先决条件

在尝试重新创建 Azure 数字孪生实例之前，请先查看原始实例的组件，以明确了解需要重新创建的所有部分。

下面是一些需要考虑的问题：

* 上载到我的实例的 *模型* 是什么？ 有多少？
* 我的实例中有哪些 *孪生* ？ 有多少？
* *图形* 在我的实例中的常规形状是什么？ 有多少关系？
* 我的实例中有哪些 *终结点* ？
* 我的实例中有哪些 *路由* ？ 它们是否具有筛选器？
* 我的实例在何处 *连接到其他 Azure 服务*？ 一些常见集成点包括：

    - Azure 事件网格、Azure 事件中心或 Azure 服务总线
    - Azure Functions
    - Azure 逻辑应用
    - Azure 时序见解
    - Azure Maps
    - Azure IoT 中心设备预配服务
* 连接到我的实例还有哪些其他 *个人或公司应用* ？

可以使用 [Azure 门户](https://portal.azure.com)、 [Azure 数字孪生 api 和 Sdk](how-to-use-apis-sdks.md)、 [azure 数字孪生 CLI 命令](how-to-use-cli.md)或 [azure 数字孪生资源管理器](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 示例来收集此信息。

## <a name="prepare"></a>准备

在本部分中，你将准备通过从原始实例下载原始模型、孪生和图形来重新创建实例。 本文使用 [Azure 数字孪生资源管理器](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 示例来执行此任务。

>[!NOTE]
>你的实例中可能已有包含模型或关系图的文件。 如果是这样，则您无需再次下载所有内容，只需下载这些文件后所丢失的部分或可能已更改的内容。 例如，你可能有使用新数据更新的孪生。

### <a name="limitations-of-azure-digital-twins-explorer"></a>Azure 数字孪生 Explorer 的限制

[Azure 数字孪生资源管理器示例](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)是一个客户端应用示例，支持图形的可视化表示形式，并提供与实例的可视化交互。 本文介绍如何使用它来下载和更高版本的重新上传、模型、孪生和图形。

此示例不是一个完整的工具。 它未进行压力测试，不是为处理大量图形而构建的。 因此，请记住以下现成的示例限制：

* 该示例当前仅在图形大小上经过测试，最多可达1000节点和2000关系。
* 如果出现任何间歇性故障，示例不支持重试。
* 如果上传的数据不完整，则该示例不一定通知用户。
* 该示例不处理由于非常大的图形超出内存等可用资源而导致的错误。

如果该示例无法处理图形的大小，则可以使用其他 Azure 数字孪生开发人员工具导出和导入图形：

* [Azure 数字孪生 CLI 命令](how-to-use-cli.md)
* [Azure 数字孪生 Api 和 Sdk](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>设置 Azure 数字孪生资源管理器应用程序

若要继续 Azure 数字孪生资源管理器，请先下载示例应用程序代码，并将其设置为在计算机上运行。

若要获取示例，请参阅 [Azure 数字孪生资源管理器](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 选择 " **下载 ZIP** " 按钮，将此示例代码的 .zip 文件作为 **Azure_Digital_Twins__ADT__explorer.zip** 下载到计算机。 解压缩文件。

接下来，设置和配置 Azure 数字孪生资源管理器的权限。 按照 Azure 数字孪生快速入门中的 [设置 Azure 数字孪生和 Azure 数字孪生资源管理器](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) 部分中的说明进行操作。 本部分将指导你完成以下步骤：

1. 设置 Azure 数字孪生实例。 你可以跳过此部分，因为你已有一个实例。
1. 设置本地 Azure 凭据以提供对实例的访问权限。
1. 运行 Azure 数字孪生资源管理器，并将其配置为连接到实例。 你将使用你要移动的原始 Azure 数字孪生实例的 *主机名* 。

现在，你应该在计算机上的浏览器中运行 Azure 数字孪生资源管理器示例应用。 该示例应连接到原始 Azure 数字孪生实例。

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="显示在 localhost：3000运行的应用的浏览器窗口。该应用程序称为 Azure 数字孪生资源管理器，其中包含用于查询资源管理器、模型视图、图形视图和属性资源管理器的框。尚未提供任何屏幕数据。" lightbox="media/how-to-move-regions/explorer-blank.png":::

若要验证连接，请在 "**图形资源管理器**" 框中选择 "**运行查询**" 按钮，以运行在图形中显示所有孪生和关系的默认查询。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="在窗口右上角显示一个按钮 &quot;运行查询&quot; 按钮。" lightbox="media/how-to-move-regions/run-query.png":::

你可以让 Azure 数字孪生 Explorer 运行，因为稍后将在本文中再次使用它将这些项重新上传到目标区域中的新实例。

### <a name="download-models-twins-and-graph"></a>下载模型、孪生和图形

接下来，将解决方案中的模型、孪生和 graph 下载到计算机。

要一次下载所有这些项，首先请确保整个关系图显示在 **图形视图** 框中。 如果尚未显示完整的关系图，请 `SELECT * FROM digitaltwins` 在 " **查询资源管理器** " 框中重新运行的默认查询。
 
然后，在 **关系图视图** 框中选择 "**导出关系图**" 图标。

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="在 &quot;关系图视图&quot; 框中，将突出显示图标。它会显示一个指向云的下拉箭头。" lightbox="media/how-to-move-regions/export-graph.png":::

此操作将启用 "**图形视图**" 框中的 **下载** 链接。 选择此项可下载包含模型、孪生和关系的基于 JSON 的查询结果表示形式。 此操作应将一个 json 文件下载到你的计算机。

>[!NOTE]
>如果下载的文件似乎具有不同的文件扩展名，请尝试直接编辑扩展并将其更改为 json。

## <a name="move"></a>移动

接下来，通过在目标区域中创建一个新实例，完成实例的 "移动"。 然后，将用原始实例中的数据和组件来填充它。

### <a name="create-a-new-instance"></a>创建新实例

首先，在目标区域中创建 Azure 数字孪生的新实例。 按照 " [操作方法：设置实例和身份验证](how-to-set-up-instance-portal.md)" 中的步骤进行操作。 请记住以下几点：

* *如果* 新实例位于不同的资源组中，则可以为其保留相同的名称。 如果需要使用包含原始实例的相同资源组，则新实例将需要其自己的不同名称。
* 提示输入位置时，输入新的目标区域。

完成此步骤后，你将需要新实例的主机名，以便继续对你的数据进行设置。 如果在安装过程中未记下主机名，请按照 [以下说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) 立即从 Azure 门户获取该名称。

### <a name="repopulate-the-old-instance"></a>重新填充旧实例

接下来，将设置新的实例，使其成为原始实例的副本。

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>使用 Azure 数字孪生资源管理器上传原始模型、孪生和图形

在本部分中，可以将模型、孪生和图形重新上传到新的实例。 如果原始实例中没有任何模型、孪生或图形，或者不想将它们移到新实例，则可以跳到 [下一部分](#re-create-endpoints-and-routes)。

否则，请返回到运行 Azure 数字孪生资源管理器的浏览器窗口，并执行以下步骤。

##### <a name="connect-to-the-new-instance"></a>连接到新实例

目前，Azure 数字孪生资源管理器已连接到原始 Azure 数字孪生实例。 通过选择窗口右上角的 " **登录** " 按钮，将连接切换为指向新的实例。

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure 数字孪生 Explorer 突出显示了窗口右上角的 &quot;登录&quot; 图标。此图标显示了一个简单的使用密钥剪影叠加的人员的影像。" lightbox="media/how-to-move-regions/sign-in.png":::

替换 **ADT URL** 以反映新的实例。 更改此值，使其读取 *https：//{new 实例主机名}*。

选择“连接”  。 系统可能会要求你用 Azure 凭据重新登录，或者向此应用程序授予此应用程序的许可。

##### <a name="upload-models-twins-and-graph"></a>上载模型、孪生和图形

接下来，将之前下载的解决方案组件上传到新实例。

若要上载模型、孪生和关系图，请在 "**关系图" 视图** 框中选择 "**导入关系图**" 图标。 此选项一次上传所有这三个组件。 它甚至会上传图形中当前未使用的模型。

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="在“图形视图”框中，一个图标突出显示。它显示了一个指向云的箭头。" lightbox="media/how-to-move-regions/import-graph.png":::

在 "文件选择器" 框中，切换到下载的图形。 选择 **"graph"** 文件，然后选择 " **打开**"。

几秒钟后，Azure 数字孪生资源管理器将打开一个 **导入** 视图，其中显示了要加载的图形的预览。

若要确认图形上传，请选择“图形视图”框右上角的“保存”图标 。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="在“图形预览”窗格中突出显示“保存”图标。" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Azure 数字孪生 Explorer 现在会将模型和图形 (上传到新的 Azure 数字孪生实例的孪生和关系) 。 应该会看到一条成功消息，指出已上传的模型、孪生和关系的数量。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="指示关系图导入成功的对话框。它读取 &quot;导入成功&quot;。已导入2个模型。4孪生已导入。已导入2个关系。 &quot;" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

若要验证是否已成功上传所有内容，请选择 "**图形资源管理器**" 框中的 "**运行查询**" 按钮，以运行在图形中显示所有孪生和关系的默认查询。 此操作还会刷新 " **模型视图** " 框中的模型列表。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="在窗口右上角的 &quot;运行查询&quot; 按钮周围突出显示。" lightbox="media/how-to-move-regions/run-query.png":::

应该会看到图形，其中显示了 " **图形资源管理器** " 框中显示的所有孪生和关系。 您还应在 " **模型视图** " 框中看到您的模型。

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Azure 数字孪生资源管理器的视图显示两个模型，其中突出显示了 &quot;模型视图&quot; 框和图形资源管理器框中突出显示的关系图。" lightbox="media/how-to-move-regions/post-upload.png":::

这些视图确认已将模型、孪生和 graph 重新上传到目标区域中的新实例。

#### <a name="re-create-endpoints-and-routes"></a>重新创建终结点和路由

如果原始实例中有终结点或路由，则需要在新实例中重新创建它们。 如果原始实例中没有任何终结点或路由，或者不想将它们移到新的实例中，则可以跳到 [下一部分](#relink-connected-resources)。

否则，请按照 [操作方法：使用新实例管理终结点和路由](how-to-manage-routes-portal.md) 中的步骤操作。 请记住以下几点：

* *不* 需要重新创建要用于终结点的事件网格、事件中心或服务总线资源。 有关详细信息，请参阅终结点说明中的 "先决条件" 部分。 你只需在 Azure 数字孪生实例上重新创建终结点。
* 你可以重复使用终结点和路由名称，因为它们的作用域限定为不同的实例。
* 请记住，将任何所需的筛选器添加到所创建的路由。

#### <a name="relink-connected-resources"></a>重新链接连接的资源

如果有其他应用或 Azure 资源连接到原始 Azure 数字孪生实例，则需要编辑连接，使其转到新实例。 这些资源可能包括其他 Azure 服务、已设置为使用 Azure 数字孪生的个人或公司应用。

如果没有任何其他资源连接到原始实例，或者不想将它们移到新的实例，则可以跳到 [下一部分](#verify)。

否则，请考虑方案中的连接资源。 无需删除并重新创建任何连接的资源。 相反，你只需通过其主机名编辑它们连接到 Azure 数字孪生实例的点。 然后更新这些点，以使用新实例的主机名而不是原始实例。

你需要编辑的确切资源取决于你的方案，但以下是一些常见的集成点：

* Azure Functions。 如果 Azure 函数的代码包含原始实例的主机名，则应将此值更新为新实例的主机名，然后重新发布该函数。
* 事件网格、事件中心或服务总线。
* 逻辑应用。
* 时序见解。
* Azure Maps。
* IoT 中心设备预配服务。
* Azure 之外的个人或公司应用，例如在教程中创建的客户端应用，例如 [：编写](tutorial-code.md)用于连接到实例的客户端应用，以及调用 Azure 数字孪生 api。
* *无* 需重新创建 Azure AD 应用注册。 如果你正在使用 [应用注册](how-to-create-app-registration.md) 来连接到 Azure 数字孪生 api，则可以将相同的应用注册与新的实例一起使用。

完成此步骤后，目标区域中的新实例应为原始实例的副本。

## <a name="verify"></a>Verify

若要验证是否已正确设置了新的实例，请使用以下工具：

* [Azure 门户](https://portal.azure.com)。 门户非常适合用于验证新实例是否存在以及是否位于正确的目标区域。 它也适用于验证终结点和路由以及与其他 Azure 服务的连接。
* [Azure 数字孪生 CLI 命令](how-to-use-cli.md)。 这些命令可用于验证新实例是否存在以及是否位于正确的目标区域。 它们还可用于验证实例数据。
* [Azure 数字孪生资源管理器](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 Azure 数字孪生资源管理器适用于验证实例数据（如模型、孪生和图形）。
* [Azure 数字孪生 api 和 sdk](how-to-use-apis-sdks.md)。 这些资源适合用于验证实例数据，如模型、孪生和图形。 它们也适用于验证终结点和路由。

你还可以尝试运行你的原始实例运行的任何自定义应用或端到端流，以帮助你验证它们是否能与新实例正常工作。

## <a name="clean-up-source-resources"></a>清理源资源

现在，你的新实例已在目标区域中使用原始实例的数据和连接的副本进行了设置，你可以删除原始实例。

您可以使用 [Azure 门户](https://portal.azure.com)、 [Azure CLI](how-to-use-cli.md)或 [控制平面 api](how-to-use-apis-sdks.md#overview-control-plane-apis)。

若要使用 Azure 门户删除实例，请在浏览器窗口中 [打开门户](https://portal.azure.com) ，并通过在门户搜索栏中搜索名称来切换到原始 Azure 数字孪生实例。

选择 " **删除** " 按钮，然后按照提示完成删除操作。

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Azure 门户的 &quot;概述&quot; 选项卡上的 Azure 数字孪生实例详细信息的视图。将突出显示 &quot;删除&quot; 按钮。":::