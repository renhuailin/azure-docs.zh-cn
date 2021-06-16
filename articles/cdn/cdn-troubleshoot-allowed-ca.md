---
title: 启用自定义 HTTPS 的允许的 CA
titleSuffix: Azure Content Delivery Network
description: 若要使用自己的证书在自定义域上启用 HTTPS，必须使用允许的证书颁发机构 (CA) 来创建证书。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112003606"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>启用自定义 HTTPS 的允许的证书颁发机构

当为 Azure CDN （内容分发网络）自定义域[使用自己的证书启用 HTTPS 功能](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates)时，需要特定的证书要求。 

* “Microsoft 的Azure CDN 标准”配置文件要求以下列表中的来自其中一个批准的证书颁发机构 (CA) 的证书。 如果使用的是来自未经批准的 CA 的证书或自签名证书，则将拒绝该请求。 

* “Verizon 的 Azure CDN 标准”和“Verizon 的 Azure CDN 高级”配置文件均接受来自任何有效 CA 的任何有效证书。  Verizon 配置文件不支持自签名证书。

> [!NOTE]
> 使用自己的证书启用自定义域 HTTPS 功能的选项“不”适用于“Akamai 的 Azure CDN 标准”配置文件。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

