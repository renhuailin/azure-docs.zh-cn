---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 3e2b9ab4eaf794e5cf64861d7c641b82f18b64dc
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201011"
---
| 资源 | 限制 | 注意 |
| --- | --- | --- |
| Azure 应用程序网关 |每个订阅 1000 个 | |
| 前端 IP 配置数 |2 |1 个公共，1 个专用 |
| 前端端口 |100<sup>1</sup> | |
| 后端地址池 |100<sup>1</sup> | |
| 每个池的后端服务器数 |1,200 | |
| HTTP 侦听器 |200<sup>1</sup> |限制为正在路由流量的 100 个活动侦听器。 活动侦听器数 = 侦听器的总数 - 处于非活动状态的侦听器数。<br>如果路由规则中的默认配置已设置为路由流量（例如，它具有侦听器、后端池和 HTTP 设置），则也会将其计为侦听器。|
| HTTP 负载均衡规则 |100<sup>1</sup> | |
| 后端 HTTP 设置 |100<sup>1</sup> | |
| 每个网关的实例数 |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL 证书数 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| 最大 SSL 证书大小 |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| 身份验证证书 |100 | |
| 受信任的根证书 |100 | |
| 最短请求超时 |1 秒 | |
| 最长请求超时 |24 小时 | |
| 站点数 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| 每个侦听器的 URL 映射数 |1 | |
| 每个 URL 映射基于路径的最大规则数|100||
| 重定向配置数 |100<sup>1</sup>| |
| 重写规则集的数目 |400| |
| 每个重写规则集的标头数或 URL 配置数|40| |
| 每个重写规则集的条件数|40| |
| 并发的 WebSocket 连接数 |中型网关 2 万<sup>2</sup><br> 大型网关 5 万<sup>2</sup>| |
| 最大 URL 长度|32KB| |
| 适用于 HTTP/2 的最大标头大小 |16KB| |
| 最大文件上传大小（标准 SKU） |V2 - 4 GB<br>V1 - 2 GB | |
| 最大文件上传大小 (WAF SKU) |V1 中等 - 100 MB<br>V1 大型 - 500 MB<br>V2 - 750 MB<br>V2（带有 CRS 3.2 或更高版本）- 4 GB| |
| WAF 正文大小限制（不带文件）|V1 或 V2（带有 CRS 3.1 及更低版本）- 128 KB<br>V2（带有 CRS 3.2 或更高版本）- 2 MB| |
| 最大 WAF 自定义规则数|100||
| 每个应用程序网关的最大 WAF 排除项数|40||

<sup>1</sup> 对于启用了 WAF 的 SKU，必须将资源数限制为 40。

<sup>2</sup> 限制是基于每个应用程序网关实例，并非每个应用程序网关资源。
