---
title: Azure Windows 虚拟机上的 SQL Server 概述 | Microsoft Docs
description: 了解如何在云中的 Azure 虚拟机上运行完整版本的 SQL Server，而无需管理任何本地硬件。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.subservice: service-overview
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6befd620da91b569d5bd72a4d0165e81c93583ac
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235642"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Azure 虚拟机 (Windows) 上的 SQL Server 是什么？
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[Azure 虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 允许你在云中使用完整版本的 SQL Server，而不需管理任何本地硬件。 使用即用即付时，SQL Server 虚拟机 (VM) 还可以简化许可成本。

Azure 虚拟机在全球许多不同的[地理区域](https://azure.microsoft.com/regions/)运行， 并提供各种[虚拟机大小](../../../virtual-machines/sizes.md)。 使用虚拟机映像库可以创建 SQL Server VM，而且版本和操作系统都很正确。 因此，虚拟机适用于许多不同的 SQL Server 工作负荷。

如果你不熟悉 Azure VM 上的 SQL Server，请查看我们深度讲解的 [Azure SQL 视频系列](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)中的“Azure VM 上的 SQL Server 概述”视频：
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>自动更新

Azure 虚拟机上的 SQL Server 可以使用[自动修补](automated-patching.md)来安排维护时段，以便自动安装重要的 Windows 和 SQL Server 更新。

## <a name="automated-backups"></a>自动备份

Azure 虚拟机上的 SQL Server 可以利用[自动备份](automated-backup.md)，定期创建数据库到 Blob 存储的备份。 也可以手动使用此技术。 有关详细信息，请参阅[使用 Azure 存储进行 SQL Server 备份和还原](azure-storage-sql-server-backup-restore-use.md)。

Azure 还为 Azure VM 中运行的 SQL Server 提供企业级备份解决方案。 作为完全托管的备份解决方案，它支持 AlwaysOn 可用性组、长期保留、时点恢复以及集中管理和监视。 有关详细信息，请参阅 [Azure VM 中 SQL Server 的 Azure 备份](../../../backup/backup-azure-sql-database.md)。
  

## <a name="high-availability"></a>高可用性

如果需要高可用性，请考虑配置 SQL Server 可用性组。 这涉及虚拟网络中 Azure 虚拟机上的 SQL Server 的多个实例。 可以手动配置高可用性解决方案，也可以在 Azure 门户中使用模板进行自动配置。 有关所有高可用性选项的概述，请参阅 [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](business-continuity-high-availability-disaster-recovery-hadr-overview.md)。

## <a name="performance"></a>性能

Azure 虚拟机提供的虚拟机大小取决于工作负荷需求。 SQL Server VM 还提供按性能要求优化的自动存储配置。 若要详细了解如何为 SQL Server VM 配置存储，请参阅 [SQL Server VM 的存储配置](storage-configuration.md)。 若要优化性能，请参阅 [Azure 虚拟机上 SQL Server 的性能最佳做法](./performance-guidelines-best-practices-checklist.md)。

## <a name="get-started-with-sql-server-vms"></a>SQL Server VM 入门

若要开始，请选择一个 SQL Server 虚拟机映像，其中包含所需的版本和操作系统。 下面的各部分针对相关 SQL Server 虚拟机库映像提供了指向 Azure 门户的直接链接。

> [!TIP]
> 有关如何了解 SQL Server 映像定价的详细信息，请参阅 [Azure 虚拟机上的 SQL Server 定价指南](pricing-guidance.md)。 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> 即用即付
下表提供了一个矩阵，其中包含即用即付 SQL Server 映像。

| 版本 | 操作系统 | 版本 |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise)、[Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard)、[Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web)、[Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

若要查看可用的 Azure 虚拟机上的 SQL Server 映像，请参阅 [Azure 虚拟机上的 SQL Server 概述 (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)。

> [!NOTE]
> 现在可以更改按用量付费的 SQL Server VM 的许可模式来使用自己的许可证。 有关详细信息，请参阅[如何更改 SQL Server VM 的许可模型](licensing-model-azure-hybrid-benefit-ahb-change.md)。 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> 自带许可
也可以自带许可 (BYOL)。 在此方案中，你只需支付 VM 费用，SQL Server 许可不需要任何额外的费用。  自带许可证长时间会节省资金，因为可以持续使用生产型工作负荷。 有关使用此选项的要求，请参阅 [SQL Server Azure VM 定价指南](pricing-guidance.md#byol)。

若要自带许可证，可以转换现有的按用量付费的 SQL Server VM，也可以部署前缀为 {BYOL} 的映像。 有关在按使用量付费和 BYOL 之间切换许可模型的更多信息，请参阅[如何更改 SQL Server VM 的许可模型](licensing-model-azure-hybrid-benefit-ahb-change.md)。 

| 版本 | 操作系统 | 版本 |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise)、[Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

可以使用 PowerShell 部署 Azure 门户中不可用的较旧的 SQL Server 映像。 若要使用 PowerShell 查看所有可用映像，请使用以下命令：

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

有关使用 PowerShell 部署 SQL Server VM 的详细信息，请查看[如何使用 Azure PowerShell 预配 SQL Server 虚拟机](create-sql-vm-powershell.md)。


### <a name="connect-to-the-vm"></a>连接到 VM
创建 SQL Server VM 以后，即可从 SQL Server Management Studio (SSMS) 之类的应用程序或工具连接到该 VM。 有关说明，请参阅[连接到 Azure 上的 SQL Server 虚拟机](ways-to-connect-to-sql.md)。

### <a name="migrate-your-data"></a>迁移数据
如果已有数据库，会想要将该数据库移至新预配的 SQL Server VM。 有关迁移选项的列表和指导，请参阅[将数据库迁移到 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)。

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>使用 Azure 门户创建和管理 Azure SQL 资源

Azure 门户提供了一个页面，可以在其中管理[所有 Azure SQL 资源](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql)，包括 SQL 虚拟机。

若要访问“Azure SQL 资源”，请在 Azure 门户菜单中选择“Azure SQL”，或在任何页面搜索并选择“Azure SQL”  。

![搜索 Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Azure SQL 提供了一种快速简便的方式来访问所有 Azure SQL 数据库、弹性池、逻辑服务器、托管实例和虚拟机。 Azure SQL 不是服务或资源。 

若要管理现有资源，请在列表中选择所需的项。 若要创建新的 Azure SQL 资源，请选择“+ 添加”。 

![创建 Azure SQL 资源](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

选择“+ 添加”后，可通过在任何磁贴上选择“显示详细信息”查看有关不同选项的其他信息 。

![数据库磁贴详细信息](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

有关详细信息，请参阅：

- [创建单一数据库](../../database/single-database-create-quickstart.md)
- [创建弹性池](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [创建托管实例](../../managed-instance/instance-create-quickstart.md)
- [创建 SQL Server 虚拟机](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> SQL Server VM 映像刷新策略
对于每种支持的操作系统和版本的组合，Azure 只保留一个虚拟机映像。 这意味着，随着时间的推移，映像会进行刷新，旧映像会被删除。 有关详细信息，请参阅[SQL Server VM 常见问题解答](/azure/azure-sql/virtual-machines/windows/frequently-asked-questions-faq#images)的“映像”部分。

## <a name="customer-experience-improvement-program-ceip"></a>客户体验改善计划 (CEIP)
客户体验改善计划 (CEIP) 默认情况下已启用。 这样会定期将报告发送至 Microsoft，帮助改进 SQL Server。 CEIP 不需要执行管理任务，除非用户想要在预配后将其禁用。 可以通过远程桌面连接到 VM，以自定义或禁用 CEIP。 然后运行 **SQL Server 错误和使用情况报告** 实用工具。 请按照说明禁用报告功能。 有关数据收集的详细信息，请参阅 [SQL Server 隐私声明](/sql/sql-server/sql-server-privacy)。

## <a name="related-products-and-services"></a>相关产品和服务
### <a name="windows-virtual-machines"></a>Windows 虚拟机
* [Azure 虚拟机概述](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>存储
* [Microsoft Azure 存储简介](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>网络
* [虚拟网络概述](../../../virtual-network/virtual-networks-overview.md)
* [Azure 中的 IP 地址](../../../virtual-network/public-ip-addresses.md)
* [在 Azure 门户中创建完全限定的域名](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server 文档](/sql/index)
* [Azure SQL 数据库比较](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>后续步骤

Azure 虚拟机上的 SQL Server 入门：

* [在 Azure 门户中创建 SQL Server VM](sql-vm-create-portal-quickstart.md)

获取有关 SQL Server VM 的常见问题的解答：

* [Azure 虚拟机中的 SQL Server 常见问题](frequently-asked-questions-faq.yml)

查看在 IaaS 中的 SQL Server 上运行的 N 层应用程序的参考体系结构

* [Azure 上包含 SQL Server 的 Windows N 层应用程序](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [在多个 Azure 区域中运行 N 层应用程序以确保高可用性](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
