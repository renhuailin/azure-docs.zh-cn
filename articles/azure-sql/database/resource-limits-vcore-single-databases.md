---
title: 单一数据库 vCore 资源限制
description: 本页介绍 Azure SQL 数据库的单一数据库中一些常见的 vCore 资源限制。
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: reference
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 06/04/2021
ms.openlocfilehash: 4a400417fef8ac89ccf30799f4969ba8f789e260
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111555301"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>使用 vCore 购买模型的单一数据库的资源限制
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文提供针对使用 vCore 购买模型的 Azure SQL 数据库中单一数据库的详细资源限制。

* 有关服务器上单一数据库的 DTU 购买模型限制，请参阅[服务器上的资源限制概述](resource-limits-logical-server.md)。
* 有关 Azure SQL 数据库的 DTU 购买模型资源限制，请参阅 [DTU 资源限制单一数据库](resource-limits-dtu-single-databases.md)和 [DTU 资源限制弹性池](resource-limits-dtu-elastic-pools.md)。
* 有关 vCore 资源限制，请参阅 [vCore 资源限制 - Azure SQL 数据库](resource-limits-vcore-single-databases.md)和 [vCore 资源限制 - 弹性池](resource-limits-vcore-elastic-pools.md)。
* 有关不同购买模型的更多信息，请参阅[购买模型和服务层级](purchasing-models.md)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](file-space-manage.md)。

每个只读副本都有自己的资源，如 vCore、内存、数据 IOPS、TempDB、辅助角色和会话等。 每个只读副本都受限于本文后面详细介绍的资源限制。

可以通过以下方式为单一数据库设置服务层级、计算大小（服务目标）和存储量：

