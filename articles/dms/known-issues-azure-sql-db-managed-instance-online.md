---
title: 联机迁移到 Azure SQL 托管实例时存在的已知问题和限制
description: 了解联机迁移到 Azure SQL 托管实例时存在的已知问题/迁移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98695521"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>联机迁移到 Azure SQL 托管实例时存在的已知问题/迁移限制

下面介绍了从 SQL Server 联机迁移到 Azure SQL 托管实例时的已知问题和限制。

> [!IMPORTANT]
> 将 SQL Server 联机迁移到 Azure SQL 数据库时，不支持迁移 SQL_variant 数据类型。

## <a name="backup-requirements"></a>备份要求

- **使用校验和的备份**

    Azure 数据库迁移服务使用备份和还原方法将本地数据库迁移到 SQL 托管实例。 Azure 数据库迁移服务仅支持使用校验和创建的备份。

    [在备份或还原期间启用或禁用备份校验和 (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)。

    > [!NOTE]
    > 如果通过压缩进行数据库备份，则除非明确禁用，否则校验和是默认行为。

    脱机迁移时，如果选择“我将让 Azure 数据库迁移服务...”，则 Azure 数据库迁移服务进行数据库备份时会启用校验和选项。

- **备份介质**

    确保将每个备份都置于单独的备份介质（备份文件）上。 Azure 数据库迁移服务不支持追加到单个备份文件的备份。 将完整备份和日志备份置于单独的备份文件中。

## <a name="data-and-log-file-layout"></a>数据和日志文件布局

- **日志文件数**

    Azure 数据库迁移服务不支持包含多个日志文件的数据库。 如果有多个日志文件，请将其收缩并重组到单个事务日志文件中。 由于无法远程操作非空日志文件，因此需要先备份日志文件。

## <a name="sql-server-features"></a>SQL Server 功能

- **FileStream/Filetable**

    SQL 托管实例当前不支持 FileStream 和 FileTable。 对于依赖这些功能的工作负载，建议选择 Azure VM 上运行的 SQL Server 作为 Azure 目标。

- **内存中表**

    内存中 OLTP 在 SQL 托管实例高级层和业务关键层中可用；常规用途层不支持内存中 OLTP。

## <a name="migration-resets"></a>迁移重置

- **部署**

    SQL 托管实例是一种具有自动修补和版本更新功能的 PaaS 服务。 迁移 SQL 托管实例期间，非关键更新最多保留 36 小时。 之后（对于关键更新），如果迁移中断，此过程将重置为完全还原状态。

    仅在完整备份还原完成且与所有日志备份一致后才能调用迁移切换。 如果生产迁移切换受到影响，请联系 [AZURE DMS 反馈别名](mailto:dmsfeedback@microsoft.com)。

## <a name="smb-file-share-connectivity"></a>SMB 文件共享连接

连接到 SMB 文件共享时出现的问题可能是由权限问题导致的。 

若要测试 SMB 文件共享连接，请执行以下步骤： 

1. 将备份保存到 SMB 文件共享。 
1. 验证 Azure 数据库迁移服务的子网与源 SQL Server 之间的网络连接。 执行此操作的最简单方法是将 SQL Server 虚拟机部署到 DMS 子网，并使用 SQL Server Management Studio 连接到源 SQL Server。 
1. 从文件共享上的备份中还原源 SQL Server 上的标头： 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

如果无法连接到该文件共享，请按照以下步骤配置权限： 

1. 使用文件资源管理器导航到该文件共享。 
1. 右键单击该文件共享并选择“属性”。 
1. 选择“共享”选项卡，然后选择“高级共享”。  
1. 添加用于迁移的 Windows 帐户，并为其分配“完全控制”访问权限。 
1. 添加 SQL Server 服务帐户，并为其分配“完全控制”访问权限。 请在 SQL Server 配置管理器中查找 SQL Server 服务帐户（如果你不确定要使用哪个帐户）。 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="对用于迁移的 Windows 帐户和 SQL Server 服务帐户授予“完全控制”权限。":::

