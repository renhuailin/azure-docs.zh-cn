---
title: Azure Database for PostgreSQL - 单一服务器和灵活服务器的比较
description: Azure Database for PostgreSQL 单一服务器和灵活服务器的特性与功能的详细比较
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 6d6e5ca910bfd24a8d10952d92441fcc7fe6fcfa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778647"
---
# <a name="comparison-chart---azure-database-for-postgresql-single-server-and-flexible-server"></a>比较图表 - Azure Database for PostgreSQL - 单一服务器和灵活服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

下表对单一服务器和灵活服务器的特性与功能做了概要性的比较。

| 特性/功能 | **单一服务器** | **灵活服务器** |
| ---- | ---- | ---- |
| **常规**  | | |
| 正式版 | 从 2018 年开始推出正式版 | 公共预览版 |
| PostgreSQL | 社区 | 社区 |
| 版本 | 9.6、10、11 | 11、12、13 |
| 基础 O/S | Windows | Linux  |
| 可以选择用于共置应用程序的可用性区域 | 否 | 是 |
| 内置连接池程序 | 否 | 是 (PgBouncer)|
| **连接** | | |
| 在连接字符串中包含用户名 | `<user_name>@server_name`. 例如，`pgadmusr@mypgServer` | 只需提供用户名。 例如，`pgadmusr` | 
| lc_collate  | English_United States.1252 | en_US.utf8 |
| lc_ctype    | English_United States.1252 |en_US.utf8 |
|lc_messages | English_United States.1252 |en_US.utf8|
| lc_monetary | English_United States.1252 |  en_US.utf-8 |
| lc_numeric  | English_United States.1252 |  en_US.utf-8 |
| lc_time     | English_United States.1252 | en_US.utf8 |
| 连接端口 | 5432 | 5432 (DB)、6432 (PgBouncer) |
| 最大 connections | 1982 | 5000 |
| 连接限制是否可配置？ | 否 | 是（`max_connections` 参数） |
| 计算和存储 | | |
| 计算层级 | 基本、常规用途、内存优化 | 可突发、常规用途、内存优化 |
| 可突发 SKU | 否 | 是 |
| 可跨计算层进行缩放 | 无法缩放基本层 | 是的。 可跨层缩放 |
| 停止/启动 | 否 | 是（适用于所有计算 SKU）。 仅停止/启动计算 |
| 最大 存储大小 | 1 TB（基本），4 TB 或 16 TB（常规用途、内存优化） 注意：并非所有区域都支持 16 TB。 | 16 TB |
| 最小存储大小 | 5 GB（基本），100 GB（常规用途、内存优化） | 32 GB |
| 存储自动增长 | 是（以 1 GB 为增量） | 否 |
| 最大 IOPS | 基本 - 可变。 常规用途/内存优化：最大 20K  | 最大 20K |
| 网络/安全性 | | |
| 支持的网络 | 虚拟网络、专用链接、公共访问 | 专用访问（委托子网中的 VNET 注入）、公共访问 |
| 公共访问控制 | 防火墙 | 防火墙 |
| 对专用链接的支持 | 是 |否|
| 专用 DNS 区域支持 | 否 | 是 |
| 可在专用访问与公共访问之间切换 | 否 | 否 |
| TLS 支持 | TLS 1.2 | 强制实施 TLS 1.2、1.3|
| 可以关闭 SSL | 是 | 否 |
| SCRAM 身份验证 | 否 | 是 |
| **高可用性** | | |
| 区域冗余 HA | 否 | 是（在 Azure 区域中的另一个局部区域中建立同步的备用节点） |
| HA 配置 | 内置 HA，存储固定到某个局部区域。 计算可跨区域浮动。 | 跨两个局部区域预配的物理隔离的计算和存储 |
| 节约成本 | 1 倍 | 2 倍（计算 + 存储） |
| 使用非 HA 配置实现可用性 | 自动重启，计算重定位 | 自动重启，计算重定位
| 针对局部区域故障提供保护 | 计算 - 是。 存储 - 否 | 计算和存储 - 是 |
| 针对 Azure 区域故障提供保护 | 否 | 否 |
| HA 复制模式 | 空值 | 以同步模式进行 Postgres 物理流式传输
| 备用节点可用于读取目的 | 空值 | 否 |
| 应用程序性能影响 | 否（无需复制） | 是（因为需要进行同步复制。 影响程度取决于工作负载） |
| 自动故障转移 | 是（运转另一台服务器）| 是 |
| 故障转移后的应用程序连接字符串 | 没有变化 | 没有变化 |
| 逻辑复制 | | |
| 支持逻辑解码 | 是 | 是 |
| 支持本机逻辑复制 | 否 | 是 |
| 支持 PgLogical 扩展 | 否 | 是 |
| 支持使用 HA 功能进行逻辑复制 | 空值 | 受限制 |
| **灾难恢复** | | |
| 跨区域 DR | 使用只读副本，异地冗余备份 | 空值 |
| 使用副本实现 DR | 使用异步物理复制 | 空值 |
| 自动故障转移 | 否 | 空值 |
| 可以使用同一个读/写终结点 | 否 | 不适用 |
| 备份和恢复 | | |
| 自动备份 | 是 | 是 |
| 备份保留期 | 7-35 天 | 7-35 天 |
| 在保留期内可通过 PITR 功能还原到任意时间 | 是 | 是
| 可在不同的局部区域中还原 | 不适用 | 是 |
| 可还原到不同的 VNET | 否 | 是 |
| 可还原到不同的 Azure 区域 | 是（异地冗余） | 否 |
| 可还原已删除的服务器 | 仅限通过 API 还原 | 否 |
| 只读副本 | | |
| 支持只读副本 | 是 | 否 |
| 只读副本数 | 5 | 空值 |
| 复制模式 | 异步 | 空值 |
| 跨区域支持 | 是 | 不适用 |
| **维护时段** | | |
| 系统计划的时段 | 是 | 是 |
| 客户计划的时段| 否 | 是（可以选择任何一天的任何 1 小时） |
| 通知期 | 3 天 | 5 天 |
| 维护周期 | 15 小时时段内的任意时间 | 1 小时时段 | 
| **度量值** | | |
| 错误 | 失败的连接 | 失败的连接|
| 延迟 | 不同副本的最大滞后时间，副本滞后时间 | 不适用 |
| 饱和度 | 已用备份存储、CPU 百分比、IO 百分比、内存百分比、服务器日志存储限制、服务器日志存储百分比、已用服务器日志存储、存储限制、存储百分比、已用存储 | 已用备份存储、已用 CPU 额度、剩余 CPU 额度、CPU 百分比、磁盘队列深度、IOPS、内存百分比、读取 IOPS、读取吞吐量（字节/秒）、可用存储、存储百分比、已用存储、已用事务日志存储、写入 IOPS、写入吞吐量（字节/秒） |
| 交通 | 活动连接数、网络流入量、网络流出量 | 活动连接数、最大 已用事务 ID、网络流入量、网络流出量、成功连接数 |
| **扩展** | | （提供最新版本）|
| TimescaleDB、orafce、plv8 | 是 | 否 |
| PgCron、lo、pglogical | 否 | 是 |
| pgAudit | 预览 | 是 |
| **其他功能** | | |
| 客户管理的密钥 (BYOK) | 是 | 否 |
| 警报 | 是 | 是 |
| Azure Defender | 是 | 否 |
| 资源运行状况 | 是 | 否 |
| 服务运行状况 | 是 | 是 |
| 性能见解 (iPerf) | 是 | 是（预览版） |
| 主版本升级支持 | 否 | 否 |
| 次要版本升级 | 是的。 在维护时段自动升级 | 是的。 在维护时段自动升级 |


## <a name="next-steps"></a>后续步骤

- 了解[灵活服务器中提供的计算和存储选项](concepts-compute-storage.md)
- 了解[灵活服务器支持的 PostgreSQL 数据库版本](concepts-supported-versions.md)
- 了解[灵活服务器的当前限制](concepts-limits.md)