---
title: 排查无代理 VMware VM 迁移中的复制问题
description: 获取有关复制周期失败的帮助
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: d1654a195f694b9ef8ab8150380939c0e647bcd1
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580667"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>排查无代理 VMware VM 迁移中的复制问题

本文介绍在使用“Azure Migrate: 服务器迁移”无代理方法复制本地 VMware VM 时可能遇到的一些常见问题和具体错误。

使用无代理复制方法复制 VMware 虚拟机时，虚拟机磁盘 (vmdk) 中的数据将复制到 Azure 订阅中的副本托管磁盘。 当 VM 复制开始时，将执行初始复制周期，在此周期将复制磁盘的完整副本。 初始复制完成后，将定期计划增量复制周期，以传输自上次复制周期后发生的所有更改。

你可能偶尔会发现 VM 的复制周期失败。 从本地网络配置问题到 Azure Migrate 云服务后端问题等各种原因都可能导致这些失败。 本文的内容：

 - 介绍如何监视复制状态并解决错误。
 - 列出了一些常见的复制错误，另外还建议了这些错误的修正步骤。

## <a name="monitor-replication-status-using-the-azure-portal"></a>使用 Azure 门户监视复制状态

使用以下步骤监视虚拟机的复制状态：

  1. 在 Azure 门户上转到 Azure Migrate 中的“服务器”页。
  ![图 1](./media/troubleshoot-changed-block-tracking-replication/image0.png)
  1. 在“服务器迁移”磁贴中单击“复制服务器”，导航到“复制计算机”页。
  ![图 2](./media/troubleshoot-changed-block-tracking-replication/image1.png)
  1. 你将看到复制服务器的列表以及其他信息，例如状态、运行状况、上次同步时间，等等。运行状况列指示 VM 的当前复制运行状况。 运行状况列中的“严重”或“警告”值通常指示 VM 的上一个复制周期失败。 若要获取更多详细信息，请右键单击 VM 并选择“错误详细信息”。 “错误详细信息”页包含有关错误的信息，以及有关如何进行故障排除的其他详细信息。 你还将看到可用于导航到 VM 事件页的“最近的事件”链接。
  ![图 3](./media/troubleshoot-changed-block-tracking-replication/image2.png)
  1. 单击“最近的事件”查看 VM 的上一个复制周期失败信息。 在事件页中，查看 VM 的“复制周期失败”或“针对磁盘的复制周期失败”类型的最近事件。
  ![图 4](./media/troubleshoot-changed-block-tracking-replication/image3.png)
  1. 单击事件以了解错误的可能原因和建议的修正步骤。 使用提供的信息来排查并修正错误。
 ![图 5](./media/troubleshoot-changed-block-tracking-replication/image4.png)

## <a name="common-replication-errors"></a>常见复制错误

本部分描述一些常见错误并介绍如何排查这些错误。

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>尝试复制 VM 时出现密钥保管库操作失败错误

错误：“密钥保管库操作失败。 操作: 配置托管存储帐户，密钥保管库: Key-vault-name，存储帐户: 存储帐户名称。操作失败，出现以下错误:”

错误：“密钥保管库操作失败。 操作: 生成共享访问签名定义，密钥保管库: Key-vault-name，存储帐户: 存储帐户名称。操作失败，出现以下错误:”

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

发生此错误的原因通常是，针对密钥保管库的用户访问策略未向当前已登录的用户授予所需的权限，用户无法将存储帐户配置为密钥保管库托管帐户。 若要检查针对密钥保管库的用户访问策略，请在密钥保管库的门户上转到“密钥保管库”页，然后选择“访问策略” 

当门户创建密钥保管库时，它还会添加一个用户访问策略，该策略会向当前已登录的用户授予将存储帐户配置为密钥保管库托管帐户的权限。 此过程可能会出于两种原因而失败

