---
title: Azure Monitor for SAP Solutions 概述和体系结构 | Microsoft Docs
description: 本文提供了有关 Azure Monitor for SAP Solutions 的常见问题解答
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 4b5bcb4aecab0247f47aede883e1bfe771262026
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746205"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor for SAP Solutions（预览版）

## <a name="overview"></a>概述

本文概述了 Azure Monitor for SAP Solutions 的工作原理及其功能。

Azure Monitor for SAP Solutions 是一款 Azure 本机监视产品，面向在 Azure 上运行 SAP 环境的任何人。 此产品适用于 [SAP on Azure 虚拟机](./hana-get-started.md)和 [SAP on Azure 大型实例](./hana-overview-architecture.md)。

通过 Azure Monitor for SAP Solutions，可在一个集中位置收集来自 Azure 基础结构和数据库的遥测数据，并直观地关联遥测数据来更快地排除故障。

Azure Monitor for SAP Solutions 通过 Azure 市场提供。 它提供简单、直观的设置体验，只需单击几下，即可为 Azure Monitor for SAP Solutions 部署资源（称为 SAP Monitor 资源）。

可通过为组件添加相应的提供程序，监视 SAP 环境的不同组件，例如 Azure 虚拟机 (VM)、高可用性群集、SAP HANA 数据库和 SAP NetWeaver。

支持的基础结构：

- Azure 虚拟机
- Azure 大型实例

支持的数据库：
- SAP HANA 数据库
- Microsoft SQL Server

