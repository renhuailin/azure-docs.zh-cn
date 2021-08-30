---
title: 限制 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 超大规模 (Citus) 服务器组的当前限制
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 5bf02173d105ab81807bdc4ee68e3b8f9bc8e0a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746320"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL - 超大规模 (Citus) 的限制

以下部分介绍了超大规模 (Citus) 服务中的容量和功能限制。

## <a name="maximum-connections"></a>最大连接数

每个 PostgreSQL 连接（即使是空闲连接）都至少使用 10 MB 的内存，因此，必须限制同步连接的数量。 我们选择使用以下限制来保证节点正常运行：

* 协调器节点
   * 最大连接数
       * 0-3 个 vCore：300
       * 4-15 个 vCore：500
       * 16 个以上的 vCore：1000
   * 最大用户连接数
       * 0-3 个 vCore：297
       * 4-15 个 vCore：497
       * 16 个以上的 vCore：997
* 工作器节点
   * 最大连接数
       * 600

超出这些限制后，连接尝试将失败并提示错误。 系统会保留三个连接以用于监视节点，这就是用户查询可用的连接数比总连接数少三个的原因。

### <a name="connection-pooling"></a>连接池

可以使用[连接池](concepts-hyperscale-connection-pool.md)进一步缩放连接。 超大规模 (Citus) 提供一种托管 pgBouncer 连接池程序，配置为可同时具有最多 2000 个客户端连接。

## <a name="storage-scaling"></a>存储缩放

协调器和工作器节点上的存储可以纵向扩展（增加），但无法纵向缩减（减少）。

## <a name="storage-size"></a>存储大小

协调器和工作器节点最多支持 2 TiB 的存储。 请参阅[上面](concepts-hyperscale-configuration-options.md#compute-and-storage)的可用存储选项和 IOPS 计算，了解节点和群集大小。

## <a name="database-creation"></a>数据库创建

每个超大规模 (Citus) 服务器组只具有一个数据库（`citus` 数据库），Azure 门户提供用于连接到该数据库的凭据。 目前不允许创建其他数据库，CREATE DATABASE 命令会失败并提示错误。

## <a name="columnar-storage"></a>列式存储

超大规模 (Citus) 目前对[列式表](concepts-hyperscale-columnar.md)有以下限制：

* 压缩发生在磁盘上，而不是在内存中
* 仅追加（不支持 UPDATE/DELETE）
* 不支持空间回收（例如，回滚的事务可能仍占用磁盘空间）
* 不支持索引、索引扫描或位图索引扫描
* 不支持 tidscan
* 不支持示例扫描
* 不支持 TOAST（以内联形式支持较大值）
* 不支持 ON CONFLICT 语句（未指定目标的 DO NOTHING 操作除外）
* 不支持元组锁（SELECT ... FOR SHARE、SELECT ... FOR UPDATE）
* 不支持可序列化的隔离级别
* 仅支持 PostgreSQL 服务器 12 以上的版本
* 不支持外键、唯一约束或排除约束
* 不支持逻辑解码
* 不支持节点内并行扫描
* 不支持 AFTER ... FOR EACH ROW 触发器
* 不支持 UNLOGGED 列式表
* 不支持 TEMPORARY 列式表

## <a name="next-steps"></a>后续步骤

* 了解如何[在门户中创建超大规模 (Citus) 服务器组](quickstart-create-hyperscale-portal.md)。
* 了解如何启用[连接池](concepts-hyperscale-connection-pool.md)。
