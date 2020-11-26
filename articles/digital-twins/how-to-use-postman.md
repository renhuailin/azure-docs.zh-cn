---
title: 通过 Postman 发出请求
titleSuffix: Azure Digital Twins
description: 了解如何配置和使用 Postman 来测试 Azure 数字孪生 Api。
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: f9be9dd94aad8c206b562f2c984ec944f70d3957
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187870"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>如何使用 Postman 将请求发送到 Azure 数字孪生 Api

[Postman](https://www.getpostman.com/) 是一种 REST 测试工具，可在桌面和基于插件的 GUI 中提供密钥 HTTP 请求功能。 可以使用它来创建 HTTP 请求并将其提交到 [Azure 数字孪生 REST api](how-to-use-apis-sdks.md)。

本文介绍了如何配置 [POSTMAN REST 客户端](https://www.getpostman.com/) ，以便与 Azure 数字孪生 api 交互，步骤如下：

1. 使用 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 获取将用于在 Postman 中发出 API 请求的持有者令牌。
1. 设置 Postman 集合，并将 Postman REST 客户端配置为使用持有者令牌进行身份验证。
1. 使用已配置的 Postman 创建请求并将其发送到 Azure 数字孪生 Api。

## <a name="prerequisites"></a>先决条件

若要继续使用 Postman 访问 Azure 数字孪生 Api，需要设置 Azure 数字孪生实例并下载 Postman。 本部分的其余部分将指导你完成这些步骤。

### <a name="set-up-azure-digital-twins-instance"></a>设置 Azure 数字孪生实例

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>下载 Postman

接下来，下载 Postman 客户端的桌面版本。 导航到 [*www.getpostman.com/apps*](https://www.getpostman.com/apps) ，然后按照提示下载应用。

## <a name="get-bearer-token"></a>获取持有者令牌

现在，你已设置了 Postman 和 Azure 数字孪生实例，你需要获取一个持有人令牌，Postman 请求可使用该令牌向 Azure 数字孪生 Api 授权。

可以通过多种方式来获取此令牌。 本文使用 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 登录到 Azure 帐户，并以这种方式获取令牌。

如果 [本地安装](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)了 Azure CLI，则可以在计算机上启动命令提示符以运行以下命令。
否则，可以在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com) 窗口，并在其中运行命令。

1. 首先，请通过运行以下命令，确保你已使用相应的凭据登录到 Azure：

    ```azurecli-interactive
    az login
    ```

1. 接下来，使用 [az account get-help](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_get_access_token) 命令获取持有 Azure 数字孪生服务访问权限的持有者令牌。

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. 复制 `accessToken` 结果中的值，并将其保存以供下一部分使用。 这是你将向 Postman 提供验证请求身份的 **令牌值** 。

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="显示 az account get-help 命令结果的本地控制台窗口视图。结果中的其中一个字段称为 accessToken，其示例值--以3om-ey-2vk 开头--将突出显示。":::

>[!TIP]
>此令牌的有效期至少为五分钟，最长为60分钟。 如果为当前令牌分配的时间不足，可以重复此部分中的步骤来获取新的。

## <a name="set-up-postman-collection-and-authorization"></a>设置 Postman 集合和授权

接下来，设置 Postman 以发出 API 请求。
这些步骤发生在本地 Postman 应用程序中，因此，请继续打开计算机上的 Postman 应用程序。

### <a name="create-a-postman-collection"></a>创建 Postman 集合

Postman 中的请求保存在 (组) 的 **集合** 中。 当你创建集合以对你的请求进行分组时，你可以同时向多个请求应用常见设置。 如果你计划针对 Azure 数字孪生 Api 创建多个请求，则这可以极大地简化授权，因为你只需为整个集合配置身份验证一次。

1. 若要创建集合，请单击 " *+ 新建集合* " 按钮。

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="新打开的 Postman 窗口的视图。&quot;新建集合&quot; 按钮已突出显示":::

1. 在下面的 " *创建新集合* " 窗口中，为集合提供 **名称** 和可选 **说明** 。

接下来，转到下一部分，将持有者令牌添加到集合以进行授权。

### <a name="add-authorization-token-and-finish-collection"></a>添加授权令牌并完成收集

1. 在 "*创建新集合*" 对话框中，转到 "*授权*" 选项卡。此时，你将在 "[获取持有者令牌](#get-bearer-token)" 部分中放置收集的 **令牌值**，以便将其用于集合中的所有 API 请求。

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="显示 &quot;授权&quot; 选项卡的 &quot;创建新集合&quot; Postman 窗口。":::

1. 将 *类型* 设置为 _**OAuth 2.0**_，并将访问令牌粘贴到 " *访问令牌* " 框中。

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="显示 &quot;授权&quot; 选项卡的 &quot;创建新集合&quot; Postman 窗口。选择了 &quot;OAuth 2.0&quot; 类型，并且突出显示了访问令牌值可以粘贴到的 &quot;访问令牌&quot; 框。":::

1. 粘贴到持有者令牌中后，单击 " *创建* " 以完成创建集合的操作。

现在可以从主 Postman 视图的 " *集合*" 下查看新集合。

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="主 Postman 窗口的视图。新创建的集合将在 &quot;集合&quot; 选项卡中突出显示。":::

## <a name="create-a-request"></a>创建请求

完成前面的步骤后，可以创建对 Azure 数字克隆 Api 的请求。

1. 若要创建请求，请单击 " *+ 新建* " 按钮。

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="主 Postman 窗口的视图。突出显示 &quot;新建&quot; 按钮":::

1. 选择 " *请求*"。

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="查看可选择创建新内容的选项。&quot;Request&quot; 选项突出显示":::

1. 此操作将打开 " *保存请求* " 窗口，您可以在其中输入请求的名称，为其提供一个可选描述，然后选择其所属的集合。 填写详细信息，并将请求保存到前面创建的集合。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="&quot;保存请求&quot; 窗口的视图，可在其中填写所述的字段。&quot;保存到 Azure 数字孪生集合&quot; 按钮已突出显示":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

你现在可以在集合下查看你的请求，并选择它来提取其可编辑的详细信息。

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="主 Postman 窗口的视图。Azure 数字孪生集合已展开，并突出显示 &quot;Query 孪生&quot; 请求。该请求的详细信息显示在页面的中央。" lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>设置请求详细信息

若要向 Azure 数字孪生 Api 之一发出 Postman 请求，需要提供 API 的 URL 以及所需的详细信息。 可以在 [Azure 数字孪生 REST API 参考文档](/rest/api/azure-digitaltwins/)中找到此信息。

若要继续执行示例查询，本文将使用查询 API (及其 [参考文档](/rest/api/digital-twins/dataplane/query/querytwins)) 来查询实例中的所有数字孪生。

1. 获取参考文档中的请求 URL 和类型。 对于查询 API，这是 *POST https://digitaltwins-name.digitaltwins.azure.net/query?api-version=2020-10-31*。
1. 在 Postman 中，设置请求的类型并输入请求 URL，并根据需要填写 URL 中的占位符。 这是你将在 [*先决条件*](#prerequisites)部分中使用实例的 **主机名** 的位置。
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="在新请求的详细信息中，引用文档中的查询 URL 已填充到 &quot;请求 URL&quot; 框中。" lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. 检查在 " *参数* " 选项卡中为请求显示的参数是否与参考文档中所述的参数匹配。 对于 Postman 中的此请求， `api-version` 当在上一步中输入请求 URL 时，将自动填充参数。 对于查询 API，这是唯一必需的参数，因此执行此步骤。
1. 在 " *授权* " 选项卡中，将 " *类型* " 设置为 *从父项继承身份验证*。 这表明此请求将使用之前为整个集合设置的身份验证。
1. 检查 " *标头* " 选项卡中为请求显示的标头是否与参考文档中所述的标头相匹配。 对于此请求，已经自动填写了若干标头。 对于查询 API，无需任何标头选项，因此执行此步骤。
1. 检查 " *正文* " 选项卡中为请求显示的正文是否与参考文档中所述的需求相匹配。 对于查询 API，需要 JSON 正文来提供查询文本。 下面是此请求的示例正文，查询实例中的所有数字孪生：

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="在新请求的详细信息中，会显示 &quot;正文&quot; 选项卡。它包含具有 &quot;SELECT * FROM DIGITALTWINS&quot; 查询的原始 JSON 正文。"lightbox="media/how-to-use-postman/postman-request-body.png":::

   有关预编制 Azure 数字孪生查询的详细信息，请参阅 [*如何：查询双子图形*](how-to-query-graph.md)。

1. 检查参考文档中是否存在请求类型可能需要的任何其他字段。 对于查询 API，已经满足了 Postman 请求中的所有要求，因此执行此步骤。
1. 使用 " *发送* " 按钮发送已完成的请求。
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="在新请求的详细信息附近，将突出显示 &quot;发送&quot; 按钮。" lightbox="media/how-to-use-postman/postman-request-send.png":::

发送请求后，响应详细信息将在 Postman 窗口中的请求下显示。 您可以查看响应的状态代码和任何正文文本。

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="在发送请求的详细信息下面，将突出显示响应的详细信息。状态为 200 OK 和正文文本，描述查询返回的数字孪生。" lightbox="media/how-to-use-postman/postman-request-response.png":::

你还可以将响应与引用文档中给定的预期响应数据进行比较，以验证结果或了解有关出现的任何错误的详细信息。

## <a name="next-steps"></a>后续步骤

若要了解有关数字孪生 Api 的详细信息，请参阅 [*操作方法：使用 Azure 数字孪生 api 和 sdk*](how-to-use-apis-sdks.md)，或查看 [REST api 的参考文档](/rest/api/azure-digitaltwins/)。