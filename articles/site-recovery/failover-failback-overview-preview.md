---
title: 关于 Azure Site Recovery 中的故障转移和故障回复 - 预览版
description: 了解 Azure Site Recovery 中的故障转移和故障回复 - 预览版
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: d7e08623a1e297d618365a126f835f88e9dfb716
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446636"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback---preview"></a>关于本地灾难恢复故障转移/故障回复 - 预览版

本文概述了使用 [Azure Site Recovery](site-recovery-overview.md)（预览版）将本地机器灾难恢复到 Azure 期间的故障转移和故障回复。

有关 Azure Site Recovery 经典版中故障转移和故障回复的信息，请[参阅本文](failover-failback-overview.md)。

## <a name="recovery-stages"></a>恢复阶段

Site Recovery 中的故障转移和故障回复分为四个阶段：

- **第 1 阶段：从本地故障转移**：设置将本地计算机复制到 Azure 后，当本地站点发生故障时，这些计算机将故障转移到 Azure。 故障转移后，将会基于复制的数据创建 Azure VM。
- **第 2 阶段：重新保护 Azure VM**：在 Azure 中重新保护 Azure VM，使之开始复制回到本地站点。 重新保护期间，为帮助确保数据一致性，本地 VM（如果可用）将会关闭。
- **第 3 阶段：从 Azure 故障转移**：如果本地站点再次恢复运行正常，则运行另一次故障转移，这一次是将 Azure VM 故障回复到本地站点。 可以故障回复到从中进行故障转移的原始位置，或故障回复到备用位置。 此活动称为“计划的故障转移”。
- **第 4 阶段：重新保护本地计算机**：故障回复后，再次启用本地计算机到 Azure 的复制。

## <a name="failover"></a>故障转移

根据业务连续性和灾难恢复 (BCDR) 策略执行故障转移。

- BCDR 策略的第一步是持续将本地计算机复制到 Azure。 用户访问本地源计算机上运行的工作负荷和应用。
- 如果有需要（例如，如果本地发生服务中断），可将复制计算机故障转移到 Azure。 使用复制的数据创建 Azure VM。
- 为实现业务连续性，用户可以继续访问 Azure VM 上的应用。

故障转移是由两个阶段组成的活动：

- **故障转移**：使用所选恢复点创建并启动 Azure VM 的故障转移。
- **提交**：故障转移后，验证 Azure 中的 VM：
    - 然后，可将故障转移提交到所选的恢复点，或者为提交选择另一个恢复点。
    - 提交故障转移后无法更改恢复点。


## <a name="connect-to-azure-after-failover"></a>故障转移后连接到 Azure

若要使用 RDP/SSH 连接到故障转移后创建的 Azure VM，需要满足多个要求。

