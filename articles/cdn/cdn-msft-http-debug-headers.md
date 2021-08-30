---
title: 调试 Microsoft Azure CDN 的 HTTP 标头 |Microsoft Docs
description: 调试缓存请求标头提供有关应用到所请求资产的缓存策略的附加信息。 这些标头特定于 Microsoft Azure CDN。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 19553eb4f5f7df7c3edf54ff3fded3727feb2e54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727975"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>调试 Microsoft Azure CDN 的 HTTP 标头
调试响应标头 `X-Cache` 提供有关提供内容的 CDN 堆栈层的详细信息。 此标头特定于 Microsoft Azure CDN。

### <a name="response-header-format"></a>响应标头格式

标头 | 说明
-------|------------
X-Cache：TCP_HIT | 从 CDN 边缘缓存提供内容时，将返回此标头。 
X-Cache：TCP_REMOTE_HIT | 从 CDN 区域缓存（原始屏蔽层）提供内容时，将返回此标头
X-Cache：TCP_MISS | 如果缓存未命中，并从原始位置提供内容，则会返回此标头。
X-Cache: PRIVATE_NOSTORE | 当无法缓存请求时返回此标头，因为 Cache-Control 响应头设置为“专用”或“无存储”。
X-Cache: CONFIG_NOCACHE | 当请求配置为不在 CDN 配置文件中缓存时，返回此标头。

有关 Azure CDN 支持的 HTTP 标头的其他信息，请参阅 [Front Door 到后端](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend)。
