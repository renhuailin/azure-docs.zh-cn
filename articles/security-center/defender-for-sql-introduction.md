---
title: 适用于 SQL 的 Azure Defender - 优点和功能
description: 了解适用于 SQL 的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 05/27/2021
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 7f5427edb2b48065ea67e589602be2278c7410c9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711728"
---
# <a name="introduction-to-azure-defender-for-sql"></a>适用于 SQL 的 Azure Defender 简介

适用于 SQL 的 Azure Defender 包括两个 Azure Defender 计划，这些计划可扩展 Azure 安全中心的[数据安全包](../azure-sql/database/azure-defender-for-sql.md)以保护任何位置的数据库及其数据。 Azure Defender for SQL 具有以下功能：发现并减少潜在的数据库漏洞，并检测可能表明数据库受到威胁的异常活动。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|**适用于 Azure SQL 数据库服务器的 Azure Defender** - 正式发布 (GA)<br>**适用于计算机上的 SQL Server 的 Azure Defender** - 正式发布 (GA) |
|定价：|适用于 SQL 的 Azure Defender 包含的两个计划按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中所示的定价计费|
|受保护的 SQL 版本：|[Azure 虚拟机上的 SQL](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[已启用 Azure Arc 的 SQL Server](/sql/sql-server/azure-arc/overview)<br>不带 Azure Arc 的 Windows 计算机上的本地 SQL Server<br>Azure SQL [单一数据库](../azure-sql/database/single-database-overview.md)和[弹性池](../azure-sql/database/elastic-pool-overview.md)<br>[Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics（以前称为 SQL DW）专用 SQL 池](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 政府<br>:::image type="icon" source="./media/icons/yes-icon.png":::Azure 中国世纪互联（部分：SQL 服务器的警报和漏洞评估的子集。 行为威胁防护功能不可用。）|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>适用于 SQL 的 Azure Defender 保护了哪些项目？

适用于 SQL 的 Azure Defender 包含两个单独的 Azure Defender 计划：

- 适用于 Azure SQL 数据库服务器的 Azure Defender 保护：
    - [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)
    - [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Azure Synapse 中的专用 SQL 池](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- 适用于计算机上的 SQL Server 的 Azure Defender 会扩展对 Azure 原生 SQL Server 的保护以完全支持混合环境，并保护在 Azure、其他云环境，甚至本地计算机上托管的 SQL server（所有受支持的版本）：
    - [虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - 本地 SQL Server：
        - [已启用 Azure Arc 的 SQL Server（预览）](/sql/sql-server/azure-arc/overview)
        - [在不带 Azure Arc 的 Windows 计算机上运行的 SQL Server](../azure-monitor/agents/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>适用于 SQL 的 Azure Defender 有哪些优点？

这两项计划包括用于识别和减少潜在的数据库漏洞的功能，以及用于检测可能表明数据库有威胁的异常活动的功能。

漏洞评估服务可发现、跟踪并帮助你修正潜在的数据库漏洞。 评估扫描概述了 SQL 计算机的安全状态以及任何安全发现结果的详细信息。

- 详细了解 [Azure SQL 数据库的漏洞评估](../azure-sql/database/sql-vulnerability-assessment.md)。
- 详细了解[计算机上 Azure SQL 服务器的漏洞评估](defender-for-sql-on-machines-vulnerability-assessment.md)。

高级威胁防护服务持续监视 SQL 服务器的威胁，例如 SQL 注入、暴力攻击和特权滥用。 此服务在 Azure 安全中心提供面向操作的安全警报，其中包括可疑活动的详细信息、有关如何减少威胁的指导以及使用 Azure Sentinel 继续调查的选项。 详细了解[高级威胁防护](../azure-sql/database/threat-detection-overview.md)。

 > [!TIP]
 > [在警报参考页中](alerts-reference.md#alerts-sql-db-and-warehouse)查看 SQL Server 的安全警报列表。


## <a name="is-there-a-performance-impact-from-deploying-azure-defender-for-sql-on-machines"></a>在计算机上部署 Azure Defender for SQL 是否会影响性能？

针对计算机上的 Azure Defender for SQL 的重点显然是安全性。 但我们同样关心你的业务，因此会优先考虑性能，以确保对 SQL 服务器的影响降至最低。 

这项服务有一个拆分体系结构，用于平衡数据上传、速度与性能： 

- 我们的一些检测器在计算机上运行，具有实时速度优势
- 其他一些检测器则在云中运行，以分担计算机繁重的计算负载

我们的解决方案实验室测试（与基准负载相比较）显示，峰值部分的平均 CPU 使用率为 3%。 对当前用户的遥测数据分析显示，对 CPU 和内存使用情况的影响可以忽略不计。

当然，性能始终因环境、计算机和负载而异。 上述声明和数字作为一般准则提供，而不是对任何个别部署的保证。


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>适用于 SQL 的 Azure Defender 提供哪种类型的警报？

存在以下情况时，会触发具有大量威胁情报的安全警报：

- **潜在的 SQL 注入攻击** - 包括应用程序在数据库中生成错误的 SQL 语句时检测到的漏洞
- **异常的数据库访问和查询模式** - 例如，使用不同的凭据尝试登陆，但登录失败的次数异常多（强制尝试）
- **可疑的数据库活动** - 例如，合法用户从遭到入侵的计算机访问 SQL Server，而此计算机曾与加密挖掘 C&C 服务器通信

警报包含触发警报的事件的详细信息，并提供有关如何调查和消除威胁的建议。



## <a name="next-steps"></a>后续步骤

本文介绍了适用于 SQL 的 Azure Defender。 若要使用所描述的服务：

- 在计算机上使用 Azure Defender for SQL 服务器[扫描 SQL 服务器是否存在漏洞](defender-for-sql-usage.md)
- 有关 Azure Defender for SQL 的演示，请参阅 [Azure Defender for SQL 如何随时随地保护 SQL 服务器](https://www.youtube.com/watch?v=V7RdB6RSVpc)
