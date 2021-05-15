---
title: Hyper-v 迁移在 Azure Migrate 中是否有效？
description: 了解如何使用 Azure Migrate 进行 Hyper-V 迁移
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751013"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-V 复制的工作原理？

本文概述了使用 Azure Migrate Server 迁移工具迁移 Hyper-V VM 时使用的体系结构和过程。

[Azure Migrate](migrate-services-overview.md) 提供一个中心用于跟踪本地应用、工作负荷与私有云/公有云 VM 的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

## <a name="agentless-migration"></a>无代理迁移

Azure Migrate 服务器迁移工具使用针对 Hyper-V 进行优化的迁移工作流为本地 Hyper-V VM 提供无代理复制。 仅在 Hyper-V 主机或群集节点上安装软件代理。 无需在 Hyper-V VM 上安装任何软件。

## <a name="server-migration-and-azure-site-recovery"></a>服务器迁移和 Azure Site Recovery

Azure Migrate Server 迁移是一种用于将本地工作负载和基于云的 VM 迁移到 Azure 的工具。 Site Recovery 是一种灾难恢复工具。 这些工具共享一些用于数据复制的常见技术组件，但其用途不同。 


## <a name="architectural-components"></a>体系结构组件

![关系图显示了一个源 Hyper-V 网络，包含一个用于 Microsoft Azure 的 H T T P S 数据通道，表中解释了详细信息。](./media/hyper-v-replication-architecture/architecture.png)



**组件** | **部署** | 
--- | --- 
**复制提供程序** | Microsoft Azure Site Recovery 提供程序安装在 Hyper-v 主机上，并已注册到 Azure 迁移服务器迁移。<br/> 提供程序会协调 Hyper-V VM 的复制。
**恢复服务代理** | Microsoft Azure 恢复服务代理处理数据复制。 它与提供程序协同工作，以将数据从 Hyper-V VM 复制到 Azure。<br/> 复制的数据将上传到 Azure 订阅中的存储帐户。 服务器迁移工具将处理复制的数据，并将其应用于订阅中的副本磁盘。 迁移时，副本磁盘用于创建 Azure VM。

- 组件由单个安装程序文件进行安装，可从门户中的 Azure Migrate 服务器迁移下载。
- 提供程序和设备使用出站 HTTPS 端口 443 连接与 Azure Migrate 服务器迁移通信。
- 来自提供程序和代理的通信是安全的，且经过加密。


## <a name="replication-process"></a>复制过程

1. 启用 Hyper-V VM 复制时，将开始初始复制。
2. 生成一个 Hyper-V VM 快照。
3. VM 上的 VHD 是逐一复制的，直至全部复制到 Azure 为止。 初始复制时间取决于 VM 大小和网络带宽。
4. 使用 Hyper-V 副本跟踪在初始复制期间发生的磁盘更改，并将这些更改存储在日志文件中（hrl 文件）。
    - 日志文件与磁盘位于相同的文件夹中。
    - 每个磁盘都有一个关联的 hrl 文件，该文件将发送到辅助存储器。
    - 当初始复制正在进行时，快照和日志将占用磁盘资源。
4. 在初始复制完成后，将删除 VM 快照，并开始增量复制。
5. 增量磁盘更改将在 hrl 文件中进行跟踪。 恢复服务代理定期将复制日志上传到 Azure 存储帐户。


## <a name="performance-and-scaling"></a>性能和缩放

Hyper-V 的复制性能受到各种因素的影响，这些因素包括：VM 大小、数据更改率（变动）、Hyper-V 主机上用于日志文件存储的可用空间、上传复制数据的带宽，以及 Azure 中的目标存储。

- 如果要同时复制多台计算机，请使用适用于 Hyper-V 的 [Azure Site Recovery 部署规划器](../site-recovery/hyper-v-deployment-planner-overview.md)来帮助优化复制。
- 按照容量规划 Hyper-v 复制，并在 Azure 存储帐户上分配复制。

### <a name="control-upload-throughput"></a>控制上传吞吐量

可以在每个 Hyper-V 主机上限制用于将数据上传到 Azure 的带宽量。 请小心。 如果将值设置得过低，则会对复制产生不利影响，并延迟迁移。


1. 登录到 Hyper-V 主机或群集节点。
2. 运行 **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**，以打开 Microsoft AZURE Backup MMC 管理单元。
3. 在该管理单元中，选择“更改属性”。 
4. 在“限制”上，选择“为备份操作启用 Internet 带宽使用限制”。 设置工作和非工作小时数限制。 有效范围为 512 Kbps 到 1,023 Mbps。
I

### <a name="influence-upload-efficiency"></a>影响上传效率

如果有用于复制的备用带宽，并且想要增加上载，则可以增加为上载任务分配的线程数，如下所示：

1. 用 Regedit 打开注册表。
2. 导航到注册表项 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. 增加每个复制 VM 用于数据上传的线程数的值。 默认值为 4，最大值为 32。 




## <a name="next-steps"></a>后续步骤

使用 Azure Migrate 服务器迁移尝试 [Hyper-V 迁移](tutorial-migrate-hyper-v.md)。
