---
title: Azure Front Door - HTTP2 支持 | Microsoft Docs
description: 本文有助于你了解 Azure Front Door 中的 HTTP/2 支持
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: dc5679036eb241abc82a57779e41e2d667238216
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601419"
---
# <a name="http2-support-in-azure-front-door"></a>Azure Front Door 中的 HTTP/2 支持

目前，HTTP/2 支持对于所有 Azure Front Door 配置均有效。 客户无需进一步执行操作。

HTTP/2 是 HTTP/1.1 的一个主要修订版，通过减少响应时间来提供更高的 Web 性能。 HTTP/2 保留了 HTTP/1.1 熟悉的 HTTP 方法、状态代码和语义，以改善用户体验。 尽管 HTTP/2 根据设计是兼容 HTTP 和 HTTPS 的，但许多客户端 Web 浏览器仅支持基于传输层安全性 (TLS) 的 HTTP/2。

> [!NOTE]
> HTTP/2 协议支持仅适用于从客户端到 Front Door 的请求。 从 Front Door 到后端池中后端的通信通过 HTTP/1.1 进行。 

### <a name="http2-benefits"></a>HTTP/2 优点

HTTP/2 的优点包括：

*   **多路复用和并发**

    使用 HTTP 1.1，发出多个资源请求需要多个 TCP 连接，每个连接都会强加自己的性能开销。 HTTP/2 允许通过单个 TCP 连接请求多个资源。

*   **标头压缩**

    通过压缩所服务资源的 HTTP 标头，通过网络发送的数据会大大减少。

*   **流依赖关系**

    客户端使用流依赖关系可向服务器表明哪些资源具有优先级。


## <a name="http2-browser-support"></a>HTTP/2 浏览器支持

所有主流浏览器均已在其当前版本中实现 HTTP/2 支持。 不受支持的浏览器会自动回退到 HTTP/1.1。

|浏览者|最低版本|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>后续步骤

若要了解有关 HTTP/2 的详细信息，请访问以下资源：

- [HTTP/2 规范主页](https://http2.github.io/)
- [正式版 HTTP/2 常见问题解答](https://http2.github.io/faq/)
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
