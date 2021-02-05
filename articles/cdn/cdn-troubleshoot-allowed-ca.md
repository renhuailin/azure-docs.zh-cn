---
title: 允许的 CA 启用自定义 HTTPS
titleSuffix: Azure Content Delivery Network
description: 如果你使用自己的证书在自定义域上启用 HTTPS，则必须使用允许的证书颁发机构 (CA) 来创建它。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573392"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>允许用于启用自定义 HTTPS 的证书颁发机构

当你使用自己的证书为 Azure CDN (内容分发网络) 自定义域 [启用 HTTPS 功能](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) 时，需要特定的证书要求。 

* Microsoft 配置文件中的 **Azure CDN 标准** 要求以下列表中的一个批准的证书颁发机构 (CA) 证书。 如果来自未经批准的 CA 的证书或使用了自签名证书，则将拒绝该请求。 

* **来自 Verizon 的标准 Azure CDN 标准** **Azure CDN 和 Verizon** 配置文件中的高级配置文件均接受来自任何有效 CA 的任何有效证书。 Verizon 配置文件不支持自签名证书。

> [!NOTE]
> 使用自己的证书启用自定义域 HTTPS 功能的 *选项不可用于* Akamai 配置文件中的 **Azure CDN Standard** 。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

