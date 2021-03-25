---
title: 允许用于 Azure Front Door 标准版/高级版（预览版）的证书颁发机构
description: 本文列出了创建自己的证书时允许的所有证书颁发机构。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434860"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>允许用于 Azure Front Door 标准版/高级版（预览版）的证书颁发机构

使用自己的证书为 Azure Front Door 标准版/高级版自定义域启用 HTTPS 功能时。 需要允许的证书颁发机构 (CA) 来创建自己的 TLS/SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
