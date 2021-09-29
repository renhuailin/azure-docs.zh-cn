---
title: 排查 Azure VM 备份错误
description: 在本文中，学习如何排查在备份和还原 Azure 虚拟机时遇到的错误。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 06/02/2021
ms.openlocfilehash: d3afc24f11400a5d2e7e099690ba9312e9b25ae7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599860"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>排查 Azure 虚拟机上的备份失败问题

可使用下面列出的信息排查使用 Azure 备份时遇到的错误：

## <a name="backup"></a>备份

本部分介绍了 Azure 虚拟机备份操作失败的情况。

### <a name="basic-troubleshooting"></a>基本故障排除

* 确保 VM 代理（WA 代理）为[最新版本](./backup-azure-arm-vms-prepare.md#install-the-vm-agent)。
* 确保 Windows 或 Linux VM OS 版本受支持，详见 [IaaS VM 备份支持矩阵](./backup-support-matrix-iaas.md)。
* 验证另一备份服务是否在运行。
  * 若要确保没有快照扩展问题，请[卸载扩展，然后强制重新加载并重试备份](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)。
* 验证 VM 是否已建立 Internet 连接。
  * 确保另一备份服务未运行。
* 在 `Services.msc` 中确保 **Windows Azure 来宾代理** 服务处于“正在运行”状态。 如果 **Windows Azure 来宾代理** 服务缺失，请按照 [在恢复服务保管库中备份 Azure VM](./backup-azure-arm-vms-prepare.md#install-the-vm-agent) 中的说明来安装它。
* 事件日志可能会显示其他备份产品（例如 Windows Server 备份）的备份故障，而不会显示因 Azure 备份导致的故障。 通过以下步骤确定问题是否来自 Azure 备份：
  * 如果事件源或消息的“备份”条目出现错误，请检查 Azure IaaS VM Backup 备份是否已成功，以及是否已使用所需快照类型创建一个还原点。
  * 如果 Azure 备份正常运行，则问题可能出在其他备份解决方案。
  * 下面是一个示例，介绍了事件查看器错误 517，其中的 Azure 备份正常运行，但“Windows Server 备份”发生故障：![Windows Server 备份故障](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * 如果 Azure 备份故障，则请在本文的“常见 VM 备份错误”部分查找相应的错误代码。
  * 如果在 Azure VM 上看到 Azure 备份选项显示为灰色，请将鼠标悬停在“禁用”菜单上以查找原因。 原因可能是“不支持 EphemeralDisk”或“不支持超级磁盘”。
    ![禁用 Azure 备份选项的原因](media/backup-azure-vms-troubleshoot/azure-backup-disable-reasons.png)

## <a name="common-issues"></a>常见问题

下面是 Azure 虚拟机上出现的常见备份故障问题。

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError - VM 中配置的防病毒软件正在限制备份扩展的执行

错误代码：VMRestorePointInternalError

如果在备份时，“事件查看器应用程序日志”显示消息“发生故障的应用程序名称 **:** IaaSBcdrExtension.exe”，经过确认，问题是 VM 中配置的防病毒软件正在限制备份扩展的执行。
若要解决此问题，请在防病毒软件配置中排除以下目录，然后重试备份操作。

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - 从保管库复制已备份数据已超时

错误代码：CopyingVHDsFromBackUpVaultTakingLongTime <br/>
错误消息：从保管库复制备份的数据超时

发生这种情况可能是因为暂时性的存储错误或存储帐户 IOPS 不足，导致备份服务无法在超时范围内将数据传输到保管库。 使用以下[最佳做法](backup-azure-vms-introduction.md#best-practices)配置 VM 备份，然后重试备份操作。

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - VM 未处于允许备份的状态

错误代码：UserErrorVmNotInDesirableState <br/>
错误消息：VM 未处于允许备份的状态。<br/>

因为 VM 处于“失败”状态，所以备份操作失败。 若要成功进行备份，VM 状态应该是“正在运行”、“已停止”或“已停止(已解除分配)”。

* 如果 VM 处于“运行”和“关闭”之间的瞬时状态，请等待状态更改 。 然后触发备份作业。
* 如果 VM 是 Linux VM 并使用安全性增强的 Linux 内核模块，则需要从安全策略排除 Azure Linux 代理路径 (/var/lib/waagent)，确保已安装备份扩展。

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - 无法冻结一个或多个 VM 装入点以获取文件系统一致性快照

错误代码：UserErrorFsFreezeFailed <br/>
错误消息：未能冻结一个或多个 VM 装入点来获取文件系统一致快照。

* 使用 umount 命令卸载未清除文件系统状态的设备。
* 使用 **fsck** 命令在这些设备上运行文件系统一致性检查。
* 再次装载设备，并重试备份操作。</ol>

如果无法卸载设备，则可以更新 VM 备份配置以忽略某些装入点。 例如，如果“/mnt/resource”装入点无法卸载并导致 VM 备份失败，则可按如下所示使用 `MountsToSkip` 属性更新 VM 备份配置文件。

```bash
cat /var/lib/waagent/Microsoft.Azure.RecoveryServices.VMSnapshotLinux-1.0.9170.0/main/tempPlugin/vmbackup.conf[SnapshotThread]
fsfreeze: True
MountsToSkip = /mnt/resource
SafeFreezeWaitInSeconds=600
```

### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - COM+ 错误导致扩展安装/操作失败

错误代码：ExtensionSnapshotFailedCOM <br/>
错误消息：由于 COM + 错误导致快照操作失败

错误代码：ExtensionInstallationFailedCOM  <br/>
错误消息：COM+ 错误导致扩展安装/操作失败

错误代码：ExtensionInstallationFailedMDTC <br/>
错误消息：扩展安装失败，出现错误“COM+ 无法与 Microsoft 分布式事务处理协调器通信”。 <br/>

备份操作失败，因为 Windows 服务 **COM+ 系统** 应用程序出现问题。  若要解决此问题，请执行以下步骤：

* 尝试启动/重启 Windows 服务“COM+ 系统应用程序”（通过权限提升的命令提示符“- net start COMSysApp”）。
* 确保“分布式事务处理协调器”服务作为“网络服务”帐户运行。  否则，请将其更改为以“网络服务”帐户的身份运行，并重启“COM+ 系统应用程序”。
* 如果无法重启服务，请执行以下步骤，重新安装“分布式事务处理协调器”服务：
  * 停止 MSDTC 服务
  * 打开命令提示符 (cmd)
  * 运行命令 `msdtc -uninstall`
  * 运行命令 `msdtc -install`
  * 启动 MSDTC 服务
* 启动 Windows 服务“COM+ 系统应用程序”。 “COM+ 系统应用程序”启动后，从 Azure 门户触发备份作业。</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - 快照操作失败，因为 VSS 编写器处于错误状态

错误代码：ExtensionFailedVssWriterInBadState <br/>
错误消息：快照操作失败，因为 VSS 编写器处于错误状态。

发生此错误的原因是 VSS 编写器处于错误状态。 Azure 备份扩展与 VSS 编写器交互以拍摄磁盘快照。 若要解决此问题，请执行以下步骤：

步骤 1：请重启处于错误状态的 VSS 编写器。

* 在提升的命令提示符处，运行 `vssadmin list writers`。
* 输出包含所有 VSS 编写器及其状态。 对于状态不是“[1] 稳定”的每个 VSS 编写器，请重启相应 VSS 编写器的服务。
* 若要重启服务，请从提升的命令提示符处运行以下命令：

  `net stop serviceName` <br>
  `net start serviceName`

> [!NOTE]
> 重启某些服务可能会影响生产环境。 请确保遵循批准过程，并在计划的停机时间重启服务。

步骤 2：如果重启 VSS 编写器不能解决该问题，请从提升的命令提示符（以管理员身份）运行以下命令，以防止为 blob 快照创建线程。

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

步骤 3：如果步骤 1 和 2 不能解决该问题，则故障可能是由于 IOPS 有限而导致 VSS 编写器超时。<br>

若要进行验证，请导航到“系统和事件查看器应用程序日志”，然后检查以下错误消息：<br>
*将写入操作保存到影子复制的卷时，影子副本提供程序超时。这可能是应用程序或系统服务在卷上进行过多活动所致。请稍后在卷上的活动减少时重试。*<br>

解决方案：

* 检查是否可以跨 VM 磁盘分配负载。 这将减少单个磁盘上的负载。 可以[通过在存储级别启用诊断指标来检查 IOP 限制](/troubleshoot/azure/virtual-machines/performance-diagnostics#install-and-run-performance-diagnostics-on-your-vm)。
* 更改备份策略，以在非高峰时段（VM 上的负载最低时）执行备份。
* 升级 Azure 磁盘以支持更高的 IOP。 [在此处了解详细信息](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState - 由于 VSS（卷影复制）服务的状态错误，快照操作失败

错误代码：ExtensionFailedVssServiceInBadState <br/>
错误消息：由于 VSS（卷影复制）服务的状态错误，快照操作失败。

发生此错误的原因是 VSS 服务处于错误状态。 Azure 备份扩展与 VSS 服务交互以拍摄磁盘快照。 若要解决此问题，请执行以下步骤：

重启 VSS（卷影复制）服务。

* 请导航到 Services.msc，然后重启“卷影复制服务”。<br>
（或者）<br>
* 在提升的命令提示符下运行以下命令：

  `net stop VSS` <br>
  `net start VSS`

如果问题仍然存在，请在计划的停机时间重启 VM。

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable - VM 创建失败，因为所选 VM 大小不可用

错误代码：UserErrorSkuNotAvailable 错误消息：VM 创建失败，因为所选 VM 大小不可用。

发生此错误是因为在还原操作过程中选择的 VM 大小不受支持。 <br>

若要解决此问题，请在还原操作过程中使用[还原磁盘](./backup-azure-arm-restore-vms.md#restore-disks)选项。 使用这些磁盘通过 [PowerShell cmdlet](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 根据[可用的受支持 VM 大小](./backup-support-matrix-iaas.md#vm-compute-support)列表创建 VM。

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported - 由于没有市场购买请求，创建 VM 失败

错误代码：UserErrorMarketPlaceVMNotSupported 错误消息：由于没有市场购买请求，创建 VM 失败。

Azure 备份支持备份和还原 Azure 市场中可用的 VM。 尝试还原 Azure 市场中不再可用的 VM（具有特定的计划/发布者设置）时，会发生此错误，[请在此处了解详细信息](/legal/marketplace/participation-policy#offering-suspension-and-removal)。

* 若要解决此问题，请在还原操作过程中使用[还原磁盘](./backup-azure-arm-restore-vms.md#restore-disks)选项，然后使用 [PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 或 [Azure CLI](./tutorial-restore-disk.md) cmdlet 创建 VM，其中包含与该 VM 对应的最新市场信息。
* 如果发布者没有任何市场信息，你可以使用数据磁盘来检索数据，并将其附加到现有 VM。

### <a name="extensionconfigparsingfailure---failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - 无法分析备份扩展的配置

错误代码：ExtensionConfigParsingFailure<br/>
错误消息：无法分析备份扩展的配置。

发生此错误的原因是 MachineKeys 目录 %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys  上的权限已更改。
运行以下命令，并验证 MachineKeys 目录上的权限是否为默认权限：`icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys`。

默认权限如下：

* Everyone:(R,W)
* BUILTIN\Administrators：(F)

如果在“MachineKeys”目录中看到的权限与默认值不同，请执行以下步骤以更正权限、删除证书以及触发备份：

1. 修复“MachineKeys”目录上的权限。 通过在目录中使用 Explorer 安全属性和高级安全设置，将权限重新设为默认值。 从目录中删除所有用户对象（默认值除外），确保 Everyone 权限具有以下特殊访问权限：

   * 列出文件夹/读取数据
   * 读取属性
   * 读取扩展的属性
   * 创建文件/写入数据
   * 创建文件夹/附加数据
   * 写入属性
   * 写入扩展的属性
   * 读取权限
2. 删除其中发布对象为经典部署模式或“Windows Azure CRP 证书生成器”的所有证书 ：

   * [在本地计算机控制台上打开证书](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)。
   * 在“个人” > “证书”下，删除其中发布对象为经典部署模式或 Windows Azure CRP 证书生成器的所有证书   。
3. 触发 VM 备份作业。

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - 扩展状态不支持备份操作

错误代码：ExtensionStuckInDeletionState <br/>
错误消息：扩展状态不支持备份操作

备份操作由于备份扩展出现不一致状态而失败。 若要解决此问题，请执行以下步骤：

* 确保来宾代理已安装并可做出响应
* 从 Azure 门户中，转到“虚拟机” > “所有设置” > “扩展”  
* 选择备份扩展 VmSnapshot 或 VmSnapshotLinux，然后选择“卸载”。
* 在删除备份扩展后重试备份操作
* 后续备份操作将以所需的状态安装新扩展

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - 由于某些附加的磁盘已超出快照限制，因此快照操作失败

错误代码：ExtensionFailedSnapshotLimitReachedError   <br/>
错误消息：由于某些附加的磁盘已超出快照限制，因此快照操作失败

由于某些附加的磁盘已超出快照限制，因此快照操作失败。 完成以下故障排除步骤，然后重试操作。

* 删除不需要的磁盘 blob 快照。 请注意不要删除磁盘 blob。 只应删除快照 blob。
* 如果在 VM 磁盘存储帐户上启用了软删除，请配置软删除保留，以使现有快照小于任何时间点允许的最大值。
* 如果在备份的 VM 中启用了 Azure Site Recovery，请执行以下步骤：

  * 确保在 /etc/azure/vmbackup.conf 中将“isanysnapshotfailed”的值设置为 false
  * 在不同时间计划 Azure Site Recovery，使其不会与备份操作产生冲突。

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - 快照操作因 VM 资源不足而失败

错误代码：ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
错误消息：快照操作因 VM 资源不足而失败。

VM 上的备份操作由于执行快照操作时进行的网络调用发生延迟而失败。 若要解决此问题，请执行步骤 1。 如果问题仍然存在，请尝试执行步骤 2 和 3。

**步骤 1**：通过主机创建快照

从提升的（管理员）命令提示符下，运行以下命令：

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

这将确保通过主机而不是来宾来拍摄快照。 请重试备份操作。

**步骤 2**：尝试将备份计划更改到 VM 的负载较小（如 CPU 或 IOPS 较小）的某个时间

**步骤 3**：尝试 [增大 VM 的大小](../virtual-machines/resize-vm.md)并重试操作

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001，ResourceNotFound - 无法执行操作，因为 VM 不存在/400094, BCMV2VMNotFound - 虚拟机不存在/找不到 Azure 虚拟机

错误代码：320001，ResourceNotFound <br/> 错误消息：无法执行该操作，因为 VM 已不存在。 <br/> <br/> 错误代码：400094，BCMV2VMNotFound <br/> 错误消息：虚拟机不存在 <br/>
找不到 Azure 虚拟机。

删除主 VM 时会发生此错误，但备份策略仍会查找要备份的 VM。 要修复此错误，请执行以下步骤：

* 重新创建具有相同名称和相同资源组名称的虚拟机，“云服务名称”<br>或
* 通过删除或不删除备份数据来停止保护虚拟机。 有关更多信息，请参阅[停止保护虚拟机](backup-azure-manage-vms.md#stop-protecting-a-vm)。</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError - 由于存储帐户中的可用空间不足，无法复制虚拟机的快照

错误代码：UserErrorBCMPremiumStorageQuotaError<br/> 错误消息：由于存储帐户中的可用空间不足，无法复制虚拟机的快照

 对于 VM 备份堆栈 V1 上的高级 VM，我们将快照复制到存储帐户。 此步骤可确保在快照上运行的备份管理流量不会限制使用高级磁盘的应用程序的可用 IOPS 数。 <br><br>我们建议只分配总存储帐户空间的 50%（即 17.5 TB）。 这样，Azure 备份服务可以将快照复制到存储帐户，并将数据从存储帐户中的复制位置传输到保管库。

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008，AzureVmOffline - 无法安装 Microsoft 恢复服务扩展，因为虚拟机未运行

错误代码：380008，AzureVmOffline <br/> 错误消息：无法安装 Microsoft 恢复服务扩展，因为虚拟机未运行

VM 代理是 Azure 恢复服务扩展的先决条件。 安装 Azure 虚拟机代理并重启注册操作。 <br> <ol> <li>检查 VM 代理是否安装正确。 <li>确保已正确设置 VM 配置中的标志。</ol> 阅读有关安装 VM 代理以及如何验证 VM 代理安装的详细信息。

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError - 快照操作失败，出现卷影复制服务 (VSS) 操作错误

错误代码：ExtensionSnapshotBitlockerError <br/> 错误消息：快照操作失败，出现卷影复制服务 (VSS) 操作错误“此驱动器已通过 BitLocker 驱动器加密锁定。必须通过控制面板解锁此驱动器”。

关闭 VM 上的所有驱动器的 BitLocker，并检查 VSS 问题是否得到解决。

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState - VM 未处于允许备份的状态

错误代码：VmNotInDesirableState <br/> 错误消息：VM 未处于允许备份的状态。

* 如果 VM 处于“运行”和“关闭”之间的瞬时状态，请等待状态更改 。 然后触发备份作业。
* 如果 VM 是 Linux VM 并使用安全性增强的 Linux 内核模块，则需要从安全策略排除 Azure Linux 代理路径 (/var/lib/waagent)，确保已安装备份扩展。

* 虚拟机上不存在 VM 代理： <br>安装任何必备组件和 VM 代理。 然后，重启该操作。 阅读有关 [VM 代理安装以及如何验证 VM 代理安装](#vm-agent)的详细信息。

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork - 由于无法创建安全的网络通信通道，快照操作失败

错误代码：ExtensionSnapshotFailedNoSecureNetwork <br/> 错误消息：由于无法创建安全的网络通信通道，因此快照操作失败。

* 通过在权限提升模式下运行“regedit.exe”来打开注册表编辑器。
* 标识系统中存在的所有 .NET Framework 版本。 它们位于注册表项“HKEY_LOCAL_MACHINE \ SOFTWARE \ Microsoft”的层次结构下。
* 请为注册表项中存在的每个 .Net Framework 添加以下键： <br> “SchUseStrongCrypto"=dword:00000001”。 </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure - 由于 Visual C++ Redistributable for Visual Studio 2012 安装失败，快照操作失败

错误代码：ExtensionVCRedistInstallationFailure <br/> 错误消息：由于 Visual C++ Redistributable for Visual Studio 2012 安装失败，因此快照操作失败。

* 导航到 `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` 并安装 vcredist2013_x64。<br/>请确保允许此服务安装的注册表项值设置为正确的值。 也就是说，将 **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** 中的 **Start** 值设置为 **3**，而不是 **4**。 <br><br>如果仍然遇到安装问题，请通过权限提升的命令提示符运行“MSIEXEC /UNREGISTER”，接着运行“MSIEXEC /REGISTER”来重启安装服务 。
* 检查事件日志以验证是否注意到与访问相关的问题。 例如：*Product:Microsoft Visual C++ 2013 x64 Minimum Runtime - 12.0.21005 -- Error 1401.Could not create key:Software\Classes.System error 5.请验证你是否拥有足够的权限访问该注册表项，或者与支持人员联系。* <br><br> 确保管理员或用户帐户有足够的权限更新注册表项“HKEY_LOCAL_MACHINE\SOFTWARE\Classes”。 提供足够的权限并重启 Windows Azure 来宾代理。<br><br> <li> 如果你安装了防病毒产品，请确保它们具有正确的排除规则以允许安装。

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy - 在 VM 上配置了防止快照操作的无效策略

错误代码：UserErrorRequestDisallowedByPolicy <BR> 错误消息：在 VM 上配置了防止快照操作的无效策略。

如果你的 Azure 策略[控制环境中的标记](../governance/policy/tutorials/govern-tags.md)，请考虑将该策略从 [Deny 效果](../governance/policy/concepts/effects.md#deny)更改为 [Modify 效果](../governance/policy/concepts/effects.md#modify)，或者按照 [Azure 备份所需的命名架构](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)要求手动创建资源组。

## <a name="jobs"></a>作业

| 错误详细信息 | 解决方法 |
| --- | --- |
| 此作业类型不支持取消： <br>请等待作业完成。 |无 |
| 该作业未处于可取消状态： <br>请等待作业完成。 <br>**or**<br> 所选作业未处于可取消状态： <br>请等待作业完成。 |这项作业很可能快完成了。 等待作业完成。|
| 备份不能取消该作业，因为它没有正在进行： <br>仅支持取消正在进行的作业。 尝试取消正在进行的作业。 |由于临时状态而发生此错误。 请稍等片刻，并重试取消操作。 |
| 备份未能取消作业： <br>请等待作业完成。 |无 |

## <a name="restore"></a>还原

### <a name="disks-appear-offline-after-file-restore"></a>文件还原后磁盘显示为脱机状态

如果还原后发现磁盘处于脱机状态，请执行以下操作：

* 验证执行脚本的计算机是否满足 OS 要求。 [了解详细信息](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script)。
* 确保不会还原到同一个源，[了解详细信息](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound - 还原失败，因为找不到 VM 的快照

错误代码：UserErrorInstantRpNotFound <br>
错误消息：还原失败，因为找不到 VM 的快照。 快照可能已被删除，请检查。<br>

尝试从尚未转移到保管库且已在快照阶段删除的恢复点还原时，会发生此错误。 
<br>
若要解决此问题，请尝试从其他还原点还原 VM。<br>

#### <a name="common-errors"></a>常见错误

| 错误详细信息 | 解决方法 |
| --- | --- |
| 还原失败，发生云内部错误。 |<ol><li>尝试还原的云服务使用 DNS 设置进行配置。 可以检查： <br>“$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings”。<br>如果配置了“地址”，则配置了 DNS 设置。<br> <li>尝试还原的云服务配置了“ReservedIP”，且云服务中的现有 VM 处于停止状态。 可以使用以下 PowerShell cmdlet 检查云服务是否已保留 IP：$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName。 <br><li>正在尝试将具有以下特殊网络配置的虚拟机还原到同一个云服务中： <ul><li>采用负载均衡器配置的虚拟机（内部和外部）。<li>具有多个保留 IP 的虚拟机。 <li>具有多个 NIC 的虚拟机。 </ul><li>请在 UI 中选择新的云服务，或参阅[还原注意事项](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)，了解具有特殊网络配置的 VM。</ol> |
| 已存在所选的 DNS 名称： <br>请指定其他 DNS 名称，然后重试。 |此 DNS 名称是指云服务名称，通常以 “cloudapp.net”结尾。 此名称必须是唯一名称。 如果出现此错误，则需在还原期间选择其他 VM 名称。 <br><br> 此错误仅向 Azure 门户用户显示。 通过 PowerShell 进行的还原操作成功，因为它仅还原磁盘而不创建 VM。 如果在磁盘还原操作之后显式创建 VM，则会遇到该错误。 |
| 指定的虚拟网络配置不正确： <br>指定其他虚拟网络配置，然后重试。 |无 |
| 指定的云服务使用与要还原的虚拟机的配置不匹配的保留 IP： <br>指定不使用保留的 IP 的其他云服务。 或者选择要还原的其他恢复点。 |无 |
| 云服务已达到其输入终结点数量的限制： <br>通过指定其他云服务或使用现有终结点重试该操作。 |无 |
| 恢复服务保管库和目标存储帐户位于两个不同的区域： <br>确保还原操作中指定的存储帐户与恢复服务保管库位于同一 Azure 区域中。 |无 |
| 为还原操作指定的存储帐户不受支持： <br>仅支持具有本地冗余或异地冗余复制设置的基本或标准存储帐户。 选择受支持的存储帐户。 |无 |
| 为还原操作指定的存储帐户的类型不是联机状态： <br>确保还原操作中指定的存储帐户处于联机状态。 |如果 Azure 存储中出现暂时性错误或中断，可能会发生此错误。 请选择另一个存储帐户。 |
| 资源组配额已达限制： <br>请从 Azure 门户中删除某些资源组，或者与 Azure 支持部门联系，以提高限额。 |无 |
| 所选子网不存在： <br>选择存在的子网。 |无 |
| 备份服务无权访问订阅中的资源。 |要修复此错误，请首先使用[还原备份磁盘](backup-azure-arm-restore-vms.md#restore-disks)中的步骤来还原磁盘。 然后使用[从已还原的磁盘创建 VM](backup-azure-vms-automation.md#restore-an-azure-vm) 中的 PowerShell 步骤。 |

## <a name="backup-or-restore-takes-time"></a>备份或还原需要一定时间

如果备份时间超过 12 小时，或者还原时间超过 6 小时，请查看[最佳做法](backup-azure-vms-introduction.md#best-practices)和[性能注意事项](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM 代理

### <a name="set-up-the-vm-agent"></a>设置 VM 代理

通常，VM 代理已存在于从 Azure 库创建的 VM 中。 但是，从本地数据中心迁移的虚拟机上将不会安装 VM 代理。 对于这些 VM，必须显式安装 VM 代理。

#### <a name="windows-vms---set-up-the-agent"></a>Windows VM - 设置代理

* 下载并安装 [代理 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 需要有管理员权限才能完成安装。
* 对于使用经典部署模型创建的虚拟机，请[更新 VM 属性](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms)以指示已安装代理。 Azure 资源管理器虚拟机不需要此步骤。

#### <a name="linux-vms---set-up-the-agent"></a>Linux VM - 设置代理

* 从分发存储库安装最新版本的代理。 有关包名称的详细信息，请参阅 [Linux 代理存储库](https://github.com/Azure/WALinuxAgent)。
* 对于使用经典部署模型创建的 VM，请[更新 VM 属性](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms)并验证是否已安装代理。 无需对资源管理器虚拟机执行此步骤。

### <a name="update-the-vm-agent"></a>更新 VM 代理

#### <a name="windows-vms---update-the-agent"></a>Windows VM - 更新代理

* 若要更新 VM 代理，请重新安装 [VM 代理二进制文件](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 在更新代理之前，请确保在更新 VM 代理期间不会执行备份操作。

#### <a name="linux-vms---update-the-agent"></a>Linux VM - 更新代理

* 要更新 Linux VM 代理，请按照[更新 Linux VM 代理](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json)一文中的说明进行操作。

    > [!NOTE]
    > 始终使用分发存储库来更新代理。

    请勿从 GitHub 下载代理代码。 如果最新代理不适用于发行版，请与分发支持部门联系，获取有关获取最新代理的说明。 还可以在 GitHub 存储库中查看最新的 [Windows Azure Linux 代理](https://github.com/Azure/WALinuxAgent/releases)信息。

### <a name="validate-vm-agent-installation"></a>验证 VM 代理安装

验证 Windows VM 上的 VM 代理版本：

1. 登录到 Azure 虚拟机并导航到 **C:\WindowsAzure\Packages** 文件夹。 应会发现“WaAppAgent.exe”文件。
2. 右键单击该文件并转到“属性”。 然后选择“详细信息”选项卡。“产品版本”字段应为 2.6.1198.718 或更高版本。

## <a name="troubleshoot-vm-snapshot-issues"></a>排查 VM 快照问题

VM 备份依赖于向底层存储发出快照命令。 如果无法访问存储或者快照任务运行延迟，则备份作业可能会失败。 以下状态可能会导致快照任务失败：

* 配置了 SQL Server 备份的 VM 可能会导致快照任务延迟。 默认情况下，VM 备份在 Windows VM 上创建 VSS 完整备份。 运行 SQL Server 且配置有 SQL Server 备份的 VM 可能会遇到快照延迟。 如果快照延迟导致备份失败，请设置以下注册表项：

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* 由于在 RDP 中关闭了 VM，VM 状态报告不正确。 如果使用远程桌面关闭虚拟机，请验证门户中的 VM 状态是否正确。 如果状态不正确，请使用门户 VM 仪表板中的“关闭”选项关闭 VM。
* 如果四个以上的 VM 共享同一云服务，请为 VM 选择多个不同的备份策略。 错开备份时间，使同时开始的 VM 备份不超过四个。 尝试将策略中的开始时间至少隔开一小时。
* VM 在高 CPU 或内存情况下运行。 如果虚拟机在高内存或 CPU 使用率（超过 90%）情况下运行，则快照任务将排队并延迟。 最终会超时。如果发生此问题，请尝试按需备份。

## <a name="networking"></a>网络

必须在来宾内启用 DHCP，才能正常进行 IaaS VM 备份。 如果需要静态专用 IP，请通过 Azure 门户或 PowerShell 配置该 IP。 请确保已启用 VM 内的 DHCP 选项。
获取有关如何通过 PowerShell 设置静态 IP 的详细信息：

* [如何向现有 VM 添加静态内部 IP](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [更改分配给网络接口的专用 IP 地址的分配方法](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
