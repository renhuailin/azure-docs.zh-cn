---
title: Azure CDN DDoS 防护功能 | Microsoft Docs
description: Microsoft 提供的 Azure CDN 受 DDoS 防护基础版保护，无需额外付费
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
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: ac00491a34e6e7d18c74827afc6647358794a8be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351684"
---
# <a name="azure-cdn-ddos-protection"></a>Azure CDN DDoS 防护

内容交付网络根据设计提供了 DDoS 防护。 除了用于吸收容量耗尽攻击的全局容量之外，Azure CDN 还另外提供了 DDoS 防护（如下所述），无需额外付费。

## <a name="azure-cdn-from-microsoft"></a>Microsoft 推出的 Azure CDN

Microsoft 提供的 Azure CDN 受 [Azure 基础版 DDoS](../ddos-protection/ddos-protection-overview.md)保护。 此保护机制已默认集成在 Microsoft 提供的 Azure 平台中，无需额外付费。 Microsoft 提供的 Azure CDN 在全球部署了网络，具备完整的规模和容量，可通过始终开启的监视和实时缓解措施来防御常见的网络层攻击。 基本 DDoS 防护还可以抵御最常见的、频繁出现的第 7 层 DNS 查询泛滥以及针对 CDN 终结点的第 3 层和第 4 层容量耗尽攻击。 此服务还提供了有效的跟踪记录，用于保护 Microsoft 的企业和消费者服务免受大规模攻击。

## <a name="azure-cdn-from-verizon"></a>Verizon 的 Azure CDN

Verizon 提供的 Azure CDN 受 Verzion 的专用 DDoS 缓解平台保护。 此保护机制已默认集成在 Verizon 提供的 Azure CDN 中，无需额外付费。 它提供基本保护来抵御最常见的、频繁出现的第 7 层 DNS 查询泛滥以及针对 CDN 终结点的第 3 层和第 4 层容量耗尽攻击。

## <a name="azure-cdn-from-akamai"></a>Akamai 的 Azure CDN

Akamai 提供的 Azure CDN 受 Akamai 的专用 DDoS 缓解平台保护。 此保护机制已默认集成在 Akamai 提供的 Azure CDN 中，无需额外付费。 它提供基本保护来抵御最常见的、频繁出现的第 7 层 DNS 查询泛滥以及针对 CDN 终结点的第 3 层和第 4 层容量耗尽攻击。

## <a name="next-steps"></a>后续步骤

详细了解 [Azure DDoS](../ddos-protection/ddos-protection-overview.md)。