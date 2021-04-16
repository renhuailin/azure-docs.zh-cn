---
title: 限制 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 超大规模 (Citus) 服务器组的当前限制
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023794"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL - 超大规模 (Citus) 的限制

以下部分介绍了超大规模 (Citus) 服务中的容量和功能限制。

## <a name="maximum-connections"></a>最大连接数

每个 PostgreSQL 连接（即使是空闲连接）都至少使用 10 MB 的内存，因此，必须限制同步连接的数量。 我们选择使用以下限制来保证节点正常运行：

* 协调器节点
   * 最大连接数：300
   * 最大用户连接数：297
* 工作器节点
   * 最大连接数：600
   * 最大用户连接数：597

> [!NOTE]
> 在启用了[预览功能](hyperscale-preview-features.md)的服务器组中，对协调器的连接限制略有不同：
>
> * 协调器节点最大连接数
>    * 0-3 个 vCore：300
>    * 4-15 个 vCore：500
>    * 16 个以上的 vCore：1000

超出这些限制后，连接尝试将失败并提示错误。 系统会保留三个连接以用于监视节点，这就是用户查询可用的连接数比总连接数少三个的原因。

### <a name="connection-pooling"></a>连接池

建立新连接需要时间。 这会对多数应用程序产生不利影响，因为这些应用程序会请求建立很多短时的连接。 建议使用连接池程序，既可以减少空闲事务，又可以重用现有连接。 要了解详细信息，请访问[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)。

你可以运行自己的连接池程序，也可以使用 Azure 托管的 PgBouncer。

#### <a name="managed-pgbouncer-preview"></a>托管的 PgBouncer（预览版）

> [!IMPORTANT]
> 超大规模 (Citus) 中的托管 PgBouncer 连接池程序当前处于预览阶段。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>
> 请参阅[超大规模 (Citus) 的预览功能](hyperscale-preview-features.md)，以查看其他新功能的完整列表。

使用 PgBouncer 之类的连接池程序时，可以一次性将更多客户端连接到协调器节点。 应用程序连接到池程序，池程序将命令中继到目标数据库。

当客户端通过 PgBouncer 连接时，可在数据库中主动运行的连接数不变。 相反，PgBouncer 会将超出的连接排入队列，并在数据库准备就绪时运行这些连接。

超大规模 (Citus) 现在为服务器组提供 PgBouncer 托管实例（预览版）。 它支持多达 2,000 个客户端同时连接。
若要通过 PgBouncer 连接，请按照以下步骤操作：

1. 在 Azure 门户中转到相应服务器组的“连接字符串”页。
2. 启用“PgBouncer 连接字符串”复选框。 （列出的连接字符串将发生更改。）
3. 将客户端应用程序更新为使用新的字符串进行连接。

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

了解如何[在门户中创建超大规模 (Citus) 服务器组](quickstart-create-hyperscale-portal.md)。
