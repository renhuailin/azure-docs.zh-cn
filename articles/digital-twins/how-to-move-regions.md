---
title: 将实例移动到其他 Azure 区域
titleSuffix: Azure Digital Twins
description: 请参阅如何将 Azure 数字孪生实例从一个 Azure 区域移动到另一个 Azure 区域。
author: baanders
ms.author: baanders
ms.date: 9/8/2021
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 9fc28555be055fe74f183c965190b2c784c4533b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676782"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>将 Azure 数字孪生实例移动到其他 Azure 区域

如果需要将 Azure 数字孪生实例从一个区域移动到另一个区域，当前过程是在新区域中重新创建资源。 在新区域中重新创建资源后，将删除原始资源。 此过程结束时，除了更新的位置以外，将使用的新 Azure 数字孪生实例与第一个实例完全相同。

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

可以使用 [Azure 门户](https://portal.azure.com)、[Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md)、[Azure 数字孪生 CLI 命令](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)或 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) 来收集此信息。

## <a name="prepare"></a>准备

在本部分中，将准备通过从原始实例下载原始模型、孪生体和图形来重新创建实例。 本文使用 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) 来执行此任务。

>[!NOTE]
>实例中可能已存在包含模型或图形的文件。 如果是这样，则无需再次下载所有内容，只需下载缺少的内容或者自初始上传这些文件以来可能已更改的内容。 例如，可能存在使用新数据更新的孪生体。

### <a name="download-models-twins-and-graph-with-azure-digital-twins-explorer"></a>通过 Azure Digital Twins Explorer 下载模型、孪生体和图

首先，在 [Azure 门户](https://portal.azure.com)中打开 Azure Digital Twins Explorer 以查看你的 Azure 数字孪生实例。 为此，请在门户搜索栏中搜索 Azure 数字孪生实例的名称来导航到该实例。 然后，选择“转到资源管理器(预览版)”按钮。 

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="显示 Azure 数字孪生实例概述页面的 Azure 门户的屏幕截图。其中的“转到 Explorer (预览版)”按钮突出显示。" lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

设置此按钮将打开连接到此实例的 Azure Digital Twins Explorer 窗口。

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Internet 浏览器中 Azure 门户的屏幕截图。门户显示 Azure Digital Twins Explorer，它不包含任何数据。" lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

按照 Azure Digital Twins Explorer 说明[导出图和模型](how-to-use-azure-digital-twins-explorer.md#export-graph-and-models)。 按照这些说明操作可以将一个 JSON 文件下载到你的计算机，其中包含模型、孪生体和关系的代码（包括图中当前未使用的模型）。

## <a name="move"></a>移动

接下来，通过在目标区域中创建一个新实例，完成实例的“移动”。 然后，使用原始实例中的数据和组件来填充它。

### <a name="create-a-new-instance"></a>创建新实例

首先，在目标区域中创建一个新的 Azure 数字孪生实例。 按照[设置实例和身份验证](how-to-set-up-instance-portal.md)中的步骤操作。 请记住以下几点：

* 如果新实例位于不同的资源组中，则可以为其保留相同的名称。 如果需要使用包含原始实例的相同资源组，则新实例需要一个不同的名称。
* 提示输入位置时，请输入新的目标区域。

完成此步骤后，需要新实例的主机名，以便使用数据继续进行设置。 如果设置过程中未记下主机名，请遵照[以下说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)立即从 Azure 门户获取该名称。

接下来，设置新实例的数据，使其成为原始实例的副本。

#### <a name="upload-models-twins-and-graph-with-azure-digital-twins-explorer"></a>通过 Azure Digital Twins Explorer 上传模型、孪生体和图

在本部分中，可以将模型、孪生体和图形重新上传到新实例。 如果原始实例中没有任何模型、孪生体或图形，或者不想将它们移动到新实例，则可以跳到[下一部分](#recreate-endpoints-and-routes)。

首先，在 [Azure 门户](https://portal.azure.com)中导航到“Azure 数字孪生资源管理器”以查看新实例。 

将本文前面[下载的 JSON 文件](#download-models-twins-and-graph-with-azure-digital-twins-explorer)导入到新实例。 为此，可以按照 Azure Digital Twins Explorer 说明中的步骤操作，[将文件导入到 Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md#import-file-to-azure-digital-twins-explorer)。 可以通过这些步骤将原始实例中的所有模型、孪生体和关系上传到新实例。

若要验证是否成功上传所有内容，请切换回“孪生图”选项卡，然后选择“查询资源管理器”框中的“运行查询”按钮以运行默认查询，以便显示图形中的所有孪生体和关系。   此操作还会刷新“模型”面板中的模型列表。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，突出显示了窗口右上角的“运行查询”按钮。" lightbox="media/how-to-move-regions/run-query.png":::

“孪生图”面板中将显示图形及其所有的孪生体和关系。 “模型”面板中还会列出模型。

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中“模型”框中突出显示两个模型，“孪生图”框中突出显示图形。" lightbox="media/how-to-move-regions/post-upload.png":::

通过这些视图，可以确认模型、孪生体和图形已重新上传到目标区域中的新实例。

#### <a name="recreate-endpoints-and-routes"></a>重新创建终结点和路由

如果原始实例拥有终结点或路由，则需要在新实例中重新创建它们。 否则，如果原始实例中没有任何终结点或路由，或者不想将它们移动到新实例，则可以跳到[下一部分](#relink-connected-resources)。

否则，请按照[管理终结点和路由](how-to-manage-routes.md)中的步骤，使用新实例进行操作。 请记住以下几点：

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
* Azure 外部连接到实例且调用 Azure 数字孪生 API 的个人或公司应用（例如在[为客户端应用编写代码](tutorial-code.md)中创建的客户端应用）。
* 无需重新创建 Azure AD 应用注册。 如果要使用[应用注册](./how-to-create-app-registration-portal.md)连接到 Azure 数字孪生 API，则可以将相同的应用注册重新与新实例一起使用。

完成此步骤后，目标区域中的新实例应为原始实例的副本。

## <a name="verify"></a>验证

若要验证是否已正确设置新实例，请使用以下工具：

* [Azure 门户](https://portal.azure.com)。 门户适用于验证新实例是否存在且是否位于正确的目标区域。 它也适用于验证终结点和路由以及与其他 Azure 服务的连接。
* [Azure 数字孪生 CLI 命令](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)。 这些命令适用于验证新实例是否存在且是否位于正确的目标区域。 它们还可用于验证实例数据。
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 Azure Digital Twins Explorer 适用于验证实例数据，如模型、孪生体和图形。
* [Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md)。 这些资源适用于验证实例数据，如模型、孪生体和图形。 它们也适用于验证终结点和路由。

还可以尝试运行曾使用原始实例运行的任何自定义应用或端到端流，以帮助你验证它们是否能与新实例正常工作。

## <a name="clean-up-source-resources"></a>清理源资源

现在，已使用原始实例的数据和连接的副本在目标区域中设置新实例，可以删除原始实例。

可使用 [Azure 门户](https://portal.azure.com)、[Azure CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 或[控制平面 API](concepts-apis-sdks.md#overview-control-plane-apis)。

若要使用 Azure 门户删除实例，请在浏览器窗口中[打开门户](https://portal.azure.com) ，并在门户搜索栏中搜索原始 Azure 数字孪生实例名，转到该实例。

选择“删除”按钮，然后按照提示完成删除操作。

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Azure 门户“概述”选项卡中 Azure 数字孪生实例详细信息的屏幕截图。其中突出显示了“删除”按钮。":::