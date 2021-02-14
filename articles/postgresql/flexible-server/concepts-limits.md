---
title: 限制 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍了 Azure Database for PostgreSQL 灵活服务器中的限制，例如连接数和存储引擎选项。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0221022c342735744d59f956d6047b4abf23b5cf
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516509"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中的限制

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

下列各部分介绍数据库服务中的容量和功能限制。 如果想了解资源（计算、内存、存储）层，请参阅[计算和存储](concepts-compute-storage.md)一文。

## <a name="maximum-connections"></a>最大连接数

每个定价层的最大连接数和 vCore 数如下所示。 Azure 系统需要使用三个连接来监视 Azure Database for PostgreSQL 灵活服务器。

| SKU 名称             | vCore 数 | 内存大小 | 最大连接数 | 最大用户连接数 |
|----------------------|--------|-------------|-----------------|----------------------|
| **可突发**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **常规用途**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5000            | 4997                 |
| **内存优化** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

当连接数超出限制时，可能会收到以下错误：
> 严重：很抱歉，客户端数过多

> [!IMPORTANT]
> 为了获得最佳体验，我们建议你使用 PgBouncer 之类的连接池来有效地管理连接。

PostgreSQL 连接，即使空闲，也可以占用大约 10MB 的内存。 而且，创建新连接需要时间。 大多数应用程序请求许多生存期短的连接，这加剧了这种情况。 其结果是可用于实际工作负荷的资源减少，从而导致性能下降。 连接池可用于减少空闲连接和重用现有连接。 要了解详细信息，请访问[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)。

## <a name="functional-limitations"></a>功能限制

### <a name="scale-operations"></a>缩放操作

- 缩放服务器存储需要重启服务器。
- 服务器存储只能以 2 倍增量进行扩展，请参阅[计算和存储](concepts-compute-storage.md)，了解详细信息。
- 目前不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级

- 目前不支持在主要数据库引擎版本之间进行自动迁移。 如果要升级到下一个主版本，请进行[转储并将其还原](../howto-migrate-using-dump-and-restore.md)到使用新引擎版本创建的服务器。

### <a name="storage"></a>存储

- 配置完成后，将无法减少存储大小。
- 目前，存储自动增长功能不可用。 请监视使用情况，并将存储增加到更大的大小。 
- 当存储使用率达到95% 或可用容量小于 5 GiB 时，服务器会自动切换为 **只读模式** ，以避免与磁盘完全相同的情况下出现的错误。 
- 建议为或设置警报规则， `storage used` `storage percent` 使其超过特定阈值，以便您可以主动采取措施，例如增加存储大小。 例如，可以在存储百分比超过80% 时设置警报。
  
### <a name="networking"></a>网络

- 目前不支持移入或移出 VNET。
- 当前不支持将公共访问与 VNET 内的部署相结合。
- VNET 不支持防火墙规则，可以改为使用网络安全组。
- 公共访问数据库服务器可以连接到公共 Internet（例如通过 `postgres_fdw`），并且不能限制此访问。 基于 VNET 的服务器可以使用网络安全组限制出站访问。

### <a name="high-availability-ha"></a>高可用性 (HA) 

- 可突增服务器当前不支持 HA Zone-Redundant。
- 服务器故障转移到 HA 备用服务器时，数据库服务器的 IP 地址会发生变化。 确保使用 DNS 记录而不是服务器 IP 地址。
- 如果为逻辑复制配置了 HA 配置灵活的服务器，则在故障转移到备用服务器时，不会将逻辑复制槽复制到备用服务器。 
- 有关区域冗余 HA 包括限制的更多详细信息，请参阅 [概念-HA 文档](concepts-high-availability.md) 页。

### <a name="availability-zones"></a>可用性区域

- 当前不支持将服务器手动移到不同的可用性区域。
- 不能手动配置 HA 备用服务器的可用性区域。

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres 引擎、扩展和 PgBouncer

- 不支持 Postgres 10 及更早版本。 如果需要较旧的 Postgres 版本，建议使用[单一服务器](../overview-single-server.md)选项。
- 扩展支持目前仅限于 Postgres `contrib` 扩展。
- 内置的 PgBouncer 连接池程序当前不适用于 VNET 中的数据库服务器，也不适用于可突发服务器。

### <a name="stopstart-operation"></a>停止/启动操作

- 服务器不能停止超过7天。

### <a name="scheduled-maintenance"></a>计划性维护

- 在已计划的升级开始前五天内更改维护时段不会影响升级。 更改仅在下次计划维护时生效。

### <a name="backing-up-a-server"></a>对服务器进行备份

- 备份由系统管理，目前无法手动运行这些备份。 我们推荐改用`pg_dump`。
- 备份始终是基于快照的完整备份（不是差异备份），可能导致更高的备份存储利用率。 请注意，事务日志（预写日志 - WAL）独立于完整/差异备份，并且会持续存档。

### <a name="restoring-a-server"></a>还原服务器

- 使用时间点还原功能时，将使用与新服务器所基于的服务器相同的计算和存储配置创建新服务器。
- 从备份还原时，基于 VNET 的数据库服务器将还原到同一 VNET 中。
- 还原期间创建的新服务器没有原始服务器上存在的防火墙规则。 需要为新服务器单独创建防火墙规则。
- 不支持还原已删除的服务器。
- 不支持跨区域还原。

### <a name="other-features"></a>其他功能

* 不支持 Azure AD 身份验证。 如果需要 Azure AD 身份验证，建议使用[单一服务器](../overview-single-server.md)选项。
* 尚不支持只读副本。 如果需要只读副本，建议使用[单一服务器](../overview-single-server.md)选项。
* 不支持将资源移到另一个订阅。 


## <a name="next-steps"></a>后续步骤

- 了解[可用于计算和存储选项的内容](concepts-compute-storage.md)
- 了解[支持的 PostgreSQL 数据库版本](concepts-supported-versions.md)
- 查看[如何使用 Azure 门户在 Azure Database for PostgreSQL 中备份和还原服务器](how-to-restore-server-portal.md)
