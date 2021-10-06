---
title: Azure Migrate 中的 VMware 迁移支持
description: 了解 Azure Migrate 中的 VMware VM 迁移支持。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 1217acb5911366b8e3943d414aeeeaf8a9e5e1e1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357254"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 迁移支持矩阵

本文汇总了将 VMware VM 与 [Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)集成的支持设置和限制。 如果要查找有关如何评估 VMware VM 以便迁移到 Azure 的信息，请查看[评估支持矩阵](migrate-support-matrix-vmware.md)。


## <a name="migration-options"></a>迁移选项

可以通过多种方式迁移 VMware VM：

- 使用无代理迁移：迁移 VM 且无需在其上安装任何组件。 部署 [Azure Migrate 设备](migrate-appliance.md)进行无代理迁移。
- 使用基于代理的迁移：在要复制的 VM 上安装代理。 对于基于代理的迁移，部署[复制设备](migrate-replication-appliance.md)。

查看[本文](server-migrate-overview.md)来确定你想要使用的方法。

## <a name="agentless-migration"></a>无代理迁移

本部分总结了从无代理 VMware VM 迁移到 Azure 的要求。

### <a name="vmware-requirements-agentless"></a>VMware 要求（无代理）

下表汇总了 VMware 虚拟机监控程序要求。

**VMware** | **详细信息**
--- | ---
**VMware vCenter Server** | 版本 5.5、6.0、6.5、6.7、7.0。
**VMware vSphere ESXI 主机** | 版本 5.5、6.0、6.5、6.7、7.0。
**vCenter Server 权限** | 无代理迁移使用[迁移设备](migrate-appliance.md)。 此设备需要 vCenter Server 中的这些权限：<br/><br/> - Datastore.Browse（数据存储 -> 浏览数据存储）：允许浏览 VM 日志文件来排查快照创建和删除问题。<br/><br/> - Datastore.FileManagement（数据存储 -> 低级别文件操作）：允许在数据存储浏览器中执行读取/写入/删除/重命名操作，以便排查快照创建和删除问题。<br/><br/> - VirtualMachine.Config.ChangeTracking（虚拟机 -> 磁盘更改跟踪）：允许启用或禁用 VM 磁盘的更改跟踪，以便在快照之间请求更改的数据块。<br/><br/> - VirtualMachine.Config.DiskLease（虚拟机 -> 磁盘租用）：允许对 VM 执行磁盘租用操作，以便使用 VMware vSphere 虚拟磁盘开发工具包 (VDDK) 读取磁盘。<br/><br/> - VirtualMachine.Provisioning.DiskRandomRead（虚拟机 -> 预配 -> 允许只读磁盘访问）：允许在 VM 上打开磁盘，以便使用 VDDK 读取磁盘。<br/><br/> - VirtualMachine.Provisioning.DiskRandomAccess（虚拟机 -> 预配 -> 允许磁盘访问）：允许在 VM 上打开磁盘，以便使用 VDDK 读取磁盘。<br/><br/> - VirtualMachine.Provisioning.GetVmFiles（虚拟机 -> 预配 -> 允许虚拟机下载）：允许对与 VM 关联的文件执行读取操作，以便下载日志并在发生故障时进行故障排除。<br/><br/> - VirtualMachine.State.\*（虚拟机 -> 快照管理）：允许创建和管理 VM 快照以用于复制。<br/><br/> - VirtualMachine.Interact.PowerOff（虚拟机 -> 交互 -> 关闭电源）：允许在迁移到 Azure 期间关闭 VM 电源。



### <a name="vm-requirements-agentless"></a>VM 要求（无代理）

下表汇总了 VMware VM 的无代理迁移要求。

