---
title: 管理终结点和路由
titleSuffix: Azure Digital Twins
description: 了解如何为 Azure 数字孪生数据设置和管理终结点和事件路由
author: baanders
ms.author: baanders
ms.date: 7/30/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 99fbf0a6ecd232ae646639e84864addd103c1f5a
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807774"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>在 Azure 数字孪生中管理终结点和路由

在 Azure 数字孪生中，可将[事件通知](concepts-event-notifications.md)路由到下游服务或连接的计算资源。 首先需要设置可接收事件的终结点。 然后，可创建事件路由来指定由 Azure 数字孪生生成的哪些事件传递到哪些终结点。

本文介绍使用 [Azure 门户](https://portal.azure.com)、[REST API](/rest/api/azure-digitaltwins/)、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 和 [Azure 数字孪生 CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 创建终结点和路由的过程。

## <a name="prerequisites"></a>先决条件

* 你需要一个 Azure 帐户（可[免费设置一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)）
* 在 Azure 订阅中需要一个“Azure 数字孪生实例”。 如果还没有实例，可以使用[设置实例和身份验证](how-to-set-up-instance-portal.md)中的步骤创建一个。 准备好以下设置值，以便稍后在本文中使用：
    - 实例名称
    - 资源组

设置实例后，可以在 [Azure 门户](https://portal.azure.com)中找到这些详细信息。 登录到门户，并在门户搜索栏中搜索实例的名称。
 
:::image type="content" source="media/how-to-manage-routes/search-field-portal.png" alt-text="Azure 门户搜索栏的屏幕截图。" lightbox="media/how-to-manage-routes/search-field-portal.png":::

从结果中选择实例，即可在实例概述中查看这些详细信息：

:::image type="content" source="media/how-to-manage-routes/instance-details.png" alt-text="Azure 门户中 Azure 数字孪生实例概述页面的屏幕截图。名称和资源组突出显示。":::

若要遵循本指南的要求来使用 Azure CLI，请按以下说明操作。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-an-endpoint-for-azure-digital-twins"></a>为 Azure 数字孪生创建终结点

以下是可为实例创建的受支持的终结点类型：
* [事件网格](../event-grid/overview.md)主题
* [事件中心](../event-hubs/event-hubs-about.md)中心
* [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)主题

>[!NOTE]
> 对于事件网格端点，只支持事件网格主题。 不支持将事件网格域作为终结点。

有关不同终结点类型的更多信息，请参阅在 Azure 消息服务之间进行选择。

此部分介绍如何使用 [Azure 门户](https://portal.azure.com)或 [Azure CLI](/cli/azure/dt/endpoint?view=azure-cli-latest&preserve-view=true) 创建终结点。 还可以使用 [DigitalTwinsEndpoint 控制平面 API](/rest/api/digital-twins/controlplane/endpoints) 管理终结点。

### <a name="prerequisite-create-endpoint-resources"></a>必备条件：创建终结点资源

若要将终结点链接到 Azure 数字孪生，需具备将用于终结点的事件网格主题、事件中心或服务总线主题。

使用下图查看在创建终结点之前应该设置的资源。

| 终结点类型 | 所需的资源（已链接到创建说明） |
| --- | --- |
| 事件网格终结点 | [事件网格主题](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)<br/>\* 事件架构必须是事件网格架构或云事件架构 v1.0 |
| 事件中心终结点 | [事件&nbsp;中心&nbsp;命名空间](../event-hubs/event-hubs-create.md)<br/><br/>[事件中心](../event-hubs/event-hubs-create.md)<br/><br/>（可选）基于密钥的身份验证的[授权规则](../event-hubs/authorize-access-shared-access-signature.md) | 
| Service Bus 终结点 | [服务总线命名空间](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[服务总线主题](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> （可选）基于密钥的身份验证的[授权规则](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)|

### <a name="create-the-endpoint"></a>创建终结点 

创建终结点资源后，就可以将其用于 Azure 数字孪生终结点。 

# <a name="portal"></a>[Portal](#tab/portal) 

要创建新的终结点，请在 [Azure 门户](https://portal.azure.com)中转到实例页面（可以通过在门户搜索栏中输入名称来查找实例）。

1. 从实例菜单中选择“终结点”。 然后，在显示的“终结点”页选择“+ 创建终结点”。 随后将打开“创建终结点”页，可在该页面中按以下步骤填写字段。

    :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-grid.png" alt-text="在 Azure 门户中创建“事件网格”类型终结点的屏幕截图。" lightbox="media/how-to-manage-routes/create-endpoint-event-grid.png":::

1. 输入终结点的“名称”，并选择“终结点类型”。

1. 完成终结点类型所需的其他详细信息，包括订阅和[上面](#prerequisite-create-endpoint-resources)所述的终结点资源。
    1. 只有“事件中心”和“服务总线”终结点必须选择“身份验证类型”。 可以使用已预先创建授权规则的基于密钥的身份验证，如果要对 Azure 数字孪生实例使用带[托管标识](concepts-security.md#managed-identity-for-accessing-other-resources)的终结点，也可以使用基于标识的身份验证。 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="在 Azure 门户中创建“事件中心”类型终结点的屏幕截图。" lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. 选择“保存”，完成终结点创建。

>[!IMPORTANT]
> 若要成功地对终结点使用基于标识的身份验证，需要按照[使用托管标识来路由事件](how-to-route-with-managed-identity.md)中的步骤为实例创建托管标识。

创建终结点后，可以通过查看 Azure 门户顶部栏中的通知图标来验证是否已成功创建终结点： 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-notifications.png" alt-text="指示验证 Azure 门户中终结点的创建的通知的屏幕截图。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

如果终结点创建失败，可查看错误消息并在几分钟后重试。

还可以返回 Azure 数字孪生实例的“终结点”页上查看已创建的终结点。

目前，根据为终结点所选的名称，可使用事件网格、事件中心或服务总线主题作为 Azure 数字孪生内的终结点。 通常使用该名称作为事件路由（将在[本文的后面部分](#create-an-event-route)创建）的目标。

# <a name="cli"></a>[CLI](#tab/cli) 

以下示例演示如何使用 [Azure 数字孪生 CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 的 [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) 命令创建终结点。 将命令中的占位符替换为你自己的资源的详细信息。

创建事件网格终结点：

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
```

创建事件中心终结点（基于密钥的身份验证）：
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

创建服务总线主题终结点（基于密钥的身份验证）：
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

成功运行这些命令后，事件网格、事件中心或服务总线主题将作为 Azure 数字孪生内的终结点提供，以你通过 `--endpoint-name` 参数所提供的名称命名。 通常使用该名称作为事件路由（将在[本文的后面部分](#create-an-event-route)创建）的目标。

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>创建使用基于身份的身份验证的终结点

还可以创建具有基于身份的身份验证的终结点，以使用具有[托管标识](concepts-security.md#managed-identity-for-accessing-other-resources)的终结点。 此选项仅适用于事件中心和服务总线类型终结点（事件网格不支持此选项）。

创建这种类型的终结点的 CLI 命令如下所示。 需要将以下值插入命令中的占位符：
* Azure 数字孪生实例的 Azure 资源 ID
* 终结点名称
* 终结点类型
* 终结点资源的命名空间
* 事件中心或服务总线主题的名称
* Azure 数字孪生实例的位置

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

### <a name="create-an-endpoint-with-dead-lettering"></a>创建使用死信的终结点

当终结点无法在特定时间段内传递事件，或在尝试传递事件一定次数后仍无法传递，它可将未传递的事件发送到存储帐户。 此过程称为“死信处理”。

可以使用 [Azure 门户](https://ms.portal.azure.com/#home)或 [Azure 数字孪生 CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 来设置必需的存储资源。 但是，若要创建一个启用了死信的终结点，需使用 [Azure 数字孪生 CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 或[控制平面 API](concepts-apis-sdks.md#overview-control-plane-apis)。

若要详细了解死信，请参阅[事件路由](concepts-route-events.md#dead-letter-events)。 有关如何设置使用死信的终结点的说明，请继续阅读本部分的其余内容。

#### <a name="set-up-storage-resources"></a>设置存储资源

在设置死信位置之前，必须在 Azure 帐户中设置一个带有[容器](../storage/common/storage-account-create.md?tabs=azure-portal)的[存储帐户](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)。 

稍后创建终结点时，需要提供此容器的 URI。 死信位置将作为带有 [SAS 令牌](../storage/common/storage-sas-overview.md)的容器 URI 提供给终结点。 该令牌需要存储帐户中目标容器的 `write` 权限。 完全格式化的死信 SAS URI 的格式为：`https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`。

按照以下步骤在 Azure 帐户中设置这些存储资源，为在下一部分中设置终结点连接做准备。

1. 按照[创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)中的步骤，在 Azure 订阅中创建一个存储帐户。 记下存储帐户名以便以后使用。
1. 按照[创建容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)中的步骤，在新的存储帐户中创建一个容器。 记下容器名称以便以后使用。

##### <a name="create-a-sas-token"></a>创建 SAS 令牌

接下来，为存储帐户创建一个 SAS 令牌，终结点可以使用该令牌来访问它。

# <a name="portal"></a>[Portal](#tab/portal)

1. 首先导航到 [Azure 门户](https://ms.portal.azure.com/#home)中的存储帐户（可以通过门户搜索栏按名称找到它）。
1. 在存储帐户页面中，选择左侧导航栏中的“共享访问签名”链接以开始设置 SAS 令牌。

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-1.png" alt-text="Azure 门户中存储帐户页的屏幕截图。" lightbox="./media/how-to-manage-routes/generate-sas-token-1.png":::

1. 在“共享访问签名”页的“允许的服务”和“允许的资源类型”下，选择所需的任何设置  。 需要在每个类别中至少选择一个框。 在“允许的权限”下，选择“写入”（如果需要，还可以选择其他权限）。
1. 对于其余的设置，设置所需的任何值。
1. 完成后，选择“生成 SAS 和连接字符串”按钮来生成 SAS 令牌。 

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-2.png" alt-text="Azure 门户中存储帐户页的屏幕截图，其中显示了生成 SAS 令牌的所有设置选择。" lightbox="./media/how-to-manage-routes/generate-sas-token-2.png"::: 

1. 这将在同一个页面底部的各设置选项下生成几个 SAS 和连接字符串值。 向下滚动以查看这些值，并使用“复制到剪贴板”图标复制“SAS 令牌”值。 保存它以供以后使用。

    :::image type="content" source="./media/how-to-manage-routes/copy-sas-token.png" alt-text="Azure 门户中存储帐户页的屏幕截图，其中突出显示了如何复制 SAS 令牌以在死信机密中使用。" lightbox="./media/how-to-manage-routes/copy-sas-token.png":::

# <a name="cli"></a>[CLI](#tab/cli)

1. 使用以下命令检索存储帐户密钥，并复制其中一个密钥的值：

    ```azurecli
    az storage account keys list --account-name <storage-account-name>
    ```

1. 使用以下命令选择到期日期，为存储帐户生成 SAS 令牌：

    ```azurecli
    az storage account generate-sas --account-name <storage-account-name> --account-key <storage-account-key> --expiry <expiration-date> --services bfqt --resource-types o --permissions w
    ```

    此命令的输出为 SAS 令牌。 复制 SAS 令牌值供稍后使用。

    > [!NOTE]
    > 此命令包含“**b** lob”、“**f** ile”（文件）、“**q** ueue”（队列）和“**t** able”（表）服务和一个“**o** bject”（对象）资源类型，并允许“**w** rite”（写入）权限。
    >
    > 有关 `az storage account generate-sas` 命令及其参数的详细信息，请参阅 [Azure CLI 参考](/cli/azure/storage/account?view=azure-cli-latest&preserve-view=true#az_storage_account_generate_sas)。

---

#### <a name="create-the-dead-letter-endpoint"></a>创建死信终结点

# <a name="portal"></a>[Portal](#tab/portal)

要创建启用死信的终结点，必须使用 [CLI 命令](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)或[控制平面 API](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) 来创建终结点，而不是 Azure 门户。

有关如何使用 Azure CLI 执行此操作的说明，请切换到此部分的“CLI”标签页。

# <a name="cli"></a>[CLI](#tab/cli)

若要创建启用死信的终结点，请将以下死信参数添加到 [Azure 数字孪生 CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 的 [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) 命令中。

参数的值是“死信 SAS URI”，由你在[上一部分](#set-up-storage-resources)中收集的存储帐户名、容器名和 SAS 令牌组成。 此参数创建使用基于密钥的身份验证的终结点。

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

将此参数添加到前面的创建终结点部分中的终结点创建命令的末尾，以创建启用了死信的所需类型的终结点。

或者，可以使用 [Azure 数字孪生控制平面 API](concepts-apis-sdks.md#overview-control-plane-apis) 而不是 CLI 创建死信终结点。 为此，请查看 [DigitalTwinsEndpoint 文档](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)，了解如何构造请求并添加死信参数。

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>创建使用基于身份的身份验证的死信终结点

还可以创建具有基于身份的身份验证的死信终结点，以使用具有[托管标识](concepts-security.md#managed-identity-for-accessing-other-resources)的终结点。 此选项仅适用于事件中心和服务总线类型终结点（事件网格不支持此选项）。

若要创建这种类型的终结点，请使用与前面相同的 CLI 命令[创建使用基于身份的身份验证的终结点](#create-an-endpoint-with-identity-based-authentication)，并在 JSON 有效负载中为 `deadLetterUri` 添加一个额外字段。

以下是需要插入命令占位符的值：
* Azure 数字孪生实例的 Azure 资源 ID
* 终结点名称
* 终结点类型
* 终结点资源的命名空间
* 事件中心或服务总线主题的名称
* “死信 SAS URI”详细信息：存储帐户名、容器名
* Azure 数字孪生实例的位置

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

#### <a name="message-storage-schema"></a>消息存储架构

设置了使用死信的终结点后，经过死信处理的消息将采用以下格式存储在存储帐户中：

`<container>/<endpoint-name>/<year>/<month>/<day>/<hour>/<event-ID>.json`

经过死信处理的消息将与打算传递到原始终结点的原始事件的架构匹配。

下面是[孪生体创建通知](concepts-event-notifications.md#digital-twin-lifecycle-notifications)的死信消息示例：

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>创建事件路由

若要实际地将数据从 Azure 数字孪生发送到终结点，需要定义一个事件路由。 通过这些路由，开发人员可在整个系统中串联事件流并连接到下游服务。 单一路由可允许选择多个通知和事件类型。 有关事件路由的详细信息，请参阅[路由 Azure 数字孪生事件](concepts-route-events.md)。

先决条件：在继续创建路由之前，需要按照本文前面所述创建终结点。 完成终结点设置后，可以继续创建事件路由。

>[!NOTE]
>如果最近部署了终结点，在尝试将它们用于新的事件路由之前，请验证它们是否已完成部署。 如果由于终结点未就绪而导致路由部署失败，请等待几分钟，然后重试。
>
> 如果要编写此流的脚本，你可能希望为终结点服务完成部署留出 2-3 分钟的等待时间，然后再继续进行路由设置，从而解决此问题。

路由定义可包含以下元素：
* 要使用的路由名称
* 要使用的终结点名称
* 用于定义发送到终结点的事件的筛选器
    - 若要禁用路由而不发送事件，请使用筛选器值 `false`
    - 若要启用未指定筛选条件的路由，请使用筛选器值 `true`
    - 有关任何其他类型的筛选器的详细信息，请参阅下面的[筛选器事件](#filter-events)部分

如果没有路由名称，则不会在 Azure 数字孪生之外路由任何消息。 如果存在路由名称且筛选器为 `true`，则所有消息都将路由到终结点。 如果存在路由名称并添加了不同的筛选器，则将根据筛选器筛选消息。

可使用 [Azure 门户](https://portal.azure.com)、[EventRoutes 数据平面 API](/rest/api/digital-twins/dataplane/eventroutes) 或 [az dt route CLI 命令](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true)来创建事件路由。 本部分的其余内容将介绍创建过程。

# <a name="portal"></a>[Portal](#tab/portal2)

若要创建事件路由，请在 [Azure 门户](https://portal.azure.com)中转到 Azure 数字孪生实例的详细信息页（可以通过在门户搜索栏中输入名称来查找实例）。

从实例菜单中选择“事件路由”。 然后，在显示的“事件路由”页选择“+ 创建事件路由”。 

在打开的“创建事件路由”页上，至少选择以下项目：
* “名称”字段的路由名称
* 要用于创建路由的终结点 

要启用该路由，还必须“添加事件路由筛选器”，其值至少为 `true`。 （保留默认值 `false` 会创建路由，但不会向其发送事件。）因此，请切换“高级编辑器”的开关将其启用，并在“筛选器”框中写入 `true`。

:::image type="content" source="media/how-to-manage-routes/create-event-route-no-filter.png" alt-text="在 Azure 门户中为实例创建事件路由的屏幕截图。" lightbox="media/how-to-manage-routes/create-event-route-no-filter.png":::

完成后，选择“保存”按钮创建事件路由。

# <a name="cli"></a>[CLI](#tab/cli2)

可以使用 Azure 数字孪生 CLI 的 [az dt route](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true) 命令来管理路由。 

若要详细了解如何使用 CLI 以及哪些命令可用，请参阅 [Azure 数字孪生 CLI 命令集](concepts-cli.md)。

# <a name="net-sdk"></a>[.NET SDK](#tab/sdk2)

此部分介绍如何使用 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 创建事件路由。

`CreateOrReplaceEventRouteAsync` 是用于添加事件路由的 SDK 调用。 下面是它的用法示例：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> 所有 SDK 函数都提供同步和异步版本。

#### <a name="event-route-sample-sdk-code"></a>事件路由示例 SDK 代码

下面的示例方法演示如何使用 C# SDK 创建、列出和删除事件路由：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

---

## <a name="filter-events"></a>筛选事件

如上所述，路由包含“筛选器”字段。 如果路由上的筛选器值为 `false`，则不会向终结点发送任何事件。 

启用最小筛选器 `true` 后，终结点将从 Azure 数字孪生接收各种事件：
* 遥测，由[数字孪生](concepts-twins-graph.md)使用 Azure 数字孪生服务 API 触发
* 孪生属性更改通知，在 Azure 数字孪生实例中的任何孪生属性更改时触发
* 生命周期事件，在创建或删除孪生体或关系时触发

可以通过定义更具体的筛选器来限制将发送的事件类型。

>[!NOTE]
> 筛选器区分大小写，并需要与有效负载大小写匹配。 
>
> 对于遥测筛选器，这意味着大小写需要与设备发送的遥测的大小写匹配，而不一定是在孪生体的模型中定义的大小写。

# <a name="portal"></a>[Portal](#tab/portal3)

要在创建事件路由时添加事件筛选器，请使用“创建事件路由”页的“添加事件路由筛选器”部分。 

可以从一些基本的常用筛选器选项中选择，也可以使用高级筛选器选项编写自己的自定义筛选器。

### <a name="use-the-basic-filters"></a>使用基本筛选器

若要使用基本筛选器，请展开“事件类型”选项，并选择与要发送到终结点的事件对应的复选框。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-1.png" alt-text="在 Azure 门户中使用基本筛选器创建事件路由的屏幕截图，其中突出显示了事件的复选框。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

筛选器文本框中将自动填充所选筛选器的文本：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-2.png" alt-text="在 Azure 门户中使用基本筛选器创建事件路由的屏幕截图，其中突出显示了选择事件后自动填充的筛选器文本。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="use-the-advanced-filters"></a>使用高级筛选器

也可以使用高级筛选器选项来编写自己的自定义筛选器。

要使用高级筛选器选项创建事件路由，请切换“高级筛选器”的开关将其启用。 然后，则可以在“筛选器”框中编写自己的事件筛选器：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-advanced.png" alt-text="在 Azure 门户中使用高级筛选器创建事件路由的屏幕截图。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

# <a name="api"></a>[API](#tab/api)

可以使用 API 编写自定义筛选器。 若要添加筛选器，可对 `https://<Your-Azure-Digital-Twins-host-name>/eventRoutes/<event-route-name>?api-version=2020-10-31` 使用 PUT 请求，正文如下：

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

---

### <a name="supported-route-filters"></a>支持的路由筛选器

下面是受支持的路由筛选器。

| 筛选器名称 | 说明 | 筛选器文本架构 | 支持的值 | 
| --- | --- | --- | --- |
| True/False | 允许创建无筛选的路由，或禁用路由，这样就不会发送事件 | `<true/false>` | `true` = 启用无筛选的路由 <br> `false` = 禁用路由 |
| 类型 | 流经数字孪生体实例的[事件类型](concepts-route-events.md#types-of-event-messages) | `type = '<event-type>'` | 以下是可能的事件类型值： <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 源 | Azure 数字孪生实例的名称 | `source = '<host-name>'`| 以下是可能的主机名值： <br> **用于通知**：`<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net` <br> **用于遥测**：`<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net/<twin-ID>`|
| 使用者 | 上述事件源上下文中事件的说明 | `subject = '<subject>'` | 以下是可能的使用者值： <br>**对于通知**：使用者为 `<twin-ID>` <br> 或使用者的 URI 格式（由多个部件或 ID 唯一标识）：<br>`<twin-ID>/relationships/<relationship-ID>`<br> **对于遥测**：使用者是组件路径（如果遥测是从一个孪生体组件发出的），例如 `comp1.comp2`。 如果遥测不是从组件发出的，则其使用者字段为空。 |
| 数据架构 | DTDL 模型 ID | `dataschema = '<model-dtmi-ID>'` | **对于遥测**：数据架构是孪生体或发出遥测的组件的模型 ID。 例如： `dtmi:example:com:floor4;2` <br>**对于通知（创建/删除）** ：可以通过 `$body.$metadata.$model` 访问通知正文中的数据架构。 <br>**对于通知（更新）** ：可以通过 `$body.modelId` 访问通知正文中的数据架构|
| 内容类型 | 数据值的内容类型 | `datacontenttype = '<content-type>'` | 内容类型为 `application/json` |
| 规范版本 | 所使用的事件架构的版本 | `specversion = '<version>'` | 版本必须为 `1.0`。 这指示 CloudEvents 架构版本 1.0 |
| 通知正文 | 引用通知的 `data` 字段中的任何属性 | `$body.<property>` | 有关通知示例，请参阅[事件通知](concepts-event-notifications.md)。 `data` 字段中的任何属性都可以使用 `$body` 进行引用

>[!NOTE]
> Azure 数字孪生目前不支持基于数组中的字段筛选事件。 这包括对[数字孪生更改通知](concepts-event-notifications.md#digital-twin-change-notifications)的 `patch` 部分中的属性进行筛选。

以下数据类型可以用作对上述数据进行引用后返回的值：

| 数据类型 | 示例 |
|-|-|-|
|**字符串**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twin-ID>')`|
|**整数**|`$body.errorCode > 200`|
|**双精度**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

定义路由筛选器时支持以下运算符：

|系列|运算符|示例|
|-|-|-|
|逻辑|AND、OR、( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|比较|<、<=、>、>=、=、!=|`$body.temperature <= 5.5`

定义路由筛选器时支持以下函数：

|函数|说明|示例|
|--|--|--|
|STARTS_WITH(x,y)|如果值 `x` 以字符串 `y` 开头，则返回 true。|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x,y) | 如果值 `x` 以字符串 `y` 结尾，则返回 true。|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| 如果值 `x` 包含字符串 `y`，则返回 true。|`CONTAINS(subject, '<twin-ID>')`|

实现或更新筛选器时，更改可能需要几分钟时间才能在数据管道中反映出来。

## <a name="monitor-event-routes"></a>监视事件路由

可在 [Azure 门户](https://portal.azure.com/)中查看计数、延迟和失败率等路由指标。 

在门户主页上，搜索 Azure 数字孪生实例以获取其详细信息。 从左侧的 Azure 数字孪生实例的导航菜单中选择“指标”选项，以打开“指标”页。

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="此屏幕截图显示了 Azure 数字孪生的“指标”页。":::

在此处，你可查看实例的指标并创建自定义视图。

若要详细了解如何查看 Azure 数字孪生指标，请参阅[使用 Azure Monitor 查看指标](troubleshoot-metrics.md)。

## <a name="next-steps"></a>后续步骤

阅读可接收的不同类型的事件消息：
* [事件通知](concepts-event-notifications.md)