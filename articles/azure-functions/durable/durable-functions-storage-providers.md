---
title: Durable Functions 存储提供程序 - Azure
description: 了解 Durable Functions 的不同存储提供程序及其差异
author: cgillum
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: azfuncdf
ms.openlocfilehash: bbeb730b0e22bd555f06514870448a229fccfbc6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438094"
---
# <a name="durable-functions-storage-providers"></a>Durable Functions 存储提供程序

Durable Functions 会自动将函数参数、返回值和其他状态保存到持久存储中，以确保可靠的执行。 Durable Functions 的默认配置将此运行时状态存储在 Azure 存储（经典）帐户中。 但是，可以配置 Durable Functions v2.0 及更高版本以使用备用持久存储提供程序。

Durable Functions 是由[持久任务框架](https://github.com/Azure/durabletask) (DTFx) 在内部提供支持的一组 Azure Functions 触发器和绑定。 DTFx 支持各种后端存储提供程序，包括 Durable Functions 使用的 Azure 存储提供程序。 从 Durable Functions v2.5.0 开始，用户可以将其函数应用配置为使用 DTFx 存储提供程序而不是 Azure 存储提供程序。

> [!NOTE]
> 应该谨慎选择使用 Azure 存储之外的存储提供程序。 在 Azure 中运行的大多数函数应用应使用默认的适用于 Durable Functions 的 Azure 存储提供程序。 但是，在决定是否使用备用存储提供程序时，应就重要的成本、可伸缩性和数据管理进行权衡考虑。 本文详细介绍了其中的许多要权衡的方面。
>
> 另请注意，目前无法将数据从一个存储提供程序迁移到另一个存储提供程序。 如果要使用新的存储提供程序，则应创建配置了新存储提供程序的新应用。

我们开发了两个备用 DTFx 存储提供程序，以便与 Durable Functions、Netherite 存储提供程序和 Microsoft SQL Server (MSSQL) 存储提供程序配合使用 。 本文介绍了这三个支持的提供程序，将它们相互比较，并提供了有关如何开始使用它们的基本信息。

## <a name="azure-storage"></a>Azure 存储

Azure 存储是 Durable Functions 的默认存储提供程序。 它使用队列、表和 Blob 来持久保存业务流程和实体状态。 它还使用 Blob 和 Blob 租约来管理分区。 在许多情况下，用于存储 Durable Functions 运行时状态的存储帐户与 Azure Functions (`AzureWebJobsStorage`) 使用的默认存储帐户相同。 但是，也可以使用单独的存储帐户配置 Durable Functions。 Azure 存储提供程序内置于 Durable Functions 扩展中，并且没有任何其他依赖项。

Azure 存储提供程序的主要优势包括：

* 无需设置 - 可以使用通过函数应用设置体验创建的存储帐户。
* 成本最低的无服务器计费模型 - Azure 存储具有完全基于使用量的定价模型（[详细信息](durable-functions-billing.md#azure-storage-transactions)）。
* 最佳工具支持 - Azure 存储提供跨平台的本地仿真，并与 Visual Studio、Visual Studio Code 和 Azure Functions Core Tools 集成。
* 最成熟 - Azure 存储是初始的适用于 Durable Functions 且经过最严格测试的存储后端。

可在 [Azure/durabletask](https://github.com/Azure/durabletask/tree/main/src/DurableTask.AzureStorage) GitHub 存储库中找到 Azure 存储存储提供程序的 DTFx 组件的源代码。

> [!NOTE]
> 使用 Azure 存储提供程序时，需要标准常规用途 Azure 存储帐户。 所有其他存储帐户类型均不受支持。 强烈建议使用旧版 v1 常规用途存储帐户，因为对于 Durable Functions 工作负载而言，较新的 v2 存储帐户可能会贵得多。 若要详细了解 Azure 存储帐户类型，请参阅[存储帐户概述](../../storage/common/storage-account-overview.md)文档。

## <a name="netherite-preview"></a><a name="netherite"></a>Netherite（预览版）

Netherite 存储后端由 [Microsoft Research](https://www.microsoft.com/research) 设计和开发。 它在 [Azure 页 Blob](../../storage/blobs/storage-blob-pageblob-overview.md) 之上使用 [Azure 事件中心](../../event-hubs/event-hubs-about.md)和 [FASTER](https://www.microsoft.com/research/project/faster/) 数据库技术。 与其他提供程序相比，Netherite 的设计支持以更高的吞吐量处理业务流程和实体。 在某些基准方案中，与默认的 Azure 存储提供程序相比，其吞吐量提高了一个数量级以上。

Netherite 存储提供程序的主要优势包括：

* 与其他存储提供程序相比，吞吐量明显更高，且成本更低。
* 支持性价比优化，使你可以根据需要扩展性能。
* 最多支持 32 个数据分区，其中包含事件中心基本和标准 SKU。
* 与用于高吞吐量工作负载的其他提供程序相比，更为经济高效。

请参阅 [Netherite 文档](https://microsoft.github.io/durabletask-netherite)，详细了解 Netherite 存储提供程序的技术细节，包括如何开始使用它。 可在 [microsoft/durabletask-netherite](https://github.com/microsoft/durabletask-netherite) GitHub 存储库中找到 Netherite 存储提供程序的源代码。 有关对 Netherite 存储提供程序的更深入评估，请参阅以下研究论文：[配置了 Durable Functions 和 Netherite 的无服务器工作流](https://arxiv.org/abs/2103.00033)。

> [!NOTE]
> Netherite 一名源自 [Minecraft](https://minecraft.fandom.com/wiki/Netherite) 世界。

## <a name="microsoft-sql-server-mssql-preview"></a><a name="mssql"></a>Microsoft SQL Server (MSSQL)（预览版）

Microsoft SQL Server (MSSQL) 存储提供程序将所有状态持久保存到 Microsoft SQL Server 数据库中。 它与 SQL Server 的本地部署和云托管部署均兼容，包括 [Azure SQL 数据库](../../azure-sql/database/sql-database-paas-overview.md)。

MSSQL 存储提供程序的主要优势包括：

* 支持断开连接的环境 - 使用 SQL Server 安装时不需要 Azure 连接。
* 可跨多个环境和云（包括 Azure 托管的和本地）进行移植。
* 强大的数据一致性，支持备份/还原和故障转移，而不会丢失数据。
* 对自定义数据加密（一种 SQL Server 功能）的本机支持。
* 通过内置的存储过程与现有数据库应用程序集成。

请参阅 [Microsoft SQL 提供程序文档](https://microsoft.github.io/durabletask-mssql)，详细了解 MSSQL 存储提供程序的技术细节，包括如何开始使用它。 可在 [microsoft/durabletask-mssql](https://github.com/microsoft/durabletask-mssql) GitHub 存储库中找到 MSSQL 存储提供程序的源代码。

## <a name="configuring-alternate-storage-providers"></a>配置备用存储提供程序

配置备用存储提供程序通常是一个两个步骤的进程：

1. 将相应的 NuGet 包添加到函数应用（对于使用扩展捆绑包的应用，此要求是临时的）。
1. 更新 host.json 文件以指定要使用哪个存储提供程序。

如果 host.json 中未显式配置任何存储提供程序，则默认启用 Azure 存储提供程序。

### <a name="configuring-the-azure-storage-provider"></a>配置 Azure 存储提供程序

Azure 存储提供程序是默认的存储提供程序，不需要任何显式配置、NuGet 包引用或扩展捆绑包引用。 可以在 `extensions/durableTask/storageProvider` 路径下的[此处](durable-functions-bindings.md#hostjson-settings)找到完整的 host.json 配置选项集。

### <a name="configuring-the-netherite-storage-provider"></a>配置 Netherite 存储提供程序

若要使用 Netherite 存储提供程序，必须首先在 csproj 文件（.NET 应用）或 extensions.proj 文件（JavaScript、Python 和 PowerShell 应用）中添加对 [Microsoft.Azure.DurableTask.Netherite.AzureFunctions](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Netherite.AzureFunctions) NuGet 包的引用 。

> [!NOTE]
> 使用[扩展捆绑包](../functions-bindings-register.md#extension-bundles)的应用尚不支持 Netherite 存储提供程序。

以下 host.json 示例显示了启用 Netherite 存储提供程序所需的最低配置。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "Netherite",
        "storageConnectionName": "AzureWebJobsStorage",
        "eventHubsConnectionName": "EventHubsConnection"
      }
    }
  }
}
```

有关更详细的设置说明，请参阅 [Netherite 入门文档](https://microsoft.github.io/durabletask-netherite/#/?id=getting-started)。

### <a name="configuring-the-mssql-storage-provider"></a>配置 MSSQL 存储提供程序

若要使用 MSSQL 存储提供程序，必须首先在 csproj 文件（.NET 应用）或 extensions.proj 文件（JavaScript、Python 和 PowerShell 应用）中添加对 [Microsoft.DurableTask.SqlServer.AzureFunctions](https://www.nuget.org/packages/Microsoft.DurableTask.SqlServer.AzureFunctions) NuGet 包的引用 。

> [!NOTE]
> 使用[扩展捆绑包](../functions-bindings-register.md#extension-bundles)的应用尚不支持 MSSQL 存储提供程序。

以下示例显示了启用 MSSQL 存储提供程序所需的最低配置。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "mssql",
        "connectionStringName": "SQLDB_Connection"
      }
    }
  }
}
```

有关更详细的设置说明，请参阅 [MSSQL 提供程序的入门文档](https://microsoft.github.io/durabletask-mssql/#/quickstart)。

## <a name="comparing-storage-providers"></a>比较存储提供程序

各种支持的存储提供程序之间有许多需要权衡的重要方面。 下表可用于帮助你了解这些权衡点，并决定哪种存储提供程序最适合你的需求。

| 存储提供程序 | Azure 存储 | Netherite | MSSQL |
|- |-              |-          |-      |
| 官方支持状态 | ✅ 正式发布 (GA) | ⚠ 公共预览版 | ⚠ 公共预览版 |
| 外部依赖关系 | Azure 存储帐户（常规用途 v1） | Azure 事件中心<br/>Azure 存储帐户（常规用途） | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) 或 Azure SQL 数据库 |
| 本地开发和仿真选项 | [Azurite v3.12+](../../storage/common/storage-use-azurite.md)（跨平台）<br/>[Azure 存储仿真器](../../storage/common/storage-use-emulator.md)（仅适用于 Windows） | 内存中仿真（[详细信息](https://microsoft.github.io/durabletask-netherite/#/emulation)） | SQL Server Developer 版（支持 [Windows](/sql/database-engine/install-windows/install-sql-server)、[Linux](/sql/linux/sql-server-linux-setup) 和 [Docker 容器](/sql/linux/sql-server-linux-docker-container-deployment)） |
| 任务中心配置 | 显式 | 显式 | 默认情况下为隐式（[详细信息](https://microsoft.github.io/durabletask-mssql/#/taskhubs)） |
| 最大吞吐量 | 中等 | 很高 | 中等 |
| 最大业务流程/实体横向扩展（节点） | 16 | 32 | 不适用 |
| 最大活动横向扩展（节点） | 不适用 | 32 | 不适用 |
| 消耗计划支持 | ✅ 完全支持 | ❌ 不支持 | ❌ 不支持 |
| 弹性高级计划支持 | ✅ 完全支持 | ⚠ 需要[运行时缩放监视](../functions-networking-options.md#premium-plan-with-virtual-network-triggers) | ⚠ 需要[运行时缩放监视](../functions-networking-options.md#premium-plan-with-virtual-network-triggers) |
| [KEDA 2.0](https://keda.sh/) 缩放支持<br/>（[详细信息](../functions-kubernetes-keda.md)） | ❌ 不支持 | ❌ 不支持 | ✅ 使用 [MSSQL 缩放器](https://keda.sh/docs/scalers/mssql/)支持（[详细信息](https://microsoft.github.io/durabletask-mssql/#/scaling)） |
| 支持[扩展捆绑包](../functions-bindings-register.md#extension-bundles)（建议用于非 .NET 应用） | ✅ 完全支持 | ❌ 不支持 | ❌ 不支持 |
| 性价比可配置？ | ❌ 否 | ✅ 是（事件中心 TU 和 CU） | ✅ 是（SQL vCPU） |
| 断开连接的环境支持 | ❌ 需要 Azure 连接 | ❌ 需要 Azure 连接 | ✅ 完全支持 |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Durable Functions 性能和缩放](durable-functions-perf-and-scale.md)
