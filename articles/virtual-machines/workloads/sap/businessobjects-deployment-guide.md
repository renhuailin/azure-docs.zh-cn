---
title: Azure 上的 SAP BusinessObjects BI 平台部署 | Microsoft Docs
description: 在 Azure 上规划、部署和配置 SAP BusinessObjects BI 平台
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: a489b8fd416c750071fa5e292bf916f23c83dab5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747944"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Azure 上的 SAP BusinessObjects BI 平台规划和实现指南

## <a name="overview"></a>概述

本指南的目的是提供有关在 Azure 上规划、部署和配置 SAP BusinessObjects BI 平台（也称为 SAP BOBI 平台）的准则。 本指南介绍了与 SAP BOBI 平台相关的常见 Azure 服务和功能。 本指南未详尽列出所有可能的配置选项。 它介绍了常用于典型部署场景的解决方案。

本指南不用于替代标准 SAP BOBI 平台安装和管理指南、操作系统或任何数据库文档。

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>在 Azure 上规划和实施 SAP BusinessObjects BI 平台

Microsoft Azure 提供了各种服务（包括计算、存储、网络等等），使企业无需经过冗长的采购周期即可构建其应用程序。 Azure 虚拟机 (VM) 帮助公司根据其业务需要，为各种 SAP 应用程序（例如基于 SAP NetWeaver 的应用程序、SAP Hybris、SAP BusinessObjects BI 平台）部署可缩放的按需计算资源。 Azure 还支持跨界连接，使公司能够将 Azure 虚拟机集成到其本地域、私有云和 SAP 系统布局中。

本文档提供了有关 Azure 上的 SAP BusinessObjects BI 平台的规划和实施注意事项的指导。 本文是对 SAP 安装文档和 SAP Note 的补充，这些文档是用于安装和部署 SAP BOBI 的主要资源。

### <a name="architecture-overview"></a>体系结构概述

SAP BusinessObjects BI 平台是一个自包含系统，可以存在于单个 Azure 虚拟机上，也可以扩展到由运行不同组件的许多 Azure 虚拟机构成的群集中。 SAP BOBI 平台包含六个概念层：客户端层、Web 层、管理层、存储层、处理层和数据层。 （有关每个层的详细信息，请参阅 [SAP BusinessObjects Business 智能平台](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US)帮助门户中的“管理员指南”）。 下面是有关每个层的概要详细信息：

- **客户端层：** 它包含与 BI 平台进行交互以提供各种报告、分析和管理功能的所有桌面客户端应用程序。
- **Web 层：** 它包含部署到 JAVA Web 应用程序服务器的 Web 应用程序。 Web 应用程序通过 Web 浏览器向最终用户提供 BI 平台功能。
- **管理层：** 它协调并控制构成 BI 平台的所有组件。 它包括中央管理服务器 (CMS) 和事件服务器以及关联的服务
- **存储层：** 它负责处理文件，例如文档和报表。 它还处理报表缓存，以便在用户访问报表时节省系统资源。
- **处理层：** 它分析数据，并生成报表和其他输出类型。 它是访问包含报表数据的数据库的唯一层。
- **数据层：** 它包含承载着 CMS 系统数据库和审核数据存储的数据库服务器。

SAP BI 平台包含一台或多台主机上运行的服务器的集合。 必须根据规模、业务需求和环境类型选择正确的部署策略。 对于较小的安装（例如开发或测试），你可以将单个 Azure 虚拟机用于 Web 应用程序服务器、数据库服务器和所有 BI 平台服务器。 如果你使用的是 Azure 中的数据库即服务 (DBaaS) 产品/服务，则数据库服务器将独立于其他组件运行。 对于中型和大型安装，你可以让服务器在多个 Azure 虚拟机上运行。

下图中显示了 Azure 虚拟机上的 SAP BOBI 平台大规模部署的体系结构，其中每个组件都是分布式的，并且放置在当发生服务中断时能够支撑故障转移的可用性集。

