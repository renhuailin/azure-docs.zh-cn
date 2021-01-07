---
title: Azure Data Share 中支持的数据存储
description: 了解支持在 Azure 数据共享中使用的数据存储。
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 852c44f5edc5c0b0f5f655f63ab040927bd9bc7b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963673"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Data Share 中支持的数据存储

Azure 数据共享提供了开放式和灵活的数据共享，包括从和到不同数据存储区的能力。 数据访问接口可以共享一种类型的数据存储中的数据，数据使用者可以选择数据存储来接收数据。 

在本文中，你将了解 Azure 数据共享支持的丰富 Azure 数据存储集。 你还将了解数据访问接口和数据使用者如何合并不同的数据存储区。 

## <a name="supported-data-stores"></a>支持的数据存储 

下表介绍了 Azure 数据共享支持的数据存储。 

| 数据存储 | 基于完整快照共享 | 基于增量快照共享 | 就地共享 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 存储 |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL 数据库 |✓ | | |
| Azure Synapse Analytics（以前称为 Azure SQL 数据仓库） |✓ | | |
| Azure Synapse Analytics (工作区) 专用 SQL 池 |✓ | | |
| Azure 数据资源管理器 | | |✓ |

## <a name="data-store-support-matrix"></a>数据存储支持矩阵

Azure 数据共享允许数据使用者选择数据存储来接受数据。 例如，可将 Azure SQL 数据库中共享的数据接收到 Azure Data Lake Storage Gen2、Azure SQL 数据库或 Azure Synapse Analytics 中。 当客户设置接收数据共享时，他们可以选择接收数据的格式。 

下表说明了数据使用者在接受和配置数据共享时可以选择的组合和选项。 有关详细信息，请参阅 [配置数据集映射](how-to-configure-mapping.md)。

| 数据存储 | Blob 存储 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL 数据库 | Synapse Analytics (以前的 SQL 数据仓库)  | Synapse Analytics (工作区) 专用 SQL 池 | 数据资源管理器
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob 存储 | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL 数据库 | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (以前的 SQL 数据仓库)  | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (工作区) 专用 SQL 池 | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| 数据资源管理器 ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>从存储帐户共享
Azure 数据共享支持从 Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2 共享文件、文件夹和文件系统。 它还支持从 Azure Blob 存储共享 blob、文件夹和容器。 目前仅支持块 blob。 

在基于快照的共享中共享文件系统、容器或文件夹时，数据使用者可以选择创建共享数据的完整副本。 或者，他们可以使用增量快照功能只复制新文件或更新的文件。 

增量快照基于文件的上次修改时间。 与接收的数据中的文件同名的现有文件会在快照中被覆盖。 不会在目标上删除从源删除的文件。 

有关详细信息，请参阅 [从 Azure Blob 存储共享和接收数据和 Azure Data Lake Storage](how-to-share-from-storage.md)。

## <a name="share-from-a-sql-based-source"></a>从基于 SQL 的源共享
Azure 数据共享支持在以前的 Azure SQL 数据仓库)  (Azure SQL 数据库和 Azure Synapse 分析中的表和视图。 它支持从 Azure Synapse Analytics (工作区) 专用 SQL 池共享表。 当前不支持从 Azure Synapse Analytics (工作区共享) 无服务器 SQL 池。 

数据使用者可以选择将数据作为 CSV 文件或 parquet 文件接受 Azure Data Lake Storage Gen2 或 Azure Blob 存储。 它们还可以在 Azure SQL 数据库和 Azure Synapse 分析中以表的形式接受数据。

当使用者在 Azure Data Lake Storage Gen2 或 Azure Blob 存储中接受数据时，如果文件已存在，则完全快照将覆盖目标文件的内容。 在将数据接收到表并且目标表尚不存在时，Azure 数据共享使用源架构创建 SQL 表。 如果目标表已存在并且具有相同的名称，则会将其删除并使用最新的完整快照进行覆盖。 当前不支持增量快照。

有关详细信息，请参阅 [在 AZURE SQL 数据库和 Azure Synapse Analytics 中共享和接收数据](how-to-share-from-sql.md)。

## <a name="share-from-data-explorer"></a>从数据资源管理器共享
Azure 数据共享支持从 Azure 数据资源管理器群集共享就地数据库。 数据访问接口可以在数据库级别或群集级别共享。 

当数据在数据库级别共享时，数据使用者只能访问该数据访问接口共享的数据库。 当提供程序在群集级别共享数据时，数据使用者可以从提供程序的群集访问所有数据库，包括数据提供程序创建的未来任何数据库。

若要访问共享数据库，数据使用者需要其自己的 Azure 数据资源管理器群集。 其群集必须与数据提供商的 Azure 数据资源管理器群集位于同一 Azure 数据中心内。 

建立共享关系时，Azure 数据共享会在提供程序的群集和使用者的群集之间创建符号链接。 使用批处理模式在源群集中引入的数据会在几分钟内显示在目标群集上。

有关详细信息，请参阅 [从 Azure 数据资源管理器中共享和接收数据](/azure/data-explorer/data-share)。 

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读 [共享数据](share-your-data.md) 教程。