- 已登录的用户是客户 Azure 租户上的远程主体（云解决方案提供商订阅 - 已登录的用户是合作伙伴管理员）。 在这种情况下，解决方法是删除密钥保管库，从门户注销，然后使用客户租户中的用户帐户（不是远程主体）登录并重试操作。 云解决方案提供商合作伙伴通常在他们可以使用的客户 Azure Active Directory 租户中拥有一个用户帐户。 如果没有帐户，则他们可以在客户 Azure Active Directory 租户中为自己创建一个新的用户帐户，以新用户的身份登录到门户，然后重试该复制操作。 必须为使用的帐户授予对资源组（Migrate 项目资源组）中帐户的“所有者”权限，或者授予对该资源组中帐户的“参与者”和“用户访问管理员”权限

- 发生此错误的另一种情况是，某个用户 (user1) 首次尝试设置复制时遇到失败，但密钥保管库已创建（已经适当地向此用户分配了用户访问策略）。 后来，另一个用户 (user2) 尝试设置复制，但“配置托管存储帐户”或“生成 SAS 定义”操作失败，因为密钥保管库中没有与 user2 对应的用户访问策略。

解决方法：若要解决此问题，请在密钥保管库中为 user2 创建一个用户访问策略，该策略为 user2 授予配置托管存储帐户和生成 SAS 定义的权限。 user2 可以在 Azure PowerShell 中使用以下 cmdlet 来执行此操作：

$userPrincipalId = $(Get-AzureRmADUser -UserPrincipalName "user2_email_address").Id

Set-AzureRmKeyVaultAccessPolicy -VaultName "keyvaultname" -ObjectId $userPrincipalId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

错误 ID：181008

错误消息：VM: VMName。 错误: 遇到状态为 &'['Gateway.Service.StateMachine.SnapshotReplication.SnapshotReplicationEngine+WaitingForArtefactsDisposalPreCycle' ('WaitingForArtefactsDisposalPreCycle')]' 的超时事件 'DisposeArtefactsTimeout'。

可能的原因：

尝试将数据复制到 Azure 的组件已关闭或无响应。 可能的原因包括：

- Azure Migrate 设备中运行的网关服务已关闭。
- 网关服务遇到与服务总线/事件中心/设备存储帐户相关的连接问题。

确定 DisposeArtefactsTimedOut 的确切原因和相应的解决方法：

1. 确保 Azure Migrate 设备已启动并运行。
2. 检查网关服务是否正在设备上运行：
   1.  使用远程桌面登录到 Azure Migrate 设备，然后执行以下操作。

   2.  打开 Microsoft 服务 MMC 管理单元（“运行”>“services.msc”），检查“Microsoft Azure 网关服务”是否正在运行。 如果该服务已停止或未运行，请将它启动。 或者，可以打开命令提示符或 PowerShell，并执行“Net Start asrgwy”

