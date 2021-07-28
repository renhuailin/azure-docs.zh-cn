---
title: Azure Monitor for SAP Solutions 概述和体系结构 | Microsoft Docs
description: 本文提供了有关 Azure Monitor for SAP Solutions 的常见问题解答
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 69e5f3f454721681505d2a748aa2738f579146b8
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2021
ms.locfileid: "109754130"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor for SAP Solutions（预览版）

## <a name="overview"></a>概述

Azure Monitor for SAP Solutions 是面向在 Azure 上运行 SAP 环境的客户的 Azure 本机监视产品。 此产品适用于 [SAP on Azure 虚拟机](./hana-get-started.md)和 [SAP on Azure 大型实例](./hana-overview-architecture.md)。
利用 Azure Monitor for SAP Solutions，客户可以在一个中心位置收集来自 Azure 基础结构和数据库的遥测数据，并直观地关联遥测数据，以便更快地进行故障排除。

Azure Monitor for SAP Solutions 通过 Azure 市场提供。 它提供简单、直观的设置体验，只需单击几下，即可为 Azure Monitor for SAP Solutions 部署资源（称为 SAP Monitor 资源）。

客户可以通过为组件添加相应的提供程序，监视 SAP 布局（如 Azure 虚拟机、高可用性群集、SAP HANA 数据库、SAP NetWeaver 等）的不同组件。

支持的基础结构：

- Azure 虚拟机
- Azure 大型实例

支持的数据库：
- SAP HANA 数据库
- Microsoft SQL Server

