---
title: 将大型机计算转移到 Azure 虚拟机
description: Azure 计算资源相比大型机的容量更有优势，可让你迁移 IBM z14 应用程序并使其现代化。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.openlocfilehash: 32f259f20e0e24b4d5346c598e23fdc2df48dee6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556428"
---
# <a name="move-mainframe-compute-to-azure"></a>将大型机计算转移到 Azure

大型机凭借其高可靠性和可用性而备受盛誉，一直是许多企业所信赖的支柱。 人们经常认为大型机拥有几乎无限的可伸缩性和计算能力。 但是，某些企业的发展规模已经超越了最大可用大型机的能力了。 如果你就遇到了这种局面，Azure 可在灵活性、覆盖范围和基础结构成本节约方面满足你的需求。

若要在 Microsoft Azure 上运行大型机工作负载，需要知道你的大型机计算功能与 Azure 相比如何。 本文基于 IBM z14 大型机（在编写本文时的最新型号）介绍如何在 Azure 上获得类似的结果。

若要开始，请考虑对环境做出对照比较。 下图将用于运行应用程序的大型机环境与 Azure 托管环境做了比较。

![Azure 服务和仿真环境提供类似的支持和简化的迁移](media/mainframe-compute-azure.png)

大型机的能力通常用于联机事务处理 (OLTP) 系统，这些系统需要处理数千用户的数百万项更新。 这些应用程序通常使用软件完成事务处理、屏幕处理和表单输入。 它们可以使用客户信息控制系统 (CICS)、信息管理系统 (IMS) 或事务接口包 (TIP)。

如图所示，Azure 上的 TPM 仿真器可以处理 CICS 和 IMS 工作负载。 Azure 上的批处理系统仿真器执行作业控制语言 (JCL) 的角色。 大型机数据迁移到 Azure 数据库（例如 Azure SQL 数据库）。 Azure 服务或者托管在 Azure 虚拟机中的其他软件可用于系统管理。

## <a name="mainframe-compute-at-a-glance"></a>大型机计算概览

在 z14 大型机中，处理器排列在最多四个“抽屉”中。 “抽屉”只是由处理器和芯片组构成的群集。 每个抽屉可以包含 6 个活动的中央处理器 (CP) 芯片，而每个 CP 包含 10 个系统控制器 (SC) 芯片。 用 Intel x86 术语来讲，就是每个抽屉有 6 个插槽，每个插槽 10 个核心，共四个抽屉。 此体系结构为一个 z14 提供的配置最多大致相当于 24 个插槽和 240 个核心。

高速 z14 CP 提供 5.2 GHz 的时钟速度。 通常，z14 会在机箱中提供所有 CP。 可根据需要激活这些 CP。 不管实际使用情况如何，通常每月都会向客户收取至少四小时计算时间的费用。

可将大型机处理器配置为以下类型之一：

- 通用 (GP) 处理器
- System z 集成式信息处理器 (zIIP)
- Linux 集成式设备 (IFL) 处理器
- 系统辅助处理器 (SAP)
- 集成式耦合设备 (ICF) 处理器

## <a name="scaling-mainframe-compute-up-and-out"></a>纵向和横向扩展大型机计算

IBM 大型机提供纵向扩展为 240 个核心（单个系统的当前 z14 大小）的功能。 此外，IBM 大型机可以通过一项称作“耦合设备 (CF)”的功能进行横向扩展。 CF 允许多个大型机系统同时访问相同的数据。 大型机并行系统联合体 (Parallel Sysplex) 技术使用 CF 将大型机处理器分组到群集中。 编写本指南时，并行系统联合体功能支持 32 种分组方式，每种方式可以分组 64 个处理器。 可通过这种方式对最多 2,048 个处理器进行分组，以横向扩展计算容量。

CF 允许计算群集通过直接访问来共享数据。 它可用于共享锁定信息、缓存信息和共享数据资源列表。 可将使用一个或多个 CF 的并行系统联合体视为“共享一切”的横向扩展计算群集。 有关这些功能的详细信息，请参阅 IBM 网站上的 [Parallel Sysplex on IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)（IBM Z 上的并行系统联合体）。

应用程序可以使用这些功能来提供横向扩展性能和高可用性。 有关 CICS 如何使用包含 CF 的并行系统联合体的信息，请下载 [IBM CICS and the Coupling Facility: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)（IBM CICS 和耦合设备：进阶知识）红皮书。

## <a name="azure-compute-at-a-glance"></a>Azure 计算概览

有些人误以为基于 Intel 的服务器不像大型机那么强大。 但是，基于 Intel 的新式核心密集型系统的计算容量与大型机不相上下。 本部分介绍用于计算和存储的 Azure 基础结构即服务 (IaaS) 选项。 Azure 还提供平台即服务 (PaaS) 选项，但本文重点介绍可提供类似大型机容量的 IaaS 选项。

Azure 虚拟机按照各种大小和类型提供计算能力。 在 Azure 中，一个虚拟 CPU (vCPU) 大致等同于大型机上的一个核心。

目前，Azure 虚拟机大小范围提供 1 到 128 个 vCPU。 虚拟机 (VM) 类型已针对特定工作负载进行优化。 例如，以下列表显示了 VM 类型（编写本文时的最新类型）及其建议用途：

| 大小     | 类型和描述                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 系列 | 常规用途，提供 64 个 vCPU 和最高 3.5 GHz 的时钟速度                           |
| E 系列 | 内存优化，最多提供 64 个 vCPU                                                 |
| F 系列 | 计算优化，最多提供 64 个 vCPU 和 3.7 GHz 的时钟速度                       |
| H 系列 | 针对高性能计算 (HPC) 应用程序进行了优化                          |
| L 系列 | 针对 NoSQL 等数据库支持的高吞吐量应用程序进行了存储优化 |
| M 系列 | 最大的计算优化和内存优化 VM，最多提供 128 个 vCPU                        |

