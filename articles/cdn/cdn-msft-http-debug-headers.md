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
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "81260405"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>调试 Microsoft Azure CDN 的 HTTP 标头
调试响应标头 `X-Cache` 提供有关提供内容的 CDN 堆栈层的详细信息。 此标头特定于 Microsoft Azure CDN。

### <a name="response-header-format"></a>响应标头格式

标头 | 说明
-------|------------
X-Cache：TCP_HIT | 从 CDN 边缘缓存提供内容时，将返回此标头。 
X-Cache：TCP_REMOTE_HIT | 从 CDN 区域缓存（原始屏蔽层）提供内容时，将返回此标头
X-Cache：TCP_MISS | 如果缓存未命中，并从原始位置提供内容，则会返回此标头。 


