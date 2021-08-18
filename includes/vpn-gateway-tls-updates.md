---
title: include 文件
description: 包含文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b77128033cbe0f99023dbee8a5a15e06820399b1
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112255545"
---
>[!NOTE]
>从 2018 年 7 月 1 日开始，Azure VPN 网关将不再支持 TLS 1.0 和 1.1。 VPN 网关将仅支持 TLS 1.2。 若要维持支持，请参阅[更新以支持 TLS1.2](#tls1)。

此外，TLS 也将于 2018 年 7 月 1 日起弃用以下旧算法：

* RC4 (Rivest Cipher 4)
* DES（数据加密算法）
* 3DES（三重数据加密算法）
* MD5（消息摘要 5）

### <a name="how-do-i-enable-support-for-tls-12-in-windows-81"></a><a name="tls1"></a>如何在 Windows 8.1 中启用对 TLS 1.2 的支持？

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
