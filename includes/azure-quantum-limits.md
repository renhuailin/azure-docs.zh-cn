---
title: include 文件
description: include 文件
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98948662"
---
### <a name="provider-limits--quota"></a>提供程序限制和配额

Azure Quantum 服务支持第一方和第三方服务提供程序。 第三方提供程序有自己的限制和配额。 当用户在“提供程序”边栏选项卡中配置第三方提供程序时，可以查看 Azure 门户中的产品/服务和限制。 

你可以在下面找到针对 Microsoft 第一方优化解决方案提供程序的已发布的配额限制。 

#### <a name="learn--develop-sku"></a>了解和开发 SKU

| 资源 | 限制 |
| --- | --- |
| 基于 CPU 的并发作业 | 最多 5 个并发作业 |
| 基于 FPGA 的并发作业 | 最多 2 个并发作业 |
| 基于 CPU 的求解器时间 | 每月 20 小时  |
| 基于 FPGA 的求解器时间 | 每月 1 小时  |

如果使用的是学习和开发 SKU，则不能请求增加配额限制。 应改为切换到“大规模性能 SKU”。

#### <a name="performance-at-scale-sku"></a>大规模性能 SKU

| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 基于 CPU 的并发作业 | 最多 100 个并发作业 | 与默认限制相同 |
| 基于 FPGA 的并发作业 | 最多 10 个并发作业 | 与默认限制相同 |
| 求解器时间 | 每月 1,000 小时  | 每月最多 50,000 小时 |

如果需要请求提高限制，请联系 Azure 支持部门。 

有关详细信息，请查看 [Azure Quantum 定价页](https://aka.ms/AQ/Pricing)。
有关第三方产品/服务的信息，请查看 Azure 门户中的相关提供程序页面。
