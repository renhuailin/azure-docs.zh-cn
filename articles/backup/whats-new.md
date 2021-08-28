---
title: Azure 备份中的新增功能
description: 了解 Azure 备份中的新功能。
ms.topic: conceptual
ms.date: 08/05/2021
ms.openlocfilehash: f2c4e3fa492a4dfe3c42ffb615442d3c6c449404
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122184084"
---
# <a name="whats-new-in-azure-backup"></a>Azure 备份中的新增功能

Azure 备份会持续改进并发布用于增强 Azure 中的数据保护的新功能。 这些新功能可将数据保护扩展到新的工作负载类型，增强安全性，并提高备份数据的可用性。 它们还添加了新的管理、监控和自动化功能。

可将本页加入书签或者[在此处订阅更新](https://azure.microsoft.com/updates/?query=backup)来详细了解新版本。

## <a name="updates-summary"></a>更新摘要

- 2021 年 7 月
  - [Azure 备份针对 Azure VM 中的 SQL Server 的存档层支持现已正式发布](#archive-tier-support-for-sql-server-in-azure-vm-for-azure-backup-is-now-generally-available)
- 2021 年 5 月
  - [Azure Blob 的备份现已推出正式版](#backup-for-azure-blobs-is-now-generally-available)
- 2021 年 4 月
  - [对 Azure 备份使用客户管理的密钥加密的增强功能（预览版）](#enhancements-to-encryption-using-customer-managed-keys-for-azure-backup-in-preview)
- 2021 年 3 月
  - [Azure 磁盘备份现已推出正式版](#azure-disk-backup-is-now-generally-available)
  - [备份中心现已推出正式版](#backup-center-is-now-generally-available)
  - [Azure 备份的存档层支持（预览版）](#archive-tier-support-for-azure-backup-in-preview)
- 2021 年 2 月
  - [Azure Blob 备份（预览版）](#backup-for-azure-blobs-in-preview)
- 2021 年 1 月
  - [Azure 磁盘备份（预览版）](#azure-disk-backup-in-preview)
  - [使用客户管理的密钥进行静态加密（正式版）](#encryption-at-rest-using-customer-managed-keys)
- 2020 年 11 月
  - [用于 Azure 文件共享 (AFS) 备份的 Azure 资源管理器模板](#azure-resource-manager-template-for-afs-backup)
  - [Azure VM 上的 SAP HANA 数据库的增量备份（预览版）](#incremental-backups-for-sap-hana-databases-in-preview)
- 2020 年 9 月
  - [备份中心（预览版）](#backup-center-in-preview)
  - [备份 Azure Database for PostgreSQL（预览版）](#backup-azure-database-for-postgresql-in-preview)
  - [选择性磁盘备份和还原](#selective-disk-backup-and-restore)
  - [Azure VM 上的 SQL Server 和 SAP HANA 数据库的跨区域还原（预览版）](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [支持备份最多包含 32 个磁盘的 VM（正式版）](#support-for-backup-of-vms-with-up-to-32-disks)
  - [简化了 Azure VM 中的 SQL 的备份配置体验](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [备份 RHEL Azure 虚拟机中的 SAP HANA（预览版）](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [备份数据的区域冗余存储 (ZRS)（预览版）](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Azure VM 中 SQL Server 和 SAP HANA 工作负载的软删除](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="archive-tier-support-for-sql-server-in-azure-vm-for-azure-backup-is-now-generally-available"></a>Azure 备份针对 Azure VM 中的 SQL Server 的存档层支持现已正式发布

Azure 备份允许你将针对 Azure 虚拟机和 Azure 虚拟机中的 SQL Server 的长期保留点移到低成本存档层。 你还可以从保管库存档层中的恢复点进行还原。

除了移动恢复点的功能：

- Azure 备份还提供为 Azure 虚拟机备份移动一组特定恢复点的建议，以确保节省成本。
- 你还可以使用示例脚本一次性移动特定备份项的所有恢复点。
- 你还可以在保管库仪表板上查看存档存储使用情况。

有关详细信息，请参阅[存档层支持](./archive-tier-support.md)。

## <a name="backup-for-azure-blobs-is-now-generally-available"></a>Azure Blob 的备份现已推出正式版

Azure Blob 的操作备份是一种托管的数据保护解决方案，它使你能够保护块 blob 数据免受各种数据丢失情况的影响，如 Blob 损坏、Blob 删除和存储帐户的意外删除。

作为一种操作性备份解决方案，备份数据存储在源存储帐户本地，并且可以从选定的时间点恢复，从而为你提供一种简单且经济高效的方法来保护 Blob 数据。 为此，该解决方案使用 Blob 存储提供的 Blob 时间点还原功能。

Blob 的操作备份与 Azure 备份管理工具（包括备份中心）集成，可帮助你有效地大规模管理 Blob 数据的保护。 除了以前提供的功能之外，现在还可以使用存储帐户的“数据保护”视图配置和管理 Blob 的操作备份，也可以[通过 PowerShell](backup-blobs-storage-account-ps.md) 来实现。 此外，备份现在为你提供了增强的体验，可以更好地管理配置操作备份所需的角色分配。

有关详细信息，请参阅 [Azure Blob 操作备份概述](blob-backup-overview.md)。

## <a name="azure-disk-backup-is-now-generally-available"></a>Azure 磁盘备份现已推出正式版

Azure 备份通过自动定期创建快照，并使用备份策略在配置的时间段内保留快照，为 Azure 托管磁盘提供快照生命周期管理。

有关详细信息，请参阅 [Azure 磁盘备份概述](disk-backup-overview.md)。

## <a name="backup-center-is-now-generally-available"></a>备份中心现已推出正式版

备份中心便于你从一个中心控制台发现、调控、监视、操作和优化备份管理，从而大规模简化了数据保护管理。

有关详细信息，请参阅[备份中心概述](backup-center-overview.md)。

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Azure 备份的存档层支持（预览版）

使用 Azure 备份，现在可以通过为 Azure 虚拟机及其中的 SQL Server 提供的存档层，降低长期保留备份的成本。

有关详细信息，请参阅[存档层支持（预览版）](archive-tier-support.md)。

## <a name="backup-for-azure-blobs-in-preview"></a>Azure Blob 备份（预览版）

Blob 的操作备份是一种托管的本地数据保护解决方案，它使你能够保护块 Blob 免受各种数据丢失情况的影响，如损坏、Blob 删除和意外存储帐户删除。 数据在本地存储于源存储帐户本身中，并可在需要时恢复到选定时间点。 因此，它提供了一种保护 Blob 的简单、安全且经济高效的方法。

Blob 的操作备份与备份中心集成，还包括其他备份管理功能，可提供单一虚拟管理平台，帮助你大规模管理、监视、操作和分析备份。

有关详细信息，请参阅 [Azure Blob 操作备份概述（预览版）](blob-backup-overview.md)。

## <a name="azure-disk-backup-in-preview"></a>Azure 磁盘备份（预览版）

Azure 磁盘备份提供了一个统包式解决方案，通过使用备份策略自动定期创建快照并在配置的时间段内保留快照，为 [Azure 托管磁盘](../virtual-machines/managed-disks-overview.md)提供快照生命周期管理。 无需对基础结构投入任何成本，无需编写任何自定义脚本，无需产生任何管理开销，就能管理磁盘快照。 它是一个使用[增量快照](../virtual-machines/disks-incremental-snapshots.md)创建托管磁盘时间点备份的崩溃一致性备份解决方案，并支持每日创建多个备份。 它也是一个无代理解决方案，不会影响生产应用程序的性能。 它支持备份和还原 OS 磁盘与数据磁盘（包括共享磁盘），不管这些磁盘当前是否已附加到正在运行的 Azure 虚拟机。

有关详细信息，请参阅 [Azure 磁盘备份（预览版）](disk-backup-overview.md)。

## <a name="encryption-at-rest-using-customer-managed-keys"></a>使用客户管理的密钥进行静态加密

对使用客户管理的密钥进行静态加密的支持现已推出正式版。 你可以借助此功能，使用已存储在 Azure 密钥保管库中的自己的密钥来加密恢复服务保管库中的备份数据。 用于加密恢复服务保管库中的备份的加密密钥可能与用于加密源的密钥不同。 数据受到基于 AES 256 的数据加密密钥 (DEK) 的保护，而 DEK 又受到密钥保管库中存储的密钥的保护。 与使用平台管理的密钥进行加密（默认情况下可使用此方法）相比，你可以更好地控制密钥，并可以更好地满足合规需求。

有关详细信息，请参阅[使用客户管理的密钥加密备份数据](encryption-at-rest-with-cmk.md)。

## <a name="azure-resource-manager-template-for-afs-backup"></a>用于 AFS 备份的 Azure 资源管理器模板

Azure 备份现在支持使用 Azure 资源管理器 (ARM) 模板为现有的 Azure 文件共享配置备份。 该模板通过指定恢复服务保管库和备份策略的适当详细信息，为现有的 Azure 文件共享配置保护。 可以根据需要创建新的恢复服务保管库和备份策略，并将包含文件共享的存储帐户注册到恢复服务保管库。

有关详细信息，请参阅[用于 Azure 备份的 Azure 资源管理器模板](backup-rm-template-samples.md)。

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>SAP HANA 数据库的增量备份（预览版）

Azure 备份现在支持对 Azure VM 上托管的 SAP HANA 数据库进行增量备份。 这样，就可以更快、更经济高效地备份 SAP HANA 数据。

有关详细信息，请参阅[在创建备份策略期间可以使用的各种选项](/azure/backup/sap-hana-faq-backup-azure-vm#policy)以及[如何为 SAP HANA 数据库创建备份策略](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)。

## <a name="backup-center-in-preview"></a>备份中心（预览版）

Azure 备份已启用新的原生管理功能，以用于从中心控制台管理整个备份资产。 备份中心可让你采用与 Azure 的原生管理体验一致的统一方式大规模监视、操作、调控和优化数据保护。

有了备份中心，你可以使用 Azure Lighthouse 获取各个订阅、位置、资源组、保管库甚至租户的聚合库存视图。 备份中心还充当一个操作中心，可以在其中触发备份相关的活动，例如配置备份和还原以及创建策略或保管库，所有这些活动都可在一个位置完成。 此外，通过与 Azure Policy 的无缝集成，你现在可以从备份的角度调控环境并跟踪合规性。 特定于 Azure 备份的内置 Azure 策略还可让你大规模配置备份。

有关详细信息，请参阅[备份中心概述](backup-center-overview.md)。

## <a name="backup-azure-database-for-postgresql-in-preview"></a>备份 Azure Database for PostgreSQL（预览版）

Azure 备份和 Azure 数据库服务共同构建了一个适用于 Azure PostgreSQL 的企业级备份解决方案（目前为预览版）。 现在，可以通过一个能够实现将备份保留长达 10 年的客户控制的备份策略来满足数据保护与合规需求。 这样，在单个数据库级别管理备份和还原操作时便拥有了精细控制度。 同样，还可以轻松跨 PostgreSQL 版本还原，或还原到 Blob 存储。

有关详细信息，请参阅 [Azure Database for PostgreSQL 备份](backup-azure-database-postgresql.md)。

## <a name="selective-disk-backup-and-restore"></a>选择性磁盘备份和还原

Azure 备份支持使用虚拟机备份解决方案同时备份 VM 中的所有磁盘（操作系统和数据）。 现在，可以使用选择性磁盘备份和还原功能备份 VM 中的一部分数据磁盘。 这样就提供了一个高效且经济的针对备份和还原需求的解决方案。 每个恢复点仅包含备份操作中包含的磁盘。

有关详细信息，请参阅 [Azure 虚拟机的选择性磁盘备份和还原](selective-disk-backup-restore.md)。

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>SQL Server 和 SAP HANA 的跨区域还原（预览版）

随着跨区域还原的引入，现在你可以在次要区域中任意启动还原，以缓解环境主要区域中的实际停机问题。 这使得次要区域能够在完全受客户控制的情况下还原。 Azure 备份使用复制到次要区域的备份数据实现这种还原。

现在，除了支持 Azure 虚拟机的跨区域还原以外，该功能已经过扩展，还支持还原 Azure 虚拟机中的 SQL 和 SAP HANA 数据库。

有关详细信息，请参阅 [SQL 数据库的跨区域还原](restore-sql-database-azure-vm.md#cross-region-restore)和 [SAP HANA 数据库的跨区域还原](sap-hana-db-restore.md#cross-region-restore)。

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>支持备份最多包含 32 个磁盘的 VM

在此之前，Azure 备份支持备份每个 VM 中的 16 个托管磁盘。 而现在，Azure 备份支持备份每个 VM 中的最多 32 个托管磁盘。

有关详细信息，请参阅 [VM 存储支持矩阵](backup-support-matrix-iaas.md#vm-storage-support)。

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>简化了 Azure VM 中的 SQL 的备份配置

由于内联备份配置已集成到 Azure 门户的“VM”窗格，现在可以更轻松地为 Azure VM 中的 SQL Server 配置备份。 只需完成少数几个步骤，就能启用 SQL Server 备份，以保护所有现有数据库以及将来添加的数据库。

有关详细信息，请参阅[从“VM”窗格备份 SQL Server](backup-sql-server-vm-from-vm-pane.md)。

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>备份 RHEL Azure 虚拟机中的 SAP HANA（预览版）

Azure 备份是 Azure 原生的备份解决方案，已通过 SAP 的 BackInt 认证。 Azure 备份现已添加对 Red Hat Enterprise Linux (RHEL) 的支持。RHEL 是最广泛使用的、运行 SAP HANA 的 Linux 操作系统之一。

有关详细信息，请参阅 [SAP HANA 数据库备份方案支持矩阵](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>备份数据的区域冗余存储 (ZRS)（预览版）

Azure 存储凭借其各种冗余选项，在高性能、高可用性与高数据复原能力之间提供极佳的平衡。 Azure 备份提供用于在本地冗余存储 (LRS) 和异地冗余存储 (GRS) 中存储备份的选项，可让你将上述优势同样延伸到数据备份操作。 现在，随着添加了对区域冗余存储 (ZRS) 的支持，它还提供更多的持久性选项。

有关详细信息，请参阅[为恢复服务保管库设置存储冗余](backup-create-rs-vault.md#set-storage-redundancy)。

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server 和 SAP HANA 工作负载的软删除

对安全问题（例如恶意软件、勒索软件、入侵）的关注在逐渐上升。 这些安全问题可能会代价高昂（就金钱和数据来说）。 为了防范此类攻击，Azure 备份提供可帮助保护备份数据（即使数据已删除）的安全功能。

其中的一项功能是软删除。 在使用软删除的情况下，即使恶意行动者删除了备份（或用户意外删除了备份数据），备份数据也仍会保留 14 天，因此可以恢复该备份项，而不会丢失数据。 以“软删除”状态将备份数据额外保留 14 天不会向你收取任何费用。

现在，除了针对 Azure VM 提供软删除支持以外，还可以通过软删除来保护 Azure VM 中的 SQL Server 和 SAP HANA 工作负载。

有关详细信息，请参阅 [Azure VM 中的 SQL Server 以及 Azure VM 中的 SAP HANA 工作负载的软删除](soft-delete-sql-saphana-in-azure-vm.md)。

## <a name="enhancements-to-encryption-using-customer-managed-keys-for-azure-backup-in-preview"></a>对 Azure 备份使用客户管理的密钥加密的增强功能（预览版）

Azure 备份现在提供增强功能（预览版）以使用客户管理的密钥管理加密。 通过 Azure 备份，可以引入你自己的密钥来加密恢复服务保管库中的备份数据，从而为你提供更好的控制。

- 支持用户分配的托管标识，向密钥授予权限，以管理恢复服务保管库中的数据加密。
- 在创建恢复服务保管库时，启用使用客户管理的密钥进行加密。
  >[!NOTE]
  >此功能目前处于有限预览状态。 若要注册，请填写[此表](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u)，并向我们发送电子邮件，地址为：[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)。
- 你可以使用 Azure 策略来审核和强制使用客户管理的密钥进行加密。
>[!NOTE]
>- 以上功能仅通过 Azure 门户提供支持，目前不支持 PowerShell。<br>如果是使用 PowerShell 来管理用于备份的加密密钥，我们不建议从门户更新密钥。<br>如果从门户更新密钥，则在支持新模型的 PowerShell 更新可用之前，将无法使用 PowerShell 进一步更新加密密钥。 但是，你可以继续从 Azure 门户更新密钥。
>- 可以使用审核策略来审核使用客户管理的密钥（密钥为 2021 年 4 月 1 日之后启用）进行加密的保管库。  
>- 对于在此日期之前启用 CMK 加密的保管库，该策略可能不适用，也可能显示误报结果（也就是说，即使已启用 CMK 加密，这些保管库仍可能被报告为不合规）。 [了解详细信息](encryption-at-rest-with-cmk.md#using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview)。

有关详细信息，请参阅[使用客户管理的密钥对 Azure 备份进行加密](encryption-at-rest-with-cmk.md)。 

## <a name="next-steps"></a>后续步骤

- [Azure 备份指导和最佳做法](guidance-best-practices.md)