Azure Monitor for SAP Solutions 使用现有 [Azure Monitor](../../../azure-monitor/overview.md) 功能（如 Log Analytics 和[工作簿](../../../azure-monitor/visualize/workbooks-overview.md)）来提供更多监视功能。 客户可以通过编辑 Azure Monitor for SAP Solutions 提供的默认工作簿来创建[自定义可视化效果](../../../azure-monitor/visualize/workbooks-overview.md#getting-started)，使用 Azure Log Analytics 工作区编写[自定义查询](../../../azure-monitor/logs/log-analytics-tutorial.md)和创建[自定义警报](../../../azure-monitor/alerts/alerts-log.md)，利用[灵活的保留期](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)，并将监视数据与票证系统连接。

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Azure Monitor for SAP Solutions 收集哪些数据？

Azure Monitor for SAP Solutions 中的数据收集取决于客户配置的提供程序。 公共预览版期间，将收集以下数据。

高可用性 Pacemaker 群集遥测：
- 节点、资源和 SBD 设备状态
- Pacemaker 位置约束
- 仲裁投票和响铃状态
- [其他](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA 遥测：
- CPU、内存、磁盘和网络利用率
- HANA 系统复制 (HSR)
- HANA 备份
- HANA 主机状态
- 索引服务器和名称服务器角色

Microsoft SQL Server 遥测：
- CPU、内存、磁盘使用率
- 主机名、SQL 实例名称、SAP 系统 ID
- 一段时间内的批处理请求、编译和页生存期
- 一段时间内成本最高的 10 个 SQL 语句
- SAP 系统中排名前 12 的最大表
- SQL Server 错误日志中记录的问题
- 一段时间内的阻塞进程和 SQL 等待统计信息

操作系统遥测 (Linux) 
- CPU 利用率、分支计数、正在运行的进程和阻塞的进程。 
- 内存利用率和已利用、已缓存、已缓冲内存间的分配。 
- 交换利用率、分页和交换速率。 
- 文件系统利用率、每个块设备的读取和写入的字节数。 
- 每个块设备的读取/写入延迟。 
- 正在进行的 I/O 计数、永久性内存读取/写入字节数。 
- 网络数据包传入/传出、网络字节传入/传出 

SAP NetWeaver 遥测：

- SAP 系统和应用程序服务器可用性，包括调度程序、ICM、网关、消息服务器、排队服务器、IGS 监视器的实例进程可用性
- 工作进程利用率统计信息和趋势
- 排队锁定统计信息和趋势
- 队列利用率统计信息和趋势

## <a name="data-sharing-with-microsoft"></a>与 Microsoft 共享的数据

Azure Monitor for SAP Solutions 收集系统元数据，以便为 Azure 上的 SAP 客户提供改进的支持。 未收集任何 PII/EUII。
客户可以通过从下拉列表中选择“共享”，在创建 Azure Monitor for SAP Solutions 资源时启用与 Microsoft 共享数据。
强烈建议客户启用数据共享，因为它可以为 Microsoft 支持和工程团队提供有关客户环境的更多信息，并为任务关键型 SAP on Azure 客户提供改进的支持。

## <a name="architecture-overview"></a>体系结构概述

下图在较高层面上解释了 Azure Monitor for SAP Solutions 如何从 SAP HANA 数据库收集遥测。 该体系结构与将 SAP HANA 部署在 Azure 虚拟机还是 Azure 大型实例上无关。

![Azure Monitor for SAP Solutions 体系结构](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

此体系结构的关键组件为：
- Azure 门户 – 客户的起点。 客户可以在 Azure 门户中导航到市场，并发现 Azure Monitor for SAP Solutions
- Azure Monitor for SAP Solutions 资源 – 客户用于查看监视遥测的登陆位置
- 受管理资源组 – 作为 Azure Monitor for SAP Solutions 资源部署的一部分自动部署。 部署在受管理资源组中的资源可帮助收集遥测数据。 部署的关键资源及其用途如下：
   - Azure 虚拟机：也称为收集器 VM。 这是一个 Standard_B2ms VM。 此 VM 的主要目的是托管监视有效负载。 监视有效负载是指从源系统收集遥测数据并将收集的数据传输到监视框架的逻辑。 在上面的关系图中，监视有效负载包含通过 SQL 端口连接到 SAP HANA 数据库的逻辑。
   - [Azure 密钥保管库](../../../key-vault/general/basic-concepts.md)：部署此资源以安全地保存 SAP HANA 数据库凭据并存储有关[提供程序](./azure-monitor-providers.md)的信息。
   - Log Analytics 工作区：遥测数据驻留的目的地。
      - 可视化效果是通过使用 [Azure 工作簿](../../../azure-monitor/visualize/workbooks-overview.md)，基于 Log Analytics 中的遥测而生成的。 客户可以自定义可视化效果。 客户还可以将工作簿或工作簿中的特定可视化效果固定到 Azure 仪表板，以实现自动刷新功能，最小粒度为 30 分钟。
      - 在部署时，客户可以通过选择此选项，在与 SAP 监视器资源相同的订阅中使用其现有工作区。
      - 客户可以使用 Kusto 查询语言 (KQL) 对 Log Analytics 工作区内的原始表运行[查询](../../../azure-monitor/logs/log-query-overview.md)。 查看 *自定义日志*。

> [!Note]
> 客户负责修补和维护部署在受管理资源组中的 VM。

> [!Tip]
> 如果现有 Log Analytics 工作区被部署到了与 Azure Monitor for SAP Solutions 资源相同的 Azure 订阅，则客户可以选择使用该工作区进行遥测收集。

### <a name="architecture-highlights"></a>体系结构亮点

下面是体系结构的主要亮点：
 - 多实例 - 客户可以使用 Azure Monitor for SAP Solutions 的单个资源，在 VNET 中跨多个 SAP SID 为给定组件类型的多个实例（例如，HANA DB、HA 群集、Microsoft SQL Server、SAP NetWeaver）创建监视器。
 - 多提供程序 - 上述体系结构关系图显示了作为示例的 SAP HANA 提供程序。 同样地，客户可以为相应的组件（例如，HANA DB、HA 群集、Microsoft SQL Server、SAP NetWeaver）配置更多的提供程序，以从这些组件中收集数据。
 - 开放源代码 - Azure Monitor for SAP Solutions 的源代码可在 [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions) 中找到。 客户可以参阅提供程序代码，了解有关该产品、贡献或分享反馈的详细信息。
 - 可扩展查询框架 - 用于收集遥测数据的 SQL 查询以 [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) 格式编写。 可以轻松添加更多用于收集更多遥测数据的 SQL 查询。 客户可以通过本文档结尾处的链接留下反馈意见，或联系其客户团队，请求将特定的遥测数据添加到 Azure Monitor for SAP Solutions。

## <a name="pricing"></a>定价
Azure Monitor for SAP Solutions 为免费产品（无许可费用）。 客户只需为受管理资源组中的基础组件付费。

## <a name="next-steps"></a>后续步骤

了解提供程序并创建第一个 Azure Monitor for SAP Solutions 资源。
 - 了解有关[提供程序](./azure-monitor-providers.md)的详细信息
 - [使用 Azure PowerShell 部署 Azure Monitor for SAP Solutions](azure-monitor-sap-quickstart-powershell.md)
 - 是否对 Azure Monitor for SAP Solutions 有疑问？ 请查看[常见问题解答](./azure-monitor-faq.md)部分