**故障转移** | **位置** | **操作**
--- | --- | ---
**运行 Windows 的 Azure VM** | 故障转移之前在本地计算机上 | **通过 Internet 访问**：启用 RDP。 确保已针对“公共”添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP    。<br/><br/> **通过站点到站点 VPN 访问**：在计算机上启用 RDP。 检查是否在“Windows 防火墙” -> “允许的应用和功能”中针对“域和专用”网络允许 RDP    。<br/><br/>  确保操作系统 SAN 策略已设置为 **OnlineAll**。 [了解详细信息](https://support.microsoft.com/kb/3031135)。<br/><br/> 在触发故障转移时，请确保 VM 上没有处于挂起状态的 Windows 更新。 Windows 更新可能会在故障转移时启动，在更新完成之前，无法登录到 VM。
**运行 Windows 的 Azure VM** | 故障转移之后在 Azure VM 上 |  为 VM [添加公共 IP 地址](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)。<br/><br/> 已故障转移的 VM（及其连接到的 Azure 子网）上的网络安全组规则必须允许与 RDP 端口建立传入连接。<br/><br/> 选中“启动诊断”可查看 VM 的屏幕截图  。 如果无法连接，请检查 VM 是否正在运行，并查看[故障排除提示](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。
**运行 Linux 的 Azure VM** | 故障转移之前在本地计算机上 | 确保 VM 上的安全外壳服务已设置为在系统引导时自动启动。<br/><br/> 确保防火墙规则允许 SSH 连接。
**运行 Linux 的 Azure VM** | 故障转移之后在 Azure VM 上 | 已故障转移的 VM（及其连接到的 Azure 子网）上的网络安全组规则需要允许与 SSH 端口建立传入连接。<br/><br/> 为 VM [添加公共 IP 地址](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)。<br/><br/> 选中“启动诊断”可查看 VM 的屏幕截图  。<br/><br/>

## <a name="types-of-failover"></a>故障转移类型

Site Recovery 提供不同的故障转移选项。

**故障转移** | **详细信息** | **恢复** | **Workflow**
--- | --- | --- | ---
**测试故障转移** | 用于运行演练，以在不丢失任何数据或造成停机的情况下验证 BCDR 策略。| 在 Azure 中创建 VM 的副本，这不会对进行中的复制或生产环境造成任何影响。 | 1.针对单个 VM 或恢复计划中的多个 VM 运行测试故障转移。<br/><br/> 2.选择用于测试故障转移的恢复点。<br/><br/> 3.选择一个 Azure 网络，故障转移后创建的 Azure VM 将放入该网络。 该网络仅用于测试故障转移。<br/><br/> 4.验证演练是否按预期方式进行。 Site Recovery 将自动清理演练期间在 Azure 中创建的 VM。
**计划内故障转移 - Hyper-V**  | 通常用于计划内停机。<br/><br/> 源 VM 已关闭。 在启动故障转移之前，将同步最新的数据。 | 计划的工作流不会发生任何数据丢失。 | 1.规划停机维护时段并通知用户。<br/><br/> 2.使面向用户的应用脱机。<br/><br/> 3.使用最新的恢复点启动计划内故障转移。 如果计算机未关闭或遇到错误，则故障转移不会运行。<br/><br/> 4.故障转移后，检查副本 Azure VM 是否在 Azure 中处于活动状态。<br/><br/> 5.提交故障转移以完成操作。 提交操作将删除所有恢复点。
**故障转移 - Hyper-V** | 通常在出现计划外服务中断或主站点不可用的情况时运行。<br/><br/> （可选）关闭 VM，并在启动故障转移之前同步最终更改。  | 应用只会出现极少量的数据丢失。 | 1.启动 BCDR 计划。 <br/><br/> 2.启动故障转移。 指定在触发故障转移之前 Site Recovery 应关闭 VM 并同步/复制最新的更改。<br/><br/> 3.可以故障转移到许多恢复点选项，下表对此做了汇总。<br/><br/> 如果未启用关闭 VM 的选项，或者 Site Recovery 无法关闭 VM，则会使用最新的恢复点。<br/>即使无法关闭计算机，也会运行故障转移。<br/><br/> 4.故障转移后，检查副本 Azure VM 是否在 Azure 中处于活动状态。<br/> 如果需要，可以从 24 小时保留时段中选择不同的恢复点。<br/><br/> 5.提交故障转移以完成操作。 提交操作将删除所有可用的恢复点。
**故障转移 - VMware** | 通常在出现计划外服务中断或主站点不可用的情况时运行。<br/><br/> （可选）指定在启动故障转移之前，Site Recovery 应尝试触发 VM 关闭，并同步和复制最终的更改。  | 应用只会出现极少量的数据丢失。 | 1.启动 BCDR 计划。 <br/><br/> 2.从 Site Recovery 启动故障转移。 指定在运行故障转移之前，Site Recovery 是否应尝试触发 VM 关闭并进行同步。<br/> 即使无法关闭计算机，也会运行故障转移。<br/><br/> 3.故障转移后，检查副本 Azure VM 是否在 Azure 中处于活动状态。 <br/>如果需要，可以从 72 小时保留时段中选择不同的恢复点。<br/><br/> 5.提交故障转移以完成操作。 提交操作将删除所有恢复点。<br/> 对于 Windows VM，Site Recovery 在故障转移期间会禁用 VMware 工具。
计划内故障转移 - VMware | 你可以执行从 Azure 到本地的计划内故障转移。 | 由于它是计划的故障转移活动，因此在触发计划的故障转移作业后会生成恢复点。 | 触发计划的故障转移时，挂起的更改将复制到本地，而系统会生成 VM 的最新恢复点并关闭 Azure VM。<br/><br/> 按照[此处](vmware-azure-tutorial-failover-failback-preview.md#planned-failover-from-azure-to-on-premises)讨论的故障转移过程操作。 在此之后，本地计算机将启动。 成功完成计划的故障转移后，计算机将在本地环境中处于活动状态。

## <a name="failover-processing"></a>故障转移处理

在某些情况下，故障转移需要大约 8 到 10 分钟的时间完成其他进程。 对于以下情况，你可能会发现测试故障转移会持续较长时间：

* VMware VM 未启用 DHCP 服务。
* VMware VM 不包含以下启动驱动程序：storvsc、vmbus、storflt、intelide、atapi。

## <a name="recovery-point-options"></a>恢复点选项

在故障转移期间，可以选择许多恢复点选项。

**选项** | **详细信息**
--- | ---
**最新(最低 RPO)** | 此选项提供最低的恢复点目标 (RPO)。 它会首先处理已发送到 Site Recovery 服务的所有数据，为每个 VM 创建恢复点，然后将其故障转移到该恢复点。 触发故障转移后，此恢复点的所有数据将复制到 Site Recovery。
**最新处理**  | 此选项将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 若要查看特定 VM 的最新恢复点，请检查 VM 设置中的“最新恢复点”。  此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
**最新应用一致性** |  如果已启用应用一致性恢复点，此选项会将 VM 故障转移到 Site Recovery 处理的最新应用程序一致性恢复点。 在 VM 设置中检查最新的恢复点。
**最新处理的多 VM** | 此选项适用于包含一个或多个已启用多 VM 一致性的 VM 的恢复计划。 已启用该设置的 VM 会故障转移到最新的常用多 VM 一致恢复点。 计划中的任何其他 VM 将故障转移到最新的已处理恢复点。
**最新的多 VM 应用一致性** |  此选项适用于包含一个或多个已启用多 VM 一致性的 VM 的恢复计划。 属于复制组的 VM 会故障转移到最新的常用多 VM 应用程序一致恢复点。 其他 VM 故障转移到其最新的应用程序一致恢复点。
**自定义** | 使用此选项可将特定的 VM 故障转移到特定的恢复时间点。 此选项不适用于恢复计划。

> [!NOTE]
> 恢复点无法迁移到另一个恢复服务保管库。

## <a name="reprotectionplanned-failover"></a>重新保护/计划的故障转移

故障转移到 Azure 后，复制的 Azure VM 处于不受保护状态。

- 故障回复到本地站点的第一步是开始将 Azure VM 复制到本地。 重新保护过程取决于故障转移的计算机的类型。
- 将计算机从 Azure 复制到本地后，可以运行从 Azure 故障转移到本地站点的过程。
- 计算机再次在本地运行后，可以启用复制，以便将其复制到 Azure 进行灾难恢复。

计划故障转移的工作方式如下：

- 若要将故障回复到本地，VM 至少需要一个恢复点才能进行故障回复。 恢复计划中的所有 VM 至少需有一个恢复点。
- 由于这是计划的故障转移活动，因此可以选择要将故障回复到的恢复点类型。 建议使用崩溃一致点。
    - 还有一个应用一致恢复点选项。 在这种情况下，单个 VM 将恢复到其最新可用的应用一致性恢复点。 对于包含复制组的恢复计划，每个复制组将恢复到其公共的可用恢复点。
    - 应用一致性恢复点在时间上可能会落后，并且可能会发生数据丢失。
- 从 Azure 故障转移到本地站点期间，Site Recovery 将关闭 Azure VM。 提交故障转移时，Site Recovery 将删除 Azure 中已故障回复的 Azure VM。


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/物理机重新保护/故障回复

若要重新保护 VMware 计算机和物理服务器，以及将它们的故障从 Azure 回复到本地，请确保设备正常运行。

设备选择

- 你可以选择在保管库注册的任何 Azure Site Recovery 复制设备，以重新保护到本地。 不需要在 Azure 中单独使用进程服务器执行重新保护操作，也不需要为 Linux VM 提供横向扩展主目标服务器。
- 与转接保护相比，在故障回复期间，复制设备无需其他网络连接/端口。 如果设备运行状态正常，则同一设备可用于向前和向后保护。 它不应影响复制的性能。
- 确保所选数据存储或主机是指设备所在的位置，并且可以通过所选设备访问。
  > [!NOTE]
  > 在重新保护操作后，不支持复制设备的存储 vMotion。


重新保护作业

- 如果这是一个新的重新保护操作，默认情况下，目标区域的 Azure Site Recovery 会自动创建日志存储帐户。 无需保留磁盘。
- 对于备用位置恢复和原始位置恢复，将检索源计算机的原始配置。
  > [!NOTE]
  > - 在重新保护备用位置 (ALR) 或原始位置 (OLR) 的情况下，无法保留静态 IP 地址。
  > - fstab、LVMconf 将发生更改。


**失败**

- 可以重试任何失败的重新保护作业。 重试期间，你可以选择任何正常运行的复制设备。

将 Azure 计算机重新保护到本地时，系统会通知你故障回到原始位置或备用位置。

- **原始位置恢复**：这会从 Azure 故障回复到同一台源本地计算机（如果存在）。 在这种情况下，只会将更改复制回到本地。
  - OLR 期间选择数据存储：自动选择附加到源计算机的数据存储区。
- **备用位置恢复**：如果本地计算机不存在，可以从 Azure 故障回复到备用位置。 在本地重新保护 Azure VM 时，将创建本地计算机。 将发生从 Azure 到本地的完整数据复制。 [查看](concepts-types-of-failback.md)位置故障回复的要求和限制。
  - ALR 期间选择数据存储：可以选择由保存设备且可供设备访问的 vCenter（读取和写入权限）管理的任何数据存储权限（原始/新）。 你可以选择用于重新保护的缓存存储帐户。

- 故障转移完成后，Azure VM 中的移动代理将自动注册到 Site Recovery 服务。 如果注册失败，将在已执行故障转移的 VM 上引发严重健康状况问题。 解决问题后，系统将自动触发注册。 解决错误后，你可以手动完成注册。


## <a name="cancel-failover"></a>取消故障转移

如果本地环境未准备就绪，或者你面临着任何挑战，则可以取消故障转移。

在你启动计划的故障转移并成功完成转移后，你的本地环境将可供使用。 但在完成操作后，如果要故障转移到另一个恢复点，则可以取消故障转移。


- 只能取消计划的故障转移。

- 你可以从恢复服务保管库中的“复制的项”页取消计划的故障转移。

- 取消故障转移后，Azure 中的计算机将重新打开，并再次启动从 Azure 到本地的复制。


## <a name="next-steps"></a>后续步骤
- [将 VMware VM 故障转移到 Azure（预览版）](vmware-azure-tutorial-failover-failback-preview.md#run-a-failover-to-azure)
- [计划内故障转移（预览版）](vmware-azure-tutorial-failover-failback-preview.md#planned-failover-from-azure-to-on-premises)
- [重新保护（预览版）](vmware-azure-tutorial-failover-failback-preview.md#re-protect-the-on-premises-machine-to-azure-after-successful-planned-failover)
- [取消故障转移（预览版）](vmware-azure-tutorial-failover-failback-preview.md#cancel-planned-failover)
