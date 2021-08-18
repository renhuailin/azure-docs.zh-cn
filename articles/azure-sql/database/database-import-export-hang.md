---
title: 导入和导出数据库会花费较长时间
description: Azure SQL 数据库和 Azure SQL 托管实例导入/导出服务需要很长时间来导入或导出数据库
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: cawrites
ms.date: 09/27/2019
ms.openlocfilehash: 27431b24b0f6507ac7031deae6573a8b111e0f76
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913659"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL 数据库和托管实例导入/导出服务需要很长时间来导入或导出数据库

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

使用导入/导出服务时，相关过程花费的时间可能比预期要长。 本文介绍此延迟的可能原因和替代解决方法。

## <a name="azure-sql-database-importexport-service"></a>Azure SQL 数据库导入/导出服务

Azure SQL 数据库导入/导出服务是一项在每个 Azure 数据中心运行的基于 REST 的 Web 服务。 在 Azure 门户中使用[导入数据库](database-import.md#using-azure-portal)或[导出](./database-import.md#using-azure-portal)选项移动数据库时，会调用该服务。 此服务提供免费的请求排队和计算服务以在 Azure SQL 数据库和 Azure Blob 存储之间执行导入和导出操作。

这些导入和导出操作不代表传统的物理数据库备份，而是代表对使用特殊 BACPAC 格式的数据库进行的逻辑备份。 有了 BACPAC 格式，就不必使用 Microsoft SQL Server 版本、Azure SQL 数据库版本和 Azure SQL 托管实例版本之间可能存在差异的物理格式。

## <a name="what-causes-delays-in-the-process"></a>什么导致此过程中出现的延迟？

Azure SQL 数据库导入/导出服务为每个区域提供有限数量的计算虚拟机 (VM) 来处理导入和导出操作。 计算 VM 按区域托管，确保导入或导出避免跨区域带宽延迟和收费。 如果在同一区域中同时发出过多请求，则可能会在处理这些操作时出现显著延迟。 完成请求所需的时间可能短至数秒，也可能长达许多小时。


## <a name="recommended-solutions"></a>建议的解决方案

如果数据库导出仅用于从意外数据删除进行的恢复，则所有 Azure SQL 数据库版本都会提供从系统生成的备份进行自助还原的功能。 但是，如果因为其他原因而需要这些导出，并且需要持续情况下更快或可预测性更强的导入/导出性能，请考虑以下选项：

* [使用 SQLPackage 实用程序导出到 BACPAC 文件](./database-export.md#sqlpackage-utility)。
* [使用 SQL Server Management Studio (SSMS) 导出到 BACPAC 文件](./database-export.md#sql-server-management-studio-ssms)。
* 通过 Microsoft SQL Server Data-Tier Application Framework (DacFx) API 直接在代码中运行 BACPAC 导入或导出。 如需其他信息，请参阅：
  * [导出数据层应用程序](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft.SqlServer.Dac Namespace](/dotnet/api/microsoft.sqlserver.dac)（Microsoft.SqlServer.Dac 命名空间）
  * [下载 DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>导出或导入数据库时要考虑的事项

* 本文讨论的所有方法用光了数据库事务单位 (DTU) 配额，导致 Azure SQL 数据库服务进行相关限制。 可以[在 Azure 门户中查看数据库的 DTU 统计信息](./monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring)。 如果数据库达到其资源限制，请[升级服务层级](./scale-resources.md)以添加更多资源。
* 理想情况下，应该在数据库所在区域的 VM 中运行客户端应用程序（例如 sqlpackage 实用工具或自定义 DAC 应用程序）。 否则，可能会遇到与网络延迟相关的性能问题。
* 在没有聚集索引的情况下导出大型表可能会很慢，甚至会导致故障。 出现此行为的原因是：表不能拆分，因此无法以并行方式导出， 而只能在单个事务中导出，这导致在导出过程中出现性能降低的情况，并且存在故障的可能，大型表尤其如此。


## <a name="related-documents"></a>相关文档

[导出数据库时的注意事项](./database-export.md#considerations)
