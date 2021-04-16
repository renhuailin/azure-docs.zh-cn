---
title: include 文件
description: include 文件
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504665"
---
创建虚拟网络网关时，需要指定要使用的网关 SKU。 如果选择更高级的网关 SKU，则将为该网关分配更多的 CPU 和网络带宽，这样使网关能够支持到虚拟网络更高的吞吐量。 

ExpressRoute 虚拟网络网关可使用以下 SKU：

|     | VPN 网关和 ExpressRoute 共存 | FastPath | 最大线路连接数 |
| --- | --- | --- | --- |
| **标准** | 是 | 否 | 4 |
| 高性能 | 是 | 否 | 4 |
| **UltraPerformance** | 是 | 是 | 16 |