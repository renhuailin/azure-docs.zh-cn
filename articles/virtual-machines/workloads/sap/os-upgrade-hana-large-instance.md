---
title: Azure SAP HANA（大型实例）操作系统升级 | Microsoft Docs
description: 执行 Azure SAP HANA（大型实例）操作系统升级
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7be86ca3895d7ed98d676a5c9fc19c873da6d5e
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110577217"
---
# <a name="operating-system-upgrade"></a>操作系统升级
本文档介绍 HANA 大型实例操作系统升级的详细信息。

>[!NOTE]
>OS 升级由客户负责，Microsoft 运营支持团队可指导你明确升级过程中需要留意的关键方面。 在计划升级前，你还应咨询操作系统供应商。

在 HLI 单元预配期间，Microsoft 运营团队会安装操作系统。
随着时间推移，你需要维护 HLI 单元的操作系统（例如，修补、优化、升级等）。

在对操作系统进行重大更改（例如将 SP1 升级到 SP2）之前，应创建支持票证与 Microsoft 运营团队联系进行咨询。

在票证中提供：

* 你的 HLI 订阅 ID。
* 你的服务器名称。
* 你打算应用的修补程序级别。
* 你计划此更改的日期。 

建议至少在所需升级之前一周创建此票证，这将使运营团队获知所需的固件版本。

有关不同 Linux 版本的不同 SAP HANA 版本的支持矩阵，请参阅 [SAP 说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。

## <a name="known-issues"></a>已知问题

以下是升级过程中几个常见的已知问题：
- 在 II 类 SKU 上，Software Foundation Software (SFS) 会在操作系统升级后移除。 需要在 OS 升级后重新安装兼容的 SFS。
- 以太网卡驱动程序（ENIC 和 FNIC）会回滚到旧版本。 需要在升级后重新安装兼容版本的驱动程序。

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA 大型实例（类型 I）推荐配置

由于修补、系统升级和客户所作更改，操作系统配置可能会随着时间的推移偏离建议的设置。 此外，Microsoft 会识别现有系统所需的更新，确保它们经过最优配置来实现最佳性能和复原能力。 下面的说明概述了应对网络性能、系统稳定性和最佳 HANA 性能需求的建议。

### <a name="compatible-enicfnic-driver-versions"></a>兼容的 eNIC/fNIC 驱动程序版本
  为了获得适当的网络性能和系统稳定性，建议确保按照下面的兼容性表格中的说明，安装 OS 特定的适当版本的 eNIC 和 fNIC 驱动程序。 服务器交付到具有兼容版本的客户。 某些情况下，在 OS/内核修补期间，驱动程序可回滚到默认的驱动程序版本。 确保在 OS/内核修补操作后运行适当的驱动程序版本。
       
      
  |  OS 供应商    |  OS 包版本     |  固件版本  |  eNIC 驱动程序 |  fNIC 驱动程序 | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP4            |   4.1.1b           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP5            |   3.2.3i           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>用于升级驱动程序和清理旧的 rpm 包的命令

#### <a name="command-to-check-existing-installed-drivers"></a>用于检查现有已安装的驱动程序的命令
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>删除现有的 eNIC/fNIC rpm
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>安装建议的 eNIC/fNIC 驱动程序包
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>用于确认安装的命令
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>OS 升级期间的 eNIC/fNIC 驱动程序安装步骤

* 升级 OS 版本
* 删除旧的 rpm 包
* 按照已安装的 OS 版本安装兼容的 eNIC/fNIC 驱动程序
* 重启系统
* 重启后，检查 eNIC/fNIC 版本


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLI GRUB 更新失败
升级后，Azure 上的 SAP HANA 大型实例（类型 I）可能会进入不可启动的状态。 以下过程解决了此问题。
#### <a name="execution-steps"></a>执行步骤


*   执行 `multipath -ll` 命令。
*   获取大小约为 50G 的 LUN ID，或者使用 `fdisk -l | grep mapper` 命令
*   向 `/etc/default/grub_installdevice` 文件更新行 `/dev/mapper/<LUN ID>`。 示例：/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN ID 因服务器而异。


### <a name="disable-edac"></a>禁用 EDAC 
   错误检测与更正 (EDAC) 模块可帮助检测和更正内存错误。 不过，Azure 上的 SAP HANA 大型实例（类型 I）的基础硬件已在执行此功能。 在硬件和操作系统 (OS) 级别启用同一功能可能会导致冲突，而且可能导致服务器偶尔出现意外停机。 因此，建议从 OS 禁用此模块。

#### <a name="execution-steps"></a>执行步骤

* 检查是否已启用 EDAC 模块。 如果以下命令中返回了输出，则表示已启用此模块。 
```
lsmod | grep -i edac 
```
* 通过向文件 `/etc/modprobe.d/blacklist.conf` 追加以下行来禁用模块
```
blacklist sb_edac
blacklist edac_core
```
需要重启才能进行更改。 执行 `lsmod` 命令并验证输出中不存在模块。

### <a name="kernel-parameters"></a>内核参数
   确保已对 `transparent_hugepage`、`numa_balancing`、`processor.max_cstate`、`ignore_ce` 和 `intel_idle.max_cstate` 应用正确的设置。

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>执行步骤

* 将这些参数添加到 `/etc/default/grub` 文件的 `GRB_CMDLINE_LINUX` 行
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* 创建新的 grub 文件。
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* 重启系统。


## <a name="next-steps"></a>后续步骤
- 有关 I 类 SKU 操作系统备份的信息，请参阅[备份和恢复](hana-overview-high-availability-disaster-recovery.md)。
- 请参阅 [OS 备份](./large-instance-os-backup.md)以了解 HLI。
