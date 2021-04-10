---
title: 在 Azure 虚拟机上重新托管大型机
description: 在 Microsoft Azure 上使用虚拟机 (VM) 重新托管大型机工作负荷（如基于 IBM Z 的系统）。
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.openlocfilehash: 1baba0afc08c6a4df63c38cafe9231411ecf8f1f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048844"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>在 Azure 虚拟机上重新托管大型机

将工作负荷从大型机环境迁移到云可以使基础结构现代化，通常可以节省成本。 许多工作负荷只需进行轻微的代码更改（如更新数据库的名称）便可传输到 Azure。

一般而言，“大型机”一词是指大型计算机系统。 具体而言，绝大部分当前正在使用的大型机是 IBM System Z 服务器或可运行 MVS、DOS、VSE、OS/390 或 z/OS 的 IBM 插件兼容系统。

Azure 虚拟机 (VM) 用于隔离和管理单个实例上特定应用程序的资源。 IBM z/OS 之类的大型机使用逻辑分区 (LPARS) 来实现此目的。 大型机可能会对具有关联 COBOL 程序的 CICS 区域使用一个 LPAR，对 IBM Db2 数据库使用另一个 LPAR。 [Azure 上典型的 n 层应用程序](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)会将 Azure VM 部署到一个虚拟网络中，该虚拟网络可针对每个层细分为多个子网。

Azure VM 可以运行支持直接迁移方案的大型机仿真环境和编译器。 开发和测试通常属于需要从大型机迁移到 Azure 开发/测试环境的第一批工作负荷。 可仿真的常见服务器组件包括联机事务处理 (OLTP)、批处理和数据引入系统，如下图所示。

![Azure 上的仿真环境使你能够运行基于 z/OS 的系统。](media/01-overview.png)

