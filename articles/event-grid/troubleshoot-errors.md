---
title: Azure 事件网格 - 故障排除指南
description: 本文提供错误代码列表、错误消息、说明和建议的措施。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 79533918ccc6995f459b39f058de9e01091c0958
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592985"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>排查 Azure 事件网格错误
此故障排除指南提供以下信息： 

- Azure 事件网格错误代码
- Error messages
- 错误描述
- 收到这些错误时应采取的建议操作。 

## <a name="error-code-400"></a>错误代码：400
| 错误代码 | 错误消息 | 说明 | 建议 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | 主题名称的长度必须为 3 到 50 个字符。 | 自定义主题名称的长度应为 3 到 50 个字符。 主题名称中只允许字母数字字符、数字和“-”字符。 此外，名称的开头不能是以下保留字： <ul><li>微软</li><li>EventGrid</li><li>主板</li></ul> | 请选择符合主题名称要求的其他主题名称。 |
| HttpStatusCode.BadRequest<br/>400 | 域名的长度必须为 3 到 50 个字符。 | 域名的长度应为 3 到 50 个字符。 域名中只允许使用字母数字字符、数字和 "-" 字符。 此外，名称的开头不能是以下保留字：<ul><li>微软</li><li>EventGrid</li><li>主板</li> | 请选择符合域名要求的其他域名。 |
| HttpStatusCode.BadRequest<br/>400 | 过期时间无效。 | 事件订阅的过期时间决定了事件订阅何时停用。 此值应是将来的有效日期时间值。| 确保事件订阅过期时间采用有效的日期时间格式，并设置为将来的时间。 |

## <a name="error-code-409"></a>错误代码：409
| 错误代码 | 错误消息 | 说明 | 建议的操作 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | 已存在具有指定名称的主题。 请选择其他主题名称。   | 在单个 Azure 区域中，自定义主题名称应是唯一的，以确保正确发布操作。 同一名称可在不同的 Azure 区域中使用。 | 请为主题选择其他名称。 |
| HttpStatusCode.Conflict <br/> 409 | 已存在具有指定名称的域。 请选择其他域名。 | 域名在单个 Azure 区域中应该是唯一的，以确保正确发布操作。 同一名称可在不同的 Azure 区域中使用。 | 请为该域选择其他名称。 |
| HttpStatusCode.Conflict<br/>409 | 已达配额限制。 有关这些限制的详细信息，请参阅 [Azure 事件网格限制](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)。  | 每个 Azure 订阅可使用的 Azure 事件网格资源数量有限制。 已超过部分或全部配额，无法创建更多的资源。 |    请检查当前的资源用量，并删除任何不需要的资源。 如果仍需提高配额，请向 [aeg@microsoft.com](mailto:aeg@microsoft.com) 发送电子邮件并在其中指出所需的确切资源数。 |

## <a name="error-code-403"></a>错误代码：403

| 错误代码 | 错误消息 | 说明 | 建议的操作 |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode。禁止 <br/>403 | 由于 IpAddress 筛选规则，客户端 {IpAddress} 发布到 {主题/域} 已被拒绝。 | 主题或域配置了 IP 防火墙规则，访问仅限于配置的 IP 地址。 | 向 IP 防火墙规则添加 IP 地址，请参阅 [配置 ip 防火墙](configure-firewall.md) |
| HttpStatusCode。禁止 <br/> 403 | 由于请求来自专用终结点，并且没有为资源找到匹配的专用终结点连接，因此，客户端发布到 {主题/域} 时被拒绝。 | 主题或域具有专用终结点，并且发布请求来自未配置或批准的专用终结点。 | 配置主题/域的专用终结点。 [配置专用终结点](configure-private-endpoints.md) |

同时，检查 webhook 是否位于 Azure 应用程序网关或 Web 应用程序防火墙后面。 如果是这样，请禁用以下防火墙规则，并再次执行 HTTP POST：

- 920300 (请求缺少 accept 标头) 
- 942430 (受限制的 SQL 字符异常检测 (参数) ：超出 (了 12) # A5 的特殊字符数
- 920230 (检测到多个 URL 编码) 
- 942130 (SQL 注入攻击：检测到 SQL tautology。 ) 
- 931130 (可能的远程文件包含 (RFI) 攻击 = 域外引用/链接) 



## <a name="next-steps"></a>后续步骤
如需更多帮助，请在 [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid)中发布问题，或开具[支持票证](https://azure.microsoft.com/support/options/)。 
