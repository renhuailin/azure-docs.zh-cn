---
title: 基础结构双重加密 - Azure Database for MySQL
description: 了解如何使用基础结构双重加密通过服务托管密钥添加另一层加密。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 9b6d87c3bfabf3884d9a90966994eea002a45dd8
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674428"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Azure Database for MySQL 基础结构双重加密

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL 使用 Microsoft 的托管密钥对数据进行[静态数据存储加密](concepts-security.md#at-rest)。 数据（包括备份）在磁盘上加密，这种加密始终可用且无法禁用。 加密使用 FIPS 140-2 验证的加密模块和 AES 256 位密码进行 Azure 存储加密。

基础结构双重加密可使用服务托管密钥添加另一层加密。 这种功能使用 FIPS 140-2 验证的加密模块，但使用不同的加密算法。 这样可为静态数据提供额外一层保护。 基础结构双重加密中使用的密钥也由 Azure Database for MySQL 服务管理。 基础结构双重加密默认处于禁用状态，因为额外一层加密可能会影响性能。

> [!NOTE]
> 与静态数据加密一样，仅在“通用”和“内存优化”定价层中可用的“通用存储 v2（支持高达 16TB）”存储上支持此功能。 有关更多详细信息，请参阅[存储概念](concepts-pricing-tiers.md#storage)。 有关其他限制，请参阅[限制](concepts-infrastructure-double-encryption.md#limitations)部分。

基础结构层加密的优势是可在距离存储设备或网络线路最近的层实现加密。 Azure Database for MySQL 使用服务托管密钥来实现两层加密。 尽管从技术上讲，加密仍位于服务层，但距离存储静态数据的硬件非常近。 对于预配的 MySQL 服务器，仍可以选择使用[客户管理的密钥](concepts-data-encryption-mysql.md)来启用静态数据加密。 

在基础结构层实现加密也支持密钥多样化。 基础结构必须了解计算机和网络的不同群集。 因此，将使用不同的密钥来最大程度地减小基础结构攻击的冲击半径，以及各种硬件和网络故障。 

> [!NOTE]
> 由于有额外的加密过程，所以使用基础结构双重加密将会对 Azure Database for MySQL 服务器的吞吐量造成 5% 到 10% 的性能影响。

## <a name="benefits"></a>优点

Azure Database for MySQL 基础结构双重加密具有以下优势：

1. 实现加密的方式更加多样化 - 除基于软件的加密实现之外，计划的基于硬件加密的举措可通过提供基于硬件的加密实现，让加密实现方式进一步多样化。
2. 实现错误 - 基础结构层的两层加密可防止展示纯文本数据的更高层中出现任何高速缓存或内存管理错误。 此外，这两层还可以大体上预防加密实现过程中出现错误。

这种组合可对用于攻击加密的常见威胁和漏洞提供强大防护。

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>基础结构双重加密支持的方案

Azure Database for MySQL 提供的加密功能可以一起使用。 下面是可以使用的各种方案的摘要：

|  ##   | 默认加密 | 基础结构双重加密 | 使用客户管理的密钥进行数据加密  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *是*              | *否*                             | *是*                                         |
| 2     | *是*              | *是*                            | *否*                                         |
| 3     | *是*              | *否*                             | *是*                                        |
| 4     | *是*              | *是*                            | *是*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - 由于有额外一层基础结构加密，方案 2 和 4 可能会根据 Azure Database for MySQL 服务器的工作负载类型导致吞吐量下降 5-10%。
> - 只允许在创建服务器过程中配置 Azure Database for MySQL 基础结构双重加密。 预配服务器之后，则不能更改存储加密。 不过，仍可以使用客户管理的密钥对创建时已启用/未启用基础结构双重加密的服务器启用数据加密。

## <a name="limitations"></a>限制

对于 Azure Database for MySQL，对基础设施双重加密的支持几乎没有限制 -

* 对此功能的支持仅限“常规用途”和“内存优化”定价层。
* 此功能仅在支持通用存储 v2（最高 16 TB）的区域和服务器上受支持。 有关支持存储最多 16 TB 的 Azure 区域的列表，请参阅[此处](concepts-pricing-tiers.md#storage)文档中的“存储”部分

    > [!NOTE]
    > - [Azure 区域](concepts-pricing-tiers.md#storage)中创建的所有新 MySQL 服务器都支持通用存储 v2，因此也支持使用客户管理器密钥的加密。 时间点还原 (PITR) 服务器或只读副本不符合条件，尽管其在理论上是“新的”。
    > - 要验证预配的服务器是否支持通用存储 v2，可以在门户中访问“定价层”边栏选项卡，并查看预配服务器支持的最大存储大小。 如果可以将滑块向上移动到 4TB，则服务器是通用存储 v1，不支持通过客户管理的密钥进行加密。 但是，始终使用服务托管密钥对数据进行加密。 如果有任何疑问，请联系 AskAzureDBforMySQL@service.microsoft.com。



## <a name="next-steps"></a>后续步骤

了解如何[设置 Azure Database For MySQL 基础结构双重加密](howto-double-encryption.md)。
