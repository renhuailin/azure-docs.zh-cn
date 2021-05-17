---
title: Azure Migrate 服务器迁移中基于代理的迁移
description: 概述了 Azure Migrate 中基于代理的 VMware VM 迁移。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: f4f79725d0eda65ba00a44e9e7fc2a51c024eccf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864051"
---
# <a name="agent-based-migration-architecture"></a>基于代理的迁移体系结构

本文概述了使用 [Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具对 VMware VM 进行基于代理的复制所使用的体系结构和过程。

通过“Azure Migrate：服务器迁移”，可以使用以下几个选项复制 VMware VM：

- 使用基于代理的复制迁移 VM，如本文所述。
- 使用无代理复制迁移 VMware VM。 这样迁移 VM 无需在其上安装任何组件。

详细了解 VMware VM 的[选择和比较](server-migrate-overview.md)迁移方法。 


## <a name="agent-based-migration"></a>基于代理的迁移

基于代理的迁移用于将本地 VMware VM 和物理服务器迁移到 Azure。 它还可用于迁移其他本地虚拟化服务器，以及私有和公有云 VM，包括 AWS 实例和 GCP VM。 Azure Migrate 中基于代理的迁移使用 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 服务中的某个后端功能。


## <a name="architectural-components"></a>体系结构组件

此图说明了基于代理的迁移中涉及的组件。

![图中显示了用于基于代理的迁移的组件，这些组件以表格形式进行说明。](./media/agent-based-replication-architecture/architecture.png)

该表汇总了用于基于代理的迁移的组件。

**组件** | **详细信息** | **安装**
--- | --- | ---
**复制设备** | 复制设备（配置服务器/进程服务器）是在本地环境和服务器迁移之间充当桥梁的本地服务器。 该设备会发现本地服务器清单，使服务器迁移能够协调复制和迁移。 该设备有两个组件：<br/><br/> **配置服务器**：连接到服务器迁移并协调复制。<br/> **进程服务器**：处理数据复制。 进程服务器接收服务器数据，压缩并加密该数据，然后将其发送至 Azure。 在 Azure 中，服务器迁移将数据写入托管磁盘。 | 默认情况下，进程服务器与配置服务器一起安装在复制设备上。
**移动服务** | 出行服务是在想要复制和迁移的每个服务器上安装的代理。 它将复制数据从服务器发送到进程服务器。 | 用于不同版本的出行服务的安装文件位于复制设备上。 根据要复制的服务器的操作系统和版本，下载并安装所需的代理。

## <a name="mobility-service-installation"></a>移动服务安装

可以使用下述方法部署移动服务：

- **推送安装**：启用服务器保护时，进程服务器将安装出行服务。 
- **手动安装**：可以通过 UI 或命令提示符在每台计算机上手动安装出行服务。

出行服务与复制设备和复制的服务器进行通信。 如果在复制设备、进程服务器或正在复制的服务器上运行防病毒软件，则应从扫描中排除以下文件夹：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent（在安装了出行服务的 Windows 服务器上）

## <a name="replication-process"></a>复制过程

1. 启用服务器复制时，将开始对 Azure 进行初始复制。
2. 在初始复制期间，出行服务将从服务器磁盘中读取数据，并将数据发送到进程服务器。
3. 此数据用于对 Azure 订阅中的磁盘副本进行种子设定。 
4. 完成初始复制后，开始将增量更改复制到 Azure。 复制是块级、近乎连续的过程。
4. 出行服务通过与操作系统的存储子系统集成来截获写入到磁盘内存。 此方法避免了在复制服务器上执行磁盘 I/O 操作，以便进行增量复制。 
5. 在入站端口 HTTPS 9443 上将跟踪的服务器更改发送到进程服务器。 可以修改此端口。 进程服务器压缩并加密该数据，然后将其发送至 Azure。 

## <a name="ports"></a>端口

**设备** | **Connection**
--- | --- 
**复制服务器** | 为了进行复制管理，VM 上运行的出行服务需要与 HTTPS 443 入站端口上的本地复制设备通信。<br/><br/> 服务器通过 HTTPS 9443 入站端口将复制数据发送到进程服务器。 可以修改此端口。
**复制设备** | 复制设备通过 HTTPS 443 出站端口来与 Azure 协调复制。
**进程服务器** | 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。


## <a name="performance-and-scaling"></a>性能和缩放

默认情况下，部署一个同时运行配置服务器和进程服务器的复制设备。 如果只是复制几台服务器，则此部署已足够。 但是，如果要复制和迁移数百台服务器，则单个进程服务器可能无法处理所有复制流量。 在这种情况下，可以部署额外的横向扩展进程服务器。

### <a name="plan-vmware-deployment"></a>计划 VMware 部署

如果要复制 VMware VM，可以使用 [VMware Site Recovery 部署规划器](../site-recovery/site-recovery-deployment-planner.md)来帮助确定性能要求，包括每日数据变化率以及所需的进程服务器。

### <a name="replication-appliance-capacity"></a>复制设备容量

使用此表中的值来确定在部署中是否需要额外的进程服务器。

- 如果每日变化率（变动率）超过 2 TB，则部署附加进程服务器。
- 如果要复制超过 200 台服务器，请部署附加复制设备。

**CPU** | **内存** | **可用空间 - 数据缓存** | **变动率** | **复制限制**
--- | --- | --- | --- | ---
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz） | 16 GB | 300 GB | 500 GB 或更少 | < 100 台服务器 
12 个 vCPU（2 个插槽 * 6 个核心 \@ 2.5 GHz） | 18 GB | 600 GB | 501 GB 到 1 TB | 100-150 台服务器。
16 个 vCPU（2 个插槽 * 8 个核心 \@ 2.5 GHz） | 32 GB |  1 TB | 1 TB 到 2 TB | 151-200 服务器。

### <a name="sizing-scale-out-process-servers"></a>调整横向扩展进程服务器大小

如果需要部署横向扩展进程服务器，请使用此表来确定服务器大小。

**进程服务器** | **用于数据缓存的可用空间** | **变动率** | **复制限制**
--- | --- | --- | --- 
4 个 vCPU（2 个插槽 * 2 个核心 \@ 2.5 GHz），8 GB 内存 | 300 GB | 250 GB 或更少 | 最多 85 台服务器 
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz），12 GB 内存 | 600 GB | 251 GB 到 1 TB | 86-150 台服务器。
12 个 vCPU（2 个插槽 * 6 个核心 \@ 2.5 GHz），24 GB 内存 | 1 TB | 1-2 TB | 151-225 台服务器。

## <a name="throttle-upload-bandwidth"></a>限制上传带宽。

复制到 Azure 的 VMware 流量会经过特定的进程服务器。 可以通过限制作为进程服务器运行的服务器上的带宽来限制上传吞吐量。 可使用以下注册表项来影响带宽：

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 注册表值指定用于磁盘数据传输（初始或增量复制）的线程数。 使用较大的值会增加复制所用的网络带宽。 默认值为 4。 最大值为 32。 监视流量以优化值。
- 此外，还可以限制进程服务器上的带宽，如下所示：

    1. 在进程服务器上，打开 Azure 备份 MMC 管理单元。 桌面上或以下文件夹中有快捷方式：C:\Program Files\Microsoft Azure Recovery Services Agent\bin。 
    2. 在该管理单元中，选择“更改属性”。 
    3. 在“限制”上，选择“为备份操作启用 Internet 带宽使用限制”。 设置工作和非工作小时数限制。 有效范围为 512 Kbps 到 1,023 Mbps。


## <a name="next-steps"></a>后续步骤

对 [VMware](tutorial-migrate-vmware-agent.md) 或[物理服务器](tutorial-migrate-physical-virtual-machines.md)尝试进行[基于代理的迁移](tutorial-migrate-vmware-agent.md)。
