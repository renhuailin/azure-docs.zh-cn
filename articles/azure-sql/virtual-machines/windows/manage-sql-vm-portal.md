---
title: 使用 Azure 门户管理 Azure 中的 SQL Server 虚拟机 | Microsoft Docs
description: 了解如何在 Azure 门户中访问 Azure 上托管的 SQL Server VM 的 SQL 虚拟机资源，以修改 SQL Server 设置。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37b4f063f2d08c137c44784c55cc3c47569a3c56
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434470"
---
# <a name="manage-sql-server-vms-by-using-the-azure-portal"></a>使用 Azure 门户管理 SQL Server VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 [Azure 门户](https://portal.azure.com)中，[SQL 虚拟机](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines)资源是一个独立的管理服务，用于管理已注册到 SQL Server IaaS 代理扩展的 Azure 虚拟机 (VM) 上的 SQL Server。 可以使用该资源同时查看所有 SQL Server VM，以及修改专用于 SQL Server 的设置： 

![SQL 虚拟机资源](./media/manage-sql-vm-portal/sql-vm-manage.png)

SQL 虚拟机资源管理点与用于管理 VM（例如启动、停止或重启 VM）的虚拟机资源不同 。 


## <a name="prerequisite"></a>先决条件 

SQL 虚拟机资源只可用于已[注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)的 SQL Server VM。 


## <a name="access-the-resource"></a>访问资源

要访问 SQL 虚拟机资源，请执行以下操作：

1. 打开 [Azure 门户](https://portal.azure.com)。 
1. 选择“所有服务”。 
1. 在搜索框中输入“SQL 虚拟机”。
1. （可选）：选择“SQL 虚拟机”旁边的星号将此选项添加到“收藏夹”菜单 。 
1. 选择“SQL 虚拟机”。 

   ![在所有服务中查找 SQL Server 虚拟机](./media/manage-sql-vm-portal/sql-vm-search.png)

1. 该门户列出订阅内所有可用的 SQL Server VM。 选择要管理的那一个，以打开“SQL 虚拟机”资源。 如果未显示你的 SQL Server VM，请使用搜索框。 

   ![所有可用的 SQL Server VM](./media/manage-sql-vm-portal/all-sql-vms.png)

   选择你的 SQL Server VM 可打开该 SQL 虚拟机资源： 


   ![查看 SQL 虚拟机资源](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> SQL 虚拟机资源用于专用的 SQL Server 设置。 选择“虚拟机”框中的 VM 名称，以打开特定于该 VM 但不专用于 SQL Server 的设置。 


## <a name="license-and-edition"></a>许可证和产品版本 

使用 SQL 虚拟机资源的“配置”页，为[用于灾难恢复的免费 Azure 副本](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)将 SQL Server 许可元数据更改为“即付即用”、“Azure 混合权益”或“HA/DR”   。



![使用 SQL 虚拟机资源在 Azure 门户中更改 SQL Server VM 元数据的发行版本和产品版本](./media/manage-sql-vm-portal/sql-vm-license-edition.png)

也可以从“配置”页修改 SQL Server 的产品版本，例如 Enterprise、Standard 或 Developer   。 

只有在已从内部将 SQL Server 的发行版本和产品版本修改为 VM 后，才支持在 Azure 门户中更改许可证和产品版本元数据。 若要了解详细信息，请参阅：在 Azure VM 上更改 SQL Server 的[发行版本](change-sql-server-version.md)和[产品版本](change-sql-server-edition.md)。 

## <a name="storage"></a>存储 

使用 SQL 虚拟机资源的“配置”页可以扩展数据、日志和 tempdb 驱动器。 

![在 Azure 门户中使用 SQL 虚拟机资源扩展存储](./media/manage-sql-vm-portal/sql-vm-storage-configuration.png)

## <a name="patching"></a>修补

使用 SQL 虚拟机资源的“修补”页可以启用 VM 自动修补，并自动安装标记为“重要”的 Windows 和 SQL Server 更新。 在此页中还可以配置维护计划，例如每日运行修补，以及本地的维护开始时间和维护时段。 


![在 Azure 门户中使用 SQL 虚拟机资源配置自动修补和计划](./media/manage-sql-vm-portal/sql-vm-automated-patching.png)


有关详细信息，请参阅[自动修补](automated-patching.md)。 



## <a name="backups"></a>备份

使用 SQL 虚拟机资源的“备份”页可以配置自动备份设置，例如保留期、要使用的存储帐户、加密、是否备份系统数据库和备份计划。 

![在 Azure 门户中使用 SQL 虚拟机资源配置自动备份和计划](./media/manage-sql-vm-portal/sql-vm-automated-backup.png)

有关详细信息，请参阅[自动修补](automated-backup.md)。 


## <a name="high-availability-preview"></a>高可用性（预览版）

使用 SQL 虚拟机资源的“高可用性”页来创建 Windows Server 故障转移群集，并配置 Always On 可用性组、可用性组侦听器和 Azure 负载均衡器。 使用 Azure 门户配置高可用性的功能目前为预览版。 


![使用 SQL 虚拟机资源在 Azure 门户中配置 Windows Server 故障转移群集和 Always On 可用性组](./media/manage-sql-vm-portal/sql-vm-high-availability.png)


若要了解详细信息，请参阅[使用 Azure 门户配置可用性组](availability-group-azure-portal-configure.md)。

## <a name="security-configuration"></a>安全性配置 

使用 SQL 虚拟机资源的“安全配置”页可以配置 SQL Server 安全设置（例如要使用的端口、是否启用 SQL 身份验证）和启用 Azure 密钥保管库集成。 

![在 Azure 门户中使用 SQL 虚拟机资源配置 SQL Server 安全性](./media/manage-sql-vm-portal/sql-vm-security-configuration.png)

有关详细信息，请参阅[安全最佳做法](security-considerations-best-practices.md)。


## <a name="security-center"></a>安全中心 

使用 SQL 虚拟机资源的“安全中心”页，在 SQL 虚拟机边栏选项卡中直接查看安全中心建议。 启用 [Azure Defender for SQL](../../../security-center/defender-for-sql-usage.md) 以利用此功能。 

![使用 SQL 虚拟机资源在 Azure 门户中配置 SQL Server 安全中心设置](./media/manage-sql-vm-portal/sql-vm-security-center.png)


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.yml)
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](doc-changes-updates-release-notes.md)


