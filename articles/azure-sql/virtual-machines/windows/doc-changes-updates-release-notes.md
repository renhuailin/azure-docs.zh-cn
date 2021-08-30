---
title: Azure 虚拟机上 SQL Server 的文档更改
description: 了解 Azure 虚拟机上 SQL Server 的各个版本的新功能和改进。
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.subservice: service-overview
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/21/2021
ms.openlocfilehash: c8f8699d58d26c745be8cb1207a88cdad6857f1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752182"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上 SQL Server 的文档更改
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure 允许你使用内置的 SQL Server 映像部署虚拟机 (VM)。 本文汇总了 [Azure 虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 的最新版本中与新功能和改进相关的文档更改。 

## <a name="july-2021"></a>2021 年 7 月

| 更改 | 详细信息 |
| --- | --- |
| **在门户中修复 SQL Server IaaS 扩展** | 现在可以直接从 Azure 门户验证 SQL Server IaaS 代理扩展的状态，并在必要时进行[修复](sql-agent-extension-manually-register-single-vm.md#repair-extension)。 | 


## <a name="june-2021"></a>2021 年 6 月

| 更改 | 详细信息 |
| --- | --- |
| Azure 门户中的安全性增强功能 | 如果已启用 [Azure Defender for SQL](../../../security-center/defender-for-sql-usage.md)，则可在 [Azure 门户的 SQL 虚拟机资源](manage-sql-vm-portal.md#security-center)中查看安全中心建议。 | 

## <a name="may-2021"></a>2021 年 5 月

| 更改 | 详细信息 |
| --- | --- |
| **HADR 内容刷新** | 我们已刷新并增强了高可用性和灾难恢复 (HADR) 内容！ 现提供 [Windows Server 故障转移群集概述](hadr-windows-server-failover-cluster-overview.md)，以及适用于 SQL Server VM 的合并[配置仲裁操作说明](hadr-cluster-quorum-configure-how-to.md)。  此外，我们还通过对云采用更全面的设置建议来增强[群集最佳做法](hadr-cluster-best-practices.md)。| 


## <a name="april-2021"></a>2021 年 4 月

| 更改 | 详细信息 |
| --- | --- |
| **将高可用性迁移到 VM** | Azure Migrate 支持将整个高可用性解决方案提升并转移到 Azure VM 上的 SQL Server！ 立即使用 Azure Migrate 将[可用性组](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)或[故障转移群集实例](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)引入 SQL Server VM！ | 


## <a name="march-2021"></a>2021 年 3 月

| 更改 | 详细信息 |
| --- | --- |
| 性能最佳做法刷新 | 我们已经重写、刷新并更新了性能最佳做法文档，将一篇文章拆分为包含以下内容的系列：[清单](performance-guidelines-best-practices-checklist.md)、[VM 大小指南](performance-guidelines-best-practices-vm-size.md)、[存储指南](performance-guidelines-best-practices-storage.md)和[收集基线说明](performance-guidelines-best-practices-collect-baseline.md)。   | 



## <a name="2020"></a>2020

| 更改 | 详细信息 |
| --- | --- |
| **Azure 政府支持** | 现在，可以为 [Azure 政府](https://azure.microsoft.com/global-infrastructure/government/)云中托管的虚拟机向 SQL IaaS 代理扩展注册 SQL Server 虚拟机。 | 
| Azure SQL 系列 | Azure 虚拟机上的 SQL Server 现在是 [Azure SQL 产品系列](../../azure-sql-iaas-vs-paas-what-is-overview.md)的一部分。 查看我们的[新外观](../index.yml)！ 产品未发生任何更改，更改文档是为了使 Azure SQL 产品决策更为容易。 | 
| **分布式网络名称 (DNN)** | Windows Server 2016+ 上的 SQL Server 2019 现在以预览版形式支持使用[分布式网络名称](./failover-cluster-instance-distributed-network-name-dnn-configure.md)而非 Azure 负载均衡器将流量路由到故障转移群集实例 (FCI)。 此支持简化了连接到 Azure 中的高可用性 (HA) 解决方案的操作。 | 
| 使用 Azure 共享磁盘的 FCI | 现在可以使用 [Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)部署[故障转移群集实例 (FCI)](failover-cluster-instance-overview.md)。 |
| 重新组织的 FCI 文档 | 为了清晰明了，有关 [Azure VM 上 SQL Server 的故障转移群集实例](failover-cluster-instance-overview.md)的文档已重新编写并重新组织。 我们已拆分了一些配置内容，例如[群集配置最佳做法](hadr-cluster-best-practices.md)、如何[为 SQL Server FCI 准备虚拟机](failover-cluster-instance-prepare-vm.md)，以及 [Azure 负载均衡器](./availability-group-vnn-azure-load-balancer-configure.md)配置方法。 | 
| 将日志迁移到超级磁盘 | 了解如何[将日志文件迁移到超级磁盘](storage-migrate-to-ultradisk.md)，以利用高性能和低延迟。 | 
| 使用 Azure PowerShell 创建 AG | 现在，可以通过使用 [Azure PowerShell](availability-group-az-commandline-configure.md) 和 Azure CLI 来简化可用性组的创建。 | 
| 在门户中配置 ag | 现在可以[通过 Azure 门户配置可用性组](availability-group-azure-portal-configure.md)。 此功能目前为预览版，并且正在部署，因此，如果所需区域不可用，请稍后再查看。 | 
| 自动扩展注册 | 现在可以启用[自动注册](sql-agent-extension-automatic-registration-all-vms.md)功能，以使用 [SQL IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)自动注册所有已部署到订阅的 SQL Server VM。 这适用于所有现有的 VM，并且还将自动注册所有将来添加的 SQL Server VM。   | 
| AG 的 DNN | 你现在可以为 SQL Server 2019 CU8 及更高版本配置[分布式网络名称 (DNN) 侦听器](availability-group-distributed-network-name-dnn-listener-configure.md)来替换传统的 [VNN 侦听器](availability-group-overview.md#connectivity)，这样就不再需要使用 Azure 负载均衡器。   | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|更改 | 详细信息 |
 --- | --- |
| **Azure 中的免费 DR 副本** | 如果你拥有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，则可以为本地 SQL Server 实例托管一个[免费的被动实例](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)，用于在 Azure 中进行灾难恢复。 | 
| 批量 SQL IaaS 扩展注册 | 现在，可以使用 [SQL IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)[批量注册](sql-agent-extension-manually-register-vms-bulk.md) SQL Server 虚拟机。 | 
|性能优化的存储配置 | 现在，在创建新的 SQL Server VM 时，可实现[完全自定义存储配置](storage-configuration.md#new-vms)。 |
|**适用于 FCI 的高级文件共享** | 现在可使用[高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)而非[存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)原始方法来创建故障转移群集实例。 
| **Azure 专用主机** | 可在 [Azure 专用主机](dedicated-host.md)上运行 SQL Server VM。 | 
| 将 SQL Server VM 迁移到其他区域 | 使用 Azure Site Recovery [将 SQL Server VM 从一个区域迁移到另一个区域](move-sql-vm-different-region.md)。 |
|  **新 SQL IaaS 安装模式** | 现在可在[轻型模式](sql-server-iaas-agent-extension-automate-management.md)下安装 SQL Server IaaS 扩展，以避免重新启动 SQL Server 服务。  |
| **SQL Server 版本修订** | 现在可以更改 SQL Server VM 的[版本属性](change-sql-server-edition.md)。 |
| 对 SQL IaaS 代理扩展的更改 | 可以通过使用新的 SQL IaaS 模式[向 SQL IaaS 代理扩展注册 SQL Server VM](sql-agent-extension-manually-register-single-vm.md)。 此功能包括 [Windows Server 2008](sql-server-iaas-agent-extension-automate-management.md#management-modes) 映像。|
| **使用 Azure 混合权益的自带许可映像** | 从 Azure 市场部署的自带许可映像现在可以[将其许可证类型切换为即付即用](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)。| 
| Azure 门户中的新 SQL Server VM 管理 | 现在有一种方法可以管理 Azure 门户中的 SQL Server VM。 有关详细信息，请参阅[在 Azure 门户中管理 SQL Server VM](manage-sql-vm-portal.md)。  | 
| 扩展了对 SQL Server 2008 和 2008 R2 的支持 | 通过“按原样”迁移到 Azure VM，[扩展对 SQL Server 2008 和 SQL Server 2008 R2 的支持](sql-server-2008-extend-end-of-support.md)。 | 
| **自定义映像可支持性** | 现在，可以将 [SQL Server IaaS 扩展](sql-server-iaas-agent-extension-automate-management.md#installation)安装到自定义 OS 和 SQL Server 映像，这样就可以具备[灵活许可](licensing-model-azure-hybrid-benefit-ahb-change.md)的有限功能。 在向 SQL IaaS 代理扩展注册自定义映像时，请将许可证类型指定为“AHUB”。 否则，注册会失败。 | 
| **命名实例可支持性** | 现在，如果已正确卸载默认实例，可以将 [SQL Server IaaS 扩展](sql-server-iaas-agent-extension-automate-management.md#installation)与命名实例一起使用。 | 
| **门户增强功能** | 改进了 Azure 门户的 SQL Server VM 部署体验，从而提高了可用性。 有关详细信息，请参见简要的[快速入门](sql-vm-create-portal-quickstart.md)和更详尽的[操作指南](create-sql-vm-portal.md)，以部署 SQL Server VM。|
| **门户改进** | 现在，可以使用 [Azure 门户网站](licensing-model-azure-hybrid-benefit-ahb-change.md#change-license-model)将 SQL Server VM 的许可模式从“即用即付”更改为“自带许可”。|
| 通过 Azure CLI 简化将可用性组部署到 SQL Server VM 的操作 | 现在，将可用性组部署到 Azure 中的 SQL Server VM 比以往更加容易。 可以使用 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true) 来创建 Windows 故障转移群集、内部负载均衡器和可用性组侦听器，所有这些操作都可以从命令行完成。 有关详细信息，请参阅[使用 Azure CLI 为 Azure VM 上的 SQL Server 配置 Always On 可用性组](./availability-group-az-commandline-configure.md)。 | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 更改 | 详细信息 |
| --- | --- |
|  **SQL Server 群集的新资源提供程序** | 新增了一个资源提供程序 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)，用于定义 Windows 故障转移群集的元数据。 将 SQL Server VM 加入到 SqlVirtualMachineGroups，会启动 Windows Server 故障转移群集 (WSFC) 服务，并将该 VM 加入到群集。  |
| **使用 Azure 快速入门模板自动设置可用性组部署** |现在，可以通过使用两个 Azure 快速入门模板来创建 Windows 故障转移群集，将 SQL Server VM 加入到该群集，创建侦听器，并配置内部负载均衡器。 有关详细信息，请参阅[使用 Azure 快速启动模板为 Azure VM 上的 SQL Server 配置可用性组](availability-group-quickstart-template-configure.md). | 
| 自动注册到 SQL IaaS 代理扩展 | 在本月之后部署的 SQL Server VM 会自动注册到新的 SQL IaaS 代理扩展。 在本月之前部署的 SQL Server VM 仍然需要手动注册。 有关详细信息，请参阅[在 Azure 中将 SQL Server 虚拟机注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)。|
|新 SQL IaaS 代理扩展 |  新增了一个资源提供程序 (Microsoft.SqlVirtualMachine)，可用于更好地管理 SQL Server VM。 若要详细了解如何注册 VM，请参阅[在 Azure 中将 SQL Server 虚拟机注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)。 |
|**切换许可模型** | 现在，可以使用 Azure CLI 或 Powershell 在 SQL Server VM 的“即用即付”模型与“自带许可”模型之间进行切换。 有关详细信息，请参阅[如何在 Azure 中更改 SQL Server 虚拟机的许可模型](licensing-model-azure-hybrid-benefit-ahb-change.md)。 | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>其他资源

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [在 Windows VM 上预配 SQL Server](create-sql-vm-portal.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)
* [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 虚拟机中 SQL Server 的性能最佳做法](./performance-guidelines-best-practices-checklist.md)
* [Azure 虚拟机中的 SQL Server 的应用程序模式和开发策略](application-patterns-development-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [在 Linux 虚拟机上预配 SQL Server](../linux/sql-vm-create-portal-quickstart.md)
* [常见问题 (Linux)](../linux/frequently-asked-questions-faq.yml)
* [“Linux 上的 SQL Server”文档](/sql/linux/sql-server-linux-overview)