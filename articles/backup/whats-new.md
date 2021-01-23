---
title: Azure 备份中的新增功能
description: 了解 Azure 备份中的新增功能。
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 935c2a806ed3b08f19e680bf1522d98a9ad67666
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730283"
---
# <a name="whats-new-in-azure-backup"></a>Azure 备份中的新增功能

Azure 备份不断改进和发布新功能，增强了 Azure 中数据的保护。 这些新功能将数据保护扩展到新的工作负荷类型，提高安全性并提高备份数据的可用性。 它们还添加了新的管理、监控和自动化功能。

你可以通过将此页面加入书签或 [在此处订阅更新](https://azure.microsoft.com/updates/?query=backup)来了解有关新版本的详细信息。

## <a name="updates-summary"></a>更新摘要

- 2021 年 1 月
  - [预览版中的 Azure 磁盘备份 () ](disk-backup-overview.md)
  - [使用客户托管密钥的静态加密现已正式发布](encryption-at-rest-with-cmk.md)
- 2020 年 11 月
  - [Azure 文件共享的 azure 资源管理器模板 (AFS) 备份](#azure-resource-manager-template-for-afs-backup)
  - [Azure Vm 上 SAP HANA 数据库的增量备份](#incremental-backups-for-sap-hana-databases)
- 2020 年 9 月
  - [备份中心](#backup-center)
  - [备份 Azure Database for PostgreSQL](#backup-azure-database-for-postgresql)
  - [选择性磁盘备份和还原](#selective-disk-backup-and-restore)
  - [在 Azure Vm 上跨区域还原 SQL Server 和 SAP HANA 数据库](#cross-region-restore-for-sql-server-and-sap-hana)
  - [支持备份最多包含32磁盘的 Vm](#support-for-backup-of-vms-with-up-to-32-disks)
  - [为 Azure Vm 中的 SQL 提供简化的备份配置体验](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [RHEL Azure 虚拟机中的备份 SAP HANA](#backup-sap-hana-in-rhel-azure-virtual-machines)
  - [用于备份数据的区域冗余存储 (ZRS) ](#zone-redundant-storage-zrs-for-backup-data)
  - [Azure Vm 中的 SQL Server 和 SAP HANA 工作负荷的软删除](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-in-preview"></a>预览版中的 Azure 磁盘备份 () 

Azure 磁盘备份提供了一个全包式解决方案，该解决方案通过使用备份策略自动创建快照并将其保留为配置的持续时间，为 [Azure 托管磁盘](../virtual-machines/managed-disks-overview.md) 提供快照生命周期管理。 你可以用零的基础结构成本管理磁盘快照，而无需自定义脚本处理或管理开销。 这是一个崩溃一致的备份解决方案，它使用 [增量快照](../virtual-machines/disks-incremental-snapshots.md) 获取托管磁盘的时间点备份，每日支持多个备份。 它也是一个无代理的解决方案，不会影响生产应用程序的性能。 它支持 (包括共享磁盘) 的操作系统和数据磁盘的备份和还原，无论它们当前是否已附加到正在运行的 Azure 虚拟机。

有关详细信息，请参阅 [预览版) 中的 Azure 磁盘备份 (](disk-backup-overview.md)。

## <a name="encryption-at-rest-using-customer-managed-keys"></a>使用客户管理的密钥进行静态加密

现在，使用客户管理的密钥对静态加密的支持现已正式发布。 这使你能够使用 Azure 密钥保管库中存储的密钥来加密恢复服务保管库中的备份数据。 用于对恢复服务保管库中的备份进行加密的加密密钥可能不同于用于对源进行加密的加密密钥。 使用基于 AES 256 的数据加密密钥保护数据 (DEK) ，进而使用存储在 Key Vault 中的密钥进行保护。 与使用平台托管密钥的加密相比 (默认情况下提供) ，这使你可以更好地控制密钥，并可帮助你更好地满足你的符合性需求。

有关详细信息，请参阅 [使用客户管理的密钥加密备份数据](encryption-at-rest-with-cmk.md)。

## <a name="azure-resource-manager-template-for-afs-backup"></a>用于 AFS 备份的 Azure 资源管理器模板

Azure 备份现在支持使用 Azure 资源管理器 (ARM) 模板为现有的 Azure 文件共享配置备份。 模板通过为恢复服务保管库和备份策略指定适当的详细信息，为现有的 Azure 文件共享配置保护。 可以根据需要创建新的恢复服务保管库和备份策略，并将包含文件共享的存储帐户注册到恢复服务保管库。

有关详细信息，请参阅 azure [备份的 azure 资源管理器模板](backup-rm-template-samples.md)。

## <a name="incremental-backups-for-sap-hana-databases"></a>SAP HANA 数据库的增量备份

Azure 备份现在支持对 Azure Vm 上托管 SAP HANA 数据库进行增量备份。 这样就可以更快、更经济高效地备份 SAP HANA 数据。

有关详细信息，请参阅 [创建备份策略过程中可用的各种选项](sap-hana-faq-backup-azure-vm.md#policy) ，以及 [如何为 SAP HANA 数据库创建备份策略](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)。

## <a name="backup-center"></a>备份中心

Azure 备份启用了新的本机管理功能，可以从中央控制台管理整个备份空间。 利用备份中心，你可以采用与 Azure 的本机管理体验一致的统一方式监视、操作、管理和优化数据保护。

利用备份中心，你可以通过订阅、位置、资源组、保管库，甚至是使用 Azure Lighthouse 的租户获得库存汇总视图。 备份中心也是一个操作中心，你可以在其中触发与备份相关的活动，例如配置备份、还原、创建策略或保管库。 此外，通过与 Azure 策略的无缝集成，你现在可以从备份的角度来控制环境和跟踪符合性。 专用于 Azure 备份的内置 Azure 策略还允许大规模配置备份。

有关详细信息，请参阅 [备份中心概述](backup-center-overview.md)。

## <a name="backup-azure-database-for-postgresql"></a>备份 Azure Database for PostgreSQL

Azure 备份和 Azure 数据库服务共同构建了一个企业级备份解决方案，适用于 Azure PostgreSQL (现在提供预览版) 。 现在，你可以使用客户控制的备份策略满足你的数据保护和符合性需求，这使得备份保留最多10年。 这样，你可以精细地控制在单个数据库级别管理备份和还原操作。 同样，你可以轻松地跨 PostgreSQL 版本或 blob 存储进行还原。

有关详细信息，请参阅 [Azure Database for PostgreSQL 备份](backup-azure-database-postgresql.md)。

## <a name="selective-disk-backup-and-restore"></a>选择性磁盘备份和还原

Azure 备份支持使用虚拟机备份解决方案同时备份 VM 中的所有磁盘（操作系统和数据）。 现在，可以使用选择性磁盘备份和还原功能备份 VM 中的一部分数据磁盘。 这样就提供了一个高效且经济的针对备份和还原需求的解决方案。 每个恢复点仅包含备份操作中包含的磁盘。

有关详细信息，请参阅 [Azure 虚拟机的选择性磁盘备份和还原](selective-disk-backup-restore.md)。

## <a name="cross-region-restore-for-sql-server-and-sap-hana"></a>跨区域还原 SQL Server 和 SAP HANA

随着跨区域还原的引入，你现在可以在次要区域中启动还原，以减轻环境主要区域的实时停机问题。 这会使次要区域恢复完全受客户控制。 Azure 备份使用复制到辅助区域的备份数据进行此类还原。

现在，除了支持 Azure 虚拟机的跨区域还原外，该功能已扩展到在 Azure 虚拟机中还原 SQL 和 SAP HANA 数据库。

有关详细信息，请参阅针对 [SQL 数据库的跨区域还原](restore-sql-database-azure-vm.md#cross-region-restore) 和 [针对 SAP HANA 数据库的跨区域还原](sap-hana-db-restore.md#cross-region-restore)。

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>支持备份最多包含32磁盘的 Vm

到现在为止，Azure 备份已支持每个 VM 16 个托管磁盘。 现在，Azure 备份支持每个 VM 最多备份32个托管磁盘。

有关详细信息，请参阅 [VM 存储支持矩阵](backup-support-matrix-iaas.md#vm-storage-support)。

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>为 Azure Vm 中的 SQL 提供更简单的备份配置

在 Azure Vm 中配置 SQL Server 的备份现在更容易，同时集成到 Azure 门户的 "VM" 窗格中。 只需几个步骤，即可启用 SQL Server 的备份，以保护所有现有数据库以及将来添加的数据库。

有关详细信息，请参阅 [从 VM 窗格备份 SQL Server](backup-sql-server-vm-from-vm-pane.md)。

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines"></a>RHEL Azure 虚拟机中的备份 SAP HANA

Azure 备份是适用于 Azure 的本机备份解决方案，由 SAP 对 BackInt 进行了认证。 Azure 备份现已添加对 Red Hat Enterprise Linux (RHEL) ，这是运行 SAP HANA 的最广泛使用的 Linux 操作系统之一。

有关详细信息，请参阅 [SAP HANA 数据库备份方案支持矩阵](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="zone-redundant-storage-zrs-for-backup-data"></a>用于备份数据的区域冗余存储 (ZRS) 

Azure 存储使用各种冗余选项，提供高性能、高可用性和高数据复原的极佳平衡。 通过 Azure 备份，你还可以将这些优势扩展到备份数据，并可以选择将备份存储在本地冗余存储中， (LRS) 和异地冗余存储 (GRS) 。 现在，还提供了更多持续性选项，其中增加了对区域冗余存储 (ZRS) 的支持。

有关详细信息，请参阅 [设置恢复服务保管库的存储冗余](backup-create-rs-vault.md#set-storage-redundancy)。

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server 和 SAP HANA 工作负荷的软删除

对安全问题（例如恶意软件、勒索软件、入侵）的关注在逐渐上升。 这些安全问题可能会代价高昂（就金钱和数据来说）。 为了防止此类攻击，Azure 备份提供了安全功能来帮助保护备份数据，即使在删除后也是如此。

其中的一项功能是软删除。 在使用软删除的情况下，即使恶意行动者删除了备份（或用户意外删除了备份数据），备份数据也仍会保留 14 天，因此可以恢复该备份项，而不会丢失数据。 以“软删除”状态将备份数据额外保留 14 天不会向你收取任何费用。

现在，除了软删除对 Azure Vm 的支持外，还可以通过软删除来保护 Azure Vm 中的 SQL Server 和 SAP HANA 工作负荷。

有关详细信息，请参阅 [AZURE vm 中的 SQL Server 软删除和 AZURE vm 工作负荷中的 SAP HANA](soft-delete-sql-saphana-in-azure-vm.md)。

## <a name="next-steps"></a>后续步骤

- [Azure 备份指南和最佳实践](guidance-best-practices.md)