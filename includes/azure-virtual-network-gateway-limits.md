---
title: include 文件
description: include 文件
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95553969"
---
| 资源                                | 限制        |
|-----------------------------------------|------------------------------|
| VNet 地址前缀                   | 600/VPN 网关          |
| 聚合 BGP 路由                    | 4,000/VPN 网关        |
| 本地网关地址前缀  | 1000/本地网关               |
| S2S 连接                         | [取决于网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| P2S 连接                         | [取决于网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| P2S 路由限制 - IKEv2                 | 256（非 Windows） **/** 25 (Windows)           |
| P2S 路由限制 - OpenVPN               | 1000                         |
| 最大 flows                              | 100K (VpnGw1/AZ) / 512K (VpnGw2-4/AZ)|