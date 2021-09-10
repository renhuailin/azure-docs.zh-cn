---
title: Azure Migrate 中的 Hyper-V 迁移支持
description: 了解如何使用 Azure Migrate 支持 Hyper-V 迁移。
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 267b05a13d951e111f056097c6d7dc8f3edef221
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187282"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Hyper-V 迁移的支持矩阵

本文汇总了将 Hyper-V VM 与 [Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)集成的支持设置和限制。 如果要查找有关如何评估 Hyper-V VM 以便迁移到 Azure 的信息，请查看[评估支持矩阵](migrate-support-matrix-hyper-v.md)。

## <a name="migration-limitations"></a>迁移限制

一次最多可选择复制 10 个 VM。 如果要迁移更多计算机，请以 10 个一组的形式进行复制。


## <a name="hyper-v-host-requirements"></a>Hyper-V 主机要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **部署**       | Hyper-V 主机可以是独立的，也可以部署到群集中。 <br/>Hyper-V 主机上安装了 Azure 迁移复制软件（Hyper-V Replication 提供程序）。|
| **权限**           | 你需要具有 Hyper-V 主机上的管理员权限。 |
| 主机操作系统 | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2（含最新更新）。 注意：还支持这些操作系统的服务器核心安装。 |
| **其他软件要求** | .NET Framework 4.7 或更高版本 |
| **端口访问** |  HTTPS 端口 443 上的出站连接，用于发送 VM 复制数据。



## <a name="hyper-v-vms"></a>Hyper-V VM

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | Azure 支持的所有 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 和 [Linux](../virtual-machines/linux/endorsed-distros.md) 操作系统。 |
**Windows Server 2003** | 对于运行 Windows Server 2003 的 VM，需要在迁移之前[安装 Hyper-V 集成服务](prepare-windows-server-2003-migration.md)。 | 
**Azure 中的 Linux VM** | 某些 VM 可能需要经过更改才能在 Azure 中运行。<br/><br/> 对于 Linux，Azure Migrate 会自动对以下操作系统做出这些更改：<br/> - Red Hat Enterprise Linux 7.8、7.7、7.6、7.5、7.4、7.0、6.x<br/> - Cent OS 7.7、7.6、7.5、7.4、6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 20.04、19.04、19.10、14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8 <br/> Oracle Linux 7.7、7.7-CI<br/> 对于其他操作系统，手动进行[所需的更改](prepare-for-migration.md#verify-required-changes-before-migrating)。
| **Azure 所需的更改** | 某些 VM 可能需要经过更改才能在 Azure 中运行。 在迁移之前手动进行调整。 相关文章包含有关如何执行此操作的说明。 |
| **Linux 启动**                 | 如果 /boot 位于专用分区上，则它应驻留在 OS 磁盘上，而不是分布在多个磁盘上。<br/> 如果 /boot 是根 (/) 分区的一部分，则“/”分区应在 OS 磁盘上，而不是分布在其他磁盘上。 |
| **UEFI 启动**                  | 支持。 基于 UEFI 的 VM 将迁移到 Azure 第 2 代 VM。  |
| **UEFI - 安全启动**         | 不支持迁移。|
| **磁盘大小**                  | 最大 2 TB OS 磁盘，4 TB 数据磁盘。|
| **磁盘编号** | 每个 VM 最多 16 个磁盘。|
| **加密磁盘/卷**    | 不支持迁移。|
| **RDM/传递磁盘**      | 不支持迁移。|
| **共享磁盘** | 使用共享磁盘的 VM 不支持迁移。|
| **NFS**                        | 不会复制装载为 VM 上的卷的 NFS 卷。|
| **ISCSI**                      | 具有 iSCSI 目标的 VM 不支持用于迁移。
| **目标磁盘**                | 只能迁移到具有托管磁盘的 Azure VM。 |
| **IPv6** | 不支持。|
| **NIC 组合** | 不支持。|
| **Azure Site Recovery** | 如果启用 VM 来通过 Azure Site Recovery 进行复制，则无法使用 Azure Migrate 服务器迁移进行复制。|
| **端口** | HTTPS 端口 443 上的出站连接，用于发送 VM 复制数据。|

### <a name="url-access-public-cloud"></a>URL 访问（公有云）

Hyper-V 主机上的副本提供程序软件将需要访问这些 URL。

**URL** | **详细信息**
--- | ---
login.microsoftonline.com | 使用 Active Directory 进行访问控制和标识管理。
backup.windowsazure.com | 复制数据传输和协调。
*.hypervrecoverymanager.windowsazure.com | 用于复制管理。
\* .blob.core.windows.net | 将数据上传到存储帐户。 
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
time.windows.com | 验证系统时间与全球时间之间的时间同步。

### <a name="url-access-azure-government"></a>URL 访问（Azure 政府）

Hyper-V 主机上的副本提供程序软件将需要访问这些 URL。

**URL** | **详细信息**
--- | ---
login.microsoftonline.us | 使用 Active Directory 进行访问控制和标识管理。
backup.windowsazure.us | 复制数据传输和协调。
*.hypervrecoverymanager.windowsazure.us | 用于复制管理。
*.blob.core.usgovcloudapi.net | 将数据上传到存储帐户。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
time.nist.gov | 验证系统时间与全球时间之间的时间同步。   

>[!Note]
>
> 如果 Migrate 项目具有专用终结点连接，则 Hyper-V 主机上的复制提供程序软件需访问这些 URL 以获得专用链接支持。 
> - *.blob.core.windows.com - 用于访问存储已复制数据的存储帐户 在存储帐户附加了专用终结点的情况下，这是可选的，不是必需的。 
> - login.windows.net，用于通过 Active Directory 进行访问控制和标识管理。

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的所有本地 VM 必须满足此表中汇总的 Azure VM 要求。

**组件** | **要求** | **详细信息**
--- | --- | ---
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 1 | 如果不支持，检查会失败。
数据磁盘计数 | 16 或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 最大 4,095 GB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。
VM 名称 | 1 到 63 个字符。<br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。
在迁移 Windows 后进行连接 | 若要在迁移后连接到运行 Windows 的 Azure VM，请执行以下操作：<br/><br/> - 在迁移之前，在本地 VM 上启用 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP  。<br/><br/> - 为了通过站点到站点 VPN 进行访问，启用 RDP 并在“Windows 防火墙” -> “允许的应用和功能”中同意域和专用网络使用 RDP。 此外，检查操作系统的 SAN 策略是否已设置为 OnlineAll。 [了解详细信息](prepare-for-migration.md)。 |
在迁移后进行连接 - Linux | 若要在使用 SSH 迁移后连接到 Azure VM，请执行以下操作：<br/><br/> - 在迁移之前，请在本地计算机上检查安全外壳服务是否设置为“启动”，以及防火墙规则是否允许 SSH 连接。<br/><br/> - 在 Azure VM 上执行迁移后，允许已故障转移的 VM 及其所连接 Azure 子网上的网络安全组规则与 SSH 端口建立传入连接。 此外，为 VM 添加公共 IP 地址。 |  

## <a name="next-steps"></a>后续步骤

[迁移 Hyper-V VM](tutorial-migrate-hyper-v.md)。
