---
title: 从 VNet 注入缓存迁移到专用链接缓存
description: 了解如何将 Azure Cache for Redis 虚拟网络 (VNet) 缓存迁移至专用链接缓存。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 8ab6acaf1ff6742db0b6dc750e39803123829742
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779704"
---
# <a name="migrate-from-vnet-injection-caches-to-private-link-caches"></a>从 VNet 注入缓存迁移到专用链接缓存
本文介绍将 VNet Azure Cache for Redis 虚拟网络 (VNet) 注入缓存实例迁移到 Azure Cache for Redis 专用链接缓存实例的一些方法。 

[Azure 专用链接](../private-link/private-link-overview.md)可简化网络体系结构，并保护 Azure 中终结点之间的连接。 你可以通过专用终结点从虚拟网络连接到 Azure 缓存实例，该终结点在虚拟网络中的子网内分配有一个专用 IP 地址。 使用 Azure Cache for Redis 专用链接的优点包括： 

* 层级灵活性 – 我们的所有层级（基本、标准、高级、Enterprise 和 Enterprise Flash）都支持 Azure 专用链接。 而虚拟网络注入仅适用于高级层。   

* 简化的网络安全组 (NSG) 规则管理 - 无需配置 NSG 规则以遵守 Azure Cache for Redis 中的要求。

* Azure Policy 支持 – 确保使用专用链接创建组织的所有缓存，并审核组织的现有缓存，以验证它们是否全部使用专用链接。

## <a name="migration-options"></a>迁移选项

可以使用几种不同的方式从 VNet 注入切换到专用链接。 根据缓存所在的位置以及应用程序与缓存的交互方式，其中一种方法将比其他方法更有用。 下面详细介绍一些常用的迁移策略。 

### <a name="if-youre-using-any-combination-of-geo-replication-clustering-or-arm-vnet"></a>如果使用异地复制、群集或 ARM VNet 的任意组合：

   | 选项       | 优点 | 缺点 |
   | ------------ | ---------- | ------------- |
   | 将数据双重写入两个缓存 | 无数据丢失或停机时间。 现有缓存不间断操作。 更轻松地测试新缓存。 | 需要两个缓存以延长时间。 | 
   | 创建新缓存 | 最易于实现。 | 需要将数据重新填充到新的缓存中（这可能不适用于许多应用程序）。 |
   | 通过 RDB 文件导出和导入数据 | 必须迁移数据。 | 如果某些数据在生成 RDB 文件后写入现有缓存，这些数据可能会丢失。 | 
   | 以编程方式迁移数据 | 对数据的移动方式具有完全控制。 | 需要自定义代码。 | 

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>在迁移期间同时写入两个 Redis 缓存

可以使用应用程序将数据写入现有缓存和要设置的新缓存，而不是直接在缓存之间移动数据。 应用程序一开始仍将从现有缓存中读取数据。 当新缓存拥有必要的数据后，就可以将应用程序切换到该缓存，然后停用旧缓存。 举例来说，假设使用 Redis 作为会话存储，且应用程序会话的有效期为 7 天。 写入两个缓存一周后，即可确定新缓存包含所有未过期会话信息。 此后，可放心地依靠它，而不必担心数据丢失。

实现此选项的一般步骤如下：

1. 使用与现有缓存相同大小（或比之更大）的专用终结点创建新的 [Azure Cache for Redis](cache-private-link.md) 实例。

2. 修改应用程序代码以写入新实例和原始实例。

3. 继续从原始实例读取数据，直到新实例填充足够的数据为止。

4. 更新应用程序代码以仅从新实例读取和写入。

5. 删除原始实例。

### <a name="create-a-new-azure-cache-for-redis"></a>创建新的 Azure Cache for Redis

从技术上讲，这种方法不是迁移。 如果无需担心丢失数据，则迁移 Azure Cache for Redis 的最简单方法是创建缓存实例并将应用程序连接到该实例。 例如，如果将 Redis 用作数据库记录的后备缓存，则可以轻松地从头开始重新生成缓存。