**支持** | **详细信息**
--- | ---
**受支持的操作系统** | 可以迁移 Azure 支持的 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 和 [Linux](../virtual-machines/linux/endorsed-distros.md) 操作系统。
**Azure 中的 Windows VM** | 在迁移之前，你可能需要对 VM [进行一些更改](prepare-for-migration.md#verify-required-changes-before-migrating)。
**Azure 中的 Linux VM** | 某些 VM 可能需要经过更改才能在 Azure 中运行。<br/><br/> 对于 Linux，Azure Migrate 会自动对以下操作系统做出这些更改：<br/> - Red Hat Enterprise Linux 8、7.9、7.8、7.7、7.6、7.5、7.4、7.0、6.x <br/> - Cent OS 8、7.7、7.6、7.5、7.4、6.x</br> - SUSE Linux Enterprise Server 11、12、15 SP0、15 SP1 <br/>- Ubuntu 20.04、19.04、19.10、14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8、9 <br/> Oracle Linux 6、7.7、7.7-CI<br/> 对于其他操作系统，手动进行[所需的更改](prepare-for-migration.md#verify-required-changes-before-migrating)。
**Boot 要求** | 如果 /boot 位于专用分区上，则它应驻留在 OS 磁盘上，而不是分布在多个磁盘上。<br/> 如果 /boot 是根 (/) 分区的一部分，则“/”分区应在 OS 磁盘上，而不是分布在其他磁盘上。
**UEFI 启动** | 支持。 基于 UEFI 的 VM 将迁移到 Azure 第 2 代 VM。
**磁盘大小** | 第 1 代和第 2 代 VM 的 OS 磁盘最大为 2 TB；数据磁盘为 32 TB。
**磁盘限制** |  每个 VM 最多 60 个磁盘。
**加密磁盘/卷** | 具有加密磁盘/卷的 VM 不支持用于迁移。
**共享磁盘群集** | 不支持。
**独立磁盘** | 不支持。
**RDM/传递磁盘** | 如果 VM 具有 RDM 或传递磁盘，则这些磁盘不会复制到 Azure。
**NFS** | 不会复制装载为 VM 上的卷的 NFS 卷。
**iSCSI 目标** | 具有 iSCSI 目标的 VM 不支持用于无代理迁移。
**多路径 IO** | 不支持。
**存储 vMotion** | 不支持。 如果 VM 使用存储 vMotion，则复制将不起作用。
**成组 NIC** | 不支持。
**IPv6** | 不支持。
**目标磁盘** | VM 只能迁移到 Azure 中的托管磁盘（标准 HDD、标准 SSD、高级 SSD）。
**同时复制** | 1 台设备中每个 vCenter Server 最多可有 300 个 VM 同时复制。 如果额外部署一个[横向扩展设备](./how-to-scale-out-for-migration.md)，每个 vCenter Server 最多可有 500 个 VM 同时复制。
**自动安装 Azure VM 代理（Windows 和 Linux 代理）** | 支持 Windows Server 2008 R2 及更高版本。 <br/> 支持 RHEL6、RHEL7、CentOS7、Ubuntu 14.04、Ubuntu 16.04、Ubuntu 18.04、Ubuntu 19.04、Ubuntu 19.10、Ubuntu 20.04。

> [!Note]
> 对于 Linux VM，要成功安装 Microsoft Azure Linux 代理 (waagent)，除了 Internet 连接还请确保已安装以下程序包：
>- Python 2.6+
>- OpenSSL 1.0+
>- OpenSSH 5.3+
>- 文件系统实用程序：sfdisk、fdisk、mkfs、parted
>- 密码工具：chpasswd、sudo
>- 文本处理工具：sed、grep
>- 网络工具：ip-route

> [!TIP]
>  使用 Azure 门户，一次最多可选择 10 个 VM 来配置复制。 要复制更多的 VM，可使用门户添加要复制的 VM（按每批 10 个 VM 分批复制），也可使用 Azure Migrate PowerShell 接口配置复制。 请确保配置的同时复制的 VM 数不超过支持的同时复制的最大 VM 数量。

### <a name="appliance-requirements-agentless"></a>设备要求（无代理）

无代理迁移使用 [Azure 迁移设备](migrate-appliance.md)。 可以将设备部署为 VMware VM，具体方法为使用 OVA 模板、导入到 vCenter Server 或使用 [PowerShell 脚本](deploy-appliance-script.md)。

- 了解 VMware 的[设备要求](migrate-appliance.md#appliance---vmware)。
- 了解设备需要在[公有](migrate-appliance.md#public-cloud-urls)云和[政府](migrate-appliance.md#government-cloud-urls)云中访问的 URL。
- 在 Azure 政府中，必须[使用脚本](deploy-appliance-script-government.md)部署设备。

### <a name="port-requirements-agentless"></a>端口要求（无代理）

**设备** | **Connection**
--- | ---
设备 | 端口 443 上的出站连接，用于将复制的数据上传到 Azure，并与 Azure Migrate 服务进行通信以协调复制和迁移。
vCenter 服务器 | 端口 443 上的入站连接，可便于设备协调复制 - 创建快照、复制数据、发布快照。
vSphere/ESXI 主机 | TCP 端口 902 上的入站，可便于设备从快照复制数据。 ESXi 主机的出站端口 902。

## <a name="agent-based-migration"></a>基于代理的迁移


本部分汇总了基于代理的迁移的要求。


### <a name="vmware-requirements-agent-based"></a>VMware 要求（基于代理）

此表汇总了 VMware 虚拟化服务器的评估支持和限制。

**VMware 要求** | **详细信息**
--- | ---
**VMware vCenter Server** | 版本 5.5、6.0、6.5 或 6.7
**VMware vSphere ESXI 主机** | 版本 5.5、6.0、6.5 或 6.7
**vCenter Server 权限** | vCenter Server 的只读帐户。

### <a name="vm-requirements-agent-based"></a>VM 要求（基于代理）

下表汇总了对要使用基于代理的迁移进行迁移的 VMware VM 的支持。

**支持** | **详细信息**
--- | ---
**计算机工作负载** | Azure Migrate 支持迁移支持的计算机上运行的任何工作负载（如 Active Directory、SQL Server 等）。
**操作系统** | 有关最新信息，请查看 Site Recovery 的[操作系统支持](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure Migrate 提供相同的 VM 操作系统支持。
**Linux 文件系统/来宾存储** | 有关最新信息，请查看 Site Recovery 的 [Linux 文件系统支持](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。 Azure Migrate 具有相同的 Linux 文件系统支持。
**网络/存储** | 有关最新信息，请查看 Site Recovery 的[网络](../site-recovery/vmware-physical-azure-support-matrix.md#network)和[存储](../site-recovery/vmware-physical-azure-support-matrix.md#storage)先决条件。 Azure Migrate 提供相同的网络/存储要求。
**Azure 要求** | 有关最新信息，请查看 Site Recovery 的 [Azure 网络](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[存储](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[计算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)要求。 Azure Migrate 具有相同的 VMware 迁移要求。
**移动服务** | 必须在要迁移的每个 VM 上安装出行服务代理。
**UEFI 启动** | 支持。 基于 UEFI 的 VM 将迁移到 Azure 第 2 代 VM。
**UEFI - 安全启动**         | 不支持迁移。
**目标磁盘** | VM 只能迁移到 Azure 中的托管磁盘（标准 HDD、标准 SSD、高级 SSD）。
**磁盘大小** | 第 1 代 VM 的 OS 磁盘最大为 2 TB；第 2 代 VM 的 OS 磁盘最大为 4 TB；数据磁盘为 32 TB。
**磁盘限制** |  每个 VM 最多 63 个磁盘。
**加密磁盘/卷** | 具有加密磁盘/卷的 VM 不支持用于迁移。
**共享磁盘群集** | 不支持。
**独立磁盘** | 支持。
**传递磁盘** | 支持。
**NFS** | 不会复制装载为 VM 上的卷的 NFS 卷。
**iSCSI 目标** | 支持。
**多路径 IO** | 不支持。
**存储 vMotion** | 支持
**成组 NIC** | 不支持。
**IPv6** | 不支持。




### <a name="appliance-requirements-agent-based"></a>设备要求（基于代理）

使用 Azure Migrate 中心提供的 OVA 模板设置复制设备时，设备将运行 Windows Server 2016 并符合支持要求。 如果在物理服务器上手动设置复制设备，请确保它符合要求。

- 了解 VMware 的[复制设备要求](migrate-replication-appliance.md#appliance-requirements)。
- MySQL 必须安装在设备上。 了解[安装选项](migrate-replication-appliance.md#mysql-installation)。
- 了解复制设备需要在[公有](migrate-replication-appliance.md#url-access)云和[政府](migrate-replication-appliance.md#azure-government-url-access)云中访问的 URL。
- 查看复制设备需要访问的[端口](migrate-replication-appliance.md#port-access)。

### <a name="port-requirements-agent-based"></a>端口要求（基于代理）

**设备** | **Connection**
--- | ---
VM | 为了进行复制管理，VM 上运行的出行服务需要与 HTTPS 443 入站端口上的本地复制设备（配置服务器）通信。<br/><br/> VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在配置服务器计算机上运行）。 可以修改此端口。
复制设备 | 复制设备通过 HTTPS 443 出站端口来与 Azure 协调复制。
进程服务器 | 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。<br/> 默认情况下，进程服务器在复制设备上运行。

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure（无代理或基于代理的迁移）的所有本地 VM 必须满足此表中汇总的 Azure VM 要求。

**组件** | **要求**
--- | --- | ---
来宾操作系统 | 验证迁移支持的 VMware VM 操作系统。<br/> 可以迁移在受支持的操作系统上运行的任何工作负载。
来宾操作系统体系结构 | 64 位。
操作系统磁盘大小 | 最大 2,048 GB。
操作系统磁盘计数 | 1
数据磁盘计数 | 64 或更少。
数据磁盘大小 | 最多 32 TB
网络适配器 | 支持多个适配器。
共享 VHD | 不支持。
FC 磁盘 | 不支持。
BitLocker | 不支持。<br/><br/> 迁移计算机之前，必须先禁用 BitLocker。
VM 名称 | 1 到 63 个字符。<br/><br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。
在迁移 Windows 后进行连接 | 若要在迁移后连接到运行 Windows 的 Azure VM，请执行以下操作：<br/><br/> - 在迁移之前，在本地 VM 上启用 RDP。<br/><br/> 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP  。<br/><br/> 为了通过站点到站点 VPN 进行访问，启用 RDP 并在“Windows 防火墙” -> “允许的应用和功能”中同意域和专用网络使用 RDP。<br/><br/> 此外，检查操作系统的 SAN 策略是否已设置为 OnlineAll。 [了解详细信息](prepare-for-migration.md)。
在迁移后进行连接 - Linux | 若要在使用 SSH 迁移后连接到 Azure VM，请执行以下操作：<br/><br/> 在迁移之前，请在本地计算机上检查安全外壳服务是否设置为“启动”，以及防火墙规则是否允许 SSH 连接。<br/><br/> 在 Azure VM 上执行故障转移后，允许已故障转移的 VM 及其所连接 Azure 子网上的网络安全组规则与 SSH 端口建立传入连接。<br/><br/> 此外，为 VM 添加公共 IP 地址。  


## <a name="next-steps"></a>后续步骤

[选择](server-migrate-overview.md) VMware 迁移选项。
