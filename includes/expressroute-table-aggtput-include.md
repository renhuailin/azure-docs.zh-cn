---
title: include 文件
description: include 文件
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504666"
---
| 网关 SKU | 每秒连接数 | 流数 | 每秒兆位 | 每秒数据包数 | 线路带宽 | 网关播发（传递到 MSEE）的路由数 | 网关学到（源自 MSEE）的路由数 | 虚拟网络中的 VM 数量 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **基本 SKU（已弃用）** | 空值 | 空值 | 500 | 空值 | 空值 | 空值 | 空值 | 空值 |
| **标准 SKU/ErGw1AZ** | 7,000 | 400,000 | 1,000 | >100,000 | 1 Gbps |  500 | 4,000 | 2,000（在维护期间降至 1,000，之后恢复。） | 
| **高性能 SKU/ErGw2AZ** | 14,000 | 840,000 | >2,000 | 250,000 | 1 Gbps | 500 | ~9,500（如果虚拟网络中的 VM 超过 6,500 个，则降至 4,000。） | 4,500 |
| **超高性能 SKU/ErGw3AZ** | 16,000 | 950,000 | ~10,000 | 1,000,000 | 1 Gbps | 500 | ~9,500 | 11,000 |
