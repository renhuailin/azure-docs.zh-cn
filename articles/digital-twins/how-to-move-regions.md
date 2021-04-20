---
title: 将实例移动到其他 Azure 区域
titleSuffix: Azure Digital Twins
description: 请参阅如何将 Azure 数字孪生实例从一个 Azure 区域移动到另一个 Azure 区域。
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e268cca87479625af023b5970bb27c56721f6d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102049842"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>将 Azure 数字孪生实例移动到其他 Azure 区域

如果需要将 Azure 数字孪生实例从一个区域移动到另一个区域，当前过程是在新区域中重新创建资源，然后删除原始资源。 此过程结束时，除了更新的位置以外，将使用的新 Azure 数字孪生实例与第一个实例完全相同。

本文提供了有关如何执行完整的移动和复制所需的全部数据以使新实例与原始实例相匹配的指导。

此过程包括下列步骤：

1. 准备：下载原始模型、孪生体和图形。
1. 移动：在新区域中创建新的 Azure 数字孪生实例。
1. 移动：重新填充新的 Azure 数字孪生实例。
    - 上传原始模型、孪生体和图形。
    - 重新创建终结点和路由。
    - 重新链接连接的资源。
1. 清理源资源：删除原始实例。

## <a name="prerequisites"></a>先决条件

在尝试重新创建 Azure 数字孪生实例之前，请先查看原始实例的组件，以明确了解需要重新创建的所有部分。

下面是一些需要考虑的问题：

* 实例中上传了哪些模型？ 有多少个模型？
* 实例中有哪些孪生体？ 有多少对孪生体？
* 实例中图形的常规形状是什么？ 有多少对关系？
* 实例中有哪些终结点？
* 实例中有哪些路由？ 它们是否具有筛选器？
* 实例在何处连接到其他 Azure 服务？ 常见集成点包括：

    - Azure 事件网格、Azure 事件中心或 Azure 服务总线
    - Azure Functions
    - Azure 逻辑应用
    - Azure 时序见解
    - Azure Maps
    - Azure IoT 中心设备预配服务
* 其他哪些个人或公司应用连接到实例？

