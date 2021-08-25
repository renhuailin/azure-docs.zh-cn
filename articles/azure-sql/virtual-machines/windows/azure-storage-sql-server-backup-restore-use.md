---
title: 如何使用 Azure 存储执行 SQL Server 备份和还原 | Microsoft Docs
description: 了解如何将 SQL Server 备份到 Azure 存储。 说明将 SQL 数据库备份到 Azure 存储的好处。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: 52e9ba6dcf0a8f6f987206cac2881ada098a8bcc
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635182"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>将 Azure 存储用于 SQL Server 备份和还原
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

从 SQL Server 2012 SP1 CU2 开始，现在可以将备份 SQL Server 数据库直接写入到 Azure Blob 存储中。 请使用此功能来执行备份到 Azure Blob 存储以及从中还原的操作。 备份到云具有以下方面的优点：可用性、无限制的异地复制场外存储，以及将数据迁移到云并从云中迁移数据的便利性。 可以通过使用 Transact-SQL 或 SMO 来发出 `BACKUP` 或 `RESTORE` 语句。

## <a name="overview"></a>概述
SQL Server 2016 引入了新功能；可以使用[文件快照备份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)来执行几乎实时的备份和极其快速的还原。

本主题说明可以选择使用 Azure 存储执行 SQL Server 备份的原因，并介绍了相关的组件。 可以使用本文结尾处提供的资源来访问演练和其他信息，以开始将此服务与 SQL Server 备份共同使用。

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>使用 Azure Blob 存储执行 SQL Server 备份的优点
备份 SQL Server 时，会面临多项挑战。 这些挑战包括存储管理、存储故障产生的风险、访问场外存储以及硬件配置。 这些挑战当中许多都是通过使用 Azure Blob 存储进行 SQL Server 备份来解决的。 请考虑以下好处：

* **易用性**：在 Azure Blob 中存储备份非常方便、灵活且可轻松访问场外存储。 为 SQL Server 备份创建场外存储就像修改现有脚本/作业以使用 **BACKUP TO URL** 语法一样简单。 场外存储通常应当远离生产数据库位置，以防止某个灾难可能同时影响场外和生产数据库位置。 通过选择[异地复制 Azure blob](../../../storage/common/storage-redundancy.md)，可以在发生可能影响整个区域的灾难时进一步加强保护。
* **备份存档**：对备份进行存档时，Azure Blob 存储提供可替代常用磁带存储方式的更好方式。 选择磁带存储时可能需要将数据实际运输到场外设施，并且需要采取一些介质保护措施。 在 Azure Blob 存储中存储备份可提供即时、具有高可用性且持久的存档方式。
* **受管理的硬件**：使用 Azure 服务没有硬件管理开销。 Azure 服务可管理硬件并提供地域异地复制和硬件故障防护。
* **无限制的存储**：通过启用直接备份到 Azure Blob，可以访问几乎无限的存储。 或者，还可以选择备份到 Azure 虚拟机磁盘，所受的限制取决于计算机的大小。 只能将有限数量的磁盘附加到用于备份的 Azure 虚拟机。 对特大实例的限制为 16 个磁盘；对较小实例的磁盘限制数更少。
* **备份可用性**：存储在 Azure Blob 中的备份可随时从任何位置使用，并可供轻松访问以还原到 SQL Server 实例，而无需进行数据库附加/分离，或者无需下载和附加 VHD。
* **成本**：只需要为使用的服务付费。 作为场外和备份存档方式可能更加划算。 有关详细信息，请参阅 [Azure 定价计算器](https://go.microsoft.com/fwlink/?LinkId=277060 "定价计算器")和 [Azure 定价文章](https://go.microsoft.com/fwlink/?LinkId=277059 "定价文章")。
* **存储快照**：如果数据库文件存储在 Azure Blob 中并且使用的是 SQL Server 2016，则可以使用 [文件快照备份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)来执行几乎实时的备份和极其快速的还原。

有关更多详细信息，请参阅[使用 Azure Blob 存储执行 SQL Server 备份和还原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)。

接下来两节介绍 Azure Blob 存储，包括必要的 SQL Server 组件。 若要从 Azure Blob 存储成功进行备份和还原，一定要了解这些组件及其交互方式。

## <a name="azure-blob-storage-components"></a>Azure Blob 存储组件
备份到 Azure Blob 存储时，会使用以下 Azure 组件。

| 组件 | 说明 |
| --- | --- |
| **存储帐户** |存储帐户是所有存储服务的起点。 若要访问 Azure Blob 存储，请先创建一个 Azure 存储帐户。 SQL Server 对于使用的存储冗余类型不可知。 每种存储冗余（LRS、ZRS、GRS、RA-GRS、RA-GZRS 等）都支持备份到页 blob 和块 blob。 有关 Azure Blob 存储的详细信息，请参阅[如何使用 Azure Blob 存储](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)。 |
| **容器** |容器提供一组 Blob 集，并且可存储无限数量的 Blob。 若要将 SQL Server 备份写入到 Azure Blob 存储，必须创建至少一个根容器。 |
| **Blob** |任何类型和大小的文件。 可以使用以下 URL 格式来对 Blob 寻址：`https://<storageaccount>.blob.core.windows.net/<container>/<blob>`。 有关页 Blob 的详细信息，请参阅[了解块 Blob 和页 Blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) |

## <a name="sql-server-components"></a>SQL Server 组件
备份到 Azure Blob 存储时，会使用以下 SQL Server 组件。

| 组件 | 说明 |
| --- | --- |
| **URL** |URL 指定到唯一备份文件的统一资源标识符 (URI)。 URL 提供 SQL Server 备份文件的位置和名称。 URL 必须指向实际 blob，而不是仅指向容器。 如果 Blob 不存在，Azure 会创建它。 如果指定现有的 Blob，那么，除非指定 `WITH FORMAT` 选项，否则 backup 命令会失败。 以下是在 BACKUP 命令中指定的 URL 示例：`https://<storageaccount>.blob.core.windows.net/<container>/<FILENAME.bak>` 。<br><br> HTTPS 不是必需的，但建议使用它。 |
| **凭据** |连接到 Azure Blob 存储并通过其进行身份验证所需的信息将存储为凭据。 为了使 SQL Server 将备份写入 Azure Blob 或从中进行还原，必须创建 SQL Server 凭据。 有关详细信息，请参阅 [SQL Server 凭据](/sql/t-sql/statements/create-credential-transact-sql)。 |

> [!NOTE]
> SQL Server 2016 已更新以支持块 blob。 有关详细信息，请参阅[教程：将 Microsoft Azure Blob 存储用于 SQL Server 2016 数据库](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)。
> 

## <a name="next-steps"></a>后续步骤

1. 创建 Azure 帐户（如果还没有帐户）。 如果正在评估 Azure，请考虑使用[免费试用版](https://azure.microsoft.com/free/)。
2. 接着，完成以下教程之一，这些教程会引导创建存储帐户以及执行还原。
   
   * SQL Server 2014：[教程：SQL Server 2014 备份和还原到 Microsoft Azure Blob 存储](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-backup-to-url)。
   * SQL Server 2016：[教程：将 Microsoft Azure Blob 存储用于 SQL Server 2016 数据库](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)
3. 查看其他文档，从[使用 Microsoft Azure Blob 存储进行 SQL Server 备份和还原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)开始。

如果有任何问题，请查看 [SQL Server 备份到 URL 最佳实践和故障排除](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)主题。

有关其他 SQL Server 备份和还原选项，请参阅 [Azure 虚拟机上的 SQL Server 的备份和还原](backup-restore.md)。
