---
title: include 文件
description: include 文件
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 06/25/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: a7035cff720b7eee4467f355a533a19f059c4baa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014609"
---
| 资源 | 限制 |
| --- | --- |
| 每个订阅的 Azure Front Door 资源数 | 100 |
| 每个资源的前端主机数（包括自定义域） | 500 |
| 每个资源的路由规则数 | 500 |
| 每个资源的后端池数 | 50 |
| 每个后端池的后端数 | 100 |
| 要匹配路由规则的路径模式数 | 25 |
| 单个缓存清除调用中的 URL | 100 |
| 每个策略的自定义 Web 应用程序防火墙规则数 | 100 |
| 每个订阅的 Web 应用程序防火墙策略 | 100 |
| Web 应用程序防火墙根据每个自定义规则匹配条件数 | 10 |
| 每个匹配条件的 Web 应用防火墙 IP 地址范围数 | 600 |
| 每个匹配条件的 Web 应用程序防火墙字符串匹配值数 | 10 |
| Web 应用程序防火墙字符串匹配值长度 | 256 |
| Web 应用程序防火墙 POST 正文参数名称长度 | 256 |
| Web 应用程序防火墙 HTTP 标头名称长度 | 256 |
| Web 应用程序防火墙 Cookie 名称长度 | 256 |
| Web 应用程序防火墙排除限制 | 100 |
| 已检查的 Web 应用程序防火墙 HTTP 请求正文大小 | 128 KB |
| Web 应用程序防火墙自定义响应正文长度 | 2 KB |

### <a name="azure-front-door-standardpremium-preview-service-limits"></a>Azure Front Door 标准版/高级版（预览版）服务限制

*** 每个订阅总共最多 500 个标准版和高级版配置文件。

| 资源 | 标准 SKU 限制 | 高级 SKU 限制 |
| --- | --- | --- |
| 每个配置文件的最大终结点数  | 10 | 25 |
| 每个配置文件的最大自定义域数 | 100 | 200 |
| 每个配置文件的最大源组数 | 100 | 200 |
| 每个配置文件的最大机密数 | 100 | 200 |
| 每个配置文件的最大安全策略数 | 100 | 200 |
| 每个配置文件的最大规则集数 | 100 | 200 |
| 每个规则集的最大规则数 | 100 | 100 |
| 每个源组的最大源数 | 50 | 50 |
| 每个终结点的最大路由数 | 100 | 200 |
| 单个缓存清除调用中的 URL | 100 | 100 |
| 每个策略的自定义 Web 应用程序防火墙规则数 | 100 | 100 |
| Web 应用程序防火墙根据每个自定义规则匹配条件数 | 10 | 10 |
| 每个匹配条件的 Web 应用防火墙 IP 地址范围数 | 600 | 600 |
| 每个匹配条件的 Web 应用程序防火墙字符串匹配值数 | 10 | 10 |
| Web 应用程序防火墙字符串匹配值长度 | 256 | 256 |
| Web 应用程序防火墙 POST 正文参数名称长度 | 256 | 256 |
| Web 应用程序防火墙 HTTP 标头名称长度 | 256 | 256 |
| Web 应用程序防火墙 Cookie 名称长度 | 256 | 256|
| 已检查的 Web 应用程序防火墙 HTTP 请求正文大小 | 128 KB | 128 KB |
| Web 应用程序防火墙自定义响应正文长度 | 2 KB | 2 KB |

#### <a name="timeout-values"></a>超时值
##### <a name="client-to-front-door"></a>客户端到 Front Door
* Front Door 的空闲 TCP 连接超时为 61 秒。

##### <a name="front-door-to-application-back-end"></a>Front Door 到应用程序后端
* 如果响应是分块响应，则在收到第一个区块时返回 200。
* HTTP 请求转发到后端后，Front Door 将等待来自后端的第一个数据包 30 秒。 然后，它会向客户端返回 503 错误。 此值可通过 API 中的字段 sendRecvTimeoutSeconds 进行配置。
    * 如果缓存了某个请求，并且来自 Front Door 或后端的第一个数据包花费时间超过 30 秒，会向客户端返回 504 错误。 
* 从后端收到第一个数据包后，Front Door 将在空闲超时中等待 30 秒。 然后，它会向客户端返回 503 错误。 此超时值是不可配置的。
* Front Door 到后端的 TCP 会话超时为 90 秒。

#### <a name="upload-and-download-data-limit"></a>上传和下载数据限制

|  | 使用分块传输编码 (CTE) | 不使用 HTTP 分块 |
| ---- | ------- | ------- |
| **下载** | 下载大小没有限制。 | 下载大小没有限制。 |
| **上传** |    只要每个 CTE 上传都小于 2 GB，就没有限制。 | 大小不得大于 2 GB。 |

#### <a name="other-limits"></a>其他限制
* 最大 URL 大小 - 8,192 字节 - 指定原始 URL 的最大长度（方案 + 主机名 + 端口 + 路径 + URL 的查询字符串）
* 最大查询字符串大小 - 4,096 字节 - 指定查询字符串的最大长度（以字节为单位）。
* 运行状况探测 URL 中的最大 HTTP 响应标头大小 - 4,096 字节 - 指定运行状况探测的所有响应标头的最大长度。 
* 规则引擎操作标头值最大字符数：640 个字符。
* 规则引擎条件标头值最大字符数：256 个字符。
* 最大 ETag 标头大小：128 字节