![Azure 上的 SAP BusinessObjects BI 平台体系结构](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>体系结构详细信息

- 负载均衡器

  在 SAP BOBI 多实例部署中，Web 应用程序服务器（或 Web 层）在两个或更多主机上运行。 若要在 Web 服务器之间均匀分配用户负载，可以在最终用户与 Web 服务器之间使用负载均衡器。 在 Azure 中，可以使用 [Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)或 [Azure 应用程序网关](../../../application-gateway/overview.md)来管理发往 Web 服务器的流量。

- Web 应用程序服务器

  Web 服务器承载着 SAP BOBI 平台的 Web 应用程序（例如 CMC 和 BI 启动面板）。 若要实现 Web 服务器的高可用性，必须至少部署两个 Web 应用程序服务器，以管理冗余和负载均衡。 在 Azure 中，可将这些 Web 应用程序服务器放置在可用性集或可用性区域中，以提高可用性。

  Tomcat 是 SAP BI 平台的默认 Web 应用程序。 若要实现 tomcat 的高可用性，请在 Azure 中启用使用[静态成员身份侦听器](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes)的会话复制。 它可确保即使 tomcat 服务中断，用户也可以访问 SAP BI Web 应用程序。

  > [!Important]
  > 默认情况下，Tomcat 使用多播 IP 和端口进行群集，而这在 Azure 上不受支持 (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907))。

- BI 平台服务器

  BI 平台服务器包含属于 SAP BOBI 应用程序一部分的所有服务（管理层、处理层和存储层）。 当 Web 服务器收到请求时，它会检测每个 BI 平台服务器（具体而言是群集中的所有 CMS 服务器）并自动对其请求进行负载均衡。 如果其中一个 BI 平台主机发生故障，则 Web 服务器会自动将请求发送到其他主机。

  若要实现 BI 平台的高可用性或冗余，必须在至少两个 Azure 虚拟机中部署该应用程序。 通过调整大小，你可以缩放 BI 平台，使其在更多的 Azure 虚拟机上运行。

