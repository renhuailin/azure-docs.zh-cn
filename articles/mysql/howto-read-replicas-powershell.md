---
title: 管理只读副本 - Azure PowerShell - Azure Database for MySQL
description: 了解如何使用 PowerShell 在 Azure Database for MySQL 中设置和管理只读副本。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f4980692be64c2a8b3918d2dbaab5ef9c983f453
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777805"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>如何使用 PowerShell 在 Azure Database for MySQL 中创建和管理只读副本

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本文介绍如何使用 PowerShell 在 Azure Database for MySQL 服务中创建和管理只读副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

## <a name="azure-powershell"></a>Azure PowerShell

可以使用 PowerShell 创建和管理只读副本。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 本地安装 [Az PowerShell 模块](/powershell/azure/install-az-ps)或浏览器中有 [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az.MySql PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.MySql -AllowPrerelease`。
> Az.MySql PowerShell 模块正式版推出后，它将会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

如果选择在本地使用 PowerShell，请使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 服务器。 请确保源服务器位于其中一个定价层中。
>
>如果在主服务器上启用了 GTID (`gtid_mode` = ON)，则新创建的副本也会启用 GTID 并使用基于 GTID 的复制。 若要了解详细信息，请参阅[全局事务标识符 (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid)

### <a name="create-a-read-replica"></a>创建只读副本

> [!IMPORTANT]
> 如果为没有现有副本的源服务器创建副本，源服务器将首先重启，以便为复制做好准备。 请考虑这一点并在非高峰期执行这些操作。

可以使用以下命令创建只读副本服务器：

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMySqlReplica` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  在其中创建副本服务器的资源组。  |
| 名称 | mydemoreplicaserver | 所创建的新副本服务器的名称。 |

若要创建跨区域只读副本，请使用 Location 参数。 以下示例在美国西部区域创建了一个副本。

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

> [!NOTE]
> 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

默认情况下，除非指定了 Sku 参数，否则将使用与源服务器相同的服务器配置来创建只读副本。

> [!NOTE]
> 建议副本服务器的配置应始终采用与源服务器相同或更大的值，以确保副本能够与主服务器保持一致。

### <a name="list-replicas-for-a-source-server"></a>列出源服务器的副本

若要查看给定源服务器的所有副本，请运行以下命令：

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMySqlReplica` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  要在其中创建副本服务器的资源组。  |
| ServerName | mydemoserver | 源服务器的名称或 ID。 |

### <a name="delete-a-replica-server"></a>删除副本服务器

可以通过运行 `Remove-AzMySqlServer` cmdlet 来删除只读副本服务器。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>删除源服务器

> [!IMPORTANT]
> 删除源服务器会停止复制到所有副本服务器，并删除源服务器本身。 副本服务器成为现在支持读取和写入的独立服务器。

若要删除源服务器，可以运行 `Remove-AzMySqlServer` cmdlet。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

### <a name="known-issue"></a>已知问题

常规用途和内存优化层中的服务器使用两代存储，常规用途存储 v1（支持最高 4 TB）和常规用途存储 v2（支持最高 16 TB 存储）。
源服务器和副本服务器应该具有相同的存储类型。 由于[常规用途存储 v2](./concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage) 在所有区域中均不可用，因此请确保在将位置与 PowerShell 配合使用来创建只读副本时选择正确的副本区域。 有关如何确定源服务器的存储类型，请参阅链接[如何确定服务器上运行的存储类型](./concepts-pricing-tiers.md#how-can-i-determine-which-storage-type-my-server-is-running-on)。 

如果选择无法为源服务器创建只读副本的区域，则会遇到这样的问题：部署将持续运行（如下图所示），然后超时并收到错误“资源预配操作未在允许的超时时间内完成。”

[:::image type="content" source="media/howto-read-replicas-powershell/replcia-ps-known-issue.png" alt-text="只读副本 cli 错误":::](media/howto-read-replicas-powershell/replcia-ps-known-issue.png#lightbox)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 重启 Azure Database for MySQL 服务器](howto-restart-server-powershell.md)