有关可用 VM 的详细信息，请参阅[虚拟机系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)。

一台 z14 大型机最多可以包含 240 个核心。 但是，z14 大型机几乎永远不会将所有核心用于单个应用程序或工作负载， 而是将工作负载分隔到多个逻辑分区 (LPAR) 中，并且 LPAR 有分级 — MIPS（每秒百万指令）或 MSU（百万服务单位）。 确定在 Azure 上运行大型机工作负载所需的对应 VM 大小时，请考虑到 MIPS（或 MSU）分级。

下面是一般估算值：

-   每个 vCPU 提供 150 MIPS

-   每个处理器提供 1,000 MIPS

若要为 LPAR 中的给定工作负载确定正确的 VM 大小，请先针对工作负载优化 VM。 然后确定所需的 vCPU 数目。 保守估算值为每个 vCPU 提供 150 MIPS。 例如，根据这种估算，具有 16 个 vCPU 的 F 系列 VM 提供 2400 MIPS，可轻松支持来自 LPAR 的 IBM Db2 工作负载。

## <a name="azure-compute-scale-up"></a>Azure 计算纵向扩展

M 系列 VM 可纵向扩展为 128 个 vCPU（在编写本文时）。 使用保守估算值（每个 vCPU 提供 150 MIPS），M 系列 VM 相当于能够提供大约 19,000 MIPS。 估算大型机 MIPS 的经验法则是假设每个处理器提供 1000 MIPS。 一台 z14 大型机最多可以包含 24 个处理器，可为单个大型机系统提供大约 24,000 MIPS。

最大的单个 z14 大型机可提供的 MIPS 比 Azure 中的最大可用 VM 大约高出 5,000 MIPS。 不过，比较工作负载的部署方式非常重要。 如果大型机系统既包含应用程序，也包含关系数据库，该应用程序和数据库通常会部署在同一台物理大型机上 - 位于其各自的 LPAR 中。 Azure 上的相同解决方案通常是按以下方式部署的：对应用程序使用一个 VM，对数据库使用另一个适当大小的 VM。

例如，如果 M64 vCPU 系统支持应用程序，而 M96 vCPU 用于数据库，则大约需要 150 个 vCPU - 或者大约 24,000 MIPS，如下图所示。

![比较 24,000 MIPS 的工作负载部署](media/mainframe-compute-mips.png)

方法是将 LPAR 迁移到各个 VM。 然后，Azure 可轻松纵向扩展为在单个大型机系统上部署的大多数应用程序所需的大小。

## <a name="azure-compute-scale-out"></a>Azure 计算横向扩展

基于 Azure 的解决方案的优势之一是可以横向扩展。缩放使得应用程序有几乎无限的计算容量可用。 Azure 支持通过多种方法来横向扩展计算能力：

- **跨群集负载均衡。** 在此方案中，应用程序可以使用[负载均衡器](../../../../load-balancer/load-balancer-overview.md)或资源管理器将工作负载分散到群集中的多个 VM。 如果需要更多计算容量，可将更多 VM 添加到群集中。

- **虚拟机规模集。** 在此突增方案中，应用程序可以根据 VM 的用途扩展为使用更多[计算资源](../../../../virtual-machine-scale-sets/overview.md)。 当需求下降时，规模集中的 VM 数目也可以下降，从而确保有效利用计算能力。

- **PaaS 缩放。** Azure PaaS 产品/服务可以缩放计算资源。 例如，[Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) 可以分配计算资源以适应请求量的增加。

- **Kubernetes 群集。** Azure 上的应用程序可将 [Kubernetes 群集](../../../../aks/concepts-clusters-workloads.md)用作指定资源的计算服务。 Azure Kubernetes 服务 (AKS) 是用于协调 Azure 上的 Kubernetes 节点、池和群集的一个托管服务。

若要为横向扩展计算资源选择适当的方法，必须了解 Azure 与大型机的差异。 关键在于，计算资源如何或者是否共享数据。 在 Azure 中，数据（默认情况下）通常不会由多个 VM 共享。 如果横向扩展计算群集中的多个 VM 需要数据共享，则共享数据必须驻留在支持此功能的资源中。 在 Azure 上，数据共享涉及到以下部分所述的存储。

## <a name="azure-compute-optimization"></a>Azure 计算优化

可以在 Azure 体系结构中优化每个处理层。 对每个环境使用最适当类型的 VM 和功能。 下图显示了用于在 Azure 中部署 VM 以支持使用 Db2 的 CICS 应用程序的一种可能模式。 在主站点中，以高可用性部署生产、预生产和测试 VM。 辅助站点用于备份和灾难恢复。

每个层还可提供相应的灾难恢复服务。 例如，生产和数据库 VM 可能需要热或温恢复，而开发和测试 VM 支持冷恢复。

![支持灾难恢复的高可用性部署](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>后续步骤

- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [在 Azure 虚拟机上重新托管大型机](../overview.md)
- [将大型机存储转移到 Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 资源

- [Parallel Sysplex on IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)（IBM Z 上的并行系统联合体）
- [IBM CICS and the Coupling Facility: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)（IBM CICS 和耦合设备：进阶知识）
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)（创建所需用户以安装 Db2 pureScale 功能）
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)（Db2icrt - 创建实例命令）
- [Db2 pureScale Clustered Database Solution](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)（Db2 pureScale 群集数据库解决方案）
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [适用于大型机应用程序的 Microsoft Azure 政府云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 和 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多迁移资源

- [Azure Virtual Data Center Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)（Azure 虚拟数据中心提升和迁移指南）
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