- 文件存储库服务器 (FRS)

  文件存储库服务器包含已创建的所有报表和其他 BI 文档。 在多实例部署中，BI 平台服务器在多个虚拟机上运行，并且每个 VM 都应该有权访问这些报表和其他 BI 文档。 因此，需要在所有 BI 平台服务器之间共享文件系统。

  在 Azure 中，可将 [Azure 高级文件存储](../../../storage/files/storage-files-introduction.md)或 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)用于文件存储库服务器。 这两种 Azure 服务都有内置的冗余。

  > [!Important]
  > Azure 文件存储的 SMB 协议已公开发布，但针对 Azure 文件存储的 NFS 协议支持目前为预览版。 有关详细信息，请参阅[对 Azure 文件存储的 NFS 4.1 支持现在为预览版](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- CMS 和审核数据库
  
  SAP BOBI 平台需要一个数据库来存储其系统数据，该数据库称为 CMS 数据库。 它用来存储 BI 平台信息，例如用户、服务器、文件夹、文档、配置和身份验证详细信息。

  Azure 提供了 [MySQL 数据库](https://azure.microsoft.com/services/mysql/)和 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)数据库即服务 (DBaaS) 产品/服务，该产品/服务可用于 CMS 数据库和审核数据库。 由于这是一种 PaaS 产品/服务，因此客户无需担心数据库的操作、可用性和维护。 客户还可以根据其业务需求为 CMS 和审核存储库选择自己的数据库。

## <a name="support-matrix"></a>支持矩阵

本部分介绍了 Azure 中对各种 SAP BOBI 组件（例如 SAP BusinessObjects BI 平台版本、操作系统和数据库）的支持性。

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 平台

使用 Azure 基础结构即服务 (IaaS)，你可以在 Azure 计算上部署和配置 SAP BusinessObjects BI 平台。 它支持以下版本的 SAP BOBI 平台 -

- SAP BusinessObjects BI 平台 4.3
- SAP BusinessObjects BI 平台 4.2 SP04+
- SAP BusinessObjects BI 平台 4.1 SP05+

SAP BI 平台可以在不同的操作系统和数据库上运行。 可以在 SAP BOBI 的[产品可用性矩阵](https://apps.support.sap.com/sap/support/pam)中找到各个操作系统和数据库版本对 SAP BOBI 平台的支持性。

### <a name="operating-system"></a>操作系统

Azure 支持使用以下操作系统进行 SAP BusinessObjects BI 平台部署。

- Microsoft Windows 服务器
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

[SAP BusinessObjects BI 平台的产品可用性矩阵 (PAM)](https://support.sap.com/pam) 中列出的操作系统版本受支持，前提是它们与 Azure 基础结构兼容，可在 Azure 基础结构上运行。

### <a name="databases"></a>数据库

BI 平台需要将数据库用于 CMS 和审核数据存储，该存储可以在 [SAP 产品可用性矩阵](https://support.sap.com/pam)中列出的任何受支持数据库上安装，这些数据库包括：

- Microsoft SQL Server

- [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)（仅受 Windows 上的 SAP BOBI 平台支持的数据库）

  它是一个完全托管的 SQL Server 数据库引擎，基于最新的稳定 SQL Server Enterprise Edition。 Azure SQL 数据库可处理大多数数据库管理功能（例如升级、修补和监视），无需用户参与。 使用 Azure SQL 数据库，可以为 Azure 中的应用程序和解决方案创建高度可用且高性能的数据存储层。 有关更多详细信息，请查看 [Azure SQL 数据库](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)文档。

- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)（遵循与 SAP PAM 中的 MySQL AB 相同的兼容性准则）

  它是一种由 MySQL 社区版提供支持的关系数据库服务。 作为完全托管的数据库即服务 (DBaaS) 产品/服务，它能够处理任务关键型工作负荷，并提供可预测的性能和动态可伸缩性。 它提供内置的高可用性、自动备份、软件修补、自动故障检测和最长可达 35天的时间点还原，这大大减少了操作任务。 有关更多详细信息，请查看 [Azure Database for MySQL](../../../mysql/overview.md) 文档。

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle（有关版本和限制，请查看 SAP Note [2039619](https://launchpad.support.sap.com/#/notes/2039619)）

- MaxDB

本文档介绍了 **在具有 Azure SQL 数据库的 Windows 上部署 SAP BOBI 平台** 和 **在具有 Azure Database for MySQL 的 Linux 上部署 SAP BOBI 平台** 的准则。 这也是我们建议用于在 Azure 上运行 SAP BusinessObjects BI 平台的方法。

## <a name="sizing"></a>调整大小

调整大小是一个确定硬件要求（以便高效运行应用程序）的过程。 对于 SAP BOBI 平台，需要使用名为 [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer) 的 SAP 大小调整工具来完成大小调整。 此工具基于输入提供 SAPS，后者需要映射到适用于 SAP 的经过认证的 Azure 虚拟机类型。 SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) 提供了受支持 SAP 产品和 Azure VM 类型以及 SAPS 的列表。 有关调整大小的详细信息，请参阅 [SAP BI 大小调整指南](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons)。

为满足 SAP BOBI 平台的存储需求，Azure 提供了各种类型的[托管磁盘](../../managed-disks-overview.md)。 对于 SAP BOBI 安装目录，建议使用高级托管磁盘，对于在虚拟机上运行的数据库，请按照[适用于 SAP 工作负载的 DBMS 部署](dbms_guide_general.md)中提供的指导进行操作。

Azure 支持适用于 SAP BOBI 平台数据层的两种 DBaaS 产品/服务 - [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database)（在 Windows 上运行的 BI 应用程序）和 [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)（在 Linux 和 Windows 上运行的 BI 应用程序）。 因此，你可以根据大小调整结果选择最适合你的需求的购买模型。

> [!Tip]
> 若要快速调整大小，请考虑 800 SAP = 1 vCPU，同时将 SAP BOBI 平台数据库层的 SAPS 结果映射到 Azure 数据库即服务（Azure SQL 数据库或 Azure Database for MySQL）。

### <a name="sizing-models-for-azure-sql-database"></a>Azure SQL 数据库的大小调整模型

Azure SQL 数据库提供了以下三种购买模型：

- 基于 vCore

  它允许选择 vCore 数、内存容量，以及存储的容量和速度。 基于 vCore 的购买模型还允许使用[适用于 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来节省成本。 此模型适用于注重灵活性、控制度和透明度的客户。

  VCore 模型提供了三个[服务层级选项](../../../azure-sql/database/service-tiers-vcore.md#service-tiers)，包括：“常规用途”、“业务关键”和“超大规模”。 服务层级定义了与可用性和灾难恢复相关的存储体系结构、空间、I/O 限制以及业务连续性选项。 下面是有关每个服务层级选项的概要详细信息 -

  1. **常规用途** 服务层级最适合业务工作负载。 它提供预算导向的、均衡的可缩放计算和存储选项。 有关详细信息，请参阅[资源选项和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)。
  2. **业务关键** 服务层级使用多个独立副本为业务应用程序提供最强的失败后复原能力，为每个数据库副本提供最高的 I/O 性能。 有关详细信息，请参阅[资源选项和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)。
  3. **超大规模** 服务层级最适合具有高度可缩放存储和读取缩放要求的业务工作负载。 它通过允许配置多个独立数据库副本来提供更高的失败后复原能力。 有关详细信息，请参阅[资源选项和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)。

- 基于 DTU

  基于 DTU 的购买模型在三个服务层级中提供计算、内存和 I/O 资源的组合，以支持轻型和重型数据库工作负载。 每个层级中的不同计算大小提供这些资源的不同组合，你可以向其添加更多的存储资源。 它最适合希望获得简单预配置资源选项的客户。

  基于 DTU 的购买模型中的[服务层级](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)根据一系列具有固定随附存储量、固定备份保留期和固定价格的计算大小进行区分。

- 无服务器

  无服务器模型可根据工作负荷需求自动缩放计算，并按每秒使用的计算量计费。 当仅对存储计费时，无服务器计算层将在非活动期间自动暂停数据库；当活动返回时，它将自动恢复数据库。 有关详细信息，请参阅[资源选项和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)。
  
  它比较适合间歇性的、不可预测的在一段时间内的平均计算利用率较低的使用情况。 因此，此模型可用于非生产 SAP BOBI 部署。

> [!Note]
> 对于 SAP BOBI，可以方便地使用基于 vCore 的模型，并根据业务需求选择“常规用途”或“业务关键”服务层级。

### <a name="sizing-models-for-azure-database-for-mysql"></a>适用于 Azure database for MySQL 的大小调整模型

Azure Database for MySQL 提供了三个不同的定价层。 它们的区别在于 vCore 中的计算量、每个 vCore 的内存，以及用来存储日期的存储技术。 下面是有关选项的概要详细信息，有关各个属性的更多详细信息，请参阅 Azure Database for MySQL 的 [定价层](../../../mysql/concepts-pricing-tiers.md)。

- 基本

  它用于所需的计算和 I/O 性能较低的目标工作负载。

- 常规用途

  它适用于需要均衡的计算和内存性能以及可缩放的 I/O 吞吐量的大多数业务工作负载。

- 内存优化

  适用于高性能数据库工作负载，此类工作负载需要内存中性能来实现更快的事务处理速度和更高的并发性。

> [!Note]
> 对于 SAP BOBI，可以方便地根据业务工作负载使用“常规用途”或“内存优化”定价层。

## <a name="azure-resources"></a>Azure 资源

### <a name="choosing-regions"></a>选择区域

Azure 区域是一个数据中心或数据中心集合，其中包含用于运行和承载各种 Azure 服务的基础结构。 该基础结构中包括的大量节点充当计算节点或存储节点，或者运行网络功能。 并非所有区域都提供相同的服务。

SAP BI 平台包含的各种组件可能需要将特定的 VM 类型、存储（例如 Azure 文件存储、Azure NetApp 文件或数据库即服务 (DBaaS)）用于其数据层，某些区域中可能未提供这些 VM 类型和存储。 可以在[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)站点中了解有关 VM 类型、Azure 存储类型或其他 Azure 服务的确切信息。 如果你已在 Azure 上运行 SAP 系统，则可能已确定了区域。 在这种情况下，需要首先调查这些区域中是否提供了必需的服务，以确定 SAP BI 平台的体系结构。

### <a name="availability-zones"></a>可用性区域

可用性区域是 Azure 区域中的物理上独立的位置。 每个可用性区域都由一个或多个数据中心组成，这些数据中心都配置了独立电源、冷却和网络。

若要在 SAP BI 平台的每个层上实现高可用性，可以通过实现高可用性框架将 VM 分布在可用性区域中，这可以在 Azure 中提供最佳 SLA。 对于 Azure 中的虚拟机 SLA，请选中最新版本的[虚拟机 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。

对于数据层，Azure 数据库即服务 (DBaaS) 服务默认提供了高可用性框架。 你只需要选择区域和服务固有的高可用性、冗余性和复原能力，便可缓解计划内和计划外中断造成的数据库停机，不需要你配置任何其他组件。 有关 Azure 上受支持的 DBaaS 产品/服务的 SLA 的更多详细信息，请参阅 [Azure Database for MySQL 中的高可用性](../../../azure-sql/database/high-availability-sla.md)和 [Azure SQL 数据库的高可用性](../../../mysql/concepts-high-availability.md)。

### <a name="availability-sets"></a>可用性集

可用性集是一种逻辑分组功能，可将所部署的虚拟机 (VM) 资源相互隔离。 Azure 确保可用性集中部署的 VM 能够跨多个物理服务器、计算机架、存储单元和网络交换机运行。 如果发生硬件或软件故障，只有一部分 VM 会受到影响，整体解决方案仍会保持正常运行。 因此，当虚拟机放置在可用性集中时，Azure 结构控制器会将 VM 分布到不同的[容错](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains)域和[升级](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains)域中，以防止由于单个容错域中发生基础结构维护或故障而导致所有 VM 都无法访问。

SAP BI 平台包含许多不同的组件，在设计体系结构时，你必须确保每个组件在发生任何中断后都能够复原。 可以通过将每个组件的 Azure 虚拟机放入可用性集中来实现此目的。 请记住，当在一个可用性集中混合使用不同 VM 系列的各种 VM 时，可能会遇到问题，导致无法将某种 VM 类型包含在这样的可用性集中。 因此，如体系结构概述中重点介绍的那样，请为 SAP BI 平台的 Web 应用程序和 BI 应用程序使用不同的可用性集。

另外，Azure 缩放单元内的 Azure 可用性集可使用不限数目的更新和容错域。 因此，如果你不断将 VM 添加到单个可用性集，则会有两个或更多 VM 最终将位于同一个容错或更新域中。 有关详细信息，请参阅“适用于 SAP 的 Azure 虚拟机计划和实现”文档中的 [Azure 可用性集](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets)部分。

若要了解 Azure 可用性集的概念以及可用性集与容错域和升级域的关联方式，请阅读[管理可用性](../../availability.md)一文。

> [!Important]
> Azure 可用性区域和 Azure 可用性集的概念是互斥的。 这意味着，可以将一对或多个 VM 部署到特定的可用性区域或 Azure 可用性集中。 但不能同时部署到这两者中。

### <a name="virtual-machines"></a>虚拟机

Azure 虚拟机是一种服务产品，可用来将自定义映像作为基础结构即服务 (IaaS) 实例部署到 Azure。 它可以按需提供计算资源和存储用于托管、缩放及管理 Web 应用程序与连接的应用程序，因此简化了应用程序的维护和操作。

Azure 提供了各种虚拟机来满足你的所有应用程序需求。 但是，对于 SAP 工作负载，Azure 已将选择范围缩小到了更适合 SAP 工作负载和 SAP HANA 工作负载的各个 VM 系列。 有关更多见解，请查看 [Azure 部署支持的 SAP 软件](sap-supported-product-on-azure.md)。

根据 SAP BI 平台大小调整，你需要将你的需求映射到 Azure 中适用于 SAP 产品的受支持 Azure 虚拟机。 SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) 是一个很好的起点，其中列出了适用于 Windows 和 Linux 上的 SAP 产品的受支持 Azure VM 类型。 另请记住，除了选择纯粹受支持的 VM 类型之外，还需要检查这些 VM 类型在特定区域是否可用。 可以在[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)页上查看 VM 类型的可用性。 若要选择定价模型，可以参考[适用于 SAP 工作负载的 Azure 虚拟机](planning-guide.md#azure-virtual-machines-for-sap-workload)。

### <a name="storage"></a>存储

Azure 存储是 Azure 托管的云服务，它提供的存储的可用性、安全性、持久性、可伸缩性和冗余性都很高。 某些存储类型在 SAP 方案中的用途有限。 但是，有几种 Azure 存储类型非常适合特定的 SAP 工作负载方案或针对特定的 SAP 工作负载方案进行了优化。 有关详细信息，请参阅[适用于 SAP 工作负载的 Azure 存储类型](planning-guide-storage.md)指南，因为它重点介绍了适用于 SAP 的各种存储选项。

Azure 存储提供了可供客户使用的各种存储类型，有关这些存储类型的详细信息，请参阅 [Azure 中提供了哪些磁盘类型？](../../disks-types.md) SAP BOBI 平台使用以下 Azure 存储来构建应用程序 -

- Azure 托管磁盘

  它是由 Azure 管理的块级存储卷。 当安装在 Azure 虚拟机上时，可以将这些磁盘用于 SAP BOBI 平台应用程序服务器和数据库。 提供了各种类型的 [Azure 托管磁盘](../../managed-disks-overview.md)，但建议为 SAP BOBI 平台应用程序和数据库使用[高级 SSD](../../disks-types.md#premium-ssd)。

  在下面的示例中，为 BOBI 平台安装目录使用了高级 SSD。 对于在虚拟机上安装的数据库，你可以根据准则为数据和日志卷使用托管磁盘。 CMS 和审核数据库通常很小，它对存储性能的要求与其他 SAP OLTP/OLAP 数据库的要求不同。

- Azure 高级文件或 Azure NetApp 文件

  在 SAP BOBI 平台中，文件存储库服务器 (FRS) 指的是存储报表、universe 和连接等内容的磁盘目录，这些内容由该系统的所有应用程序服务器使用。 [Azure 高级文件](../../../storage/files/storage-files-introduction.md)或 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)存储可以用作 SAP BOBI 应用程序 FRS 的共享文件系统。 由于此存储产品/服务并非在所有区域中可用，因此请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)站点来了解最新信息。

  如果该服务在你的区域中不可用，则你可以创建 NFS 服务器，并从中将文件系统共享给 SAP BOBI 应用程序。 但你还需要考虑其高可用性。

![Azure 上的 SAP BusinessObjects BI 平台存储布局](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>网络

SAP BOBI 是一个不包含任何业务数据的报告和分析 BI 平台。 因此，系统会连接到其他数据库服务器，从中获取所有数据并为用户提供见解。 Azure 提供了一个网络基础结构，允许映射可通过 SAP BI 平台实现的所有方案，例如连接到本地系统、其他虚拟网络中的系统和其他系统。 有关详细信息，请查看[适用于 SAP 工作负载的 Microsoft Azure 网络](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)。

对于数据库即服务产品/服务，任何新创建的数据库（Azure SQL 数据库或 Azure Database for MySQL）都有一个会阻止所有外部连接的防火墙。 若要允许从 BI 平台虚拟机访问 DBaaS 服务，你需要指定一个或多个服务器级防火墙规则以启用对 DBaaS 服务器的访问权限。 有关详细信息，请参阅 Azure Database for MySQL 的[防火墙规则](../../../mysql/concepts-firewall-rules.md)和 Azure SQL 数据库的[网络访问控制](../../../azure-sql/database/network-access-controls-overview.md)部分。

## <a name="next-steps"></a>后续步骤

- [Linux 上的 SAP BusinessObjects BI 平台部署](businessobjects-deployment-guide-linux.md)
- [适用于 SAP 的 Azure 虚拟机规划和实施](planning-guide.md)
- [适用于 SAP 的 Azure 虚拟机部署](deployment-guide.md)
- [适用于 SAP 的 Azure 虚拟机 DBMS 部署](./dbms_guide_general.md)