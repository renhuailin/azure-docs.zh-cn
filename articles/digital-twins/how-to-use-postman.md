---
title: 通过 Postman 发出 API 请求
titleSuffix: Azure Digital Twins
description: 了解如何配置和使用 Postman 来调用 Azure 数字孪生 API。
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 6/16/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 3974b080c2a763db4ffa0f344ef18ce129959a53
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459446"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>如何使用 Postman 将请求发送到 Azure 数字孪生 API

[Postman](https://www.getpostman.com/) 是一种 REST 测试工具，它在桌面和基于插件的 GUI 中提供关键 HTTP 请求功能。 你可用它来创建 HTTP 请求并将其提交到 [Azure 数字孪生 REST API](concepts-apis-sdks.md)。

本文介绍如何通过以下步骤来配置 [Postman REST 客户端](https://www.getpostman.com/)以与 Azure 数字孪生 API 进行交互：

1. 使用 Azure CLI 获取将用于在 Postman 中发出 API 请求的持有者令牌。
1. 设置 Postman 集合并配置 Postman REST 客户端以使用持有者令牌进行身份验证。 设置集合时，你可以选择以下两种选项之一：
    1. [导入预生成的 Azure 数字孪生 API 请求集合](#import-collection-of-azure-digital-twins-apis)。
    1. [从头开始创建自己的集合](#create-your-own-collection)。
1. [将请求添加到已配置的集合](#add-an-individual-request)，并将其发送到 Azure 数字孪生 API。

Azure 数字孪生提供了两个可用的 API 集：数据平面和控制平面 。 如需详细了解这些 API 集之间的差异，请参阅 [Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md)。 本文包含这两个 API 集的信息。

## <a name="prerequisites"></a>先决条件

若要继续使用 Postman 访问 Azure 数字孪生 API，需要设置 Azure 数字孪生实例并下载 Postman。 本部分的其余部分将指导你完成这些步骤。

### <a name="set-up-azure-digital-twins-instance"></a>设置 Azure 数字孪生实例

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>下载 Postman

接下来，[下载 Postman 客户端的桌面版本](https://www.getpostman.com/apps)。

## <a name="get-bearer-token"></a>获取持有者令牌

现在，你已设置 Postman 和 Azure 数字孪生实例，接下来需要获取持有者令牌，Postman 请求可使用该令牌向 Azure 数字孪生 API 授权。

可通过多种方式获取此令牌。 本文使用 [Azure CLI](/cli/azure/install-azure-cli) 登录到 Azure 帐户，并以这种方式获取令牌。

如果已[在本地安装 Azure CLI](/cli/azure/install-azure-cli)，可在计算机上启动命令提示符来运行以下命令。
否则，可以在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com) 窗口，并在其中运行命令。

1. 首先，请通过运行以下命令来确保你已使用相应的凭据登录到 Azure：

    ```azurecli-interactive
    az login
    ```

2. 接下来，使用 [az account get-access-token](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_get_access_token) 命令获取具有 Azure 数字孪生服务访问权限的持有者令牌。 在此命令中，传入 Azure 数字孪生服务终结点的资源 ID，以获取可访问 Azure 数字孪生资源的访问令牌。 

    令牌需要的上下文取决于使用的 API 集，因此请使用下面的选项卡在[数据平面](concepts-apis-sdks.md#overview-data-plane-apis)和[控制平面](concepts-apis-sdks.md#overview-control-plane-apis) API 之间进行选择。

    # <a name="data-plane"></a>[数据平面](#tab/data-plane)
    
    如果要获取用于数据平面 API 的令牌，请为令牌上下文使用以下静态值：`0b07f429-9f4b-4714-9392-cc5e8e80c8b0`。 该值为 Azure 数字孪生服务终结点的资源 ID。
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[控制面板](#tab/control-plane)
    
    如果要获取用于控制平面 API 的令牌，请为令牌上下文使用以下值：`https://management.azure.com/`。
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > 如果需要使用属于不同于实例的 Azure Active Directory 租户的服务主体或用户帐户来访问 Azure 数字孪生实例，则需要从 Azure 数字孪生实例的“主”租户请求令牌。 有关此过程的详细信息，请参阅[编写应用身份验证代码](how-to-authenticate-client.md#authenticate-across-tenants)。

3. 复制结果中 `accessToken` 的值并将其保存，以便在下一部分中使用。 这就是你将提供给 Postman 用于授权请求的令牌值。

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="控制台的屏幕截图，其中显示了 az account get-access-token 命令的结果。突出显示了 accessToken 字段及其示例值。":::

>[!TIP]
>此令牌的有效期至少为五分钟，最长为 60 分钟。 如果为当前令牌分配的时间不足，则可以重复本部分中的步骤来获取新令牌。

接下来，设置 Postman 以使用此令牌向 Azure 数字孪生发出 API 请求。

## <a name="about-postman-collections"></a>关于 Postman 集合

Postman 中的请求保存在集合（请求组）中。 创建集合以对请求进行分组时，可以同时将通用设置应用于多个请求。 如果计划针对 Azure 数字孪生 API 创建多个请求，这样做可以大大简化授权，因为你只需为整个集合配置一次这些详细信息。

使用 Azure 数字孪生时，可从导入[所有 Azure 数字孪生请求的预生成集合](#import-collection-of-azure-digital-twins-apis)开始。 如果你在探索 API，并且希望快速设置包含请求示例的项目，可以这样做。

或者，还可选择从头开始，方法是[创建自己的空集合](#create-your-own-collection)并使用只调用所需 API 的各个请求来填充它。 

以下部分对这两种方法进行了介绍。 本文余下部分的操作都是在本地 Postman 应用程序中进行的，所以请立即打开计算机上的 Postman 应用程序。

## <a name="import-collection-of-azure-digital-twins-apis"></a>导入 Azure 数字孪生 API 集合

在 Postman 中开始使用 Azure 数字孪生的快速方法是导入预生成的 Azure 数字孪生 API 请求集合。

### <a name="download-the-collection-file"></a>下载集合文件

导入 API 集的第一步是下载集合。 选择下面的选项卡（供你选择数据平面或控制平面）来查看预生成的集合选项。

# <a name="data-plane"></a>[数据平面](#tab/data-plane)

目前有两个 Azure 数字孪生数据平面集合可供选择：
* [Azure 数字孪生 Postman 集合](https://github.com/microsoft/azure-digital-twins-postman-samples)：此集合提供了在 Postman 中使用 Azure 数字孪生的简单入门体验。 请求包括示例数据，所以你只需要进行少量编辑就能运行这些请求。 如果需要易理解的关键 API 请求（包含示例信息）集，可选择此集合。
    - 查找该集合需导航到存储库链接，然后打开名为 postman_collection.json 的文件。
* [Azure 数字孪生数据平面 Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins) ：此存储库包含 Azure 数字孪生 API 集的完整 Swagger 文件，可将其作为集合下载并导入到 Postman。 这将提供包含每个 API 请求的完整请求集，但所含的数据部分为空，而不是包含示例数据。 如果需要每个 API 调用的访问权限，并自行填充所有数据，可选择此集合。
    - 查找该集合需导航到存储库链接，然后选择最新规范版本的文件夹。 在此处，打开名为 digitaltwins.json 的文件。

# <a name="control-plane"></a>[控制面板](#tab/control-plane)

当前可用于控制平面的集合是 Azure 数字孪生控制平面 Swagger。 此存储库包含 Azure 数字孪生 API 集的完整 Swagger 文件，可将其作为集合下载并导入到 Postman 中。 这将提供包含每个 API 请求的完整请求集。

查找该集合需导航到存储库链接，然后选择最新规范版本的文件夹。 在此处，打开名为 digitaltwins.json 的文件。

---

下面介绍了如何将所选集合下载到计算机，以便可以将其导入到 Postman 中。
1. 在浏览器中使用上面的链接可在 GitHub 中打开集合文件。
1. 选择“Raw”按钮打开文件的原始文本。
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="GitHub 中的数据平面 digitaltwins.json 文件的屏幕截图。突出显示了“Raw”按钮。" lightbox="media/how-to-use-postman/swagger-raw.png":::
1. 复制窗口中的文本，将其粘贴到计算机上的新文件中。
1. 使用 .json 扩展名保存文件（文件名可以是任意名称，只要你能记住以便稍后查找）。

### <a name="import-the-collection"></a>导入集合

接下来，将集合导入 Postman 中。

1. 在 Postman 主窗口中，选择“Import”按钮。
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="新打开的 Postman 窗口的屏幕截图。突出显示了“Import”按钮。" lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. 在随后显示的“Import”窗口中，选择“Upload Files”并导航到之前在计算机上创建的集合文件。 选择“打开”。
1. 选择“Import”按钮确认。

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Postman 的“Import”窗口的屏幕截图，其中显示了要作为集合导入的文件和“Import”按钮。":::

现在，可在 Postman 主视图的“Collections”选项卡中看见新导入的集合。

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Postman 主窗口的屏幕截图。在“Collections”选项卡中突出显示了新导入的集合。" lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

接下来，请继续阅读下一部分，了解如何将持有者令牌添加到集合进行授权并将其连接到 Azure 数字孪生实例。

### <a name="configure-authorization"></a>配置授权

接下来，编辑创建的集合以配置一些访问详细信息。 突出显示已创建的集合，然后通过选择用于查看更多操作的图标显示一个菜单。 选择“编辑”。

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Postman 的屏幕截图。突出显示了导入的集合的查看更多操作图标和选项中的“Edit”。" lightbox="media/how-to-use-postman/postman-edit-collection.png":::

按照以下步骤将持有者令牌添加到集合以进行授权。 此时会使用在[获取持有者令牌](#get-bearer-token)这一部分中收集的令牌值，以便将其用于集合中的所有 API 请求。

1. 在集合的编辑对话框中，确保位于“Authorization”选项卡上。 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Postman 中导入的集合的编辑对话框的屏幕截图，其中显示了“Authorization”选项卡。" lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. 将“Type”设置为“OAuth 2.0”，将访问令牌粘贴到“Access Token”框中，然后选择“Save” 。

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Postman 导入集合的编辑对话框的屏幕截图，在“Authorization”选项卡上“Type”为“OAuth 2.0”，还突出显示了“Access Token”框。" lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>其他配置

# <a name="data-plane"></a>[数据平面](#tab/data-plane)

如果是在建立[数据平面](concepts-apis-sdks.md#overview-data-plane-apis)集合，可通过设置集合附带的某些变量让集合能够轻松地连接到 Azure 数字孪生资源。 集合中的许多请求需要相同的值（如 Azure 数字孪生实例的主机名）时，可将该值存储在一个适用于集合中每个请求的变量中。 Azure 数字孪生的两个可下载集合都附带预先创建的变量，可在集合级别对变量进行设置。

1. 还是在集合的编辑对话框中，移动到“Variables”选项卡。

1. 使用[先决条件部分](#prerequisites)中实例的主机名来设置相关变量的“CURRENT VALUE”字段。 选择“保存”。

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Postman 中导入的集合的编辑对话框的屏幕截图，显示了“Variables”选项卡。突出显示了“CURRENT VALUE”字段。" lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. 如果集合中有其他变量，还应填充并保存这些值。

完成上述步骤后，集合的配置就完成了。 如果需要，可关闭集合的编辑选项卡。

# <a name="control-plane"></a>[控制面板](#tab/control-plane)

如果是选择建立[控制平面](concepts-apis-sdks.md#overview-control-plane-apis)集合，你已经完成了配置集合所需的一切操作。 可根据需要关闭集合的编辑选项卡，然后继续下一部分。

--- 

### <a name="explore-requests"></a>探索请求

接下来，探索 Azure 数字孪生 API 集合中的请求。 可展开集合以查看预先创建的请求（按操作类别排序）。 

不同请求需要有关实例及其数据的不同信息。 若要查看创建特定请求所需的所有信息，请在 [Azure 数字孪生 REST API 参考文档](/rest/api/azure-digitaltwins/)中查找请求详细信息。

可使用以下步骤在 Postman 集合中编辑请求的详细信息：

1. 从列表中选择请求以显示可编辑的详细信息。 

1. 在“Params”选项卡中填写“Path Variables”下列出的变量的值 。

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Postman 的屏幕截图。集合展开，显示了一个请求。突出显示了请求详细信息中的“Path Variables”部分。" lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. 在 Headers 或 Body 选项卡中提供任何必要的标头或正文详细信息 。

提供所有必需的详细信息后，可通过“Send”按钮来运行请求。

还可按照下面的添加单个请求部分中所述的过程，将自己的请求添加到集合中。

## <a name="create-your-own-collection"></a>创建自己的集合

你可从头开始创建自己的集合，而不是导入所有 Azure 数字孪生 API 的现有集合。 然后，可参照 [Azure 数字孪生 REST API 参考文档](/rest/api/azure-digitaltwins/)，使用各个请求填充集合。

### <a name="create-a-postman-collection"></a>创建 Postman 集合

1. 若要创建集合，请在 Postman 主窗口中选择“New”按钮。

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Postman 主窗口的屏幕截图。突出显示了“New”按钮。" lightbox="media/how-to-use-postman/postman-new.png":::

    选择 Collection 类型。

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Postman 中“Create New”对话框的屏幕截图。突出显示了“Collection”选项。":::

1. 这会打开一个用于填写新集合的详细信息的选项卡。 选择集合默认名称 (New Collection) 旁边的编辑图标将其替换为自己选择使用的名称。 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Postman 中新集合的编辑对话框的屏幕截图。突出显示了名称“New Collection”旁的编辑图标。" lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

接下来，转到下一部分，将持有者令牌添加到集合以进行授权。

### <a name="configure-authorization"></a>配置授权

按照以下步骤将持有者令牌添加到集合以进行授权。 此时会使用在[获取持有者令牌](#get-bearer-token)这一部分中收集的令牌值，以便将其用于集合中的所有 API 请求。

1. 还是在新集合的编辑对话框中，移动到“Authorization”选项卡。

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Postman 中新集合的编辑对话框的屏幕截图，其中显示了“Authorization”选项卡。" lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. 将“Type”设置为“OAuth 2.0”，将访问令牌粘贴到“Access Token”框中，然后选择“Save” 。

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="新集合的 Postman 编辑对话框的屏幕截图，在“Authorization”选项卡上“Type”为“OAuth 2.0”，突出显示了“Access Token”框。" lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

完成上述步骤后，集合的配置就完成了。 你可以根据需要关闭新集合的编辑选项卡。

可在 Postman 主视图的“Collections”选项卡中看到新集合。

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Postman 主窗口的屏幕截图。“Collections”选项卡中突出显示了新建的自定义集合。" lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>添加单个请求

现在集合已设置完毕，可将自己的请求添加到 Azure 数字孪生 API。

1. 若要创建请求，请再次使用“New”按钮。

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Postman 主窗口的屏幕截图。突出显示了“New”按钮。" lightbox="media/how-to-use-postman/postman-new.png":::

    选择 Request 类型。

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Postman 中“Create New”对话框的屏幕截图。突出显示了“Request”选项。":::

1. 此操作会打开“SAVE REQUEST”窗口，你可以在其中输入请求的名称，为其提供可选说明，然后选择其所属的集合。 填充详细信息，并将请求保存到之前创建的集合。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Postman 中“SAVE REQUEST”窗口的屏幕截图，显示了所述的字段。突出显示了“Save to Azure Digital Twins collection”按钮。":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

你现在可以查看集合下的请求，并选择它来提取其可编辑的详细信息。

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Postman 的屏幕截图。展开了 Azure 数字孪生集合以显示请求的详细信息。" lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>设置请求详细信息

若要向 Azure 数字孪生 API 之一发出 Postman 请求，需要提供 API 的 URL 以及详细信息所需的内容。 可以在 [Azure 数字孪生 REST API 参考文档](/rest/api/azure-digitaltwins/)中找到此信息。

若要继续执行示例查询，本文将使用查询 API（及其[参考文档](/rest/api/digital-twins/dataplane/query/querytwins)）来查询实例中的所有数字孪生。

1. 获取参考文档中的请求 URL 和类型。 对于查询 API，当前为 POST `https://digitaltwins-host-name/query?api-version=2020-10-31`。
1. 在 Postman 中，设置请求的类型并输入请求 URL，并根据需要填充 URL 中的占位符。 你将在这里使用[先决条件部分](#prerequisites)中实例的主机名。
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Postman 中新请求的详细信息的屏幕截图。参考文档中的查询 URL 已填充到请求 URL 框中。" lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. 检查在“参数”选项卡中为请求显示的参数是否与参考文档中所述的参数匹配。 对于 Postman 中的此请求，当在上一步中输入请求 URL 时，将自动填充 `api-version` 参数。 对于查询 API，这是所需的唯一参数，因此已完成此步骤。
1. 在“Authorization”选项卡中，将“Type”设置为“Inherit auth from parent” 。 这表明此请求将使用之前为整个集合设置的授权。
1. 检查在“标头”选项卡中为请求显示的标头是否与参考文档中所述的标头匹配。 对于此请求，已自动填充多个标头。 对于查询 API，无需任何标头选项，因此已完成此步骤。
1. 检查在“正文”选项卡中为请求显示的正文是否与参考文档中所述的需求匹配。 对于查询 API，需要 JSON 正文来提供查询文本。 下面是此请求的示例正文，它将查询实例中的所有数字孪生：

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Postman 中新请求的详细信息的屏幕截图，其中显示了“Body”选项卡。它包含带有“SELECT * FROM DIGITALTWINS”查询的原始 JSON 正文。" lightbox="media/how-to-use-postman/postman-request-body.png":::

   有关创建 Azure 数字孪生查询的详细信息，请参阅[查询孪生体图形](how-to-query-graph.md)。

1. 检查参考文档中是否存在请求类型可能需要的任何其他字段。 对于查询 API，现已满足 Postman 请求中的所有要求，因此已完成此步骤。
1. 使用“发送”按钮发送已完成的请求。
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Postman 的屏幕截图，其中显示了新请求的详细信息。突出显示了“Send”按钮。" lightbox="media/how-to-use-postman/postman-request-send.png":::

发送请求后，响应详细信息将在 Postman 窗口中的请求下显示。 可以查看响应的状态代码和任何正文文本。

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Postman 中发送的请求的屏幕截图。在请求详细信息下面，显示了响应。“Status”为“200 OK”，正文显示查询结果。" lightbox="media/how-to-use-postman/postman-request-response.png":::

还可以将响应与参考文档中给定的预期响应数据进行比较，以验证结果或了解有关出现的任何错误的详细信息。

## <a name="next-steps"></a>后续步骤

若要详细了解数字孪生 API，请阅读 [Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md)，或查看 [REST API 的参考文档](/rest/api/azure-digitaltwins/)。