---
title: 用于在 Azure Front Door 上启用自定义 HTTPS 的获准的 CA
description: 若要使用自己的证书在 Azure Front Door 自定义域上启用 HTTPS，必须使用获准的证书颁发机构 (CA) 来创建证书。
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112003610"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>用于在 Azure Front Door 上启用自定义 HTTPS 的获准的证书颁发机构

使用自己的证书为 Azure Front Door 自定义域[启用 HTTPS 功能](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)时。 需要允许的证书颁发机构 (CA) 来创建自己的 TLS/SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