实现此选项的一般步骤如下：

1. 使用[专用终结点创建新的 Azure Cache for Redis 实例](cache-private-link.md)。

2. 更新应用程序以使用新实例。

3. 删除旧的 Redis 实例。

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis-premium-tier-only"></a>将数据导出到 RDB 文件，并将该文件导入 Azure Cache for Redis（仅限高级层）

开源 Redis 定义了一种标准机制，用于获取缓存的内存中数据集的快照并将其保存到文件中。 此文件名为 RDB，可由另一个 Redis 缓存读取。 [Azure Cache for Redis 高级层](cache-overview.md#service-tiers)支持通过 RDB 文件将数据导入缓存实例。 可以使用 RDB 文件将数据从现有缓存传输到 Azure Cache for Redis。

> [!IMPORTANT]
> RDB 文件格式可以在 Redis 版本之间更改，并且可能无法保持向后兼容。 要从中导出的缓存的 Redis 版本应等于或低于 Azure Cache for Redis 提供的版本。
>

实现此选项的一般步骤如下：

1. 使用高级层的[专用终结点创建新的 Azure Cache for Redis 实例](cache-private-link.md)，其中该实例的大小与现有缓存相同（比之更大）。

2. 保存现有 Redis 缓存的快照。 可以[配置 Redis 以定期保存快照](https://redis.io/topics/persistence)，或者使用 [SAVE](https://redis.io/commands/save) 或 [BGSAVE](https://redis.io/commands/bgsave) 命令手动运行该过程。 RDB 文件默认命名为“dump.rdb”，并将位于 redis.conf 配置文件中指定的路径下。

    > [!NOTE]
    > 如果要在 Azure Cache for Redis 中迁移数据，请参阅[这些有关如何导出 RDB 文件的说明](cache-how-to-import-export-data.md)，或改为使用 [PowerShell 导出 cmdlet](/powershell/module/azurerm.rediscache/export-azurermrediscache)。
    >

3. 将 RDB 文件复制到新缓存所在区域中的 Azure 存储帐户。 可以将 AzCopy 用于此任务。

4. 使用这些[导入说明](cache-how-to-import-export-data.md)或 [PowerShell 导入 cmdlet](/powershell/module/azurerm.rediscache/import-azurermrediscache) 将 RDB 文件导入新缓存。

5. 更新应用程序以使用新缓存实例。

### <a name="migrate-programmatically"></a>以编程方式迁移

可以通过编程方式从现有缓存中读取数据并将其写入 Azure Cache for Redis，从而创建自定义迁移过程。 此[开源工具](https://github.com/deepakverma/redis-copy)可用于将数据从一个 Azure Cache for Redis 实例复制到另一个 Azure Cache for Redis 实例。 此工具对于在不同 Azure 缓存区域中的缓存实例之间移动数据很有用。 还提供[编译版本](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)。 你可能还会发现，源代码可为编写自己的迁移工具提供有用指导。

> [!NOTE]
> Microsoft 尚未正式支持此工具。 
>

实现此选项的一般步骤如下：

1. 在现有缓存​​所在区域中创建 VM。 如果数据集很大，请选择功能相对强大的 VM 以减少复制时间。

2. 使用专用终结点创建新的 [Azure Cache for Redis 实例](cache-private-link.md)

3. 刷新新缓存中的数据以确保其为空。 由于复制工具本身不会覆盖目标缓存中的任何现有密钥，因此需要执行此步骤。

    > [!IMPORTANT]
    > 确保不要从源缓存中刷新。
    >

4. 使用应用程序（例如上述开源工具）自动将数据从源缓存复制到目标缓存。 请记住，复制过程可能需要一段时间才能完成，具体取决于数据集的大小。


## <a name="next-steps"></a>后续步骤
* 详细了解[网络隔离选项](cache-network-isolation.md)。 
* 了解如何[为所有 Azure Cache for Redis 层配置专用终结点](cache-private-link.md)。