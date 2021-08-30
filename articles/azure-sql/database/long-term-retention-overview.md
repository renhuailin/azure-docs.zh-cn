---
title: 长期备份保留
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 了解 Azure SQL 数据库和 Azure SQL 托管实例如何通过长期保留策略支持将完整数据库备份存储长达 10 年。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 07/13/2021
ms.openlocfilehash: 13e522b45673723a33b8739ccefafa4a8410910a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751325"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>长期保留 - Azure SQL 数据库和 Azure SQL 托管实例

出于法规要求、符合性或其他商业目的，许多应用程序要求保留 Azure SQL 数据库和 Azure SQL 托管实例的[自动备份](automated-backups-overview.md)功能提供的过去 7-35 天的数据库备份。 通过使用长期保留 (LTR) 功能，可以将指定的 SQL 数据库和 SQL 托管实例完整备份存储在[配置了冗余](automated-backups-overview.md#backup-storage-redundancy)的 Azure Blob 存储中长达 10 年。 然后，可将 LTR 备份还原为新数据库。

可为 Azure SQL 数据库启用长期保留，且此功能对于 Azure SQL 托管实例以公共预览版形式提供。 本文提供长期保留的概念概述。 要配置长期保留，请参阅[配置 Azure SQL 数据库 LTR](long-term-backup-retention-configure.md) 和[配置 Azure SQL 托管实例 LTR](../managed-instance/long-term-backup-retention-configure.md)。 

> [!NOTE]
> 可以使用 SQL 代理作业来安排[仅复制数据库备份](/sql/relational-databases/backup-restore/copy-only-backups-sql-server)作为超过 35 天的 LTR 的替代方案。

> [!IMPORTANT]
> 托管实例的长期保留目前仅在 Azure 公共区域以公共预览版形式提供。 


## <a name="how-long-term-retention-works"></a>长期保留的工作原理
     
长期备份保留 (LTR) 利用[自动创建](automated-backups-overview.md)的完整数据库备份来启用时间点还原 (PITR)。 如果配置了 LTR 策略，则会将这些备份复制到不同的 blob 进行长期存储。 复制是后台作业，不会对数据库工作负载造成性能影响。 SQL 数据库中每个数据库的 LTR 策略还可以指定创建 LTR 备份的频率。

若要实现 LTR，可以使用以下四个参数的组合定义策略：每周备份保留 (W)、每月备份保留 (M)、每年备份保留 (Y) 和年中的周 (WeekOfYear)。 如果指定 W，则每周会将一个备份复制到长期存储。 如果指定 M，则每月的第一个备份会复制到长期存储。 如果指定 Y，则会在 WeekOfYear 指定的周将一个备份复制到长期存储。 如果配置策略时指定的 WeekOfYear 在过去，则会在下一年创建第一个 LTR 备份。 每个备份都将按照创建 LTR 备份时配置的策略参数保留在长期存储中。

> [!NOTE]
> 对 LTR 策略所做的任何更改只应用到将来的备份。 例如，如果修改了每周备份保留 (W)、每月备份保留 (M) 或每年备份保留 (Y)，则新的保留设置仅应用于新备份 不会修改现有备份的保留期。 如果目的是在保留期到期之前删除旧的 LTR 备份，则需[手动删除备份](./long-term-backup-retention-configure.md#delete-ltr-backups)。
> 

LTR 策略示例：

-  W=0、M=0、Y=5、WeekOfYear=3

   每年的第 3 个完整备份将保留 5 年。
   
- W=0、M=3、Y=0

   每月的第一个完整备份将保留 3 个月。

- W=12、M=0、Y=0

   每个每周完整备份将保留 12 周。

- W=6、M=12、Y=10、WeekOfYear=16

   每个每周完整备份将保留 6 周。 每月的第一个完整备份例外，该备份将保留 12 个月。 每年的第 16 周创建的完整备份例外，该备份将保留 10 年。 

下表说明了以下策略的长期备份的节奏和到期：

W=12 周（84 天）、M=12 个月（365 天）、Y=10 年（3650 天）、WeekOfYear=15（4 月 15 日后的周）

   ![ltr 示例](./media/long-term-retention-overview/ltr-example.png)


如果修改以上策略并设置 W=0（无每周备份），则备份副本的节奏将更改，如上表中突出显示的日期所示。 保留这些备份所需的存储量将相应减少。 

> [!IMPORTANT]
> 单个 LTR 备份的时间由 Azure 控制。 无法手动创建 LTR 备份，或手动控制备份创建时间。 配置 LTR 策略后，最多可能需要 7 天才能在可用备份列表中显示第一个 LTR 备份。  


## <a name="geo-replication-and-long-term-backup-retention"></a>异地复制和长期备份保留

如果使用活动异地复制或故障转移组作为业务连续性解决方案，应准备好最终故障转移，并在辅助数据库中配置相同的 LTR 策略。 LTR 存储成本不会增大，因为备份不是从辅助数据库生成的。 仅当辅助数据库变为主数据库时，才会创建备份。 这样可以确保在触发故障转移以及在主数据库转移到次要区域时，不间断地生成 LTR 备份。 

> [!NOTE]
> 在发生导致故障转移的服务中断问题后恢复原始的主数据库时，该数据库将变成新的辅助数据库。 因此，在该数据库重新变成主数据库之前，备份创建操作不会恢复，并且现有的 LTR 策略不会生效。 


## <a name="configure-long-term-backup-retention"></a>配置长期备份保留

可使用 Azure 门户及适用于 Azure SQL 数据库和 Azure SQL 托管实例的 PowerShell 来配置长期备份保留。 若要从 LTR 存储还原数据库，可以根据时间戳选择一个特定备份。 数据库可以还原到原始数据库所在的订阅中的任何现有服务器或托管实例。

若要了解如何使用 Azure 门户或 PowerShell 配置长期保留或从 SQL 数据库的备份还原数据库，请参阅[管理 Azure SQL 数据库长期备份保留](long-term-backup-retention-configure.md)。

若要了解如何使用 Azure 门户或 PowerShell 配置长期保留或从 SQL 托管实例的备份还原数据库，请参阅[管理 Azure SQL 托管实例长期备份保留](../managed-instance/long-term-backup-retention-configure.md)。

若要从 LTR 存储还原数据库，可以根据时间戳选择一个特定备份。 数据库可以还原到原始数据库所在的订阅中的任何现有服务器。 若要了解如何使用 Azure 门户或 PowerShell 从 LTR 备份还原数据库，请参阅[管理 Azure SQL 数据库长期备份保留](long-term-backup-retention-configure.md)。 

## <a name="next-steps"></a>后续步骤

数据库备份可保护数据免遭意外损坏或删除，因此数据库备份是任何业务连续性和灾难恢复策略不可或缺的组成部分。 

- 若要了解其他 SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
- 若要了解服务生成的自动备份，请参阅 [自动备份](../database/automated-backups-overview.md)
