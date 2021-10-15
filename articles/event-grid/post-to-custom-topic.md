---
title: 将事件发布到自定义 Azure 事件网格主题
description: 本文说明如何将事件发布到自定义主题。 它显示发布和事件数据的格式。
ms.topic: conceptual
ms.date: 08/19/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 489ad5ab07a411612bdbce1a2dc71dc1a3441f3c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361508"
---
# <a name="publish-events-to-azure-event-grid-custom-topics-using-access-keys"></a>使用访问密钥将事件发布到 Azure 事件网格的自定义主题

本文说明如何使用访问密钥发布自定义主题事件。 它显示发布和事件数据的格式。 [服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) 仅适用于与预期格式匹配的发布。


> [!NOTE]
> Azure AD 身份验证提供的身份验证支持优于访问密钥或共享访问签名 (SAS) 令牌身份验证所提供的身份验证支持。 使用 Azure AD 身份验证时，将针对 Azure AD 标识提供者来验证标识。 作为开发人员，如果使用 Azure AD 身份验证，则不需要在代码中处理密钥。 还会受益于内置于 Microsoft 标识平台的所有安全功能，例如条件访问，有助于提高应用程序的安全性情况。 有关详细信息，请参阅[使用 Azure Active Directory 对发布客户端进行身份验证](authenticate-with-active-directory.md)。

## <a name="endpoint"></a>端点

使用 URI 格式 `https://<topic-endpoint>?api-version=2018-01-01` 将 HTTP POST 发送到自定义主题。

例如，有效的 URI 为：`https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`。

若要使用 Azure CLI 获取自定义主题的终结点，请使用：

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

若要使用 Azure PowerShell 获取自定义主题的终结点，请使用：

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>标头

在请求中包含一个名为 `aeg-sas-key` 的标头值，其中包含身份验证密钥。

例如，有效的标头值为 `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`。

若要使用 Azure CLI 获取自定义主题的密钥，请使用：

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

若要使用 PowerShell 获取自定义主题的密钥，请使用：

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>事件数据

就自定义主题而言，顶级数据包含与标准资源所定义事件相同的字段。 这些属性之一是包含自定义主题所特有的属性的数据属性。 作为事件发布者，你确定该数据对象的属性。 使用以下架构：

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

有关这些属性的说明，请参阅 [Azure 事件网格事件架构](event-schema.md)。 将事件发布到事件网格主题时，数组的总大小最大可为 1 MB。 一个事件允许的最大大小也为 1 MB。 超过 64 KB 的事件以 64 KB 为增量计费。 

例如，有效的事件数据架构是：

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>响应

发布到主题终结点后，你将收到响应。 响应是标准 HTTP 响应代码。 一些常见的响应如下所示：

|结果  |响应  |
|---------|---------|
|Success  | 200 正常  |
|事件数据的格式不正确 | 400 错误请求 |
|访问密钥无效 | 401 未授权 |
|终结点不正确 | 404 未找到 |
|数组或事件超出大小限制 | 413 有效负载太大 |

对于错误，消息正文采用以下格式：

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 有关身份验证密钥的详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
