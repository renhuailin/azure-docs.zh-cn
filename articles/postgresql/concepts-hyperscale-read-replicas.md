---
title: 只读副本 - Azure Database for PostgreSQL - 超大规模 (Citus)
description: 本文介绍 Azure Database for PostgreSQL - 超大规模 (Citus) 中的只读副本功能。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 150d6b02ff2c11d2489c46b4dc2dd44fd2276b75
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736405"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - 超大规模 (Citus) 中的只读副本

使用只读副本功能可将数据从超大规模 (Citus) 服务器组复制到只读服务器组。 副本通过 PostgreSQL 物理复制技术以异步方式更新。 你可以将主服务器的数据复制到无限数量的副本中。

副本是新的服务器组，可以像管理普通的超大规模 (Citus) 服务器组一样对其进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及每月 GB 存储量计费。

了解如何[创建和管理副本](howto-hyperscale-read-replicas-portal.md)。

## <a name="when-to-use-a-read-replica"></a>何时使用只读副本

只读副本功能可帮助改善读取密集型工作负荷的性能与规模。 “读取”工作负载可以隔离到副本，而“写入”工作负载可以定向到主服务器。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

由于副本是只读的，因此它们不能直接减少主服务器上的写入容量负担。

### <a name="considerations"></a>注意事项

此功能适用于可接受复制滞后的方案，旨在减轻查询负担。 它不适用于同步复制方案，此类方案要求副本数据是最新数据。 主服务器与副本之间将存在明显的延迟。 该延迟可能是几分钟甚至几小时，具体取决于工作负载以及主服务器与副本之间的延迟。  副本上的数据最终将与主服务器上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。 

## <a name="create-a-replica"></a>创建副本

启动“创建副本”工作流时，将创建空白的超大规模 (Citus) 服务器组。 新组中填充了主服务器组上的数据。 创建时间取决于主服务器上的数据量，以及自上次每周完整备份以来所经历的时间。 具体所需时间从几分钟到几小时不等。

只读副本功能使用 PostgreSQL 的物理复制，而不使用逻辑复制。 默认模式是使用复制槽位的流复制。
必要时，使用日志传送来跟上进度。

了解如何[在 Azure 门户中创建只读副本](howto-hyperscale-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>连接到副本

创建副本时，该副本不会继承主服务器组的防火墙规则。 必须单独为副本设置这些规则。

副本从主服务器组继承管理员（“citus”）帐户。
所有用户帐户将复制到只读副本。 只能使用主服务器上可用的用户帐户连接到只读副本。

可以使用主机名和有效的用户帐户连接到副本的协调器节点，就像在常规的超大规模 (Citus) 服务器组上连接一样。
对于名称为“my replica”、管理员用户名为“citus”的服务器，可以使用 psql 连接到副本的协调器节点 ：

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

在提示符下，输入用户帐户的密码。

## <a name="considerations"></a>注意事项

本部分汇总了有关只读副本功能的注意事项。

### <a name="new-replicas"></a>新副本

只读副本创建为新的超大规模 (Citus) 服务器组。 无法将现有的服务器组设为副本。 无法创建另一个只读副本的副本。

### <a name="replica-configuration"></a>副本配置

副本是通过使用与主服务器相同的计算、存储和工作器节点设置创建的。 创建副本后，可以更改多个设置，包括存储和备份保留期。 副本中的其他设置（如存储大小和工作器节点数）则不能更改。

记住要保持副本足够强大，以便与来自主服务器的更改保持一致。 例如，如果你在主服务器上提升了计算能力，务必在副本上也提升该能力。

创建副本时或之后，防火墙规则参数设置不会从主服务器继承到副本。

### <a name="regions"></a>区域

超大规模 (Citus) 服务器组仅支持同一区域复制。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 门户中创建和管理只读副本](howto-hyperscale-read-replicas-portal.md)。
