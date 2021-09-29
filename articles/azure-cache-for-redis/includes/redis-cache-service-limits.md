---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 4fa712e8cf19bf4deb9b536777520a3ffec7261c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645444"
---
| 资源 | 限制 |
| --- | --- |
| 缓存大小 |1.2 TB |
| 数据库 |64 |
| 连接的最大客户端数 |40,000 |
| Azure Redis 缓存副本，用于高可用性 |1 |
| 启用群集的高级缓存中的分片数 |10 |

每个定价层的 Azure Redis 缓存限制和大小都不相同。 若要查看定价层及其关联的大小，请参阅 [Azure Cache for Redis 定价](https://azure.microsoft.com/pricing/details/cache/)。

有关 Azure Redis 缓存配置限制的详细信息，请参阅[默认 Redis 服务器配置](../cache-configure.md#default-redis-server-configuration)。

由于 Azure Redis 缓存实例的配置和管理是由 Microsoft 执行的，因此 Azure Redis 缓存并非支持所有 Redis 命令。 有关详细信息，请参阅 [Azure Redis 缓存不支持的 Redis 命令](../cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)。
