---
title: 通过共享专用终结点保护 Azure SignalR 出站流量
titleSuffix: Azure SignalR Service
description: 如何通过共享专用终结点保护出站流量，以避免流量进入公共网络
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 07/08/2021
ms.author: dayshen
ms.openlocfilehash: 4be77c9dce68c55a37713c42ceee7fc2b7dbf6d9
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220184"
---
# <a name="secure-azure-signalr-outbound-traffic-through-shared-private-endpoints"></a>通过共享专用终结点保护 Azure SignalR 出站流量

如果在 Azure SignalR 服务中使用[无服务器模式](concept-service-mode.md#serverless-mode)，可能会有到上游的出站流量。 上游（例如 Azure Web 应用和 Azure Functions）可以配置为接受来自虚拟网络列表的连接，并拒绝源自公用网络的外部连接。 可以创建出站[专用终结点连接](../private-link/private-endpoint-overview.md)来访问这些终结点。

   :::image type="content" alt-text="共享专用终结点概述。" source="media\howto-shared-private-endpoints\shared-private-endpoint-overview.png" :::

此出站方法需要符合下列要求：

+ 上游必须是 Azure Web 应用或 Azure Function。

+ Azure SignalR 服务必须位于标准层中。

+ Azure Web 应用或 Azure Function 必须位于特定 SKU 中。 请参阅[为 Azure Web 应用使用专用终结点](../app-service/networking/private-endpoint.md)。

## <a name="shared-private-link-resources-management-apis"></a>共享的专用链接资源管理 API

通过 Azure SignalR 服务 API 创建的安全资源的专用终结点称为“共享专用链接资源”。 这是因为你是在“共享”对已与 [Azure 专用链接服务](https://azure.microsoft.com/services/private-link/)集成的资源（例如 Azure Function）的访问权限。 这些专用终结点是在 Azure SignalR 服务执行环境中创建的，并且不会直接显示出来。

此时，你可以使用管理 REST API 来创建或删除共享专用链接资源。 在本文的其余部分，我们将使用 [Azure CLI](/cli/azure/) 来演示 REST API 调用。

> [!NOTE]
> 本文中的示例基于以下假设：
> * 此 Azure SignalR 服务的资源 ID 为 /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr。
> * Azure Function 上游的资源 ID 为 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func。

其余示例显示了如何配置 contoso-signalr 服务，以便其上游调用可以通过专用终结点进行（而不是通过公用网络）。

### <a name="step-1-create-a-shared-private-link-resource-to-the-function"></a>步骤 1：创建函数的共享专用链接资源

可以使用 [Azure CLI](/cli/azure/) 进行下列 API 调用，以创建共享专用链接资源：

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview --body @create-pe.json
```

代表 API 的请求正文的 create-pe.json 文件的内容如下：

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve"
      }
}
```

创建出站专用终结点的过程是一个长期（异步）操作。 与所有异步 Azure 操作一样，`PUT` 调用返回一个 `Azure-AsyncOperation` 标头值，如下所示：

```plaintext
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview"
```

可以定期轮询此 URI 以获取操作状态。

如果使用的是 CLI，可以通过手动查询 `Azure-AsyncOperationHeader` 值来轮询状态。

```donetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview
```

等待状态更改为“成功”，再继续执行后续步骤。

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-function"></a>步骤 2a：为函数审批专用终结点连接

> [!NOTE]
> 在此部分，你会使用 Azure 门户逐步完成到 Azure Function 的专用终结点的审批流。 或者，可以使用通过应用服务提供程序提供的 [REST API](/rest/api/appservice/web-apps/approve-or-reject-private-endpoint-connection)。

> [!IMPORTANT]
> 批准专用终结点连接后，将无法再从公共网络访问函数。 可能需要在自己的虚拟网络中创建其他专用终结点才能访问函数终结点。

1. 在 Azure 门户中，选择函数应用的“网络”选项卡，然后导航到“专用终结点连接”。 单击“配置专用终结点连接”。 异步操作成功后，应该会有一个专用终结点连接的请求，请求消息来自于之前的 API 调用。

   :::image type="content" alt-text="Azure 门户的屏幕截图，其中显示“专用终结点连接”窗格。" source="media\howto-shared-private-endpoints\portal-function-approve-private-endpoint.png" :::

1. 选择 Azure SignalR 服务创建的专用终结点。 在“专用终结点”列中，按先前 API 中指定的名称标识专用终结点连接，选择“批准”。

   请确保显示的专用终结点连接如以下屏幕截图所示。 状态可能需要一到两分钟的时间才能在门户中更新。

   :::image type="content" alt-text="Azure 门户的屏幕截图，其中显示“专用终结点连接”窗格上的“已批准”状态。" source="media\howto-shared-private-endpoints\portal-function-approved-private-endpoint.png" :::

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>步骤 2b：查询共享专用链接资源的状态

批准传播到 Azure SignalR 服务需要几分钟时间。 若要在批准后确认共享专用链接资源已更新，还可以使用 GET API 获取“连接状态”。

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview
```

这会返回一个 JSON，其中连接状态会在“属性”部分下显示为“状态”。

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve",
        "status": "Approved",
        "provisioningState": "Succeeded"
      }
}

```

如果资源的“预配状态”(`properties.provisioningState`) 为 `Succeeded`，“连接状态”(`properties.status`) 为 `Approved`，则表示共享专用链接资源正常工作，Azure SignalR 服务可以通过专用终结点进行通信。

### <a name="step-3-verify-upstream-calls-are-from-a-private-ip"></a>步骤 3：验证上游调用是否来自专用 IP

设置专用终结点后，可以通过检查上游端的 `X-Forwarded-For` 标头来验证来自专用 IP 的传入调用。

:::image type="content" alt-text="Azure 门户屏幕截图，显示传入请求来自专用 IP。" source="media\howto-shared-private-endpoints\portal-function-log.png" :::

## <a name="next-steps"></a>后续步骤

详细了解专用终结点：

+ [什么是专用终结点？](../private-link/private-endpoint-overview.md)