某些大型机工作负荷可以相对轻松地迁移到 Azure，而另一些则可以通过合作伙伴解决方案重新托管到 Azure 上。 有关选择合作伙伴解决方案的详细指导，请访问 [Azure 大型机迁移中心](https://azure.microsoft.com/migration/mainframe/)。

## <a name="mainframe-migration"></a>大型机迁移

重新托管、重新生成、替换还是停用？ IaaS 还是 PaaS？ 若要确定适用于大型机应用程序的迁移策略，请参阅 Azure 体系结构中心的[大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)指南。

## <a name="micro-focus-rehosting-platform"></a>Micro Focus 重新托管平台

Micro Focus Enterprise Server 是目前提供的最大的大型机重新托管平台之一。 你可以使用它在 Azure 中更经济实惠的 x86 平台上运行 z/OS 工作负荷。

开始操作：

- [在 Azure 上安装 Enterprise Server 和 Enterprise Developer](./microfocus/set-up-micro-focus-azure.md)
- [在 Azure 上设置用于 Enterprise Developer 的 CICS BankDemo](./microfocus/demo.md)
- [在 Azure 上的 Docker 容器中运行 Enterprise Server](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Azure 上的 TmaxSoft OpenFrame

TmaxSoft OpenFrame 是一种常用的大型机重新托管解决方案，用于“直接迁移”场景。 Azure 上的 OpenFrame 环境适用于开发、演示、测试或生产工作负荷。

开始操作：

- [TmaxSoft OpenFrame 入门](./tmaxsoft/get-started.md)
- [下载电子书](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z Development and Test Environment (IBM zD&T) 可在 Azure 上设置一个非生产环境，该环境可用于开发、测试和演示基于 z/OS 的应用程序。

Azure 上的仿真环境可以通过应用程序开发人员控制的分发 (ADCD) 来托管不同类型的 Z 实例。 你可以在 Azure 和 Azure Stack 上运行 zD&T Personal Edition、zD&T Parallel Sysplex 和 zD&T Enterprise Edition。

开始操作：

- [在 Azure 上设置 IBM zD&T 12.0](./ibm/install-ibm-z-environment.md)
- [在 zD&T 上设置 ADCD](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM DB2 pureScale

IBM DB2 pureScale 环境为 Azure 提供了一个数据库群集。 它与原始环境并不完全相同，但它提供了与在 Parallel Sysplex 设置中运行的 IBM DB2 for z/OS 类似的可用性和规模。

若要开始，请参阅 [Azure 上的 IBM DB2 pureScale](.//ibm/ibm-db2-purescale-azure.md)。

## <a name="considerations"></a>注意事项

将大型机工作负荷迁移到 Azure 基础结构即服务 (IaaS) 时，可以从多种类型的可缩放按需计算资源（包括 Azure VM）中进行选择。 Azure 提供一系列的 [Linux](../../linux/overview.md) 和 [Windows](../../windows/overview.md) VM。

### <a name="compute"></a>计算

Azure 计算能力与大型机的能力相比更有优势。 如果考虑将大型机工作负荷转移到 Azure，请将每秒 1 百万指令 (MIPS) 的大型机指标与虚拟 CPU 进行比较。 

了解如何[将大型机计算转移到 Azure](./concepts/mainframe-compute-azure.md)。

### <a name="high-availability-and-failover"></a>高可用性和故障转移

Azure 提供基于承诺使用量的服务级别协议 (SLA)。 默认的可用性为 99% 以上，并且可以使用服务的本地复制或基于地理位置的复制来优化 SLA。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

使用 Azure IaaS（例如 VM）时，特定系统功能会提供故障转移支持，例如，故障转移群集实例和可用性集。 使用 Azure 平台即服务 (PaaS) 资源时，平台会自动处理故障转移。 示例包括 [Azure SQL 数据库](../../../azure-sql/database/sql-database-paas-overview.md)和 [Azure Cosmos DB](../../../cosmos-db/introduction.md)。

### <a name="scalability"></a>可伸缩性

大型机通常可以纵向扩展，而云环境可以横向扩展。Azure 提供了一系列的 [Linux](../../sizes.md) 和 [Windows](../../sizes.md) 大小来满足你的需求。 云还会根据确切的用户规范纵向扩展或缩减。 计算能力、存储和服务会在基于使用情况的计费模型下按需[缩放](/azure/architecture/best-practices/auto-scaling)。

### <a name="storage"></a>存储

在云中，你有一系列灵活的可缩放存储选项，并且只需为所需的内容付费。 [Azure 存储](../../../storage/common/storage-introduction.md)为数据对象提供可大规模缩放的对象存储，为云提供文件系统服务、可靠的消息传送存储，以及 NoSQL 存储。 对于 VM，托管和非托管磁盘提供持久且安全的磁盘存储。

了解如何[将大型机存储转移到 Azure](./concepts/mainframe-storage-azure.md)。

### <a name="backup-and-recovery"></a>备份和恢复

维护你自己的灾难恢复站点可能是一种代价高昂的提议。 Azure 在本地或区域级别或通过异地冗余为[备份](../../../backup/backup-overview.md)、[恢复](../../../site-recovery/site-recovery-overview.md)和[冗余](../../../storage/common/storage-redundancy.md)提供了易于实现且经济高效的选项。

## <a name="azure-government-for-mainframe-migrations"></a>适用于大型机迁移的 Azure 政府

许多公共部门实体喜欢将其大型机应用程序迁移到更新式的灵活平台。 Microsoft Azure 政府是在物理上独立的全球 Microsoft Azure 平台实例 - 已针对联邦、州和地方政府系统而打包。 它专为美国政府机构及其合作伙伴提供世界一流的安全、保护和符合性服务。

对于需要此类环境的系统，Azure 政府获得了一种暂时运营授权 (P-ATO) 来实施 FedRAMP High 影响。

若要开始，请下载[适用于大型机应用程序的 Microsoft Azure 政府云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)。

## <a name="next-steps"></a>后续步骤

要求我们的[合作伙伴](partner-workloads.md)帮助你迁移或重新托管大型机应用程序。 

另请参阅：

- [有关大型机主题的白皮书](mainframe-white-papers.md)
- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [故障排除](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [揭秘大型机到 Azure 的迁移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md