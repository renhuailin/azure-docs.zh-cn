---
title: Azure Front Door 标准/高级 SKU 比较
description: 本文概述 Azure Front Door 标准和高级 SKU 的功能差异。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 1753f2bb649e73d7a5fe6c1cc32361a418ea7f63
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181799"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Azure Front Door 标准/高级 SKU 概述（预览）

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

Azure Front Door SKU 提供 3 个不同的版本，[Azure Front Door](../front-door-overview.md)、Azure Front Door 标准（预览版）和 Azure Front Door 高级（预览版）。 Azure Front Door 标准/高级 SKU 结合了 Azure Front Door 和 Microsoft 标准 Azure CDN 的功能，将 Azure WAF 变为具有情报威胁防护功能的单一安全云 CDN 平台。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

* **Azure Front Door 标准 SKU** 具有：

    * 已优化内容分发
    * 同时提供静态和动态内容加速
    * 全局负载均衡
    * SSL 卸载
    * 域和证书管理
    * 加强流量分析 
    * 基本安全功能

* **Azure Front Door 高级 SKU** 在标准 SKU 的功能基础之上，增加了：

    * 跨 WAF 的广泛安全功能
    * 机器人防护
    * 支持专用链接
    * 与 Microsoft 威胁情报和安全分析集成。 

![图像显示不同版本 Front Door SKU 之间的比较。](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>功能比较

| 功能 |      标准      |  高级 |
|----------|:-------------:|------:|
| 自定义域 | 是 | 是 |
| 卸载 SSL | 是 | 是 |
| 缓存 |  是  | 是 |
| 压缩 | 是 | 是   |
| 全局负载均衡 | 是  | 是 |
| 第 7 层路由 | 是 | 是 |
| URL 重写 | 是 | 是 |
| 规则引擎 | 是 | 是 |
| 私有源（专用链接） | 否 | 是 |
| WAF | 仅自定义规则 | 是 |
| 机器人防护 | 否 | 是 |
| 指标和诊断已加强 | 是 | 是 |
| 流量报告 | 是 | 是 |
| 安全报表 | 否 | 是 | 

## <a name="next-steps"></a>后续步骤

了解如何[创建 Front Door](create-front-door-portal.md)。
