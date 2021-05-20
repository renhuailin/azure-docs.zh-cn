---
title: 配置选项 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 超大规模 (Citus) 服务器组的选项，包括节点计算、存储和区域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/12/2021
ms.openlocfilehash: 48537483501165d4a978afdbd05560613170d187
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165605"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – 超大规模 (Citus) 配置选项

## <a name="compute-and-storage"></a>计算和存储
 
可以单独为超大规模 (Citus) 服务器组中的工作器节点和协调器节点选择计算和存储设置。  计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 可预配的存储大小是指可用于超大规模 (Citus) 服务器组中协调器和工作器节点的容量。 存储包括数据库文件、临时文件、事务日志和 Postgres 服务器日志。
 
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

## <a name="regions"></a>区域
以下 Azure 区域提供超大规模 (Citus) 服务器组：

* 美洲：
    * 加拿大中部
    * 美国中部
    * 美国东部
    * 美国东部 2
    * 美国中北部
    * 美国西部 2
* 亚太区：
    * 澳大利亚东部
    * Japan East
    * 韩国中部
    * Southeast Asia
* 欧洲：
    * 北欧
    * 英国南部
    * 西欧

其中某些区域最初可能未在所有 Azure 订阅中激活。 如果你要使用上述列表中的某个区域但未在订阅中看到该区域，或者要使用此列表中不包括的区域，请创建[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="limits-and-limitations"></a>限制和局限性

以下部分介绍了超大规模 (Citus) 服务中的容量和功能限制。

### <a name="maximum-connections"></a>最大连接数

每个 PostgreSQL 连接（即使是空闲连接）都至少使用 10 MB 的内存，因此，必须限制同步连接的数量。 我们选择使用以下限制来保证节点正常运行：

* 协调器节点
   * 最大连接数：300
   * 最大用户连接数：297
* 工作器节点
   * 最大连接数：600
   * 最大用户连接数：597

超出这些限制后，连接尝试将失败并提示错误。 系统会保留三个连接以用于监视节点，这就是用户查询可用的连接数比总连接数少三个的原因。

建立新连接需要时间。 这会对多数应用程序产生不利影响，因为这些应用程序会请求建立很多短时的连接。 建议使用连接池程序，既可以减少空闲事务，又可以重用现有连接。 要了解详细信息，请访问[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)。

### <a name="storage-scaling"></a>存储缩放

协调器和工作器节点上的存储可以纵向扩展（增加），但无法纵向缩减（减少）。

### <a name="storage-size"></a>存储大小

协调器和工作器节点最多支持 2 TiB 的存储。 请参阅[上面](#compute-and-storage)的可用存储选项和 IOPS 计算，了解节点和群集大小。

### <a name="database-creation"></a>数据库创建

每个超大规模 (Citus) 服务器组只具有一个数据库（`citus` 数据库），Azure 门户提供用于连接到该数据库的凭据。 目前不允许创建其他数据库，CREATE DATABASE 命令会失败并提示错误。

## <a name="pricing"></a>定价
有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。
若要查看所需配置的具体成本，可以在 [Azure 门户](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)的“配置”选项卡上查看根据你选择的选项显示的每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上选择“添加项”，展开“数据库”类别，然后选择“Azure Database for PostgreSQL - 超大规模 (Citus)”来自定义选项  。
 
## <a name="next-steps"></a>后续步骤
了解如何[在门户中创建超大规模 (Citus) 服务器组](quickstart-create-hyperscale-portal.md)。
