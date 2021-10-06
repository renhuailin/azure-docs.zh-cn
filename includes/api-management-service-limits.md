---
title: include 文件
description: include 文件
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 08/20/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 43358ec8c79e09d0701a098a2bd992f2cee8b23f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378057"
---
| 资源 | 限制 |
| ---------------------------------------------------------------------- | -------------------------- |
| 最大缩放单位数 | 每个区域 12 个<sup>1</sup> |
| 缓存大小 | 每个单位 5 GiB<sup>2</sup> |
| 每个 HTTP 颁发机构的并行后端连接<sup>3</sup> | 每个单位 2,048 个<sup>4</sup> |
| 最大缓存响应大小 | 2 MiB |
| 最大策略文档大小 | 256 KiB<sup>5</sup> |
| 每个服务实例的最大自定义网关域<sup>6</sup> | 20 |
| 每个服务实例的最大 CA 证书数<sup>7</sup> | 10 |
| 每个订阅的最大服务实例数<sup>8</sup> | 20 |
| 每个服务实例的最大订阅数<sup>8</sup> | 500 |
| 每个服务实例的最大客户端证书数<sup>8</sup> | 50 |
| 每个服务实例的最大 API 数<sup>8</sup> | 50 |
| 每个服务实例的最大 API 管理操作数<sup>8</sup> | 1,000 |
| 最大总请求持续时间<sup>8</sup> | 30 秒 |
| 请求有效负载最大大小<sup>8</sup> | 1 GiB |
| 最大缓冲有效负载大小<sup>8</sup> | 2 MiB |
| 最大请求 URL 大小<sup>9</sup> | 16384 字节 |
| URL 路径段的最大长度<sup>10</sup> | 260 个字符 |
| [验证策略](../articles/api-management/validation-policies.md)使用的 API 架构的最大大小<sup>10</sup> | 4 MB |
| [验证内容策略](../articles/api-management/validation-policies.md#validate-content)<sup>10</sup>中请求或响应正文的最大大小 | 100 KB |
| 最大自托管网关数目<sup>11</sup> | 25 |

<sup>1</sup>缩放限制取决于定价层。 若要查看定价层及其缩放限制的详细信息，请参阅 [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup>每单位缓存大小取决于定价层。 若要查看定价层及其缩放限制，请参阅 [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup>除非被后端明确关闭，否则将共用并重新使用连接。<br/>
<sup>4</sup>此限制按“基本”、“标准”和“高级”层级的单位计算。 开发人员层限制为 1,024。 此限制不适用于消耗层。<br/>
<sup>5</sup>此限制适用于“基本”、“标准”和“高级”层级。 在消耗层中，策略文档大小限制为 16 KiB。<br/>
<sup>6</sup>仅开发人员层和高级层中支持多个自定义域。<br/>
<sup>7</sup>消耗层中不支持 CA 证书。<br/>
<sup>8</sup>此限制仅适用于消耗层。 对于其他层，这些类别没有限制。<br/>
<sup>9</sup>仅适用于消耗层。 包含最长 2048 字节的查询字符串。<br/>
<sup>10</sup> 若要提高此限制，请联系[客户支持](https://azure.microsoft.com/support/options/)。<br/>
<sup>11</sup>仅开发人员层和高级层中支持自托管网关。 [自托管网关资源](/rest/api/apimanagement/2021-04-01-preview/gateway)数量受此限制约束。 若要增大此限额，请联系[支持人员](https://azure.microsoft.com/support/options/)。 请注意，与自托管网关资源相关联的节点（或副本）的数量在高级层中是不受限的，而在开发人员层中则限为单个节点。
