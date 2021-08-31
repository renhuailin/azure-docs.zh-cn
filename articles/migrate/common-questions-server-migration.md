---
title: 有关 Azure Migrate 服务器迁移的常见问题
description: 获取有关使用“Azure Migrate 服务器迁移”迁移计算机时出现的常见问题的解答。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 33e332b05cbb6c0d20b73a13134590ed3cda4f38
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112298774"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate 服务器迁移：常见问题

本文解答有关“Azure Migrate: 服务器迁移”工具的常见问题。 如果你遇到其他问题，请查看以下资源：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)
- 有关 [Azure Migrate 设备](common-questions-appliance.md)的问题
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题
- 在 [Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中请求解答问题

## <a name="general-questions"></a>一般问题


### <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>“Azure Migrate: 服务器迁移”中提供哪些迁移选项？

“Azure Migrate: 服务器迁移”工具提供两个选项将源服务器和虚拟机迁移到 Azure：无代理迁移和基于代理的迁移。

无论选择哪个迁移选项，使用“Azure Migrate: 服务器迁移”迁移服务器的第一步都是启动服务器复制。 这会执行将 VM/服务器数据初始复制到 Azure 的操作。 初始复制完成后，将建立持续复制（持续差异同步），以将增量数据迁移到 Azure。 该操作进入差异同步阶段后，你可以随时选择迁移到 Azure。  

下面是在确定迁移选项时要注意的一些事项。

无代理迁移不需要在所要迁移的源 VM/服务器上部署任何软件（代理）。 无代理选项通过集成虚拟化平台提供的功能来协调复制。
无代理复制选项适用于 [VMware VM](./tutorial-migrate-vmware.md) 和 [Hyper-V VM](./tutorial-migrate-hyper-v.md)。

基于代理的迁移需要在所要迁移的源 VM/计算机上安装 Azure Migrate 软件（代理）。 基于代理的选项不依赖于虚拟化平台来实现复制功能。 因此，它可以用于任何运行 x86/x64 架构的服务器和基于代理复制方法支持的操作系统版本。

基于代理的迁移选项可用于 [VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理服务器](./tutorial-migrate-physical-virtual-machines.md)、[AWS 上运行的 VM](./tutorial-migrate-aws-virtual-machines.md)、GCP 上运行的 VM，或者其他虚拟化平台上运行的 VM。 基于代理的迁移将计算机当作物理服务器进行迁移。

尽管在支持的方案（VMware 和 Hyper-V）中，无代理迁移比基于代理的复制选项更简便，但对于以下用例，建议考虑使用基于代理的方案：

- IOPS 受限的环境：无代理复制使用快照并消耗存储 IOPS/带宽。 如果环境中的存储/IOPS 有限制，我们建议使用基于代理的迁移方法。
- 如果你没有 vCenter Server，可以将 VMware VM 视为物理服务器，并使用基于代理的迁移工作流。

有关详细信息，请查看[此文](./server-migrate-overview.md)，其中对 VMware 迁移选项做了比较。

### <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>使用 Azure Migrate 的迁移支持哪些地理位置？

查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

### <a name="can-i-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>是否可以使用同一个 Azure Migrate 项目迁移到多个区域？

尽管可以在一个 Azure Migrate 项目中为多个区域创建评估，但一个 Azure Migrate 项目只可用于将服务器迁移到一个 Azure 区域。 可为需要迁移到的每个区域创建额外的 Azure Migrate 项目。

- 对于无代理 VMware 迁移，启用首次复制后，就会锁定目标区域。
- 对于基于代理的迁移（VMware、物理服务器和其他云中的服务器），在门户中设置复制设备时单击“创建资源”按钮后，就会锁定目标区域。
- 对于无代理 Hyper-V 迁移，在门户中设置 Hyper-V 复制提供程序时单击“创建资源”按钮后，就会锁定目标区域。

### <a name="can-i-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>是否可以使用同一个 Azure Migrate 项目迁移到多个订阅？ 

是的，可以迁移到 Azure Migrate 项目的同一目标区域中的多个订阅（同一 Azure 租户）。 可以在为一台计算机或一组计算机启用复制时选择目标订阅。 为无代理 VMware 迁移完成首次复制后，以及为基于代理的迁移和无代理 Hyper-V 迁移分别进行设备复制和 Hyper-V 提供程序安装期间，将锁定目标区域。

### <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>如何将数据从本地环境传输到 Azure？ 传输前数据是否经过加密？

进行无代理复制时，Azure Migrate 设备将压缩数据，并在上传之前加密数据。 将使用 HTTPS 以及 TLS 1.2 或更高版本通过安全信道传输数据。 此外，Azure 存储在将数据保存到云时会自动加密数据（静态加密）。  

### <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>是否可以使用 Azure Migrate 为灾难恢复方案创建的恢复服务保管库？
我们不建议使用 Azure Migrate 为灾难恢复方案创建的恢复服务保管库。 这可能会导致在 Azure Migrate 中启动复制失败。

### <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>测试迁移与 Migrate 操作有何区别？

通过测试迁移可以在执行实际迁移之前测试和验证迁移。 测试迁移可让你在 Azure 的沙盒环境中创建复制 VM 的测试副本。 该沙盒环境按你指定的测试虚拟网络界定。 测试迁移操作是非破坏性的，应用程序继续在源中运行，同时你可以在隔离的沙盒环境中对克隆的副本执行测试。 可根据需要执行多项测试来验证迁移，执行应用测试，并在实际迁移之前解决任何问题。


### <a name="is-there-a-rollback-option-for-azure-migrate"></a>Azure Migrate 是否提供回滚选项？

可以使用“测试迁移”选项来验证应用程序在 Azure 中的功能和性能。 可以执行任意次测试迁移，并在通过测试迁移操作建立信心后执行最终迁移。 测试迁移不影响本地计算机。本地计算机仍保持运行并持续复制，直到你执行实际迁移。 如果在测试迁移 UAT 期间出现任何错误，你可以选择推迟最终迁移，并使源 VM/服务器保持运行并复制到 Azure。 解决错误后，可以重试最终迁移。  
注意：一旦最终迁移到 Azure 并且本地源计算机已关闭，你就无法从 Azure 回滚到本地环境。

### <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>是否可以选择用于测试迁移的虚拟网络和子网？

可以选择用于测试迁移的虚拟网络。 子网是根据以下逻辑自动选择的：

- 如果在启用复制时将某个目标子网（非默认子网）指定为输入，则 Azure Migrate 将优先使用为测试迁移选择的虚拟网络中具有相同名称的子网。
- 如果找不到具有相同名称的子网，则 Azure Migrate 将按字母顺序选择不是网关/应用程序网关/防火墙/堡垒子网的第一个可用子网。

### <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>为何我的服务器对应的“测试迁移”按钮已禁用？

在以下情况下，“测试迁移”按钮可能会处于禁用状态：

- 在完成 VM 的初始复制 (IR) 之前无法开始测试迁移。 在完成 IR 过程之前，“测试迁移”按钮将会禁用。 在 VM 进入差异同步阶段后，即可执行测试迁移。
- 如果测试迁移已完成，但未对该 VM 执行测试迁移清理，则可能会禁用该按钮。 执行测试迁移清理，然后重试操作。

### <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>不清理测试迁移会发生什么情况？

测试迁移通过使用复制的数据创建测试 Azure VM 来模拟实际迁移。 服务器将连同所复制数据的时间点副本一起部署到带有“-test”后缀的目标资源组（在启用复制时选择）。 测试迁移旨在验证服务器的功能，以最大程度地减少迁移后出现的问题。 如果在测试后不清理测试迁移，则测试虚拟机将继续在 Azure 中运行，从而产生费用。 若要在测试迁移后进行清理，请在服务器迁移工具中转到复制计算机视图，然后对计算机使用“清理测试迁移”操作。


### <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>如何知道我的 VM 是否已成功迁移？

成功迁移 VM/服务器后，可以从“虚拟机”页查看和管理 VM。 连接到已迁移的 VM 进行验证。
或者，可以查看操作的“作业状态”，以检查迁移是否成功完成。 如果看到任何错误，请解决这些错误，然后重试迁移操作。

### <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>如果迁移后不停止复制，会发生什么情况？

停止复制时，“Azure Migrate: 服务器迁移”工具将清理订阅中为复制创建的托管磁盘。 如果在迁移后不停止复制，则这些磁盘会继续产生费用。 停止复制不会影响附加到已迁移的计算机上的磁盘。

### <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>如何将基于 UEFI 的计算机作为 Azure 第 1 代 VM 迁移到 Azure？
“Azure Migrate: 服务器迁移”工具将基于 UEFI 的计算机作为 Azure 第 2 代 VM 迁移到 Azure。 如果你要将其迁移到 Azure 第 1 代 VM，请在开始复制之前将启动类型转换为 BIOS，然后使用“Azure Migrate: 服务器迁移”工具迁移到 Azure。

### <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Azure Migrate 是否将基于 UEFI 的计算机转换为基于 BIOS 的计算机，并将其作为 Azure 第 1 代 VM 迁移到 Azure？
“Azure Migrate: 服务器迁移”工具将所有基于 UEFI 的计算机作为 Azure 第 2 代 VM 迁移到 Azure。 我们不再支持将基于 UEFI 的 VM 转换为基于 BIOS 的 VM。 所有基于 BIOS 的计算机仅作为 Azure 第 1 代 VM 迁移到 Azure。

### <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>从基于 UEFI 的计算机到 Azure 的迁移支持哪些操作系统？

| **基于 UEFI 的计算机支持的操作系统** | **从 VMware 到 Azure 的无代理迁移**                                                                                                             | **从 Hyper-V 到 Azure 的无代理迁移** | **从 VMware、物理服务器和其他云到 Azure 的基于代理的迁移** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019、2016、2012 R2、2012                | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 专业版、Windows 10 企业版                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16.04、18.04、19.04、19.10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8.1、8.0、7.8、7.7、7.6、7.5、7.4、7.0、6.x        | Y<br>                 RHEL 8.x 需要[手动准备](./prepare-for-migration.md#linux-machines)   | Y                              | Y                                                          |
| Cent OS 8.1、8.0、7.7、7.6、7.5、7.4、6.x               | Y<br>Cent OS 8.x 需要[手动准备](./prepare-for-migration.md#linux-machines) | Y                              | Y                                                          |
| Oracle Linux 7.7、7.7-CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

### <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>是否可以使用 Azure Migrate 迁移 Active Directory 域控制器？

服务器迁移工具不区分应用程序，可用于大多数应用程序。 使用服务器迁移工具迁移服务器时，安装在该服务器上的所有应用程序会随之一起迁移。 但对于某些应用程序，可能更适合使用除服务器迁移以外的其他迁移方法进行迁移。  在 Active Directory 中，对于其本地站点已连接到 Azure 环境的混合环境，可以通过在 Azure 中添加更多域控制器并设置 Active Directory 复制，将目录扩展到 Azure 中。 如果要迁移到 Azure 中的一个需要其自己的域控制器的隔离环境（或者在沙盒环境中测试应用程序），可以使用服务器迁移工具来迁移服务器。


### <a name="can-i-upgrade-my-os-while-migrating"></a>在迁移时是否可以升级 OS？

“Azure Migrate: 服务器迁移”工具目前仅支持对等性的迁移。 该工具不支持在迁移期间升级 OS 版本。 已迁移的计算机的 OS 将与源计算机相同。

### <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>是否需要 VMware vCenter 才能迁移 VMware VM？

若要使用基于 VMware 代理的迁移或无代理迁移方法[迁移 VMware VM](server-migrate-overview.md)，必须通过 vCenter Server 来管理这些 VM 所在的 ESXi 主机。 如果没有 vCenter Server，可将 VMware VM 作为物理服务器进行迁移。 [了解详细信息](migrate-support-matrix-physical-migration.md)。

### <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>在迁移时是否可以将多个源 VM 合并成一个 VM？

Azure Migrate 服务器迁移功能目前支持对等性的迁移。 我们不支持在迁移过程中合并服务器或升级操作系统。 

### <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>迁移后，Windows Server 2008 和 2008 R2 是否在 Azure 中受支持？

可将本地 Windows Server 2008 和 2008 R2 服务器迁移到 Azure 虚拟机，并获取在支持终止日期之后为期三年的延期安全更新，这样，除了支付运行虚拟机的费用外，无需额外付费。 可以使用“Azure Migrate: 服务器迁移”工具迁移 Windows Server 2008 和 2008 R2 工作负载。

### <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>如何将 VMware/Hyper-V 上运行的 Windows Server 2003 迁移到 Azure？

[Windows Server 2003 外延支持](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support)已于 2015 年 7 月 14 日终止。  Azure 支持团队将继续帮助排查有关在 Azure 上运行 Windows Server 2003 的问题。 但是，这种支持仅限于不需要 OS 级故障排除或补丁的问题。
建议将应用程序迁移到运行较新版 Windows Server 的 Azure 实例，以确保有效使用 Azure 云的灵活性和可靠性。

但是，如果你仍选择将 Windows Server 2003 迁移到 Azure，并且你的 Windows Server 是在 VMware 或 Hyper-V 上运行的 VM，则可以使用“Azure Migrate: 服务器迁移”工具。请查看此文[准备要迁移的 Windows server 2003 计算机](./prepare-windows-server-2003-migration.md)。

## <a name="agentless-vmware-migration"></a>无代理 VMware 迁移
### <a name="how-does-agentless-migration-work"></a>无代理迁移的工作原理是什么？

“Azure Migrate: 服务器迁移”提供无代理复制选项用于迁移运行 Windows 或 Linux 的 VMware 虚拟机和 Hyper-V 虚拟机。 该工具还额外提供一个适用于 Windows 和 Linux 服务器的基于代理的复制选项，该选项可用于迁移物理服务器，以及 VMware、Hyper-V、AWS、GCP 等上的 x86/x64 虚拟机。基于代理的复制选项需要在所要迁移的服务器/虚拟机上安装代理软件，而无代理选项无需在虚拟机本身上安装任何软件，因此其简便性比基于代理的复制选项要高。

无代理复制选项是使用虚拟化平台（VMware、Hyper-V）提供的机制运行的。 对于 VMware 虚拟机，无代理复制机制使用 VMware 快照和 VMware 已更改块跟踪技术从虚拟机磁盘复制数据。 此机制类似于许多备份产品使用的机制。 对于 Hyper-V 虚拟机，无代理复制机制使用 VM 快照和 Hyper-V 副本的更改跟踪功能从虚拟机磁盘复制数据。

为虚拟机配置复制后，首先会经历初始复制阶段。 在初始复制期间，将创建一个 VM 快照，并将快照磁盘中的完整数据副本复制到订阅中的托管磁盘。 VM 的初始复制完成后，复制过程将转换为增量复制（差异复制）阶段。 在增量复制阶段，会定期复制自上次完成的复制周期以来发生的数据更改，并将其应用于副本托管磁盘，从而使复制与 VM 上发生的更改保持同步。 对于 VMware 虚拟机，将使用 VMware 已更改块跟踪技术来跟踪复制周期之间的变化。 复制周期开始时，将创建一个 VM 快照，并使用已更改块跟踪来获取当前快照与上次成功复制的快照之间的变化。 这样，就只需要复制自上次完成的复制周期以来发生更改的数据，以使 VM 复制保持同步。在每个复制周期结束时，将释放快照，并对虚拟机执行快照合并。 同样，对于 Hyper-V 虚拟机，将使用 Hyper-V 副本更改跟踪引擎来跟踪连续复制周期之间的变化。
在复制虚拟机上执行迁移操作时，可以选择关闭本地虚拟机，并执行一次最终增量复制，以确保不会丢失任何数据。 执行迁移选项时，将使用与虚拟机对应的副本托管磁盘在 Azure 中创建虚拟机。

若要开始，请参阅 [VMware 无代理迁移](./tutorial-migrate-vmware.md)和 [Hyper-V 无代理迁移](./tutorial-migrate-hyper-v.md)教程。

### <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>如何衡量迁移的带宽要求？

将数据复制到 Azure 所需的带宽取决于各种因素，与本地 Azure Migrate 设备读取数据以及将数据复制到 Azure 的速度有关。 复制分为两个阶段：初始复制和差异复制。

当 VM 复制开始时，将执行初始复制周期，在此周期将复制磁盘的完整副本。 初始复制完成后，将定期计划增量复制周期（差异周期），以传输自上次复制周期后发生的所有更改。

可以根据初始复制阶段和时间内需要移动的数据量大致确定带宽要求（最好是在实际迁移时段之前的至少 3-4 天内完成初始复制，以便有足够的时间在实际迁移时段之前执行测试迁移，并在此时段最大限度地减少停机时间）。

可以使用以下公式来估计无代理 VMware VM 迁移所需的带宽或时间：

完成初始复制所需的时间 = {磁盘大小（或已用大小，如果适用）* 0.7（假设平均压缩率为 30% – 保守估计）}/可用于复制的带宽。

### <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>如何限制复制过程使用 Azure Migrate 设备进行无代理 VMware 复制？  

可以使用 NetQosPolicy 进行限制。 例如：

要在 NetQosPolicy 中使用的 AppNamePrefix 为“GatewayWindowsService.exe”。 可按如下所示在 Azure Migrate 设备上创建一个策略，以限制来自设备的复制流量：

```powershell
New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB
```

若要根据计划增加和减少复制带宽，可以利用 Windows 计划任务根据需要缩放带宽。 一个任务将用于减少带宽，另一个任务将用于增加带宽。
注意：在执行下面的命令之前，需要创建上面概述的 NetQosPolicy。
```powershell
#Replace with an account part of the local Administrators group
$User = "localVmName\userName"

#Set the task names
$ThrottleBandwidthTask = "ThrottleBandwidth"
$IncreaseBandwidthTask = "IncreaseBandwidth"

#Create a directory to host PowerShell scaling scripts
if (!(Test-Path "C:\ReplicationBandwidthScripts"))
{
 New-Item -Path "C:\" -Name "ReplicationBandwidthScripts" -Type Directory
}

#Set your minimum bandwidth to be used during replication by changing the ThrottleRateActionBitsPerSecond parameter
#Currently set to 10 MBps
New-Item C:\ReplicationBandwidthScripts\ThrottleBandwidth.ps1
Set-Content C:\ReplicationBandwidthScripts\ThrottleBandwidth.ps1 'Set-NetQosPolicy -Name "ThrottleReplication" -ThrottleRateActionBitsPerSecond 10MB'
$ThrottleBandwidthScript = "C:\ReplicationBandwidthScripts\ThrottleBandwidth.ps1"

#Set your maximum bandwidth to be used during replication by changing the ThrottleRateActionBitsPerSecond parameter
#Currently set to 1000 MBps
New-Item C:\ReplicationBandwidthScripts\IncreaseBandwidth.ps1
Set-Content C:\ReplicationBandwidthScripts\IncreaseBandwidth.ps1 'Set-NetQosPolicy -Name "ThrottleReplication" -ThrottleRateActionBitsPerSecond 1000MB'
$IncreaseBandwidthScript = "C:\ReplicationBandwidthScripts\IncreaseBandwidth.ps1"

#Timezone set on the Azure Migrate Appliance (VM) will be used; change the frequency to meet your needs
#In this example, the bandwidth is being throttled every weekday at 8:00 AM local time
#The bandwidth is being increased every weekday at 6:00 PM local time
$ThrottleBandwidthTrigger = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Monday,Tuesday,Wednesday,Thursday,Friday -At 8:00am
$IncreaseBandwidthTrigger = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Monday,Tuesday,Wednesday,Thursday,Friday -At 6:00pm

#Setting the task action to execute the scripts
$ThrottleBandwidthAction = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-executionpolicy bypass -noprofile -file $ThrottleBandwidthScript" 
$IncreaseBandwidthAction = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-executionpolicy bypass -noprofile -file $IncreaseBandwidthScript" 

#Creating the Scheduled tasks
Register-ScheduledTask -TaskName $ThrottleBandwidthTask -Trigger $ThrottleBandwidthTrigger -User $User -Action $ThrottleBandwidthAction -RunLevel Highest -Force
Register-ScheduledTask -TaskName $IncreaseBandwidthTask -Trigger $IncreaseBandwidthTrigger -User $User -Action $IncreaseBandwidthAction -RunLevel Highest -Force
```

### <a name="how-does-churn-rate-affect-agentless-replication"></a>流失率对无代理复制造成怎样的影响？

由于无代理复制对半缩减数据，因此流失模式比流失率更重要 。 反复写入文件时，流失率不会造成很大的影响。 但是，每隔一个扇区写入的模式会导致下一个周期的流失变高。 由于我们会最大限度地减少传输的数据量，因此，在计划下一个周期之前允许尽可能多地对半缩减数据。

### <a name="how-frequently-is-a-replication-cycle-scheduled"></a>复制周期的计划频率是怎样的？

计划下一个复制周期的公式为 (上一周期时间/2) 或一小时，以较大的结果为准。

例如，如果某个 VM 完成一个差异周期需要四个小时，则会将下一个周期计划为两小时后，而不是下一个小时。 完成初始复制后立即计划第一个差异周期时，该过程会立即发生变化。

### <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>我部署了两个（或更多个）设备以在 vCenter Server 中发现 VM。 但是，当我尝试迁移 VM 时，只看到了与其中一个设备对应的 VM。

如果设置了多个设备，则要求提供的 vCenter 帐户上的 VM 间没有重叠。 具有此类重叠的发现是不受支持的方案。


### <a name="how-does-agentless-replication-affect-vmware-servers"></a>无代理复制会对 VMware 服务器造成怎样的影响？

无代理复制会对 VMware vCenter Server 和 VMware ESXi 主机的性能造成一定的影响。 由于无代理复制使用快照，会消耗 IOPS 或存储，因此需要占用一些 IOPS 存储带宽。 如果你环境中的存储或 IOPS 有限制，则我们不建议使用无代理复制。


## <a name="agent-based-migration"></a>基于代理的迁移

### <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>如何将 AWS EC2 实例迁移到 Azure？

查看[此文](./tutorial-migrate-aws-virtual-machines.md)，了解如何发现、评估 AWS EC2 实例并将其迁移到 Azure。

### <a name="how-does-agent-based-migration-work"></a>基于代理的迁移的工作原理是什么？

除了适用于 VMware 虚拟机和 Hyper-V 虚拟机的无代理迁移选项以外，服务器迁移工具还提供基于代理的迁移选项，用于迁移在物理服务器上运行的，或者在 VMware、Hyper-V、AWS、Google Cloud Platform 等平台上作为 x86/x64 虚拟机运行的 Windows 和 Linux 服务器。

基于代理的迁移方法使用所要迁移的服务器上安装的代理软件将服务器数据复制到 Azure。 复制过程使用卸载体系结构，在其中，代理会将复制数据中继到称作复制设备或配置服务器的专用复制服务器（或中继到横向扩展进程服务器）。 [详细了解](./agent-based-migration-architecture.md)基于代理的迁移选项的工作原理。 

注意：复制设备不同于 Azure Migrate 发现设备，必须安装在单独/专用的计算机上。

### <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>用于基于代理的迁移的复制设备应安装在何处？

应将复制设备安装在专用计算机上。 复制设备不应安装在要复制的源计算机上，也不应安装在以前可能已安装的 Azure Migrate 设备上（用于发现和评估）。 请参阅[教程](./tutorial-migrate-physical-virtual-machines.md)了解更多详细信息。

### <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>是否可以迁移运行 Amazon Linux 操作系统的 AWS VM？

由于只有 AWS 支持 Amazon Linux OS，因此无法按原样迁移运行 Amazon Linux 的 VM。
若要迁移在 Amazon Linux 上运行的工作负载，可以在 Azure 中启动 CentOS/RHEL VM，并使用相关的工作负载迁移方法迁移在 AWS Linux 计算机上运行的工作负载。 例如，根据工作负载，通过特定于工作负载的工具来帮助迁移（例如使用数据库工具迁移数据库，或者使用部署工具迁移 Web 服务器）。

### <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>如何衡量迁移的带宽要求？

将数据复制到 Azure 所需的带宽取决于各种因素，与本地 Azure Migrate 设备读取数据以及将数据复制到 Azure 的速度有关。 复制分为两个阶段：初始复制和差异复制。

当 VM 复制开始时，将执行初始复制周期，在此周期将复制磁盘的完整副本。 初始复制完成后，将定期计划增量复制周期（差异周期），以传输自上次复制周期后发生的所有更改。

对于基于代理的复制方法，部署规划器可以帮助分析环境中的数据流失率，并帮助预测所需的带宽要求。 有关详细信息，请查看[此文](./agent-based-migration-architecture.md#plan-vmware-deployment)

## <a name="agentless-hyper-v-migration"></a>无代理 Hyper-V 迁移

### <a name="how-does-agentless-migration-work"></a>无代理迁移的工作原理是什么？

“Azure Migrate: 服务器迁移”提供无代理复制选项用于迁移运行 Windows 或 Linux 的 VMware 虚拟机和 Hyper-V 虚拟机。 该工具还额外提供一个适用于 Windows 和 Linux 服务器的基于代理的复制选项，该选项可用于迁移物理服务器，以及 VMware、Hyper-V、AWS、GCP 等上的 x86/x64 虚拟机。基于代理的复制选项需要在所要迁移的服务器/虚拟机上安装代理软件，而无代理选项无需在虚拟机本身上安装任何软件，因此其简便性比基于代理的复制选项要高。

无代理复制选项是使用虚拟化平台（VMware、Hyper-V）提供的机制运行的。  对于 Hyper-V 虚拟机，无代理复制机制使用 VM 快照和 Hyper-V 副本的更改跟踪功能从虚拟机磁盘复制数据。

为虚拟机配置复制后，首先会经历初始复制阶段。 在初始复制期间，将创建一个 VM 快照，并将快照磁盘中的完整数据副本复制到订阅中的托管磁盘。 VM 的初始复制完成后，复制过程将转换为增量复制（差异复制）阶段。 在增量复制阶段，会定期复制自上次完成的复制周期以来发生的数据更改，并将其应用于副本托管磁盘，从而使复制与 VM 上发生的更改保持同步。 对于 VMware 虚拟机，将使用 VMware 已更改块跟踪技术来跟踪复制周期之间的变化。 复制周期开始时，将创建一个 VM 快照，并使用已更改块跟踪来获取当前快照与上次成功复制的快照之间的变化。 这样，就只需要复制自上次完成的复制周期以来发生更改的数据，以使 VM 复制保持同步。在每个复制周期结束时，将释放快照，并对虚拟机执行快照合并。 同样，对于 Hyper-V 虚拟机，将使用 Hyper-V 副本更改跟踪引擎来跟踪连续复制周期之间的变化。

在复制虚拟机上执行迁移操作时，可以选择关闭本地虚拟机，并执行一次最终增量复制，以确保不会丢失任何数据。 执行迁移选项时，将使用与虚拟机对应的副本托管磁盘在 Azure 中创建虚拟机。

若要开始，请参阅 [Hyper-V 无代理迁移](./tutorial-migrate-hyper-v.md)教程。

### <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>如何衡量迁移的带宽要求？

将数据复制到 Azure 所需的带宽取决于各种因素，与本地 Azure Migrate 设备读取数据以及将数据复制到 Azure 的速度有关。 复制分为两个阶段：初始复制和差异复制。

当 VM 复制开始时，将执行初始复制周期，在此周期将复制磁盘的完整副本。 初始复制完成后，将定期计划增量复制周期（差异周期），以传输自上次复制周期后发生的所有更改。

可以根据初始复制阶段和时间内需要移动的数据量大致确定带宽要求（最好是在实际迁移时段之前的至少 3-4 天内完成初始复制，以便有足够的时间在实际迁移时段之前执行测试迁移，并在此时段最大限度地减少停机时间）。

## <a name="next-steps"></a>后续步骤

阅读 [Azure Migrate 概述](migrate-services-overview.md)。