* [Transact-SQL](single-database-manage.md#transact-sql-t-sql)（通过 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)）
* [Azure 门户](single-database-manage.md#the-azure-portal)
* [PowerShell](single-database-manage.md#powershell)
* [Azure CLI](single-database-manage.md#the-azure-cli)
* [REST API](single-database-manage.md#rest-api)

> [!IMPORTANT]
> 有关缩放指南和注意事项，请参阅[缩放单一数据库](single-database-scale.md)。

## <a name="general-purpose---serverless-compute---gen5"></a>常规用途 - 无服务器计算 - Gen5

[无服务器计算层](serverless-tier-overview.md)目前仅在 Gen5 硬件上提供。

### <a name="gen5-compute-generation-part-1"></a>第 5 代计算（第 1 部分）

|计算大小（服务目标）|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|
|最小-最大 vCore 数|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|最小-最大内存 (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|最小-最大自动暂停延迟（分钟）|60-10080|60-10080|60-10080|60-10080|60-10080|
|列存储支持|是 <sup>1</sup>|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|
|最大数据大小 (GB)|512|1024|1024|1024|1536|
|最大日志大小 (GB) <sup>2</sup>|154|307|307|307|461|
|TempDB 最大数据大小 (GB)|32|64|128|192|256|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS <sup>3</sup>|320|640|1280|1920|2560|
|最大日志速率 (MBps)|4.5|9|18|27|36|
|最大并发工作线程数（请求数）|75|150|300|450|600|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|1|
|Multi-AZ|空值|空值|空值|空值|空值|
|读取横向扩展|空值|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 具有较小最大 vcore 配置的服务目标可能内存不足，无法创建和使用列存储索引。  如果列存储遇到性能问题，请增加最大 vcore 配置以增加可用的最大内存。  

<sup>2</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>3</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>第 5 代计算（第 2 部分）

|计算大小（服务目标）|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|
|最小-最大 vCore 数|1.25-10|1.50-12|1.75-14|2.00-16|
|最小-最大内存 (GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|最小-最大自动暂停延迟（分钟）|60-10080|60-10080|60-10080|60-10080|
|列存储支持|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|
|最大数据大小 (GB)|1536|3072|3072|3072|
|最大日志大小 (GB) <sup>1</sup>|461|461|461|922|
|TempDB 最大数据大小 (GB)|320|384|448|512|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|3200|3840|4480|5120|
|最大日志速率 (MBps)|45|50|50|50|
|最大并发工作线程数（请求数）|750|900|1050|1200|
|最大并发会话数|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|
|Multi-AZ|空值|空值|空值|空值|
|读取横向扩展|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-3"></a>第 5 代计算（第 3 部分）

|计算大小（服务目标）|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|
|最小-最大 vCore 数|2.25-18|2.5-20|3-24|4-32|5-40|
|最小-最大内存 (GB)|6.75-54|7.5-60|9-72|12-96|15-120|
|最小-最大自动暂停延迟（分钟）|60-10080|60-10080|60-10080|60-10080|60-10080|
|列存储支持|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|
|最大数据大小 (GB)|3072|3072|4096|4096|4096|
|最大日志大小 (GB) <sup>1</sup>|922|922|1024|1024|1024|
|TempDB 最大数据大小 (GB)|576|640|768|1024|1280|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|5760|6400|7680|10240|12800|
|最大日志速率 (MBps)|50|50|50|50|50|
|最大并发工作线程数（请求数）|1350|1500|1800|2400|3000|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|1|
|Multi-AZ|空值|空值|空值|空值|空值|
|读取横向扩展|空值|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。


## <a name="hyperscale---provisioned-compute---gen4"></a>超大规模 - 预配的计算 - Gen4

### <a name="gen4-compute-generation-part-1"></a>第 4 代计算（第 1 部分）

|计算大小（服务目标）|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|1|2|3|4|5|6|
|内存 (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) 大小|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|空值|
|最大数据大小 (TB)|100 |100 |100 |100 |100 |100|
|最大日志大小 (TB)|无限制 |无限制 |无限制 |无限制 |无限制 |无限制 |
|TempDB 最大数据大小 (GB)|32|64|96|128|160|192|
|存储类型| [注释 1](#notes) |[注释 1](#notes)|[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |
|最大本地 SSD IOPS <sup>1</sup>|4000 |8000 |12000 |16000 |20000 |24000 |
|最大日志速率 (MBps)|100 |100 |100 |100 |100 |100 |
|IO 延迟（近似）|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|次要副本|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|空值|空值|空值|空值|空值|空值|
|读取横向扩展|是|是|是|是|是|是|
|备份存储保留期|7 天|7 天|7 天|7 天|7 天|7 天|
|||

<sup>1</sup> 除了本地 SSD IO，工作负载还使用远程[页面服务器](service-tier-hyperscale.md#page-server) IO。 有效 IOPS 取决于工作负荷。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)和[资源利用率统计信息中的数据 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)。

### <a name="gen4-compute-generation-part-2"></a>第 4 代计算（第 2 部分）

|计算大小（服务目标）|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|7|8|9|10|16|24|
|内存 (GB)|49|56|63|70|112|159.5|
|[RBPEX](service-tier-hyperscale.md#compute) 大小|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|空值|
|最大数据大小 (TB)|100 |100 |100 |100 |100 |100 |
|最大日志大小 (TB)|无限制 |无限制 |无限制 |无限制 |无限制 |无限制 |
|TempDB 最大数据大小 (GB)|224|256|288|320|512|768|
|存储类型| [注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |
|最大本地 SSD IOPS <sup>1</sup>|28000 |32000 |36000 |40000 |64000 |76800 |
|最大日志速率 (MBps)|100 |100 |100 |100 |100 |100 |
|IO 延迟（近似）|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|
|最大并发工作线程数（请求数）|1400|1600|1800|2000|3200|4800|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|次要副本|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|空值|空值|空值|空值|空值|空值|
|读取横向扩展|是|是|是|是|是|是|
|备份存储保留期|7 天|7 天|7 天|7 天|7 天|7 天|
|||

<sup>1</sup> 除了本地 SSD IO，工作负载还使用远程[页面服务器](service-tier-hyperscale.md#page-server) IO。 有效 IOPS 取决于工作负荷。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)和[资源利用率统计信息中的数据 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)。

## <a name="hyperscale---provisioned-compute---gen5"></a>超大规模 - 预配的计算 - Gen5

### <a name="gen5-compute-generation-part-1"></a>第 5 代计算（第 1 部分）

|计算大小（服务目标）|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|2|4|6|8|10|12|14|
|内存 (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX](service-tier-hyperscale.md#compute) 大小|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|空值|空值|
|最大数据大小 (TB)|100 |100 |100 |100 |100 |100 |100|
|最大日志大小 (TB)|无限制 |无限制 |无限制 |无限制 |无限制 |无限制 |无限制 |
|TempDB 最大数据大小 (GB)|64|128|192|256|320|384|448|
|存储类型| [注释 1](#notes) |[注释 1](#notes)|[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |
|最大本地 SSD IOPS <sup>1</sup>|8000 |16000 |24000 |32000 |40000 |48000 |56000 |
|最大日志速率 (MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO 延迟（近似）|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|1400|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|次要副本|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|空值|空值|空值|空值|空值|空值|空值|
|读取横向扩展|是|是|是|是|是|是|是|
|备份存储保留期|7 天|7 天|7 天|7 天|7 天|7 天|7 天|
|||

<sup>1</sup> 除了本地 SSD IO，工作负载还使用远程[页面服务器](service-tier-hyperscale.md#page-server) IO。 有效 IOPS 取决于工作负荷。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)和[资源利用率统计信息中的数据 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)。

### <a name="gen5-compute-generation-part-2"></a>第 5 代计算（第 2 部分）

|计算大小（服务目标）|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|16|18|20|24|32|40|80|
|内存 (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX](service-tier-hyperscale.md#compute) 大小|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|3 倍内存|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|空值|空值|
|最大数据大小 (TB)|100 |100 |100 |100 |100 |100 |100 |
|最大日志大小 (TB)|无限制 |无限制 |无限制 |无限制 |无限制 |无限制 |无限制 |
|TempDB 最大数据大小 (GB)|512|576|640|768|1024|1280|2560|
|存储类型| [注释 1](#notes) |[注释 1](#notes)|[注释 1](#notes)|[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |[注释 1](#notes) |
|最大本地 SSD IOPS <sup>1</sup>|64000 |72000 |80000 |96000 |128000 |160000 |204800 |
|最大日志速率 (MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO 延迟（近似）|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|
|最大并发工作线程数（请求数）|1600|1800|2000|2400|3200|4000|8000|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|次要副本|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|空值|空值|空值|空值|空值|空值|空值|
|读取横向扩展|是|是|是|是|是|是|是|
|备份存储保留期|7 天|7 天|7 天|7 天|7 天|7 天|7 天|
|||

<sup>1</sup> 除了本地 SSD IO，工作负载还使用远程[页面服务器](service-tier-hyperscale.md#page-server) IO。 有效 IOPS 取决于工作负荷。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)和[资源利用率统计信息中的数据 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)。

#### <a name="notes"></a>注释

**注释 1**：超大规模是具有单独计算和存储组件的多层体系结构：[“超大规模”服务层级体系结构](service-tier-hyperscale.md#distributed-functions-architecture)

**注释 2**：对于本地计算副本 SSD 上的数据，延迟为 1-2 ms，这缓存最常用的数据页。 从页面服务器检索数据的延迟更长。

## <a name="hyperscale---provisioned-compute---dc-series"></a>超大规模 - 预配的计算 - DC 系列

|计算大小（服务目标）|HS_DC_2|HS_DC_4|HS_DC_6|HS_DC_8|
|:--- | --: |--: |--: |--: |---: | 
|计算的代|DC 系列|DC 系列|DC 系列|DC 系列|
|vCore 数|2|4|6|8|
|内存 (GB)|9|18|27|36|
|[RBPEX](service-tier-hyperscale.md#compute) 大小|3 倍内存|3 倍内存|3 倍内存|3 倍内存|
|列存储支持|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|
|最大数据大小 (TB)|100 |100 |100 |100 |
|最大日志大小 (TB)|无限制 |无限制 |无限制 |无限制 |
|TempDB 最大数据大小 (GB)|64|128|192|256|
|存储类型| [注释 1](#notes) |[注释 1](#notes)|[注释 1](#notes) |[注释 1](#notes) |
|最大本地 SSD IOPS <sup>1</sup>|14000|28000|42000|44800|
|最大日志速率 (MBps)|100 |100 |100 |100 |
|IO 延迟（近似）|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|[注释 2](#notes)|
|最大并发工作线程数（请求数）|160|320|480|640|
|最大并发会话数|30,000|30,000|30,000|30,000|
|次要副本|0-4|0-4|0-4|0-4|
|Multi-AZ|空值|空值|空值|空值|
|读取横向扩展|是|是|是|是|
|备份存储保留期|7 天|7 天|7 天|7 天|
|||

<sup>1</sup> 除了本地 SSD IO，工作负载还使用远程[页面服务器](service-tier-hyperscale.md#page-server) IO。 有效 IOPS 取决于工作负荷。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)和[资源利用率统计信息中的数据 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)。

### <a name="notes"></a>注释

**注释 1**：超大规模是具有单独计算和存储组件的多层体系结构：[“超大规模”服务层级体系结构](service-tier-hyperscale.md#distributed-functions-architecture)

**注释 2**：对于本地计算副本 SSD 上的数据，延迟为 1-2 ms，这缓存最常用的数据页。 从页面服务器检索数据的延迟更长。

## <a name="general-purpose---provisioned-compute---gen4"></a>常规用途 - 预配的计算 - Gen4

> [!IMPORTANT]
> 澳大利亚东部或巴西南部区域不再支持新的 Gen4 数据库。

### <a name="gen4-compute-generation-part-1"></a>第 4 代计算（第 1 部分）

|计算大小（服务目标）|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|1|2|3|4|5|6|
|内存 (GB)|7|14|21|28|35|42|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|空值|
|最大数据大小 (GB)|1024|1024|1536|1536|1536|3072|
|最大日志大小 (GB) <sup>1</sup>|307|307|461|461|461|922|
|TempDB 最大数据大小 (GB)|32|64|96|128|160|192|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|320|640|960|1280|1600|1920|
|最大日志速率 (MBps)|4.5|9|13.5|18|22.5|27|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|1|1|
|Multi-AZ|空值|空值|空值|空值|空值|空值|
|读取横向扩展|空值|空值|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

### <a name="gen4-compute-generation-part-2"></a>第 4 代计算（第 2 部分）

|计算大小（服务目标）|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|7|8|9|10|16|24|
|内存 (GB)|49|56|63|70|112|159.5|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|空值|
|最大数据大小 (GB)|3072|3072|3072|3072|4096|4096|
|最大日志大小 (GB) <sup>1</sup>|922|922|922|922|1229|1229|
|TempDB 最大数据大小 (GB)|224|256|288|320|512|768|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）
|最大数据 IOPS <sup>2</sup>|2240|2560|2880|3200|5120|7680|
|最大日志速率 (MBps)|31.5|36|40.5|45|50|50|
|最大并发工作线程数（请求数）|1400|1600|1800|2000|3200|4800|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|1|1|
|Multi-AZ|空值|空值|空值|空值|空值|空值|
|读取横向扩展|空值|空值|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

## <a name="general-purpose---provisioned-compute---gen5"></a>常规用途 - 预配的计算 - Gen5

### <a name="gen5-compute-generation-part-1"></a>第 5 代计算（第 1 部分）

|计算大小（服务目标）|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|2|4|6|8|10|12|14|
|内存 (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|空值|空值|
|最大数据大小 (GB)|1024|1024|1536|1536|1536|3072|3072|
|最大日志大小 (GB) <sup>1</sup>|307|307|461|461|461|922|922|
|TempDB 最大数据大小 (GB)|64|128|192|256|320|384|384|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|640|1280|1920|2560|3200|3840|4480|
|最大日志速率 (MBps)|9|18|27|36|45|50|50|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|1400|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|1|1|1|
|Multi-AZ|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|读取横向扩展|空值|空值|空值|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>第 5 代计算（第 2 部分）

|计算大小（服务目标）|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|16|18|20|24|32|40|80|
|内存 (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|空值|空值|
|最大数据大小 (GB)|3072|3072|3072|4096|4096|4096|4096|
|最大日志大小 (GB) <sup>1</sup>|922|922|922|1024|1024|1024|1024|
|TempDB 最大数据大小 (GB)|512|576|640|768|1024|1280|2560|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|5120|5760|6400|7680|10240|12800|12800|
|最大日志速率 (MBps)|50|50|50|50|50|50|50|
|最大并发工作线程数（请求数）|1600|1800|2000|2400|3200|4000|8000|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|1|1|1|
|Multi-AZ|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[以预览版提供](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|读取横向扩展|空值|空值|空值|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>常规用途 - 预配的计算 - Fsv2 系列

### <a name="fsv2-series-compute-generation-part-1"></a>Fsv2 系列计算代系（第 1 部分）

|计算大小（服务目标）|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|计算的代|Fsv2 系列|Fsv2 系列|Fsv2 系列|Fsv2 系列|Fsv2 系列|
|vCore 数|8|10|12|14|16|
|内存 (GB)|15.1|18.9|22.7|26.5|30.2|
|列存储支持|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|
|最大数据大小 (GB)|1024|1024|1024|1024|1536|
|最大日志大小 (GB) <sup>1</sup>|336|336|336|336|512|
|TempDB 最大数据大小 (GB)|37|46|56|65|74|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|2560|3200|3840|4480|5120|
|最大日志速率 (MBps)|36|45|50|50|50|
|最大并发工作线程数（请求数）|400|500|600|700|800|
|最大并发登录数|800|1000|1200|1400|1600|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|1|
|Multi-AZ|空值|空值|空值|空值|空值|
|读取横向扩展|空值|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

### <a name="fsv2-series-compute-generation-part-2"></a>Fsv2 系列计算代系（第 2 部分）

|计算大小（服务目标）|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|计算的代|Fsv2 系列|Fsv2 系列|Fsv2 系列|Fsv2 系列|Fsv2 系列|Fsv2 系列|
|vCore 数|18|20|24|32|36|72|
|内存 (GB)|34.0|37.8|45.4|60.5|68.0|136.0|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|空值|空值|
|最大数据大小 (GB)|1536|1536|1536|3072|3072|4096|
|最大日志大小 (GB) <sup>1</sup>|512|512|512|1024|1024|1024|
|TempDB 最大数据大小 (GB)|83|93|111|148|167|333|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|5760|6400|7680|10240|11520|12800|
|最大日志速率 (MBps)|50|50|50|50|50|50|
|最大并发工作线程数（请求数）|900|1000|1200|1600|1800|3600|
|最大并发登录数|1800|2000|2400|3200|3600|7200|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|1|1|
|Multi-AZ|空值|空值|空值|空值|空值|空值|
|读取横向扩展|空值|空值|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

## <a name="general-purpose---provisioned-compute---dc-series"></a>常规用途 - 预配的计算 - DC 系列

|计算大小（服务目标）|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8| 
|:---| ---:|---:|---:|---:|
|计算的代|DC 系列|DC 系列|DC 系列|DC 系列|
|vCore 数|2|4|6|8|
|内存 (GB)|9|18|27|36|
|列存储支持|是|是|是|是|
|内存中 OLTP 存储 (GB)|空值|空值|空值|空值|
|最大数据大小 (GB)|1024|1536|3072|3072|
|最大日志大小 (GB) <sup>1</sup>|307|461|922|922|
|TempDB 最大数据大小 (GB)|64|128|192|256|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|640|1280|1920|2560|
|最大日志速率 (MBps)|9|18|27|36|
|最大并发工作线程数（请求数）|160|320|480|640|
|最大并发会话数|30,000|30,000|30,000|30,000|
|副本数|1|1|1|1|
|Multi-AZ|空值|空值|空值|空值|
|读取横向扩展|空值|空值|空值|空值|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---gen4"></a>业务关键 - 预配的计算 - Gen4

> [!IMPORTANT]
> 澳大利亚东部或巴西南部区域不再支持新的 Gen4 数据库。

### <a name="gen4-compute-generation-part-1"></a>第 4 代计算（第 1 部分）

|计算大小（服务目标）|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|1|2|3|4|5|6|
|内存 (GB)|7|14|21|28|35|42|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|1|2|3|4|5|6|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|最大数据大小 (GB)|1024|1024|1024|1024|1024|1024|
|最大日志大小 (GB) <sup>1</sup>|307|307|307|307|307|307|
|TempDB 最大数据大小 (GB)|32|64|96|128|160|192|
|[最大本地存储大小](resource-limits-logical-server.md#storage-space-governance) (GB)|1356|1356|1356|1356|1356|1356|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|4,000|8,000|12,000|16,000|20,000|24,000|
|最大日志速率 (MBps)|8|16|24|32|40|48|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|
|最大并发登录数|200|400|600|800|1000|1200|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

### <a name="gen4-compute-generation-part-2"></a>第 4 代计算（第 2 部分）

|计算大小（服务目标）|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|7|8|9|10|16|24|
|内存 (GB)|49|56|63|70|112|159.5|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|7|8|9.5|11|20|36|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|最大数据大小 (GB)|1024|1024|1024|1024|1024|1024|
|最大日志大小 (GB) <sup>1</sup>|307|307|307|307|307|307|
|TempDB 最大数据大小 (GB)|224|256|288|320|512|768|
|[最大本地存储大小](resource-limits-logical-server.md#storage-space-governance) (GB)|1356|1356|1356|1356|1356|1356|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|28,000|32,000|36,000|40,000|64,000|76,800|
|最大日志速率 (MBps)|56|64|64|64|64|64|
|最大并发工作线程数（请求数）|1400|1600|1800|2000|3200|4800|
|最大并发登录数（请求数）|1400|1600|1800|2000|3200|4800|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---gen5"></a>业务关键 - 预配的计算 - Gen5

### <a name="gen5-compute-generation-part-1"></a>第 5 代计算（第 1 部分）

|计算大小（服务目标）|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|2|4|6|8|10|12|14|
|内存 (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|最大数据大小 (GB)|1024|1024|1536|1536|1536|3072|3072|
|最大日志大小 (GB) <sup>1</sup>|307|307|461|461|461|922|922|
|TempDB 最大数据大小 (GB)|64|128|192|256|320|384|448|
|[最大本地存储大小](resource-limits-logical-server.md#storage-space-governance) (GB)|4829|4829|4829|4829|4829|4829|4829|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|8000|16,000|24,000|32,000|40,000|48,000|56,000|
|最大日志速率 (MBps)|24|48|72|96|96|96|96|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|1400|
|最大并发登录数|200|400|600|800|1000|1200|1400|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|4|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>第 5 代计算（第 2 部分）

|计算大小（服务目标）|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|16|18|20|24|32|40|80|
|内存 (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|15.77|18.14|20.51|25.25|37.94|52.23|131.64|
|最大数据大小 (GB)|3072|3072|3072|4096|4096|4096|4096|
|最大日志大小 (GB) <sup>1</sup>|922|922|922|1024|1024|1024|1024|
|TempDB 最大数据大小 (GB)|512|576|640|768|1024|1280|2560|
|[最大本地存储大小](resource-limits-logical-server.md#storage-space-governance) (GB)|4829|4829|4829|4829|4829|4829|4829|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|64,000|72,000|80,000|96,000|128,000|160,000|204,800|
|最大日志速率 (MBps)|96|96|96|96|96|96|96|
|最大并发工作线程数（请求数）|1600|1800|2000|2400|3200|4000|8000|
|最大并发登录数|1600|1800|2000|2400|3200|4000|8000|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|4|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---m-series"></a>业务关键 - 预配的计算 - M 系列

### <a name="m-series-compute-generation-part-1"></a>M 系列计算代系（第 1 部分）

|计算大小（服务目标）|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|计算的代|M 系列|M 系列|M 系列|M 系列|M 系列|M 系列|
|vCore 数|8|10|12|14|16|18|
|内存 (GB)|235.4|294.3|353.2|412.0|470.9|529.7|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|64|80|96|112|128|150|
|最大数据大小 (GB)|512|640|768|896|1024|1152|
|最大日志大小 (GB) <sup>1</sup>|171|213|256|299|341|384|
|TempDB 最大数据大小 (GB)|256|320|384|448|512|576|
|[最大本地存储大小](resource-limits-logical-server.md#storage-space-governance) (GB)|13836|13836|13836|13836|13836|13836|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|12,499|15,624|18,748|21,873|24,998|28,123|
|最大日志速率 (MBps)|48|60|72|84|96|108|
|最大并发工作线程数（请求数）|800|1,000|1,200|1,400|1,600|1,800|
|最大并发登录数|800|1,000|1,200|1,400|1,600|1,800|
|最大并发会话数|30000|30000|30000|30000|30000|30000|
|副本数|4|4|4|4|4|4|
|Multi-AZ|否|否|否|否|否|否|
|读取横向扩展|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

### <a name="m-series-compute-generation-part-2"></a>M 系列计算代系（第 2 部分）

|计算大小（服务目标）|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|计算的代|M 系列|M 系列|M 系列|M 系列|M 系列|
|vCore 数|20|24|32|64|128|
|内存 (GB)|588.6|706.3|941.8|1883.5|3767.0|
|列存储支持|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|172|216|304|704|1768|
|最大数据大小 (GB)|1280|1536|2048|4096|4096|
|最大日志大小 (GB) <sup>1</sup>|427|512|683|1024|1024|
|TempDB 最大数据大小 (GB)|4096|2048|1024|768|640|
|[最大本地存储大小](resource-limits-logical-server.md#storage-space-governance) (GB)|13836|13836|13836|13836|13836|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|31,248|37,497|49,996|99,993|160,000|
|最大日志速率 (MBps)|120|144|192|264|264|
|最大并发工作线程数（请求数）|2,000|2,400|3,200|6,400|12,800|
|最大并发登录数|2,000|2,400|3,200|6,400|12,800|
|最大并发会话数|30000|30000|30000|30000|30000|
|副本数|4|4|4|4|4|
|Multi-AZ|否|否|否|否|否|
|读取横向扩展|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---dc-series"></a>业务关键 - 预配的计算 - DC 系列

|计算大小（服务目标）|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|计算的代|DC 系列|DC 系列|DC 系列|DC 系列|
|vCore 数|2|4|6|8|
|内存 (GB)|9|18|27|36|
|列存储支持|是|是|是|是|
|内存中 OLTP 存储 (GB)|1.7|3.7|5.9|8.2|
|最大数据大小 (GB)|768|768|768|768|
|最大日志大小 (GB) <sup>1</sup>|230|230|230|230|
|TempDB 最大数据大小 (GB)|64|128|192|256|
|[最大本地存储大小](resource-limits-logical-server.md#storage-space-governance) (GB)|1406|1406|1406|1406|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS <sup>2</sup>|14000|28000|42000|44800|
|最大日志速率 (MBps)|24|48|72|96|
|最大并发工作线程数（请求数）|200|400|600|800|
|最大并发登录数|200|400|600|800|
|最大并发会话数|30,000|30,000|30,000|30,000|
|副本数|4|4|4|4|
|Multi-AZ|否|否|否|否|
|读取横向扩展|否|否|否|否|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

<sup>1</sup> 记录的最大数据大小值。 减少最大数据大小会按比例减少最大日志大小。

<sup>2</sup> IO 大小的最大值，范围为 8 KB 到 64 KB。 实际 IOPS 与工作负荷相关。 有关详细信息，请参阅[数据 IO 调控](resource-limits-logical-server.md#resource-governance)。

## <a name="next-steps"></a>后续步骤

- 有关单一数据库的 DTU 资源限制，请参阅[使用 DTU 购买模型的单一数据库的资源限制](resource-limits-dtu-single-databases.md)
- 有关弹性池的 vCore 资源限制，请参阅[使用 vCore 购买模型的弹性池的资源限制](resource-limits-vcore-elastic-pools.md)
- 有关弹性池的 DTU 资源限制，请参阅[使用 DTU 购买模型的弹性池的资源限制](resource-limits-dtu-elastic-pools.md)
- 有关 SQL 托管实例的资源限制，请参阅 [SQL 托管实例资源限制](../managed-instance/resource-limits.md)。
- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 有关服务器上的资源限制的信息，请参阅[服务器资源限制概述](resource-limits-logical-server.md)了解有关服务器级别和订阅级别限制的信息。