3. 检查 Azure Migrate 设备与设备存储帐户之间的连接问题： 

    在 Azure Migrate 设备中下载 azcopy 后运行以下命令：
    
    azcopy bench https://[account].blob.core.windows.net/[container]?SAS
    
    用于运行性能基准测试的步骤：
    
      1. [下载](../storage/common/storage-use-azcopy-v10.md) azcopy
        
      2. 在资源组中找到设备存储帐户。 该存储帐户的名称类似于 migrategwsa\*\*\*\*\*\*\*\*\*\*。 这是上述命令中参数 [account] 的值。
        
      3. 在 Azure 门户中搜索你的存储帐户。 确保用于搜索的订阅是在其中创建了该存储帐户的同一订阅（目标订阅）。 转到“Blob 服务”部分的“容器”。 单击“+容器”并创建一个容器。 将“公共访问级别”保留为默认选择的值。
        
      4. 转到“设置”下的“共享访问签名”。 在“允许的资源类型”中选择“容器”。 单击“生成 SAS 和连接字符串”。 复制 SAS 值。
        
      5. 将上述命令中的 account、container 和 SAS 替换为分别在步骤 2、3 和 4 中获取的值，然后在命令提示符下执行该命令。
        
      或者，将 Azure 存储资源管理器[下载](https://go.microsoft.com/fwlink/?linkid=2138967)到设备，然后尝试将大约 64 MB 的 10 个 Blob 上传到存储帐户。 如果没有问题，则上传应会成功。
        
    解决方法：如果此测试失败，则表示存在网络问题。 请联系你的本地网络团队来检查连接问题。 通常，可能是某些防火墙设置导致了失败。
    
4.  检查 Azure Migrate 设备与服务总线之间的连接问题：

    此项测试检查 Azure Migrate 设备能否与 Azure Migrate 云服务后端通信。 设备通过服务总线和事件中心消息队列来与服务后端通信。 若要验证是否可从设备连接到服务总线，请[下载](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer，尝试连接到设备服务总线，然后执行发送消息/接收消息的操作。 如果没有问题，则此操作应会成功。

    用于运行测试的步骤：

    1. 从服务总线复制在 Migrate 项目中创建的连接字符串
    2. 打开 Service Bus Explorer
    3. 转到“文件”>“连接”
    4. 粘贴连接字符串并单击“连接”
    5. 此时会打开“服务总线命名空间”
    6. 在主题中选择“Snapshot Manager”。 右键单击“Snapshot Manager”，选择“接收消息”>“速览”，然后单击“确定”
    7. 如果连接成功，则控制台输出中会显示“已收到 [x] 条消息”。 如果连接不成功，则会显示一条指出连接失败的消息
    
    解决方法：如果此测试失败，则表示存在网络问题。 请联系你的本地网络团队来检查连接问题。 通常，可能是某些防火墙设置导致了失败。

5. Azure Migrate 设备与 Azure 密钥保管库之间的连接问题：

    此项测试检查 Azure Migrate 设备与 Azure 密钥保管库之间的连接问题。 密钥保管库用于管理在复制时使用的存储帐户访问权限。
    
    用于检查连接的步骤：
    
    1. 从对应于 Azure Migrate 项目的资源组中的资源列表提取密钥保管库 URI。
    
    1. 在 Azure Migrate 设备中打开 PowerShell 并运行以下命令：
    
    test-netconnection Key Vault URI -P 443
    
    此命令将尝试建立 TCP 连接并返回输出。
    
     - 在输出中，检查字段“TcpTestSucceeded”。 如果值为“True”，则表示 Azure Migrate 设备与 Azure 密钥保管库之间不存在连接问题。 如果值为“False”，则表示存在连接问题。
    
    解决方法：如果此测试失败，则表示 Azure Migrate 设备与 Azure 密钥保管库之间存在连接问题。 请联系你的本地网络团队来检查连接问题。 通常，可能是某些防火墙设置导致了失败。
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

错误 ID：1011

错误消息：虚拟机 VMName (VMId)的磁盘 DiskPath (DiskId)的数据上传未在预期时间内完成。

此错误通常表示执行复制的 Azure Migrate 设备无法连接到 Azure 云服务，或者复制速度逐渐减慢，导致复制周期超时。

可能的原因包括：

- Azure Migrate 设备已关闭。
- 设备上的复制网关服务未运行。
- 复制网关服务在连接到以下用于复制的 Azure 服务组件之一时遇到问题：服务总线/事件中心/Azure 缓存存储帐户/Azure 密钥保管库。
- 网关服务在尝试读取磁盘时在 vCenter 级别受到限制。

确定根本原因并解决问题：

1. 确保 Azure Migrate 设备已启动并运行。
2. 检查网关服务是否正在设备上运行：
   1.  使用远程桌面登录到 Azure Migrate 设备，然后执行以下操作。

   2.  打开 Microsoft 服务 MMC 管理单元（“运行”>“services.msc”），检查“Microsoft Azure 网关服务”是否正在运行。 如果该服务已停止或未运行，请将它启动。 或者，可以打开命令提示符或 PowerShell，并执行“Net Start asrgwy”。


3. 检查 Azure Migrate 设备与缓存存储帐户之间的连接问题： 

    在 Azure Migrate 设备中下载 azcopy 后运行以下命令：
    
    azcopy bench https://[account].blob.core.windows.net/[container]?SAS
    
    用于运行性能基准测试的步骤：
    
      1. [下载](../storage/common/storage-use-azcopy-v10.md) azcopy
        
      2. 在资源组中找到设备存储帐户。 该存储帐户的名称类似于 migratelsa\*\*\*\*\*\*\*\*\*\*。 这是上述命令中参数 [account] 的值。
        
      3. 在 Azure 门户中搜索你的存储帐户。 确保用于搜索的订阅是在其中创建了该存储帐户的同一订阅（目标订阅）。 转到“Blob 服务”部分的“容器”。 单击“+容器”并创建一个容器。 将“公共访问级别”保留为默认选择的值。
        
      4. 转到“设置”下的“共享访问签名”。 在“允许的资源类型”中选择“容器”。 单击“生成 SAS 和连接字符串”。 复制 SAS 值。
        
      5. 将上述命令中的 account、container 和 SAS 替换为分别在步骤 2、3 和 4 中获取的值，然后在命令提示符下执行该命令。
        
      或者，将 Azure 存储资源管理器[下载](https://go.microsoft.com/fwlink/?linkid=2138967)到设备，然后尝试将大约 64 MB 的 10 个 Blob 上传到存储帐户。 如果没有问题，则上传应会成功。
        
    解决方法：如果此测试失败，则表示存在网络问题。 请联系你的本地网络团队来检查连接问题。 通常，可能是某些防火墙设置导致了失败。
                
4.  检查 Azure Migrate 设备与 Azure 服务总线之间的连接问题：

    此项测试将检查 Azure Migrate 设备能否与 Azure Migrate 云服务后端通信。 设备通过服务总线和事件中心消息队列来与服务后端通信。 若要验证是否可从设备连接到服务总线，请[下载](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer，尝试连接到设备服务总线，然后执行发送消息/接收消息的操作。 如果没有问题，则此操作应会成功。

    用于运行测试的步骤：
    
    1. 从服务总线复制在与 Azure Migrate 项目对应的资源组中创建的连接字符串
    
    1. 打开 Service Bus Explorer
    
    1. 转到“文件”>“连接”
    
    1. 粘贴在步骤 1 中复制的连接字符串，然后单击“连接”
    
    1. 此时会打开“服务总线命名空间”。
    
    1. 在命名空间主题中选择“Snapshot Manager”。 右键单击“Snapshot Manager”，选择“接收消息”>“速览”，然后单击“确定”。
    
    如果连接成功，则控制台输出中会显示“已收到 [x] 条消息”。 如果连接不成功，则会显示一条指出连接失败的消息。
    
    解决方法：如果此测试失败，则表示 Azure Migrate 设备与服务总线之间存在连接问题。 请联系你的本地网络团队来检查这些连接问题。 通常，可能是某些防火墙设置导致了失败。
    
 5. Azure Migrate 设备与 Azure 密钥保管库之间的连接问题：

    此项测试检查 Azure Migrate 设备与 Azure 密钥保管库之间的连接问题。 密钥保管库用于管理在复制时使用的存储帐户访问权限。
    
    用于检查连接的步骤：
    
    1. 从对应于 Azure Migrate 项目的资源组中的资源列表提取密钥保管库 URI。
    
    1. 在 Azure Migrate 设备中打开 PowerShell 并运行以下命令：
    
    test-netconnection Key Vault URI -P 443
    
    此命令将尝试建立 TCP 连接并返回输出。
    
    1. 在输出中，检查字段“TcpTestSucceeded”。 如果值为“True”，则表示 Azure Migrate 设备与 Azure 密钥保管库之间不存在连接问题。 如果值为“False”，则表示存在连接问题。
    
    解决方法：如果此测试失败，则表示 Azure Migrate 设备与 Azure 密钥保管库之间存在连接问题。 请联系你的本地网络团队来检查连接问题。 通常，可能是某些防火墙设置导致了失败。
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>尝试提取已更改块时遇到错误

错误消息：“尝试提取更改块时遇到错误”

无代理复制方法使用 VMware 的已更改块跟踪技术 (CBT) 将数据复制到 Azure。 借助 CBT，服务器迁移工具可以仅跟踪和复制自上一复制周期以来已更改的块。 如果重置复制虚拟机的更改后的块跟踪或更改后的块跟踪文件已损坏，则会发生此错误。

可通过以下两种方式解决此错误：

- 如果你在触发了 VM 复制时通过选择“是”启用了“自动修复复制”，则该工具将尝试修复复制。 右键单击 VM 并选择“修复复制”。
- 如果未启用“自动修复复制”或者上述步骤不起作用，请停止虚拟机复制，在虚拟机上[重置已更改块跟踪](https://go.microsoft.com/fwlink/?linkid=2139203)，然后重新配置复制。

[VMware KB 1020128: Changed Block Tracking is reset after a storage vMotion operation in vSphere 5.x](https://kb.vmware.com/s/article/1020128)（VMware 知识库文章 1020128：在 vSphere 5.x 中执行存储 vMotion 操作后，已更改块跟踪被重置）中描述了一个可能导致 VMware vSphere 5.5 上的虚拟机 CBT 重置的已知问题。 如果你使用的是 VMware vSphere 5.5，请确保使用此知识库中描述的更新。

或者，可以使用 VMware PowerCLI 重置虚拟机上的 VMware 已更改块跟踪。

## <a name="an-internal-error-occurred"></a>出现内部错误

有时，你可能会遇到由于 VMware 环境/API 中的问题导致的错误。 我们已将下面一组错误标识为 VMware 环境相关的错误。 这些错误具有固定的格式。

错误消息: 出现内部错误。[错误消息]

例如：错误消息: 出现内部错误。 [检测到无效的快照配置]。

以下部分列出了一些常见 VMware 错误及其缓解方法。

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>错误消息: 出现内部错误。 [服务器拒绝连接]

这是一个已知的 VMware 问题，发生在 VDDK 6.7 中。 需要停止 Azure Migrate 设备中运行的网关服务，[从 VMware 知识库下载更新](https://go.microsoft.com/fwlink/?linkid=2138889)，然后重启网关服务。

用于停止网关服务的步骤：

1. 按 Windows + R，打开 services.msc。 单击“Microsoft Azure 网关服务”并将其停止。
2. 或者，可以打开命令提示符或 PowerShell，并执行“Net Stop asrgwy”。 请务必等到出现该服务不再运行的消息。

用于启动网关服务的步骤：

1. 按 Windows + R，打开 services.msc。 右键单击“Microsoft Azure 网关服务”并将其启动。
2. 或者，可以打开命令提示符或 PowerShell，并执行 Net Start asrgwy。

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>错误消息: 出现内部错误。 [“检测到无效的快照配置。”]

如果你的虚拟机包含多个磁盘，从虚拟机中删除某个磁盘时可能会遇到此错误。 若要修正此问题，请参阅[此 VMware 文章](https://go.microsoft.com/fwlink/?linkid=2138890)中的步骤。

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>错误消息: 出现内部错误。 [生成快照挂起]

当快照生成操作停止响应时，会出现此问题。 出现此问题时，可能会看到快照创建任务停止在 95% 或 99% 处。 请参阅 [VMware 知识库文章](https://go.microsoft.com/fwlink/?linkid=2138969)来解决此问题。

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>错误消息: 出现内部错误。 [无法合并 VM 上的磁盘 [原因]]

如果在复制周期结束时合并磁盘，该操作将会失败。 选择相应的“原因”并按照此 [VMware 知识库文章](https://go.microsoft.com/fwlink/?linkid=2138970)中的说明解决该问题。

执行 VMware 快照相关的操作（创建、删除或合并磁盘）失败时出现以下错误。 按照下一部分中的指导修正错误：

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>错误消息: 出现内部错误。 [另一任务已在进行中]

如果后台正在运行有冲突的虚拟机任务，或者 vCenter Server 中的任务超时，则会出现此问题。请遵循以下 [VMware 知识库文章](https://go.microsoft.com/fwlink/?linkid=2138891)中提供的解决方法。

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>错误消息: 出现内部错误。 [在当前状态下不允许该操作]

当 vCenter Server 管理代理停止工作时会出现此问题。 若要解决此问题，请参阅以下 [VMware 知识库文章](https://go.microsoft.com/fwlink/?linkid=2138971)中的解决方法。

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>错误消息: 出现内部错误。 [快照磁盘大小无效]

这是一个已知的 VMware 问题：快照指示的磁盘大小为零。 请遵循 [VMware 知识库文章](https://kb.vmware.com/s/)中提供的解决方法。

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>错误消息: 出现内部错误。 [内存分配失败。 内存不足。]

当 NFC 主机缓冲区的内存不足时，会发生这种情况。 若要解决此问题，需要将 VM（计算 vMotion）转移到具有可用资源的另一台主机。

### <a name="error-message-an-internal-error-occurred-file-is-larger-than-maximum-file-size-supported-1012384"></a>错误消息: 出现内部错误。 [文件大于受支持的最大文件大小 (1012384)]
如果文件大小大于创建快照时支持的最大文件大小，则会发生此情况。 请遵循 [VMware 知识库](https://kb.vmware.com/s/article/1012384)中提供的解决方法

### <a name="error-message-an-internal-error-occurred-cannot-connect-to-the-host-1004109"></a>错误消息: 出现内部错误。 [无法连接到主机 (1004109)]
当 ESXi 主机无法连接到网络时，会发生此情况。 请遵循 [VMware 知识库文章](https://kb.vmware.com/s/article/1004109)中提供的解决方法。

### <a name="error-message-an-error-occurred-while-saving-the-snapshot-invalid-change-tracker-error-code"></a>错误消息：保存快照时出错: 更改跟踪器错误代码无效
当存储快照的储基础数据存出现问题时，会出现此错误。 请遵循 [VMware 知识库文章](https://kb.vmware.com/s/article/2042742)中提供的解决方法。

### <a name="error-message-an-error-occurred-while-taking-a-snapshot-unable-to-open-the-snapshot-file"></a>错误消息：拍摄快照时出错: 无法打开快照文件。
如果创建的快照文件的大小大于 VM 所在数据存储中的可用空间，则会出现此错误。 请遵循[文档](https://go.microsoft.com/fwlink/?linkid=2166464)中提供的解决方法。

## <a name="replication-cycle-failed"></a>复制周期失败

错误 ID：181008

错误消息：VM: 'VMName'。 错误: 找不到要进行快照复制的、快照 ID 为 'SnapshotID' 的磁盘快照。

可能的原因：

可能的原因包括：
1. 一个或多个包含的磁盘的路径由于执行存储 vMotion 而发生了更改。
2. 一个或多个包含的磁盘已不再附加到 VM。
      
**建议：**

提供了以下建议
1. 使用存储 vMotion 将包含的磁盘还原到原始路径，然后禁用存储 vMotion。
2. 禁用存储 vMotion（如果已启用），在虚拟机上停止复制，然后再次复制虚拟机。 如果该问题仍然存在，请联系支持部门。

## <a name="next-steps"></a>后续步骤

继续进行 VM 复制，并执行[测试迁移](./tutorial-migrate-vmware.md#run-a-test-migration)。
