---
title: 检索 Azure CDN 的当前 POP 列表 | Microsoft Docs
description: 了解如何使用 REST API 获取 POP 服务器。 POP 服务器将请求发送到与 Azure 内容分发网络终结点相关联的源服务器。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2021
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 155f3817b913e38e503d0a8fcaa6083922067ebd
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113487608"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>检索 Azure CDN 的当前 POP IP 列表

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>检索 Azure CDN 的当前 Verizon POP IP 列表

REST API 可用于检索 Verizon 入网点 (POP) 服务器的 IP 集。 这些 POP 服务器向源服务器发出请求，源服务器与 Verizon 配置文件（Verizon 提供的 Azure CDN 标准版或 Verizon 提供的 Azure CDN 高级版）上的 Azure 内容分发网络 (CDN) 终结点相关联。 请注意，此 IP 集与客户端在向 POP 发出请求时看到的 IP 不同。 

有关用于检索 POP 列表的 REST API 操作语法，请参阅[边缘节点 - 列表](/rest/api/cdn/cdn/edgenodes/list)。

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>检索 Azure CDN 的当前 Microsoft POP IP 列表

要锁定应用程序以仅接受来自 Microsoft Azure CDN 的流量，需要为后端设置 IP ACL。 此外，还可以限制 Microsoft Azure CDN 发送的标头“X-Forwarded-Host”接受的值集。 下面详细说明了这些步骤：

将后端的 IP ACL 配置为仅接受来自 Microsoft 后端 IP 地址空间和 Azure 基础结构服务的 Azure CDN 流量。 

将 Azure Front Door [服务标记](../virtual-network/service-tags-overview.md)与 Microsoft Azure CDN 结合使用来配置 Microsoft 的后端 IP 范围。 有关完整列表，请参阅 Microsoft 服务的 [IP 范围和服务标记](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。

## <a name="typical-use-case"></a>典型用例

出于安全方面的考虑，可以使用此 IP 列表强制仅从有效的 Verizon POP 向源服务器发出请求。 例如，如果有人发现了 CDN 终结点源服务器的主机名或 IP 地址，就可以直接向源服务器发出请求，从而绕过 Azure CDN 提供的缩放和安全功能。 通过将返回列表中的 IP 设置为源服务器上仅允许的 IP，可以防止这种情况出现。 为了确保 POP 列表为最新，至少每天检索一次。 

## <a name="next-steps"></a>后续步骤

若要了解 REST API，请参阅 [Azure CDN REST API](/rest/api/cdn/)。