Azure Monitor for SAP Solutions 使用现有 [Azure Monitor](../../../azure-monitor/overview.md) 功能（如 Log Analytics 和[工作簿](../../../azure-monitor/visualize/workbooks-overview.md)）来提供更多监视功能。 可通过编辑 Azure Monitor for SAP Solutions 提供的默认工作簿来创建[自定义可视化效果](../../../azure-monitor/visualize/workbooks-overview.md#getting-started)。 使用 Azure Log Analytics 工作区编写[自定义查询](../../../azure-monitor/logs/log-analytics-tutorial.md)并创建[自定义警报](../../../azure-monitor/alerts/alerts-log.md)。 利用[灵活的保持期](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)并连接票证系统中的监视数据。

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Azure Monitor for SAP Solutions 收集哪些数据？

Azure Monitor for SAP Solutions 中的数据收集取决于配置的提供程序。 公共预览版期间，将收集以下数据。

高可用性 Pacemaker 群集遥测：
- 节点、资源和 STONITH 块设备 (SBD) 状态
- Pacemaker 位置约束
- 仲裁投票和响铃状态
- [其他](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA 遥测：
- CPU、内存、磁盘和网络使用情况
- HANA 系统复制 (HSR)
- HANA 备份
- HANA 主机状态
- 索引服务器和名称服务器角色

Microsoft SQL Server 遥测：
- CPU、内存、磁盘使用情况
- 主机名、SQL 实例名称、SAP 系统 ID
- 一段时间内的批处理请求、编译和页生存期
- 一段时间内成本最高的 10 个 SQL 语句
- SAP 系统中排名前 12 的最大表
- SQL Server 错误日志中记录的问题
- 一段时间内的阻塞进程以及 SQL 等待统计信息

操作系统遥测 (Linux) 
- CPU 使用情况、分支计数、正在运行的进程和阻塞的进程 
- 内存使用情况和已使用、已缓存、已缓冲内存间的分配 
- 交换使用情况、分页和交换速率 
- 文件系统使用情况、每个块设备的读取和写入的字节数 
- 每个块设备的读取/写入延迟 
- 正在进行的 I/O 计数、永久性内存读取/写入字节数 
- 网络数据包传入/传出、网络字节传入/传出 

SAP NetWeaver 遥测：

- SAP 系统和应用程序服务器可用性，包括调度程序、ICM、网关、消息服务器、排队服务器、IGS 监视器的实例进程可用性
- 工作进程使用情况统计信息和趋势
- 排队锁定统计信息和趋势
- 队列使用情况统计信息和趋势

## <a name="data-sharing-with-microsoft"></a>与 Microsoft 共享的数据

Azure Monitor for SAP Solutions 会收集系统元数据，为 Azure 上的 SAP 提供更卓越的支持。 未收集任何 PII/EUII。

可从下拉列表中选择“共享”，在创建 Azure Monitor for SAP Solutions 资源时启用与 Microsoft 的数据共享。 建议启用验数据共享。 数据共享为 Microsoft 支持部门和工程团队提供环境相关信息，这有助于我们更好地为 Azure 上的 SAP 任务关键型解决方案提供支持。

## <a name="architecture-overview"></a>体系结构概述

概括而言，下图展示了 Azure Monitor for SAP Solutions 如何从 SAP HANA 数据库中收集遥测。 无论 SAP HANA 是部署在 Azure VM 上还是 Azure 大型实例上，体系结构都一样。

![Azure Monitor for SAP Solutions 体系结构](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

此体系结构的关键组件为：
- Azure 门户 - 起点。 可在 Azure 门户中导航到市场，并发现 Azure Monitor for SAP Solutions。
- Azure Monitor for SAP Solutions 资源 - 用于查看监视遥测的位置。
- 受管理资源组 – 作为 Azure Monitor for SAP Solutions 资源部署的一部分自动部署。 部署在受管理资源组中的资源可帮助收集遥测数据。 部署的关键资源及其用途如下：
   - Azure 虚拟机：也称为收集器 VM，是一种 Standard_B2ms VM。 此 VM 的主要目的是托管监视有效负载。 监视有效负载是指从源系统收集遥测并将此数据传输到监视框架的逻辑。 在上面的关系图中，监视有效负载包含通过 SQL 端口连接到 SAP HANA 数据库的逻辑。
   - [Azure 密钥保管库](../../../key-vault/general/basic-concepts.md)：部署此资源以安全地保存 SAP HANA 数据库凭据并存储有关[提供程序](./azure-monitor-providers.md)的信息。
   - Log Analytics 工作区：存储遥测数据的目标。
      - 可视化效果是通过使用 [Azure 工作簿](../../../azure-monitor/visualize/workbooks-overview.md)，基于 Log Analytics 中的遥测而生成的。 可自定义可视化效果。 还可将工作簿或工作簿内的特定可视化效果固定到 Azure 仪表板，以便自动刷新。 刷新的最高频率是每 30 分钟一次。
      - 在部署时，可通过选择此选项，在与 SAP 监视器资源相同的订阅中使用其现有工作区。
      - 可使用 Kusto 查询语言 (KQL) 对 Log Analytics 工作区内的原始表运行[查询](../../../azure-monitor/logs/log-query-overview.md)。 查看 *自定义日志*。

> [!Note]
> 你负责修补和维护部署在受管理资源组中的 VM。

> [!Tip]
> 如果现有 Log Analytics 工作区被部署到了与 Azure Monitor for SAP Solutions 资源相同的 Azure 订阅，则可选择使用该工作区来收集遥测数据。

### <a name="architecture-highlights"></a>体系结构亮点

下面是体系结构的主要亮点：
 - **多实例** - 可使用 Azure Monitor for SAP Solutions 的单个资源，在 VNet 中跨多个 SAP SID 为给定组件类型的多个实例（例如 HANA 数据库、高可用性 (HA) 群集、Microsoft SQL Server、SAP NetWeaver）创建监视器。
 - 多提供程序 - 上述体系结构关系图显示了作为示例的 SAP HANA 提供程序。 同样地，可为相应的组件（例如 HANA DB、HA 群集、Microsoft SQL Server、SAP NetWeaver）配置更多提供程序，以从这些组件中收集数据。
 - 开放源代码 - Azure Monitor for SAP Solutions 的源代码可在 [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions) 中找到。 可以参考提供程序代码，了解有关产品、参与或共享反馈的详细信息。
 - 可扩展查询框架 - 用于收集遥测数据的 SQL 查询以 [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) 格式编写。 可以轻松添加更多用于收集更多遥测数据的 SQL 查询。 可通过使用本文结尾处的链接留下反馈，请求将特定遥测数据添加到 Azure Monitor for SAP Solutions。 还可联系帐户团队来留下反馈。

## <a name="pricing"></a>定价
Azure Monitor for SAP Solutions 为免费产品（无许可费用）。 你负责支付受管理资源组中基础组件的费用，以及与数据引入和数据保留相关的消耗成本。 有关详细信息，请参阅标准 Azure 定价文档：[Azure VM 定价](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)、[Azure Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)、[Azure 存储帐户定价](https://azure.microsoft.com/pricing/details/storage/queues/)、[Azure Log Analytics 和警报定价](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="next-steps"></a>后续步骤

详细了解 Azure Monitor for SAP Solutions 提供程序。

> [!div class="nextstepaction"]
> [Azure Monitor for SAP Solutions 提供程序](azure-monitor-providers.md)
