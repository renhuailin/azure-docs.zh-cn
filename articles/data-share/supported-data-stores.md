---
title: Azure Data Share 中支持的数据存储
description: 了解支持在 Azure Data Share 中使用的数据存储。
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 605405965e4c6bc17321401245866f9b21f3a988
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760054"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Data Share 中支持的数据存储

Azure Data Share 提供开放且灵活的数据共享，包括从不同数据存储共享以及共享到不同数据存储的功能。 数据提供程序可以从一种类型的数据存储共享数据，数据使用者可以选择一个数据存储来接收数据。 

本文介绍 Azure Data Share 支持的丰富的 Azure 数据存储集。 你还将了解数据提供程序和数据使用者如何组合不同的数据存储。 

## <a name="supported-data-stores"></a>支持的数据存储 

下表说明了 Azure Data Share 支持的数据存储。 

| 数据存储 | 基于完整快照进行共享 | 基于增量快照进行共享 | 就地共享 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 存储 |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL 数据库 |✓ | | |
| Azure Synapse Analytics（以前称为 Azure SQL 数据仓库） |✓ | | |
| Azure Synapse Analytics（工作区）专用 SQL 池 |✓ | | |
| Azure 数据资源管理器 | | |✓ |

## <a name="data-store-support-matrix"></a>数据存储支持矩阵

Azure Data Share 允许数据使用者选择一个数据存储来接受数据。 例如，可以将从 Azure SQL 数据库共享的数据接收到 Azure Data Lake Storage Gen2、Azure SQL 数据库或 Azure Synapse Analytics 中。 客户设置接收数据共享时，可以选择接收数据的格式。 

下表说明了数据使用者在接受和配置数据共享时可以选择的组合和选项。 有关详细信息，请参阅[配置数据集映射](how-to-configure-mapping.md)。

| 数据存储 | Blob 存储 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL 数据库 | Synapse Analytics（以前称为 SQL 数据仓库） | Synapse Analytics（工作区）专用 SQL 池 | 数据资源管理器
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob 存储 | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL 数据库 | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics（以前称为 SQL 数据仓库） | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics（工作区）专用 SQL 池 | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| 数据资源管理器 ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>从存储帐户共享
Azure Data Share 支持从 Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2 共享文件、文件夹和文件系统。 它还支持从 Azure Blob 存储共享 blob、文件夹和容器。 可以共享块 Blob、追加 Blob 或页 Blob，并将它们作为块 Blob 接收。

在基于快照的共享中共享文件系统、容器或文件夹时，数据使用者可以选择创建共享数据的完整副本。 或者，他们可以使用增量快照功能仅复制新文件或更新的文件。 

增量快照基于文件的上次修改时间。 与接收的数据中的文件同名的现有文件会在快照中被覆盖。 不会在目标上删除从源删除的文件。 

有关详细信息，请参阅[从 Azure Blob 存储和 Azure Data Lake Storage 共享和接收数据](how-to-share-from-storage.md)。

## <a name="share-from-a-sql-based-source"></a>从基于 SQL 的源共享
Azure Data Share 支持从 Azure SQL 数据库和 Azure Synapse Analytics（以前称为 Azure SQL 数据仓库）共享表和视图。 它支持从 Azure Synapse Analytics（工作区）专用 SQL 池共享表。 当前不支持从 Azure Synapse Analytics（工作区）无服务器 SQL 池共享。 

数据使用者可以选择将数据作为 CSV 文件或 parquet 文件接受到 Azure Data Lake Storage Gen2 或 Azure Blob 存储中。 还可以将数据作为表接受到 Azure SQL 数据库和 Azure Synapse Analytics 中。

当使用者将数据接受到 Azure Data Lake Storage Gen2 或 Azure Blob 存储中时，如果文件已存在，则完整快照将覆盖目标文件的内容。 在将数据接收到表中并且目标表尚不存在时，Azure Data Share 会使用源架构创建 SQL 表。 如果目标表已存在并且具有相同的名称，则会将其删除并使用最新的完整快照进行覆盖。 目前不支持增量快照。

有关详细信息，请参阅[从 Azure SQL 数据库和 Azure Synapse Analytics 共享和接收数据](how-to-share-from-sql.md)。

## <a name="share-from-data-explorer"></a>从数据资源管理器进行共享
Azure Data Share 支持从 Azure 数据资源管理器群集就地共享数据库的功能。 数据提供程序可以在数据库级别或群集级别共享。 如果正在使用 Data Share API 来共享数据，则还可以共享特定表。  

当数据在数据库级别共享时，数据使用者只能访问该数据提供程序共享的数据库。 当提供程序在群集级别共享数据时，数据使用者可以从提供程序的群集访问所有数据库，包括数据提供程序创建的任何未来数据库。

若要访问共享数据库，数据使用者需要其自己的 Azure 数据资源管理器群集。 其群集必须与数据提供程序的 Azure 数据资源管理器群集位于同一 Azure 数据中心中。 

建立共享关系时，Azure Data Share 会在提供程序的群集和使用者的群集之间创建符号链接。 几分钟后，目标群集上会显示使用批处理模式引入源群集的数据。

有关详细信息，请参阅[从 Azure 数据资源管理器共享和接收数据](/azure/data-explorer/data-share)。 

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。
