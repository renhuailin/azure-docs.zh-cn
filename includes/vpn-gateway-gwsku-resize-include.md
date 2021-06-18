---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: 478856b6e62b06259ae37b049774c9e22c840d4a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720942"
---
可以使用 `Resize-AzVirtualNetworkGateway` PowerShell cmdlet 升级或降级第 1 代或第 2 代 SKU（除基本 SKU 外，所有 VpnGw SKU 都可以调整大小）。 如果使用的是基本网关 SKU，[请改用这些说明](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)来调整网关大小。

以下 PowerShell 示例演示如何将网关 SKU 的大小调整为 VpnGw2。

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```
