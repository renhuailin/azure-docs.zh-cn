---
title: 将日志磁盘迁移到超级磁盘
description: 了解如何将 Azure 虚拟机 (VM) 上的 SQL Server 日志磁盘迁移到 Azure 超级磁盘，以便充分利用高性能和低延迟。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43bafeee90b1995c363ca03d031abc7d648001cf
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108126338"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>将日志磁盘迁移到超级磁盘
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure 超级磁盘为 Azure 虚拟机 (VM) 上的 SQL Server 提供高吞吐量、高 IOPS 和持续低延迟的磁盘存储。 

本文介绍如何将日志磁盘迁移到超级 SSD，以利用超级磁盘提供的性能优势。 

## <a name="back-up-database"></a>备份数据库

完成数据库的[完整备份](backup-restore.md)。 

## <a name="attach-disk"></a>附加磁盘

一旦已用 VM 上的超级磁盘兼容性，就将超级 SSD 附加到虚拟机。 

VM 大小和区域的子集都支持超级磁盘。 在继续之前，请验证 VM 是否位于支持超级磁盘的区域、区和大小。 可以使用 Azure CLI 或 PowerShell 来[确定和验证 VM 大小和区域](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability)。 

### <a name="enable-compatibility"></a>启用兼容性

若要启用兼容性，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中转到虚拟机。 
1. 停止/解除分配虚拟机。 
1. 选择“设置”下的“磁盘”，然后选择“其他设置”。   

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="在 Azure 门户中在“设置”下的“磁盘”选择其他设置":::

1. 选择“是”来“启用超级磁盘兼容性”。  

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="显示“是”选项的屏幕截图。":::

1. 选择“保存”。 



### <a name="attach-disk"></a>附加磁盘

使用 Azure 门户将超级磁盘附加到虚拟机。 有关详细信息，请参阅[附加超级磁盘](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk)。

附加磁盘后，使用 Azure 门户再次启动 VM。 



## <a name="format-disk"></a>格式化磁盘

连接到虚拟机并格式化超级磁盘。  

若要格式化超级磁盘，请执行以下步骤：

1. 使用远程桌面协议 (RDP) 连接到 VM。
1. 使用[磁盘管理](/windows-server/storage/disk-management/overview-of-disk-management)对新附加的超级磁盘进行格式化和分区。 


## <a name="use-disk-for-log"></a>将磁盘用于日志

配置 SQL Server 以使用新的日志驱动器。 可以使用 Transact-SQL (T-SQL) 或 SQL Server Management Studio (SSMS) 来实现此目的。 用于 SQL Server 服务帐户的帐户必须对新的日志文件位置具有完全控制。 

### <a name="configure-permissions"></a>配置权限

1. 验证 SQL Server 使用的服务帐户。 可以通过使用 SQL Server 配置管理器或 Services.msc 来实现此目的。
1. 导航到新磁盘 
1. 创建要用于日志文件的一个文件夹（或多个文件夹）。 
1. 右键单击该文件夹并选择“属性”。
1. 在“安全”选项卡上，对 SQL Server 服务帐户授予完全控制访问权限。 
1. 选择“确定”保存设置。 
1. 对计划包含 SQL 数据的每个根级别的文件夹重复此操作。 

### <a name="use-new-log-drive"></a>使用新的日志驱动器 

在授予权限后，使用 Transact-SQL (T-SQL) 或 SQL Server Management Studio (SSMS) 来分离数据库，并将现有日志文件移动到新位置。

   > [!CAUTION]
   > 分离数据库将使其脱机，关闭连接并回滚正在进行中的任何事务。 在下一次维护时段中继续操作。 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

使用 T-SQL 将现有文件移动到新位置：

1. 在 SQL Server Management Studio 中连接到数据库，然后打开一个 **新的查询** 窗口。 
1. 获取现有文件和位置：

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. 分离数据库： 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. 使用文件资源管理器将日志文件移动到超级磁盘上的新位置。 

1. 附加数据库，指定新的文件位置： 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

此时，数据库会联机，并将日志置于新位置。 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

使用 SSMS 将现有文件移动到新位置：

1. 在 SQL Server Management Studio (SSMS) 中连接到数据库。 
1. 右键单击数据库，选择“属性”，然后选择“文件”。  
1. 记下现有文件的路径。 
1. 选择“确定”关闭对话框  。 
1. 右键单击该数据库，然后选择“任务” > “分离”。  
1. 按照向导分离数据库。 
1. 使用文件资源管理器将日志文件手动移动到新位置。
1. 在 SQL Server Management Studio 中附加数据库
   1. 右键单击“对象资源管理器”中的“数据库”，然后选择“附加数据库”。   
   1. 使用该对话框，将每个文件（包括日志文件）添加到新位置。 
   1. 选择“确定”附加数据库。 

此时，数据库会联机，并将日志置于新位置。

---


## <a name="next-steps"></a>后续步骤

查看[性能最佳实践](./performance-guidelines-best-practices-checklist.md)以了解提高性能的其他设置。 

有关 Azure 虚拟机上的 SQL Server 的概述，请参阅以下文章：

- [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Linux VM 上的 SQL Server 概述](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)