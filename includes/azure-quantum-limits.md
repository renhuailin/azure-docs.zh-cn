---
title: include 文件
description: include 文件
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: c312ee63f9f00e4eef726924fc01f2862ba2884f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920456"
---
### <a name="provider-limits--quota"></a>提供程序限制 & 配额

Azure 量程服务支持第一方和第三方服务提供商。 第三方提供程序拥有其限制和配额。 当在 "提供程序" 边栏选项卡中配置第三方提供程序时，用户可以查看 Azure 门户中的产品和限制。 

你可以在下面找到 Microsoft 第一方优化解决方案提供商的已发布配额限制。 

#### <a name="learn--develop-sku"></a>了解 & 开发 SKU

| 资源 | 限制 |
| --- | --- |
| 基于 CPU 的并发作业 | 最多5个并发作业 |
| 基于 FPGA 的并发作业 | 最多2个并发作业 |
| 基于 CPU 的规划求解时间 | 每月20小时  |
| 基于 FPGA 的规划求解时间 | 每月1小时  |

如果使用的是 "了解 & 开发 SKU"，则 **不能** 请求增加配额限制。 相反，应切换到 "规模" SKU 中的 "性能"。

#### <a name="performance-at-scale-sku"></a>大规模性能 SKU

| 资源 | 限制 |
| --- | --- |
| 基于 CPU 的并发作业 | 最多100并发作业 |
| 基于 FPGA 的并发作业 | 最多10个并发作业 |
| 规划求解时间 | 每月50000小时  |

如果需要请求限制增加，请联系 Azure 支持部门。 

有关详细信息，请查看 [Azure 量程定价页](https://aka.ms/AQ/Pricing)。
有关第三方产品/服务的信息，请查看 Azure 门户中的相关提供程序页面。
