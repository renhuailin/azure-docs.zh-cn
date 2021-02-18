---
title: 配置 Azure 前门标准/高级规则集操作
description: 本文提供了可使用 Azure 前门规则集执行的各种操作的列表。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098737"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Azure 前门标准/高级规则集操作

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

Azure 前门 [规则集](concept-rule-set.md) 由包含匹配条件和操作组合的规则组成。 本文详细说明了可在规则集中使用的操作。 操作定义应用于) 确定的匹配条件 (的请求类型的行为。 在 Azure 前门规则集中，一个规则最多可以包含五个操作。 支持对所有操作执行服务器变量。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

以下操作可用于 Azure 前门规则集。  

## <a name="cache-expiration"></a>缓存到期

使用此操作来覆盖与规则匹配条件指定的请求的终结点的生存时间 (TTL) 值。

### <a name="required-fields"></a>Required fields

选择这些缓存行为和规则匹配时，将应用以下说明：

缓存行为 |  说明              
---------------|----------------
绕过缓存 | 不缓存内容。
替代 | 从源返回的 TTL 值将被该操作中指定的值覆盖。 仅当响应可缓存时才会应用此行为。 对于包含值 "非缓存"、"private"、"非商店" 的缓存控制响应标头，操作将不适用。
缺少时设置 | 如果从源返回的 TTL 值无效，则规则会将 TTL 设置为操作中指定的值。 仅当响应可缓存时才会应用此行为。 对于包含值 "非缓存"、"private"、"非商店" 的缓存控制响应标头，操作将不适用。

### <a name="additional-fields"></a>其他字段

天 | 小时 | 分钟数 | 秒
-----|-------|---------|--------
int | int | int | int 

## <a name="cache-key-query-string"></a>缓存键查询字符串

使用此操作可以根据查询字符串修改缓存键。

### <a name="required-fields"></a>Required fields

选择这些行为和规则匹配时，将应用以下说明：

行为 | 描述
---------|------------
包括 | 在生成缓存键时，将在参数中指定的查询字符串包含在内。 
缓存每个唯一的 URL | 每个唯一的 URL 都有其自己的缓存密钥。 
Exclude | 生成缓存键后，将排除参数中指定的查询字符串。
忽略查询字符串 | 生成缓存键时不考虑查询字符串。 

## <a name="modify-request-header"></a>修改请求标头

使用此操作可以修改发送到源的请求中提供的标头。

### <a name="required-fields"></a>Required fields

选择这些操作和规则匹配时，将应用以下说明：

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | **标头名称** 中指定的标头将添加到具有指定值的请求。 如果该标头已存在，则会将该值追加到现有值后面。 | 字符串
Overwrite | **标头名称** 中指定的标头将添加到具有指定值的请求。 如果该标头已存在，则指定的值将替代现有值。 | 字符串
删除 | 如果规则中指定的标头存在，则将从请求中删除标头。 | 字符串

## <a name="modify-response-header"></a>修改响应标头

使用此操作可以修改返回给客户端的响应中提供的标头。

### <a name="required-fields"></a>Required fields

选择这些操作和规则匹配时，将应用以下说明：

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | 使用指定的 **值** 将 **标头名称** 中指定的标头添加到响应中。 如果该标头已存在，则会将该“值”追加到现有值后面。 | 字符串
Overwrite | 使用指定的 **值** 将 **标头名称** 中指定的标头添加到响应中。 如果该标头已存在，则该“值”将替代现有值。 | 字符串
删除 | 如果规则中指定的标头存在，则会从响应中删除标头。 | 字符串

## <a name="url-redirect"></a>URL 重定向

使用此操作可将客户端重定向到一个新 URL。 

### <a name="required-fields"></a>Required fields

字段 | 说明 
------|------------
重定向类型 | 选择要返回给请求方的响应类型：“已找到”(302)、“已移动”(301)、“临时重定向”(307) 和“永久重定向”(308)。
重定向协议 | 匹配请求、HTTP、HTTPS。
目标主机 | 选择要将请求重定向到的主机名。 留空会保留传入主机。
目标路径 | 定义要在重定向中使用的路径。 留空会保留传入路径。  
查询字符串 | 定义重定向中使用的查询字符串。 留空会保留传入的查询字符串。 
目标片段 | 定义要在重定向中使用的片段。 留空会保留传入片段。 

## <a name="url-rewrite"></a>URL 重写

使用此操作可以重写路由到源的请求的路径。

### <a name="required-fields"></a>Required fields

字段 | 描述 
------|------------
源模式 | 在要替换的 URL 路径中定义源模式。 当前，源模式使用基于前缀的匹配项。 若要匹配所有 URL 路径，请使用正斜杠 (**/**) 为源模式值。
目标 | 定义要在重写中使用的目标路径。 目标路径将覆盖源模式。
保留不匹配的路径 | 如果设置为 **"是"**，则会将源模式后面的剩余路径追加到新的目标路径。 

## <a name="server-variable"></a>服务器变量

### <a name="supported-variables"></a>支持的变量

| 变量名称 | 说明                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | 与 Azure 前门边缘的直接连接的 IP 地址。 如果客户端使用 HTTP 代理或负载均衡器来发送请求，则 SocketIp 的值为代理或负载均衡器的 IP 地址。 |
| client_ip                  | 发出原始请求的客户端的 IP 地址。 如果请求中有一个 X 转发的标头，则从同一获取客户端 IP。 |
| client_port                | 发出请求的客户端的 IP 端口。 |
| hostname                      | 来自客户端的请求中的主机名。 |
| geo_country                     | 指示申请者所在的国家/地区（用国家/地区代码表示）。 |
| http_method                | 用于发出 URL 请求的方法。 例如 GET 或 POST。 |
| http_version               | 请求协议。 通常为 HTTP/1.0、HTTP/1.1 或 HTTP/2.0。 |
| query_string               | 请求的 URL 中“?”后面的变量/值对列表。 示例：在请求 http://contoso.com:8080/article.aspx?id=123&title=fabrikam 中，query_string 值将为 id=123&title=fabrikam  |
| request_scheme             | 请求方案：http 或 https。 |
| request_uri                | 完整的原始请求 URI（带参数）。 示例：在请求 http://contoso.com:8080/article.aspx?id=123&title=fabrikam 中，request_uri 值将为 /article.aspx?id=123&title=fabrikam  |
| server_port                | 接受请求的服务器端口。 |
| ssl_protocol    | 已建立的 TLS 连接的协议。 |
| url_path                   | 标识 Web 客户端要访问的主机中的特定资源。 这是请求 URI 中没有参数的部分。 示例：在请求 http://contoso.com:8080/article.aspx?id=123&title=fabrikam 中，uri_path 值将为 /article.aspx  |

### <a name="server-variable-format"></a>服务器变量格式    

**格式：** {variable： offset}，{variable： offset： length}，{variable}

### <a name="supported-server-variable-actions"></a>支持的服务器变量操作

* 请求标头
* 响应标头
* 缓存键查询字符串
* URL 重写
* URL 重定向

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 前门 Stanard/高级规则集](concept-rule-set.md)。
* 详细了解 [规则集匹配条件](concept-rule-set-match-conditions.md)。
