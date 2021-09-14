---
title: Azure Migrate 中的物理服务器迁移支持
description: 了解 Azure Migrate 中的物理服务器迁移支持。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 696db5ef934dd0aca5a7485e7d40bf85d0cd8cb4
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473291"
---
# <a name="support-matrix-for-physical-server-migration"></a>物理服务器迁移的支持矩阵

本文汇总了使用 [Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)将物理服务器迁移到 Azure 的支持设置和限制。 如果要查找有关如何评估物理服务器以便迁移到 Azure 的信息，请查看[评估支持矩阵](migrate-support-matrix-physical.md)。

## <a name="migrating-machines-as-physical"></a>将计算机作为物理服务器迁移

可以使用基于代理的复制将本地计算机作为物理服务器迁移。 使用此工具可将各种计算机迁移到 Azure：

- 本地物理服务器。
- Xen、KVM 等平台虚拟化的 VM。
- Hyper-V VM 或 VMware VM（如果由于某种原因不想使用标准 [Hyper-V](tutorial-migrate-hyper-v.md) 或 [VMware](server-migrate-overview.md) 流）。
- 在私有云中运行的 VM。
- 在 Amazon Web Services (AWS) 或 Google Cloud Platform (GCP) 等公有云中运行的 VM。


## <a name="migration-limitations"></a>迁移限制

一次最多可选择 10 台计算机进行复制。 如果要迁移更多计算机，请以 10 个一组的形式进行复制。


## <a name="physical-server-requirements"></a>物理服务器要求

下表汇总了对要使用基于代理的迁移进行迁移的物理服务器的支持。

**支持** | **详细信息**
--- | ---
**计算机工作负载** | Azure Migrate 支持迁移支持的计算机上运行的任何工作负载（如 Active Directory、SQL Server 等）。
**操作系统** | 有关最新信息，请查看 Site Recovery 的[操作系统支持](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure Migrate 提供相同的操作系统支持。
**Linux 文件系统/来宾存储** | 有关最新信息，请查看 Site Recovery 的 [Linux 文件系统支持](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。 Azure Migrate 提供相同的 Linux 文件系统支持。
**网络/存储** | 有关最新信息，请查看 Site Recovery 的[网络](../site-recovery/vmware-physical-azure-support-matrix.md#network)和[存储](../site-recovery/vmware-physical-azure-support-matrix.md#storage)先决条件。 Azure Migrate 提供相同的网络/存储要求。
**Azure 要求** | 有关最新信息，请查看 Site Recovery 的 [Azure 网络](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[存储](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[计算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)要求。 Azure Migrate 具有相同的物理服务器迁移要求。
**移动服务** | 必须在要迁移的每台计算机上安装出行服务代理。
**UEFI 启动** | 支持。 基于 UEFI 的计算机将迁移到 Azure 第 2 代 VM。  <br/><br/> OS 磁盘应最多有四个分区，卷应采用 NTFS 设置格式。
**UEFI - 安全启动**         | 不支持迁移。
**目标磁盘** | 计算机只能迁移到 Azure 中的托管磁盘（标准 HDD、标准 SSD、高级 SSD）。
**磁盘大小** | 第 1 代 VM 的 OS 磁盘最大为 2 TB；第 2 代 VM 的 OS 磁盘最大为 4 TB；数据磁盘为 32 TB。
**磁盘限制** |  每台计算机最多 63 个磁盘。
**加密磁盘/卷** |  具有加密磁盘/卷的计算机不支持用于迁移。
**共享磁盘群集** | 不支持。
**独立磁盘** | 支持。
**传递磁盘** | 支持。
**NFS** | 不会复制装载为计算机上的卷的 NFS 卷。
**iSCSI 目标** | 具有 iSCSI 目标的计算机不支持用于无代理迁移。
**多路径 IO** | 不支持。
**成组 NIC** | 不支持。
**IPv6** | 不支持。
PV 驱动程序/XenServer 工具 | 不支持。



## <a name="replication-appliance-requirements"></a>复制设备要求

如果在物理服务器上手动设置复制设备，请确保它符合表中汇总的要求。 使用 Azure Migrate 中心提供的 OVA 模板将 Azure Migrate 复制设备设置为 VMware VM 时，该设备将使用 Windows Server 2016 进行设置，并符合支持要求。 

- 了解[复制设备要求](migrate-replication-appliance.md#appliance-requirements)。
- MySQL 必须安装在设备上。 了解[安装选项](migrate-replication-appliance.md#mysql-installation)。
- 了解复制设备需要访问的 [URL](migrate-replication-appliance.md#url-access)。

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的所有本地 VM 必须满足此表中汇总的 Azure VM 要求。 Site Recovery 运行复制先决条件检查时，如果不符合某些要求，检查将会失败。

**组件** | **要求** | **详细信息**
--- | --- | ---
来宾操作系统 | 验证受支持的操作系统。<br/> 可以迁移在受支持的操作系统上运行的任何工作负载。 | 如果不支持，检查会失败。
来宾操作系统体系结构 | 64 位。 | 如果不支持，检查会失败。
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 1 | 如果不支持，检查会失败。
数据磁盘计数 | 64 或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 最多 32 TB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。
VM 名称 | 1 到 63 个字符。<br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。
在迁移 Windows 后进行连接 | 若要在迁移后连接到运行 Windows 的 Azure VM，请执行以下操作：<br/> - 在迁移之前，在本地 VM 上启用 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP  。<br/> 为了通过站点到站点 VPN 进行访问，启用 RDP 并在“Windows 防火墙” -> “允许的应用和功能”中同意域和专用网络使用 RDP。 此外，检查操作系统的 SAN 策略是否已设置为 OnlineAll。 [了解详细信息](prepare-for-migration.md)。 |
在迁移后进行连接 - Linux | 若要在使用 SSH 迁移后连接到 Azure VM，请执行以下操作：<br/> 在迁移之前，请在本地计算机上检查安全外壳服务是否设置为“启动”，以及防火墙规则是否允许 SSH 连接。<br/> 在 Azure VM 上执行故障转移后，允许已故障转移的 VM 及其所连接 Azure 子网上的网络安全组规则与 SSH 端口建立传入连接。 此外，为 VM 添加公共 IP 地址。 |  


## <a name="next-steps"></a>后续步骤

[迁移](tutorial-migrate-physical-virtual-machines.md)物理服务器。
