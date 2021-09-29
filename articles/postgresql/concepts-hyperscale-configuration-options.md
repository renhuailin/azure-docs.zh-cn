---
title: 配置选项 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 超大规模 (Citus) 服务器组的选项，包括节点计算、存储和区域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/03/2021
ms.openlocfilehash: e2dfb0eda9d4e10c97591a6cc534adaa188d87e1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602007"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – 超大规模 (Citus) 配置选项

## <a name="compute-and-storage"></a>计算和存储
 
可以单独为超大规模 (Citus) 服务器组中的工作器节点和协调器节点选择计算和存储设置。  计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 可预配的存储大小是指可用于超大规模 (Citus) 服务器组中协调器和工作器节点的容量。 存储包括数据库文件、临时文件、事务日志和 Postgres 服务器日志。

### <a name="standard-tier"></a>标准层
 
| 资源              | 工作器节点           | 协调器节点      |
|-----------------------|-----------------------|-----------------------|
| 计算，vCore       | 2、4、8、16、32、64      | 2、4、8、16、32、64      |
| 每个 vCore 的内存 (GiB) | 8                     | 4                     |
| 存储大小 (TiB)     | 0.5、1、2             | 0.5、1、2             |
| 存储类型          | 常规用途 (SSD) | 常规用途 (SSD) |
| IOPS                  | 最高 3 IOPS/GiB      | 最高 3 IOPS/GiB      |

单个超大规模 (Citus) 节点中的 RAM 总量取决于所选的 vCore 数量。

| vCore 数 | 一个工作器节点，GiB RAM | 协调器节点，GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

预配的总存储量也定义了可供每个工作器和协调器节点使用的 I/O 容量。

| 存储大小 (TiB) | 最大 IOPS |
|-------------------|--------------|
| 0.5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

对于整个超大规模 (Citus) 群集，聚合 IOPS 将计算为以下值：

| 辅助角色节点 | 0.5 TiB，总 IOPS | 1 TiB，总 IOPS | 2 TiB，总 IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

### <a name="basic-tier"></a>基本层

超大规模 (Citus) [基本层](concepts-hyperscale-tiers.md)是只有一个节点的服务器组。  由于协调器与工作器节点之间没有区别，因此，选择计算和存储资源就不那么复杂。

| 资源              | 可用选项     |
|-----------------------|-----------------------|
| 计算，vCore       | 2、4、8               |
| 每个 vCore 的内存 (GiB) | 4                     |
| 存储大小，GiB     | 128、256、512         |
| 存储类型          | 常规用途 (SSD) |
| IOPS                  | 最高 3 IOPS/GiB      |

单个超大规模 (Citus) 节点中的 RAM 总量取决于所选的 vCore 数量。

| vCore 数 | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

预配的总存储量也定义了可供基本层节点使用的 I/O 容量。

| 存储大小，GiB | 最大 IOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1,536        |

## <a name="regions"></a>区域
以下 Azure 区域提供超大规模 (Citus) 服务器组：

* 美洲：
    * 巴西南部
    * 加拿大中部
    * 美国中部
    * 美国东部
    * 美国东部 2
    * 美国中北部
    * 美国西部 2
* 亚太区：
    * 澳大利亚东部
    * Japan East
    * 日本西部
    * 韩国中部
    * Southeast Asia
* 欧洲：
    * 法国中部
    * 德国中西部
    * 北欧
    * 瑞士北部
    * 英国南部
    * 西欧

其中某些区域最初可能未在所有 Azure 订阅中激活。 如果你要使用上述列表中的某个区域但未在订阅中看到该区域，或者要使用此列表中不包括的区域，请创建[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="pricing"></a>定价
有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。
若要查看所需配置的具体成本，可以在 [Azure 门户](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)的“配置”选项卡上查看根据你选择的选项显示的每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上选择“添加项”，展开“数据库”类别，然后选择“Azure Database for PostgreSQL - 超大规模 (Citus)”来自定义选项  。
 
## <a name="next-steps"></a>后续步骤
了解如何[在门户中创建超大规模 (Citus) 服务器组](quickstart-create-hyperscale-portal.md)。
