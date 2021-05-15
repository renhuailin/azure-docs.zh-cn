---
title: 基础结构双重加密 - Azure Database for PostgreSQL
description: 了解如何使用基础结构双重加密通过服务托管密钥添加另一层加密。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 83635b732318a4ada76d1d71c1ce419cae8b35e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630134"
---
# <a name="azure-database-for-postgresql-infrastructure-double-encryption"></a>Azure Database for PostgreSQL 基础结构双重加密

Azure Database for PostgreSQL 使用 Microsoft 的托管密钥对数据进行[静态数据存储加密](concepts-security.md#at-rest)。 数据（包括备份）在磁盘上加密，这种加密始终可用且无法禁用。 加密使用 FIPS 140-2 验证的加密模块和 AES 256 位密码进行 Azure 存储加密。

基础结构双重加密可通过服务托管密钥再添加一层加密。 这种功能使用 FIPS 140-2 验证的加密模块，但使用不同的加密算法。 这样可为静态数据额外提供一层保护。 基础结构双重加密中使用的密钥也由 Azure Database for PostgreSQL 服务管理。 基础结构双重加密默认禁用，因为额外一层加密可能会影响性能。

> [!NOTE]
> 只有 Azure Database for PostgreSQL 的“常规用途”和“内存优化”定价层支持此功能。

基础结构层加密的优势是可在距离存储设备或网络线路最近的层实施加密。 Azure Database for PostgreSQL 使用服务托管密钥来实现两层加密。 尽管从技术上讲，加密仍位于服务层，但距离存储静态数据的硬件非常近。 对于预配的 PostgreSQL 服务器，仍可以选择使用[客户管理的密钥](concepts-data-encryption-postgresql.md)来启用静态数据加密。  

在基础结构层实现加密也支持密钥多样化。 基础结构必须知道计算机和网络的不同群集。 因此，将使用不同的密钥来最大程度地减小基础结构攻击的冲击半径，以及各种硬件和网络故障。 

> [!NOTE]
> 使用基础结构双重加密，由于加密进程增多，所以会对 Azure Database for PostgreSQL 服务器造成一定的性能影响。

## <a name="benefits"></a>优点

Azure Database for PostgreSQL 基础结构双重加密具有以下优势：

1. 实现加密的方式更加多样化 - 除基于软件的加密实现之外，计划的基于硬件加密的举措可通过提供基于硬件的加密实现，让加密实现方式进一步多样化。
2. 实现错误 - 基础结构层的两层加密可防止展示纯文本数据的更高层中出现任何高速缓存或内存管理错误。 此外，这两层还可以预防进行加密出现的一般错误。

这种组合可对用于攻击加密的常见威胁和漏洞提供强大防护。

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>基础结构双重加密支持的方案

Azure Database for PostgreSQL 提供的加密功能可以一起使用。 下面是可以使用的各种方案的摘要：

|  ##   | 默认加密 | 基础结构双重加密 | 使用客户管理的密钥进行的数据加密  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *是*              | *否*                             | *是*                                         |
| 2     | *是*              | *是*                            | *否*                                         |
| 3     | *是*              | *否*                             | *是*                                        |
| 4     | *是*              | *是*                            | *是*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - 方案 2 和 4 由于基础结构加密的层数增加，会对 Azure Database for PostgreSQL 服务器造成一定的性能影响。
> - 要配置 Azure Database for PostgreSQL 基础结构双重加密，只能在创建服务器过程中进行。 预配服务器之后，则不能更改存储加密。 不过，仍可以使用客户管理的密钥对创建时已启用/未启用基础结构双重加密的服务器启用数据加密。

## <a name="limitations"></a>限制

对于 Azure Database for PostgreSQL，支持使用服务托管密钥进行基础结构双重加密具有以下限制：

* 对此功能的支持仅限“常规用途”和“内存优化”定价层。
* 此功能仅在支持多达 16 TB 存储的区域和服务器上受支持。 有关支持多达 16TB 存储的 Azure 区域列表，请参阅[存储文档](concepts-pricing-tiers.md#storage)。

    > [!NOTE]
    > - 此外，在上述所列区域创建的所有新 PostgreSQL 服务器也支持使用客户管理的密钥进行数据加密。 对于这种情况，通过时间点还原 (PITR) 或只读副本创建的服务器不属于“新”服务器。
    > - 要验证预配的服务器是否支持多达 16 TB，可转到门户中的“定价层”边栏选项卡，查看存储滑块是否可以上移到 16 TB。 如果只能将滑块上移到 4 TB，则该服务器可能不支持使用客户管理的密钥进行加密；但是，始终可使用服务托管密钥对数据进行加密。 如果有任何疑问，请联系 AskAzureDBforPostgreSQL@service.microsoft.com。

## <a name="next-steps"></a>后续步骤

了解如何[设置 Azure Database For PostgreSQL 基础结构双重加密](howto-double-encryption.md)。