可以使用 [Azure 门户](https://portal.azure.com)、[Azure 数字孪生 API 和 SDK](how-to-use-apis-sdks.md)、[Azure 数字孪生 CLI 命令](how-to-use-cli.md)或 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 示例来收集此信息。

## <a name="prepare"></a>准备

在本部分中，将准备通过从原始实例下载原始模型、孪生体和图形来重新创建实例。 本文使用 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 示例来执行此任务。

>[!NOTE]
>实例中可能已存在包含模型或图形的文件。 如果是这样，则无需再次下载所有内容，只需下载缺少的内容或者自初始上传这些文件以来可能已更改的内容。 例如，可能存在使用新数据更新的孪生体。

### <a name="limitations-of-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer 的限制

[Azure Digital Twins Explorer 示例](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)是客户端应用示例，支持图形的可视化表示形式，并提供与实例的可视化交互。 本文介绍如何使用它下载并在之后重新上传模型、孪生体和图形。

此示例不是完整的工具。 它未进行压力测试，不是为处理大型图形而构建。 因此，请记住以下现成的示例限制：

* 此示例当前仅针对包含至多 1000 个节点和 2000 对关系的图形大小进行过测试。
* 如果出现任何间歇性故障，此示例不支持重试。
* 此示例不一定通知用户上传的数据是否不完整。
* 此示例不处理因超大型图形超出内存等可用资源而导致的错误。

如果此示例无法处理这种大小的图形，则可以使用其他 Azure 数字孪生开发者工具导出和导入图形：

* [Azure 数字孪生 CLI 命令](how-to-use-cli.md)
* [Azure 数字孪生 API 和 SDK](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>设置 Azure Digital Twins Explorer 应用程序

若要继续使用 Azure Digital Twins Explorer，请先下载示例应用程序代码，并将其设置为在计算机上运行。

若要获取示例，请参阅 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 选择“下载 ZIP”按钮，将此示例代码的 .ZIP 文件作为 Azure_Digital_Twins__ADT__explorer.zip 下载到计算机 。 解压缩文件。

接下来，设置和配置使用 Azure Digital Twins Explorer 所需的权限。 遵照 Azure 数字孪生快速入门中的[设置 Azure 数字孪生和 Azure Digital Twins Explorer](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) 部分中的说明进行操作。 其中包括以下步骤：

1. 设置 Azure 数字孪生实例。 可跳过此部分，因为已拥有一个实例。
1. 设置本地 Azure 凭据以提供对实例的访问权限。
1. 运行 Azure Digital Twins Explorer，并将其配置为连接到实例。 将使用要移动的原始 Azure 数字孪生实例的主机名。

现在，计算机的浏览器中应运行 Azure Digital Twins Explorer 示例应用。 此示例应连接到原始 Azure 数字孪生实例。

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="显示在 localhost:3000 运行的应用的浏览器窗口。此应用名为 Azure Digital Twins Explorer，其中包含查询资源管理器、模型视图、图形视图和属性资源管理器所对应的框。屏幕上尚无数据。" lightbox="media/how-to-move-regions/explorer-blank.png":::

若要验证连接，请选择“Graph 浏览器”框中的“运行查询”按钮以运行默认查询，以便显示图形中的所有孪生体和关系。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="窗口右上角突出显示了“运行查询”按钮。" lightbox="media/how-to-move-regions/run-query.png":::

可以让 Azure Digital Twins Explorer 保持运行，因为本文稍后将再次使用它将这些项重新上传到目标区域中的新实例。

### <a name="download-models-twins-and-graph"></a>下载模型、孪生体和图形

接下来，将解决方案中的模型、孪生体和图形下载到计算机。

要一次下载所有这些项，请首先确保“图形视图”框中显示完整的图形。 如果尚未显示完整的图形，请在“查询资源管理器”框中重新运行 `SELECT * FROM digitaltwins` 的默认查询。
 
然后，选择“图形视图”框中的“导出图形”图标。

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="在“图形视图”框中，一个图标突出显示。它显示了一个从云中指向下的箭头。" lightbox="media/how-to-move-regions/export-graph.png":::

此操作将启用“图形视图”框中的下载链接。 选择它将下载基于 JSON 表示形式的查询结果，其中包括模型、孪生体和关系。 此操作会将一个 .json 文件下载到计算机。

>[!NOTE]
>如果下载的文件具有其他的文件扩展名，请尝试直接编辑扩展名并将其更改为 .json。

## <a name="move"></a>移动

接下来，通过在目标区域中创建一个新实例，完成实例的“移动”。 然后，使用原始实例中的数据和组件来填充它。

### <a name="create-a-new-instance"></a>创建新实例

首先，在目标区域中创建一个新的 Azure 数字孪生实例。 遵照[操作说明：设置实例和身份验证](how-to-set-up-instance-portal.md)中的步骤操作。 请记住以下几点：

* 如果新实例位于不同的资源组中，则可以为其保留相同的名称。 如果需要使用包含原始实例的相同资源组，则新实例需要一个不同的名称。
* 提示输入位置时，请输入新的目标区域。

完成此步骤后，需要新实例的主机名，以便使用数据继续进行设置。 如果设置过程中未记下主机名，请遵照[以下说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)立即从 Azure 门户获取该名称。

### <a name="repopulate-the-old-instance"></a>重新填充旧实例

接下来，设置新的实例，使其成为原始实例的副本。

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>使用 Azure Digital Twins Explorer 上传原始模型、孪生体和图形

在本部分中，可以将模型、孪生体和图形重新上传到新实例。 如果原始实例中没有任何模型、孪生体或图形，或者不想将它们移动到新实例，则可以跳到[下一部分](#re-create-endpoints-and-routes)。

否则，请返回到运行 Azure Digital Twins Explorer 的浏览器窗口，并执行以下步骤。

##### <a name="connect-to-the-new-instance"></a>连接到新实例

Azure Digital Twins Explorer 当前连接到原始 Azure 数字孪生实例。 选择窗口右上角的“登录”按钮，将连接切换为指向新实例。

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="在窗口右上角突出显示“登录”图标的 Azure Digital Twins Explorer。该图标显示了一个简单的人员与密钥叠加的剪影。" lightbox="media/how-to-move-regions/sign-in.png":::

替换 ADT URL 以反映新的实例。 更改此值，使其显示为 https://{新实例主机名}。

选择“连接”  。 可能要求使用 Azure 凭据重新登录，或者向此实例授予应用程序许可。

##### <a name="upload-models-twins-and-graph"></a>上传模型、孪生体和图形

接下来，将之前下载的解决方案组件上传到新实例。

若要上传模型、孪生体和图形，请选择“图形视图”框中的“导入图形”图标。 此选项将一次上传所有这三个组件。 它甚至会上传图形中当前未使用的模型。

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="在“图形视图”框中，一个图标突出显示。它显示了一个指向云的箭头。" lightbox="media/how-to-move-regions/import-graph.png":::

在“文件选择器”框中，转到已下载的图形。 选择图形的 .json 文件，然后选择“打开”。

几秒钟后，Azure Digital Twins Explorer 将打开“导入”视图，其中显示了要加载的图形的预览。

若要确认图形上传，请选择“图形视图”框右上角的“保存”图标 。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="在“图形预览”窗格中突出显示“保存”图标。" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

现在，Azure Digital Twins Explorer 会将模型和图形（包括孪生体和关系）上传到新的 Azure 数字孪生实例。 应该会看到一条成功消息，指出已上传的模型、孪生体和关系的数量。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="指示图导入成功的对话框。它显示为“导入成功。已导入 2 个模型，已导入 4 个孪生体。已导入 2 个关系。”" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

若要验证是否成功上传所有内容，请选择“Graph 浏览器”框中的“运行查询”按钮以运行默认查询，以便显示图形中的所有孪生体和关系。 此操作还会刷新“模型视图”框中的模型列表。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="突出显示了窗口右上角的“运行查询”按钮。" lightbox="media/how-to-move-regions/run-query.png":::

“Graph 浏览器”框中将显示图形及其所有的孪生体和关系。 “模型视图”框中还会列出模型。

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Azure Digital Twins Explorer 视图，其中“模型视图”框中突出显示两个模型，“Graph 浏览器”框中突出显示图形。" lightbox="media/how-to-move-regions/post-upload.png":::

通过这些视图，可以确认模型、孪生体和图形已重新上传到目标区域中的新实例。

#### <a name="re-create-endpoints-and-routes"></a>重新创建终结点和路由

如果原始实例拥有终结点或路由，则需要在新实例中重新创建它们。 如果原始实例中没有任何终结点或路由，或者不想将它们移动到新实例，则可以跳到[下一部分](#relink-connected-resources)。

否则，请遵照[操作说明：管理终结点和路由](how-to-manage-routes-portal.md)中的步骤，使用新实例进行操作。 请记住以下几点：

* 无需重新创建要用于终结点的事件网格、事件中心或服务总线资源。 有关详细信息，请参阅终结点说明中的“先决条件”部分。 只需在 Azure 数字孪生实例上重新创建终结点。
* 可以重复使用终结点名和路由名，因为它们的作用域限定为不同的实例。
* 请记住，将任何所需的筛选器添加到所创建的路由。

#### <a name="relink-connected-resources"></a>重新链接连接的资源

如果有其他应用或 Azure 资源连接到原始 Azure 数字孪生实例，则需要编辑连接，使其转到新实例。 这些资源可能包括其他已设置为使用 Azure 数字孪生的 Azure 服务、个人或公司应用。

如果没有其他任何资源连接到原始实例，或者不想将它们移动到新实例，则可以跳到[下一部分](#verify)。

否则，请考虑方案中已连接的资源。 无需删除和重新创建任何连接的资源。 相反，只需通过主机名编辑资源连接到 Azure 数字孪生实例的点。 然后更新这些点，以改用新实例的主机名。

需编辑的确切资源取决于方案，但以下是一些常见的集成点：

* Azure Functions。 如果 Azure 函数的代码包含原始实例的主机名，则应将此值更新为新实例的主机名，然后重新发布此函数。
* 事件网格、事件中心或服务总线。
* 逻辑应用。
* 时序见解。
* Azure Maps。
* IoT 中心设备预配服务。
* Azure 外部连接到实例且调用 Azure 数字孪生 API 的个人或公司应用（例如在 [教程：为客户端应用编写代码](tutorial-code.md)中创建的客户端应用）。
* 无需重新创建 Azure AD 应用注册。 如果要使用[应用注册](how-to-create-app-registration.md)连接到 Azure 数字孪生 API，则可以将相同的应用注册重新与新实例一起使用。

完成此步骤后，目标区域中的新实例应为原始实例的副本。

## <a name="verify"></a>验证

若要验证是否已正确设置新实例，请使用以下工具：

* [Azure 门户](https://portal.azure.com)。 门户适用于验证新实例是否存在且是否位于正确的目标区域。 它也适用于验证终结点和路由以及与其他 Azure 服务的连接。
* [Azure 数字孪生 CLI 命令](how-to-use-cli.md)。 这些命令适用于验证新实例是否存在且是否位于正确的目标区域。 它们还可用于验证实例数据。
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 Azure Digital Twins Explorer 适用于验证实例数据，如模型、孪生体和图形。
* [Azure 数字孪生 API 和 SDK](how-to-use-apis-sdks.md)。 这些资源适用于验证实例数据，如模型、孪生体和图形。 它们也适用于验证终结点和路由。

还可以尝试运行曾使用原始实例运行的任何自定义应用或端到端流，以帮助你验证它们是否能与新实例正常工作。

## <a name="clean-up-source-resources"></a>清理源资源

现在，已使用原始实例的数据和连接的副本在目标区域中设置新实例，可以删除原始实例。

可使用 [Azure 门户](https://portal.azure.com)、[Azure CLI](how-to-use-cli.md) 或[控制平面 API](how-to-use-apis-sdks.md#overview-control-plane-apis)。

若要使用 Azure 门户删除实例，请在浏览器窗口中[打开门户](https://portal.azure.com) ，并在门户搜索栏中搜索原始 Azure 数字孪生实例名，转到该实例。

选择“删除”按钮，然后按照提示完成删除操作。

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Azure 门户“概述”选项卡中的 Azure 数字孪生实例详细信息视图。其中的“删除”按钮突出显示。":::