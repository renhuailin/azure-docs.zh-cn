---
title: Azure 前门标准/高级 SKU 比较
description: 本文概述了 Azure 前门标准和高级 SKU 以及它们之间的功能差异。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 0f5ecef1716a503b7d27d0f5cdde15a4560c0e61
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098801"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Azure 前门标准/高级 SKU 概述 (预览) 

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

Azure 前门为3个不同 Sku 提供， [Azure 前门](../front-door-overview.md)、Azure 前门标准 (预览版) 和 Azure 前门高级版 (预览版) 。 Azure 前门标准/高级 Sku 结合了 Azure 前门的功能，从 Microsoft Azure CDN Standard，将 Azure WAF 为单一安全的云 CDN 平台，具有智能威胁防护。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

* **Azure 前门标准 SKU** 如下：

    * 已优化内容传递
    * 提供静态和动态内容加速
    * 全局负载均衡
    * SSL 卸载
    * 域和证书管理
    * 增强的流量分析 
    * 基本安全功能

* **Azure 前门高级 sku** 以标准 SKU 的功能为基础，并添加：

    * 跨 WAF 的广泛安全功能
    * 机器人保护
    * 私有链接支持
    * 与 Microsoft 威胁智能和安全分析集成。 

![显示前门 Sku 之间的比较的关系图。](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>功能比较

| 功能 |      标准      |  高级 |
|----------|:-------------:|------:|
| 自定义域 | 是 | 是 |
| SSL 卸载 | 是 | 是 |
| Caching |  是  | 是 |
| 压缩 | 是 | 是   |
| 全局负载均衡 | 是  | 是 |
| 第 7 层路由 | 是 | 是 |
| URL 重写 | 是 | 是 |
| 规则引擎 | 是 | 是 |
| 私有源 (专用链接)  | 否 | 是 |
| WAF | 否 | 是 |
| 机器人保护 | 否 | 是 |
| 增强的指标和诊断 | 是 | 是 |
| 流量报表 | 是 | 是 |
| 安全报表 | 否 | 是 | 

## <a name="next-steps"></a>后续步骤

了解如何 [创建前门](create-front-door-portal.md)
