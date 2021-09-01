---
title: 对 Azure 文件同步进行故障排除 | Microsoft Docs
description: 排查 Azure 文件同步部署中的常见问题，此服务可用于将 Windows Server 转换为 Azure 文件共享的快速缓存。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 8/16/2021
ms.author: jeffpatt
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15bc06f1f84b01df8d6999147d47e5f5f48ec45f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228995"
---
# <a name="troubleshoot-azure-file-sync"></a>对 Azure 文件同步进行故障排除
使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本文旨在帮助排查和解决在 Azure 文件同步部署中可能遇到的问题。 此外，还介绍了在需要对问题进行更深入调查的情况下，如何从系统收集重要日志。 如果本文未能涵盖你的问题，欢迎通过以下渠道联系我们（以升序排列）：

- [有关 Azure 存储的 Microsoft Q&A 问题页面](/answers/products/azure?product=storage)。
- [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)。
- Microsoft 支持部门。 若要创建新的支持请求，请在 Azure 门户中的“帮助”选项卡上，选择“帮助和支持”按钮，然后选择“新建支持请求”。

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>我在服务器上遇到 Azure 文件同步问题（同步、云分层等）。 是否应删除并重新创建服务器终结点？
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>代理安装和服务器注册
<a id="agent-installation-failures"></a>**排查代理安装失败问题**  
如果 Azure 文件同步代理安装失败，请在安装代理的过程中，在权限提升的命令提示符下运行以下命令，以启用日志记录：

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

查看 installer.log 确定安装失败的原因。

<a id="agent-installation-gpo"></a>代理安装失败，并出现错误：由于出错，存储同步代理安装向导提前终止

在代理安装日志中记录了以下错误：

```
CAQuietExec64:  + CategoryInfo          : SecurityError: (:) , PSSecurityException
CAQuietExec64:  + FullyQualifiedErrorId : UnauthorizedAccess
CAQuietExec64:  Error 0x80070001: Command line returned an error.
```

如果使用组策略配置 [PowerShell 执行策略](/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy)，并且策略设置是“仅允许签名脚本”，则会出现此问题。 Azure 文件同步代理随附的所有脚本均已签名。 Azure 文件同步代理安装失败，因为安装程序使用跳过执行策略设置来完成脚本执行。

若要解决此问题，请暂时禁用服务器上的[启用脚本执行](/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy)组策略设置。 代理安装完成后，就可以重新启用组策略设置了。

<a id="agent-installation-on-DC"></a>**Active Directory 域控制器上的代理安装失败**  
如果尝试在 Active Directory 域控制器上安装同步代理，且该控制器中 PDC 角色所有者位于 Windows Server 2008 R2 上或更低的 OS 版本上，则可能遇到同步代理安装失败的问题。

要解决此问题，请将 PDC 角色转移到另一运行 Windows Server 2012 R2 或更高版本的域控制器，然后安装同步。

<a id="parameter-is-incorrect"></a>**访问 Windows Server 2012 R2 上的卷失败，并出现以下错误：参数不正确**  
在 Windows Server 2012 R2 上创建服务器终结点后，访问卷时发生以下错误：

无法访问 driveletter:\。  
参数不正确。

要解决此问题，请安装 [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014)，并重启服务器。 如果由于已安装更高版本的更新而无法安装此更新，请转到 Windows 更新，安装Windows Server 2012 R2 的最新更新，然后重启服务器。

<a id="server-registration-missing-subscriptions"></a>**服务器注册未列出所有 Azure 订阅**  
使用 ServerRegistration.exe 注册服务器时，如果单击 Azure 订阅下拉列表，订阅将丢失。

出现此问题是因为 ServerRegistration.exe 将仅检索前 5 个 Azure AD 租户的订阅。 

要增加服务器上的服务器注册租户限制值，请在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 下创建一个名为 ServerRegistrationTenantLimit 的 DWORD 值，它的值大于 5。

使用以下 PowerShell 命令注册服务器也可解决此问题：

```powershell
Connect-AzAccount -Subscription "<guid>" -Tenant "<guid>"
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

<a id="server-registration-prerequisites"></a>**服务器注册显示以下消息：“缺少必备项”**  
如果未在 PowerShell 5.1 上安装 Az 或 AzureRM PowerShell 模块，则会显示此消息。 

> [!Note]  
> ServerRegistration.exe 不支持 PowerShell 6.x。 可以在 PowerShell 6.x 上使用 Register-AzStorageSyncServer cmdlet 注册服务器。

若要在 PowerShell 5.1 上安装 Az 或 AzureRM 模块，请执行以下步骤：

1. 在提升的命令提示符处键入“powershell”，然后按 Enter。
2. 按照文档说明安装最新的 Az 或 AzureRM 模块：
    - [Az 模块（需要 .NET 4.7.2）](/powershell/azure/install-az-ps)
    - [AzureRM 模块](https://go.microsoft.com/fwlink/?linkid=856959)
3. 运行 ServerRegistration.exe 并完成向导中的操作，将服务器注册到存储同步服务。

<a id="server-already-registered"></a>**服务器注册显示以下消息：“此服务器已注册”** 

![包含“服务器已注册”错误消息的服务器注册对话框屏幕截图](media/storage-sync-files-troubleshoot/server-registration-1.png)

如果以前向存储同步服务注册了服务器，则会显示此消息。 若要从当前存储同步服务中注销服务器并向新存储同步服务进行注册，请完成[从 Azure 文件同步中注销服务器](file-sync-server-registration.md#unregister-the-server-with-storage-sync-service)中所述的步骤。

如果存储同步服务中的“已注册服务器”下未列出该服务器，请在想要注销的服务器上运行以下 PowerShell 命令：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 如果该服务器是群集的一部分，可以使用可选的 *Reset-StorageSyncServer -CleanClusterRegistration* 参数来同时删除群集注册。

<a id="web-site-not-trusted"></a>**注册服务器时，出现许多“网站不受信任”的响应。为什么？**  
如果在服务器注册过程中启用了“Internet Explorer 增强的安全性”策略，则会出现此问题。 有关如何以正确的方式禁用“Internet Explorer 增强的安全性”策略的详细信息，请参阅[准备 Windows Server 以使用 Azure 文件同步](file-sync-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync)，以及[如何部署 Azure 文件同步](file-sync-deployment-guide.md)。

<a id="server-registration-missing"></a>**服务器未在 Azure 门户中的“已注册的服务器”下列出**  
如果对于存储同步服务，服务器未在“已注册的服务器”下列出：
1. 登录到要注册的服务器。
2. 打开文件资源管理器，然后转到存储同步代理安装目录（默认位置为 C:\Program Files\Azure\StorageSyncAgent）。 
3. 运行 ServerRegistration.exe 并完成向导中的操作，将服务器注册到存储同步服务。

## <a name="sync-group-management"></a>同步组管理

### <a name="cloud-endpoint-creation-errors"></a>云终结点创建错误

<a id="cloud-endpoint-using-share"></a>**云终结点创建失败，并出现以下错误：“指定的 Azure 文件共享已被其他 CloudEndpoint 使用”**  
如果 Azure 文件共享已被其他云终结点使用，则会发生此错误。 

如果看到此消息，并且 Azure 文件共享当前未被云终结点使用，请完成以下步骤清除 Azure 文件共享上的 Azure 文件同步元数据：

> [!Warning]  
> 删除云终结点当前正在使用的 Azure 文件共享上的元数据会导致 Azure 文件同步操作失败。 如果随后将此文件共享用于另一个同步组中的同步，则旧同步组中文件的数据丢失几乎是肯定的。

1. 在 Azure 门户中，转到 Azure 文件共享。  
2. 右键单击该 Azure 文件共享，并选择“编辑元数据”。
3. 右键单击“SyncService”，并选择“删除” 。

<a id="cloud-endpoint-authfailed"></a>**云终结点创建失败，并出现以下错误：“AuthorizationFailed”**  
当用户帐户缺少足够权限来创建云终结点时，将出现此错误。 

要创建云终结点，用户帐户必须具有下列 Microsoft 授权权限：  
* 读取：获取角色定义
* 写入：创建或更新自定义角色定义
* 读取：获取角色分配
* 写入：创建角色分配

以下内置角色具有所需的 Microsoft 授权权限：  
* “所有者”
* 用户访问管理员

若要确定自己的用户帐户角色否具有适当的权限，请执行以下操作：  
1. 在 Azure 门户中，选择“资源组”。
2. 选择存储帐户所在的资源组，再选择“访问控制(IAM)”。
3. 选择“角色分配”选项卡。
4. 为用户帐户选择 **角色**（例如“所有者”或“参与者”）。
5. 在“资源提供程序”列表中，选择“Microsoft 授权”。  
    * “角色分配”应具有“读取”和“写入”权限。  
    * “角色定义”应具有“读取”和“写入”权限。  

### <a name="server-endpoint-creation-and-deletion-errors"></a>服务器终结点创建和删除错误

<a id="-2134375898"></a>**服务器终结点创建失败，并出现以下错误："MgmtServerJobFailed"（错误代码：-2134375898 或 0x80c80226）**  
如果服务器终结点路径位于系统卷上并启用了云分层，则会出现此错误。 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。

<a id="-2147024894"></a>**服务器终结点创建失败，并出现以下错误："MgmtServerJobFailed"（错误代码：-2147024894 或 0x80070002）**  
如果指定的服务器终结点路径无效，则会出现此错误。 验证指定的服务器终结点路径是否为本地附加的 NTFS 卷。 请注意，Azure 文件同步不支持将映射的驱动器用作服务器终结点路径。

<a id="-2134375640"></a>**服务器终结点创建失败，并出现以下错误："MgmtServerJobFailed"（错误代码：-2134375640 或 0x80c80328）**  
如果指定的服务器终结点路径不是 NTFS 卷，则会出现此错误。 验证指定的服务器终结点路径是否为本地附加的 NTFS 卷。 请注意，Azure 文件同步不支持将映射的驱动器用作服务器终结点路径。

<a id="-2134347507"></a>**服务器终结点创建失败，并出现以下错误："MgmtServerJobFailed"（错误代码：-2134347507 或 0x80c8710d）**  
出现此错误的原因是，Azure 文件同步不支持卷上具有压缩“系统卷信息”文件夹的服务器端点。 要解决此问题，请解压缩“系统卷信息”文件夹。 如果“系统卷信息”文件夹是在该卷上唯一压缩的文件夹，请执行以下步骤：

1. 下载 [PsExec](/sysinternals/downloads/psexec) 工具。
2. 在提升的命令提示符处运行以下命令，以启动在系统帐户下运行的命令提示符：**PsExec.exe -i -s -d cmd**
3. 在系统帐户下运行的命令提示符处，键入以下命令，然后按 Enter：   
    cd /d "drive letter:\System Volume Information"  
    compact /u /s

<a id="-2134376345"></a>**服务器终结点创建失败，并出现以下错误："MgmtServerJobFailed"（错误代码：-2134376345 或 0x80C80067）**  
如果已达到每个服务器的服务器终结点数限制，则会出现此错误。 Azure 文件同步目前允许每个服务器最多有 30 个服务器终结点。 有关详细信息，请参阅 [Azure 文件同步规模目标](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets)。

<a id="-2134376427"></a>**服务器终结点创建失败，并出现以下错误："MgmtServerJobFailed"（错误代码：-2134376427 或 0x80c80015）**  
如果另一服务器终结点已在同步指定的服务器终结点路径，则会出现此错误。 Azure 文件同步不支持多个服务器终结点同步同一目录或卷。

<a id="-2160590967"></a>**服务器终结点创建失败，并出现以下错误："MgmtServerJobFailed"（错误代码：-2160590967 或 0x80c80077）**  
如果服务器终结点路径包含孤立的分层文件，则会发生此错误。 如果最近删除了服务器终结点，请等待，直到孤立的分层文件清理完成。 启动孤立分层文件清理后，事件 ID 6662 将记录到遥测事件日志中。 孤立分层文件清理完成且可使用该路径重新创建服务器终结点后，将记录事件 ID 6661。 如果在分层文件清理完成后，服务器终结点创建失败，或者如果由于事件日志滚动更新而导致在遥测事件日志中找不到事件 ID 6661，请执行[在删除服务器终结点后无法访问服务器上的分层文件](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)部分中所述的步骤，以删除孤立的分层文件。

<a id="-2134347757"></a>**服务器终结点删除失败，并出现以下错误："MgmtServerJobExpired"（错误代码：-2134347757 或 0x80c87013）**  
如果服务器处于离线状态或没有连接网络，则会发生此错误。 如果服务器不再可用，请在门户中注销要删除服务器终结点的服务器。 要删除服务器终结点，请按照[使用 Azure 文件同步注销服务器](file-sync-server-registration.md#unregister-the-server-with-storage-sync-service)中所述的步骤操作。

### <a name="server-endpoint-health"></a>服务器终结点运行状况

<a id="server-endpoint-provisioningfailed"></a>**无法打开服务器终结点属性页或更新云分层策略**  
如果服务器终结点上的管理操作失败，则可能出现此问题。 如果未在 Azure 门户中打开服务器终结点属性页，则在服务器中使用 PowerShell 命令更新服务器终结点可修复此问题。 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**服务器终结点的运行状态为“无活动”或“挂起”，已注册服务器边栏选项卡上的服务器状态为“显示脱机”**  

如果存储同步监视器进程 (AzureStorageSyncMonitor.exe) 未运行，或如果服务器无法访问 Azure 文件同步服务，则可能会发生此问题。

在门户中显示为“显示脱机”的服务器上，查看遥测事件日志中的事件 ID 9301（位于事件查看器中的“应用程序和服务\Microsoft\文件同步\代理”下），以确定服务器无法访问 Azure 文件同步服务的原因。 

- 如果记录“GetNextJob 已完成，状态为：0”，则服务器可以与 Azure 文件同步服务通信。 
    - 在服务器上打开任务管理器，并验证存储同步监视器 (AzureStorageSyncMonitor.exe) 进程是否正在运行。 如果该进程未运行，请首先尝试重启服务器。 如果重启服务器无法解决此问题，请升级到最新的 Azure 文件同步[代理版本](file-sync-release-notes.md)。 

- 如果记录了“GetNextJob 已完成，状态为: -2134347756”，则由于防火墙、代理或 TLS 密码套件顺序配置，服务器无法与 Azure 文件同步服务通信。 
    - 如果服务器位于防火墙后面，请验证端口 443 是否允许出站。 如果防火墙限制到特定域的流量，请确认可以访问防火墙[文档](file-sync-firewall-and-proxy.md#firewall)中列出的域。
    - 如果服务器位于代理后面，请按照代理[文档](file-sync-firewall-and-proxy.md#proxy)中的步骤配置适用于整个计算机或特定于应用的代理设置。
    - 使用 Test-StorageSyncNetworkConnectivity cmdlet 检查服务终结点的网络连接情况。 若要了解详细信息，请参阅[测试服务终结点的网络连接情况](file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)。
    - 如果在服务器上配置了 TLS 密码套件顺序，则可以使用组策略或 TLS cmdlet 来添加密码套件：
        - 若要使用组策略，请参阅[使用组策略配置 TLS 密码套件顺序](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy)。
        - 若要使用 TLS cmdlet，请参阅[使用 TLS PowerShell Cmdlet 配置 TLS 密码套件顺序](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets)。
    
        Azure 文件同步目前支持用于 TLS 1.2 协议的以下密码套件：  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- 如果记录“GetNextJob 已完成，状态为：-2134347764”，则服务器无法与 Azure 文件同步服务通信，原因是证书已过期或已删除。  
    - 在服务器上运行以下 PowerShell 命令，以重置用于进行身份验证的证书：
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**服务器终结点的运行状态为“无活动”，已注册服务器边栏选项卡上的服务器状态为“在线”**  

服务器终结点运行状态为“无活动”的意味着服务器终结点在过去两小时内未记录同步活动。

若要检查服务器上当前的同步活动，请参阅[如何监视当前同步会话的进度？](#how-do-i-monitor-the-progress-of-a-current-sync-session)。

由于存在 bug 或系统资源不足，服务器终结点可能在数小时内无法记录同步活动。 验证是否已安装最新的 Azure 文件同步[代理版本](file-sync-release-notes.md)。 如果问题仍然存在，请创建支持请求。

> [!Note]  
> 如果已注册服务器边栏选项卡上的服务器状态为“显示脱机”，请执行[服务终结点运行状态为“无活动”或“挂起”且已注册服务器边栏选项卡上的服务器状态为“显示脱机”](#server-endpoint-noactivity)部分中记录的步骤。

## <a name="sync"></a>同步
<a id="afs-change-detection"></a>**我通过 SMB 或门户在 Azure 文件共享中直接创建了一个文件，该文件同步到同步组中的服务器需要多长时间？**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**服务器终结点运行状况有多个小时处于挂起状态**  
如果创建云终结点并使用包含数据的 Azure 文件共享，则可能会出现此问题。 在云与服务器终结点之间同步文件之前，扫描 Azure 文件共享中的更改的更改枚举作业必须已完成。 完成该作业所需的时间取决于 Azure 文件共享中命名空间的大小。 更改枚举作业完成后，服务器终结点运行状况应会更新。

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>如何监视同步运行状况？
# <a name="portal"></a>[门户](#tab/portal1)
在每个同步组中，可以向下钻取到单个服务器终结点，以查看最后几个已完成的同步会话的状态。 如果出现绿色的“运行状况”列，并且“文件未同步”的值为 0，则表示同步按预期工作。 否则，请参阅下文，了解常见同步错误列表以及如何处理未同步的文件。 

![Azure 门户的屏幕截图](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
转到服务器的遥测日志（可在事件查看器中通过 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` 找到）。 事件 9102 对应于已完成的同步会话；有关最新同步状态，请查找 ID 为 9102 的最新事件。 SyncDirection 会告知你此会话是上传还是下载。 如果 `HResult` 为 0，则同步会话已成功。 如果 `HResult` 不为 0，则表示同步期间发生错误；请参阅下文，了解常见错误的列表。 如果 PerItemErrorCount 大于 0，则某些文件或文件夹未正确同步。 有可能会出现 `HResult` 为 0，但 PerItemErrorCount 大于 0 的情况。

下面是成功上传的示例。 为简洁起见，下面只列出了每个 9102 事件中包含的某些值。 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

相反，不成功的上传可能如下所示：

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

有时，同步会话虽然总体失败或者出现非零值的 PerItemErrorCount，但仍能继续进行，并且某些文件能够成功同步。 可通过查看 Applied 字段（AppliedFileCount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes）来确定进度。 这些字段描述了会话成功的程度。 如果在某一行中看到多个同步会话失败，但 Applied 计数增加，则应该提供一定的同步时间以便重试，然后再开具支持票证。

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>如何监视当前同步会话的进度？
# <a name="portal"></a>[门户](#tab/portal1)
在同步组中，转到有问题的服务器终结点，并在“同步活动”部分查看当前同步会话中已上传或下载的文件计数。 注意，此状态会延迟约 5 分钟，如果同步会话足够小，可在此时间段内完成，就不会在门户中报告此会话。 

# <a name="server"></a>[Server](#tab/server)
在服务器上的遥测日志中查看最近的 9302 事件（在事件查看器中，转到“应用程序和服务日志\Microsoft\FileSync\Agent\Telemetry”）。 此事件指示当前同步会话的状态。 TotalItemCount 表示要同步多少个文件，AppliedItemCount 表示到目前为止已同步的文件数，PerItemErrorCount 表示同步失败的文件数（请参阅下文了解如何处理此问题）。

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>如何知道我的服务器是否已彼此同步？
# <a name="portal"></a>[门户](#tab/portal1)
对于给定同步组中的每个服务器，请确保：
- 上传和下载会话的“上次尝试同步”时间戳是最近的时间。
- 上传和下载会话的状态为绿色。
- “同步活动”字段显示的要同步的剩余文件数很少或者为零。
- 上传和下载会话的“文件未同步”字段值为 0。

# <a name="server"></a>[Server](#tab/server)
查看由每个服务器的遥测事件日志中的 9102 事件标记的已完成同步会话（在事件查看器中，转到 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`）。 

1. 在任何给定的服务器上，需要确保最近的上传和下载会话已成功完成。 为此，请检查上传和下载操作的 `HResult` 与 PerItemErrorCount 是否为 0（SyncDirection 字段会指示给定的会话是上传还下载会话）。 请注意，如果未看到最近完成的同步会话，原因有可能是同步会话当前正在进行；如果刚刚添加或修改了大量数据，则这种情况符合预期。
2. 如果服务器在云中完全处于最新状态，并且不包含要朝任一方向同步的更改，则你会看到空的同步会话。 这些状态由上传和下载事件指示。在这些事件中，Sync* 字段（SyncFileCount、SyncDirCount、SyncTombstoneCount 和 SyncSizeBytes）为 0，表示没有要同步的内容。请注意，在数据变动率较高的服务器上，可能不会出现这些空的同步会话，因为始终有新的内容需要同步。如果没有任何同步活动，同步会话应每隔 30 分钟发生一次。 
3. 如果所有服务器在云中处于最新状态，则意味着这些服务器的最近上传和下载会话是空的同步会话，此时可以合理地确定系统在整体上是同步的。 
    
如果直接在 Azure 文件共享中进行更改，则在更改枚举运行之前（每隔 24 小时发生一次），Azure 文件同步不会检测这些更改。 有可能服务器在云中处于最新状态，但事实上它缺少 Azure 文件共享中所做的最新更改。

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>如何确定是否有特定的文件或文件夹未同步？
对于任何给定的同步会话，如果服务器上的 PerItemErrorCount 或门户中“文件未同步”计数大于 0，则表示某些项未能同步。文件和文件夹的某些特征导致它们无法同步。 这些特征可能是永久性的，需要采取明确的措施才能恢复同步，例如，从文件或文件夹名称中删除不支持的字符。 它们也可能是暂时性的，也就是说，该文件或文件夹可自动恢复同步；例如，包含开放句柄的文件在关闭后可自动恢复同步。 当 Azure 文件同步引擎检测到此类问题时，会生成错误日志，可以分析这些日志以列出当前未正确同步的项。

若要查看这些错误，请运行 **FileSyncErrorsReport.ps1** PowerShell 脚本（位于 Azure 文件同步代理的代理安装目录中），以识别由于包含开放句柄、不支持的字符或存在其他问题而未能同步的文件。 ItemPath 字段告知相对于根同步目录的文件位置。 请参阅以下常见同步错误列表获取补救步骤。

> [!Note]  
> 如果 FileSyncErrorsReport.ps1 脚本返回“找不到任何文件错误”或未列出同步组的按项列出的错误，则原因为：
>
>- 原因 1：上次完成的同步会话没有按项列出的错误。 门户应会立即更新，以显示“0 个文件未同步”。 默认情况下，FileSyncErrorsReport.ps1 脚本将只显示上次完成同步会话的每项错误。 若要查看所有同步会话的每项错误，请使用 -ReportAllErrors 参数。
>    - 检查遥测事件日志中的最新[事件 ID 9102](?tabs=server%252cazure-portal#broken-sync)，以确认 PerItemErrorCount 是否为 0。 
>
>- 原因 2：由于按项列出的错误过多且事件日志不再包含此同步组的错误，服务器上的 ItemResults 事件日志已包装。
>    - 若要避免此问题，请增加 ItemResults 事件日志大小。 ItemResults 事件日志位于事件查看器中的“应用程序和服务日志\Microsoft\文件同步\代理”下。 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>根据文件/目录同步错误进行故障排除
**ItemResults 日志 - 按项列出的同步错误**  

| HRESULT | HRESULT（十进制） | 错误字符串 | 问题 | 补救 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | 服务器上的分层文件不可访问。 如果在删除服务器终结点之前未撤回该分层文件，则会出现此问题。 | 若要解决此问题，请参阅[在删除服务器终结点后无法访问服务器上的分层文件](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | 由于尚未同步某个相关的文件夹，无法同步文件或目录更改。 在同步相关的更改后，此项将会同步。 | 无需采取措施。 如果错误持续数天，请使用 FileSyncErrorsReport.ps1 PowerShell 脚本确定相关的文件夹尚未同步的原因。 |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | 由于尚未同步某个相关的文件夹，无法同步文件或目录更改。 在同步相关的更改后，此项将会同步。 | 无需采取措施。 如果错误持续数天，请使用 FileSyncErrorsReport.ps1 PowerShell 脚本确定相关的文件夹尚未同步的原因。 |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | 无法同步文件或目录更改，因为尚未同步某个相关的文件夹且同步会话失败。 在同步相关的更改后，此项将会同步。 | 无需采取措施。 如果错误仍然存在，请调查同步会话失败。 |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | 文件或目录名称无效。 | 重命名有问题的文件或目录。 有关详细信息，请参阅[处理不支持的字符](?tabs=portal1%252cazure-portal#handling-unsupported-characters)。 |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | 文件或目录名称无效。 | 重命名有问题的文件或目录。 有关详细信息，请参阅[处理不支持的字符](?tabs=portal1%252cazure-portal#handling-unsupported-characters)。 |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | 文件在使用中，因此无法同步。 不再使用该文件时，会将其同步。 | 无需采取措施。 Azure 文件同步每天在服务器上创建临时 VSS 快照一次，以同步包含开放句柄的文件。 |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | 文件已更改，但同步尚未检测到此项更改。检测到此项更改后，同步将会恢复。 | 无需采取措施。 |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | 文件已删除，而同步不了解此更改。 | 无需采取措施。 一旦更改检测操作检测到文件已删除，同步会停止记录此错误。 |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | 无法同步文件删除或目录删除，因为该项已在目标中删除，而同步不知道此项更改。 | 无需采取措施。 在目标上运行更改检测且同步检测到该项已删除后，同步会停止记录此错误。 |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | 文件或目录已跳过，但会在下一个同步会话期间进行同步。 如果在下载此项时报告此错误，则文件或目录名称很可能无效。 | 如果在上传文件时报告此错误，则无需采取任何措施。 如果在下载文件时报告此错误，请重命名相关文件或目录。 有关详细信息，请参阅[处理不支持的字符](?tabs=portal1%252cazure-portal#handling-unsupported-characters)。 |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | 无法同步创建文件或目录，因为该项已在目标中存在，而同步不知道此项更改。 | 无需采取措施。 在目标上运行更改检测且同步了解此新项目时，同步会停止记录此错误。 |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | 无法同步该文件，因为已达到 Azure 文件共享限制。 | 要解决此问题，请参阅疑难解答指南中的[达到 Azure 文件共享存储限制](?tabs=portal1%252cazure-portal#-2134351810)部分。 |
| 0x80c83008 | -2134364152 | ECS_E_CANNOT_CREATE_AZURE_STAGED_FILE | 无法同步该文件，因为已达到 Azure 文件共享限制。  | 要解决此问题，请参阅疑难解答指南中的[达到 Azure 文件共享存储限制](?tabs=portal1%252cazure-portal#-2134351810)部分。 |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | 文件是使用不受支持的解决方案（如 NTFS EFS）加密的。 | 解密该文件并使用支持的加密解决方案。 有关支持解决方案的列表，请参阅计划指南的[加密](file-sync-planning.md#encryption)部分。 |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | 文件位于一个 DFS-R 只读复制文件夹中。 | 文件位于一个 DFS-R 只读复制文件夹中。 Azure 文件同步不支持 DFS-R 只读复制文件夹中的服务器终结点。 请参阅[规划指南](file-sync-planning.md#distributed-file-system-dfs)以获取详细信息。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 文件处于“删除挂起”状态。 | 无需采取措施。 一旦关闭所有打开的文件句柄，就会删除文件。 |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | 文件不能同步，原因是存储帐户上的防火墙和虚拟网络设置已启用，服务器不能访问存储帐户。 | 按照部署指南中的[配置防火墙和虚拟网络设置](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)部分中所述的步骤添加服务器 IP 地址或虚拟网络。 |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | 文件因安全描述符大小超出 64 KiB 的限制而无法同步。 | 若要解决此问题，请删除文件上的访问控制项 (ACE)，减少安全描述符大小。 |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | 由于出现意外错误，文件无法同步。 | 如果此错误持续数天，请提交一个支持案例。 |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | 文件在使用中，因此无法同步。 不再使用该文件时，会将其同步。 | 无需采取措施。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 同步期间更改了文件，因此需要重新同步。 | 无需采取措施。 |
| 0x80070017 | -2147024873 | ERROR_CRC | 由于出现 CRC 错误，文件无法同步。 如果在删除服务器终结点之前未撤回该分层文件，或者文件已损坏，则会出现此错误。 | 若要解决此问题，请参阅[删除服务器终结点后无法在服务器上访问分层文件](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)以删除孤立的分层文件。 如果在删除孤立的分层文件后仍然出现错误，请在卷上运行 [chkdsk](/windows-server/administration/windows-commands/chkdsk)。 |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | 由于已达到冲突文件最大数量，文件无法同步。 Azure 文件同步支持每文件 100 个冲突文件。 若要了解有关文件冲突的详细信息，请参阅 Azure 文件同步[常见问题解答](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-conflict-resolution)。 | 若要解决此问题，请减少冲突文件数。 冲突文件数小于 100 后，文件将同步。 |
| 0x80c8027d | -2134375811 | ECS_E_DIRECTORY_RENAME_FAILED | 无法同步重命名的目录，因为目录中的文件或文件夹具有打开的句柄。 | 无需采取措施。 目录中所有打开的文件句柄遭关闭后，目录的重命名将会同步。 |

#### <a name="handling-unsupported-characters"></a>处理不受支持的字符
如果 FileSyncErrorsReport.ps1 PowerShell 脚本显示不受支持的字符导致按项列出的同步错误（错误代码 0x8007007b 或 0x80c80255），请从相关的文件名中删除或重命名错误的字符。 PowerShell 可能会以问号或空框的形式列显这些字符，因为其中的大多数字符没有标准的视觉编码。 
> [!Note]  
> [评估工具](file-sync-planning.md#evaluation-cmdlet)可用于标识不受支持的字符。 如果数据集包含若干个具有无效字符的文件，请使用 [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) 脚本重命名包含不受支持的字符的文件。

下表包含 Azure 文件同步尚不支持的所有 Unicode 字符。

| 字符集 | 字符计数 |
|---------------|-----------------|
| 0x00000000 - 0x0000001F（控制字符） | 32 |
| 0x0000FDD0 - 0x0000FDDD（阿拉伯语显示格式 a） | 14 |
| <ul><li>0x00000022（引号）</li><li>0x0000002A（星号）</li><li>0x0000002F（正斜杠）</li><li>0x0000003A（冒号）</li><li>0x0000003C（小于）</li><li>0x0000003E（大于）</li><li>0x0000003F（问号）</li><li>0x0000005C（反斜杠）</li><li>0x0000007C（竖线）</li></ul> | 9 |
| <ul><li>0x0004FFFE - 0x0004FFFF = 2（非字符）</li><li>0x0008FFFE - 0x0008FFFF = 2（非字符）</li><li>0x000CFFFE - 0x000CFFFF = 2（非字符）</li><li>0x0010FFFE - 0x0010FFFF = 2（非字符）</li></ul> | 8 |
| <ul><li>0x0000009D（`osc` 操作系统命令）</li><li>0x00000090（dcs 设备控制字符串）</li><li>0x0000008F (ss3 single shift three)</li><li>0x00000081（高八位字节预设）</li><li>0x0000007F（del 删除）</li><li>0x0000008D（ri 反向换行符）</li></ul> | 6 |
| 0x0000FFF0，0x0000FFFD，0x0000FFFE，0x0000FFFF（特殊字符） | 4 |
| 以句点结尾的文件或目录 | 1 |

### <a name="common-sync-errors"></a>常见同步错误
<a id="-2147023673"></a>**同步会话已取消。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT（十进制）** | -2147023673 | 
| **错误字符串** | ERROR_CANCELLED |
| **所需的补救措施** | 否 |

同步会话可能出于各种原因失败，包括服务器正在重启或更新、VSS 快照，等等。尽管此错误看起来需要保持跟进，但可以放心地将其忽略，除非它持续了好几个小时。

<a id="-2147012889"></a>**无法与服务建立连接。**    

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT（十进制）** | -2147012889 | 
| **错误字符串** | WININET_E_NAME_NOT_RESOLVED |
| **所需的补救措施** | 是 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

> [!Note]  
> 连接到 Azure 文件同步服务的网络连接恢复后，同步可能不会立即恢复。 默认情况下，若在服务器终结点位置未检测到任何更改，Azure 文件同步将每 30 分钟启动一次同步会话。 若要强制执行同步会话，请重启存储同步代理 (FileSyncSvc) 服务，或对服务器终结点位置内的文件或目录进行更改。

<a id="-2134376372"></a>**服务限制了用户请求。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT（十进制）** | -2134376372 |
| **错误字符串** | ECS_E_USER_REQUEST_THROTTLED |
| **所需的补救措施** | 否 |

无需采取措施；服务器会重试。 如果此错误持续了几个小时，请创建支持请求。

<a id="-2134364160"></a>**由于操作中止，同步失败**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c83000 |
| **HRESULT（十进制）** | -2134364160 |
| **错误字符串** | ECS_E_OPERATION_ABORTED |
| **所需的补救措施** | 否 |

不需要执行任何操作。 如果此错误持续了几个小时，请创建支持请求。

<a id="-2134364043"></a>同步被阻止，直至更改检测完成还原后操作  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT（十进制）** | -2134364043 |
| **错误字符串** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **所需的补救措施** | 否 |

不需要执行任何操作。 使用 Azure 备份还原某个文件或文件共享（云终结点）时，系统会阻止同步，直至在 Azure 文件共享上完成更改检测。 更改检测在还原完成后立即运行，持续时间取决于文件共享中的文件数。

<a id="-2147216747"></a>同步失败，原因是同步数据库已卸载。  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT（十进制）** | -2147216747 |
| **错误字符串** | SYNC_E_METADATA_INVALID_OPERATION |
| **所需的补救措施** | 否 |

当备份应用程序创建好 VSS 快照且同步数据库已卸载后，通常会发生此错误。 如果此错误持续了几个小时，请创建支持请求。

<a id="-2134364065"></a>**同步无法访问云终结点中指定的 Azure 文件共享。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT（十进制）** | -2134364065 |
| **错误字符串** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **所需的补救措施** | 是 |

之所以发生此错误，是因为 Azure 文件同步代理无法访问 Azure 文件共享。无法访问的可能原因是 Azure 文件共享或托管它的存储帐户不再存在。 可以执行以下步骤来排查此错误：

1. [验证存储帐户是否存在。](#troubleshoot-storage-account)
2. [确保 Azure 文件共享存在。](#troubleshoot-azure-file-share)
3. [确保 Azure 文件同步有权访问存储帐户。](#troubleshoot-rbac)
4. [验证存储帐户上的防火墙和虚拟网络设置是否已正确配置（如果已启用）](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>同步失败，原因是该请求无权执行此操作。  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT（十进制）** | -2134351804 |
| **错误字符串** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **所需的补救措施** | 是 |

出现此错误的原因是，Azure 文件同步代理无权访问 Azure 文件共享。 可以执行以下步骤来排查此错误：

1. [验证存储帐户是否存在。](#troubleshoot-storage-account)
2. [确保 Azure 文件共享存在。](#troubleshoot-azure-file-share)
3. [验证存储帐户上的防火墙和虚拟网络设置是否已正确配置（如果已启用）](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [确保 Azure 文件同步有权访问存储帐户。](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**无法解析所用的存储帐户名。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT（十进制）** | -2134364064 |
| **错误字符串** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **所需的补救措施** | 是 |

1. 检查是否可从服务器解析存储 DNS 名称。

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [验证存储帐户是否存在。](#troubleshoot-storage-account)
3. [验证存储帐户上的防火墙和虚拟网络设置是否已正确配置（如果已启用）](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**访问存储帐户时出现未知错误。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT（十进制）** | -2134364022 |
| **错误字符串** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **所需的补救措施** | 是 |

1. [验证存储帐户是否存在。](#troubleshoot-storage-account)
2. [验证存储帐户上的防火墙和虚拟网络设置是否已正确配置（如果已启用）](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>由于存储帐户被锁定，同步失败。  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT（十进制）** | -2134364014 |
| **错误字符串** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **所需的补救措施** | 是 |

出现此错误的原因是存储帐户具有只读[资源锁](../../azure-resource-manager/management/lock-resources.md)。 要解决此问题，请删除存储帐户上的只读资源锁。 

<a id="-1906441138"></a>**由于同步数据库出现问题，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT（十进制）** | -1906441138 |
| **错误字符串** | JET_errWriteConflict |
| **所需的补救措施** | 是 |

如果 Azure 文件同步使用的内部数据库出现问题，则会发生此错误。出现此问题时，请创建支持请求，到时我们将与你取得联系，并帮助解决此问题。

<a id="-2134364053"></a>服务器上安装的 Azure 文件同步代理版本不受支持。  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT（十进制）** | -2134364053 |
| **错误字符串** | ECS_E_AGENT_VERSION_BLOCKED |
| **所需的补救措施** | 是 |

如果服务器上安装的 Azure 文件同步代理版本不受支持，则会出现此错误。 若要解决此问题，请[升级](file-sync-release-notes.md#azure-file-sync-agent-update-policy)到[受支持的代理版本](file-sync-release-notes.md#supported-versions)。

<a id="-2134351810"></a>**达到了 Azure 文件共享存储限制。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT（十进制）** | -2134351810 |
| **错误字符串** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **所需的补救措施** | 是 |

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c80249 |
| **HRESULT（十进制）** | -2134375863 |
| **错误字符串** | ECS_E_NOT_ENOUGH_REMOTE_STORAGE |
| **所需的补救措施** | 是 |

达到 Azure 文件共享存储限制时，同步会话将因为出现上述任一错误而失败。若对 Azure 文件共享应用了配额，或者用量超过了 Azure 文件共享的限制，亦可能会出现这种情况。 有关详细信息，请参阅 [Azure 文件共享的当前限制](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)。

1. 导航到存储同步服务中的同步组。
2. 选择同步组中的云终结点。
3. 记下打开的窗格中的 Azure 文件共享名称。
4. 选择链接的存储帐户。 如果此链接不起作用，则表示引用的存储帐户已删除。

    ![显示云终结点详细信息窗格（包含存储帐户的链接）的屏幕截图。](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. 选择“文件”查看文件共享列表。
6. 单击云终结点引用的 Azure 文件共享所在行末尾的三点图标。
7. 检查“用量”是否低于“配额”。  请注意，除非已指定备用配额，否则配额将与 [Azure 文件共享的最大大小](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)匹配。

    ![Azure 文件共享属性的屏幕截图。](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

如果共享已满且未设置配额，则解决此问题的可行方法之一是，将当前服务器终结点的每个子文件夹放入其自己的单独同步组中自己的服务器终结点内。 这样，每个子文件夹就会同步到单个 Azure 文件共享。

<a id="-2134351824"></a>**找不到 Azure 文件共享。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT（十进制）** | -2134351824 |
| **错误字符串** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **所需的补救措施** | 是 |

当 Azure 文件共享不可访问时，将发生此错误。 故障排除：

1. [验证存储帐户是否存在。](#troubleshoot-storage-account)
2. [确保 Azure 文件共享存在。](#troubleshoot-azure-file-share)

如果 Azure 文件共享已删除，则需要创建新的文件共享，然后重新创建同步组。 

<a id="-2134364042"></a>**此 Azure 订阅暂停时，同步将会暂停。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT（十进制）** | -2134364042 |
| **错误字符串** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **所需的补救措施** | 是 |

当 Azure 订阅暂停时，将发生此错误。 还原 Azure 订阅后，会重新启用同步。 有关详细信息，请参阅[为何禁用我的 Azure 订阅？如何重新激活它？](../../cost-management-billing/manage/subscription-disabled.md)。

<a id="-2134375618"></a>**为存储帐户配置了防火墙或虚拟网络。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT（十进制）** | -2134375618 |
| **错误字符串** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **所需的补救措施** | 是 |

如果由于设置了存储帐户防火墙或者存储帐户属于虚拟网络而导致 Azure 文件共享不可访问，则会发生此错误。 验证存储帐户上的防火墙和虚拟网络设置是否已正确配置。 有关详细信息，请参阅[配置防火墙和虚拟网络设置](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)。 

<a id="-2134375911"></a>**由于同步数据库出现问题，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT（十进制）** | -2134375911 |
| **错误字符串** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **所需的补救措施** | 否 |

此错误通常会自行解决，其原因包括：

* 同步组中服务器之间的文件更改较多。
* 单个文件和目录中出现大量的错误。

如果此错误持续了几个小时，请创建支持请求，我们将与你取得联系，并帮助解决此问题。

<a id="-2146762487"></a>**服务器无法建立安全连接。云服务收到意外的证书。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT（十进制）** | -2146762487 |
| **错误字符串** | CERT_E_UNTRUSTEDROOT |
| **所需的补救措施** | 是 |

如果组织使用 TLS 终止代理，或恶意实体正在截获服务器与 Azure 文件同步服务之间的通信，则会发生此错误。 如果确定这是预期行为（因为组织使用 TLS 终止代理），请跳过证书验证并改用注册表覆盖。

1. 创建 SkipVerifyingPinnedRootCertificate 注册表值。

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. 在注册的服务器上重启同步服务。

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

通过设置此注册表值，Azure 文件同步代理将在服务器和云服务之间传输数据时接受本地受信任的任何 TLS/SSL 证书。

<a id="-2147012894"></a>**无法与服务建立连接。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT（十进制）** | -2147012894 |
| **错误字符串** | WININET_E_TIMEOUT |
| **所需的补救措施** | 是 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

> [!Note]  
> 连接到 Azure 文件同步服务的网络连接恢复后，同步可能不会立即恢复。 默认情况下，若在服务器终结点位置未检测到任何更改，Azure 文件同步将每 30 分钟启动一次同步会话。 若要强制执行同步会话，请重启存储同步代理 (FileSyncSvc) 服务，或对服务器终结点位置内的文件或目录进行更改。

<a id="-2147012721"></a>**同步失败，因为服务器无法从 Azure 文件同步服务中解码响应**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80072f8f |
| **HRESULT（十进制）** | -2147012721 |
| **错误字符串** | WININET_E_DECODING_FAILED |
| **所需的补救措施** | 是 |

若代理正在修改来自 Azure 文件同步服务的响应，通常会出现此错误。 请检查你的代理配置。

<a id="-2134375680"></a>**由于身份验证出现问题，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT（十进制）** | -2134375680 |
| **错误字符串** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **所需的补救措施** | 是 |

此错误的常见原因是服务器时间不正确。 如果服务器在虚拟机中运行，请验证主机上的时间是否正确。

<a id="-2134364040"></a>**由于证书过期，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT（十进制）** | -2134364040 |
| **错误字符串** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **所需的补救措施** | 是 |

发生此错误是因为用于身份验证的证书已过期。

若要确认证书是否已过期，请执行以下步骤：  
1. 打开“证书”MMC 管理单元，选择“计算机帐户”，然后导航到“证书(本地计算机)”\“个人”\“证书”。
2. 检查客户端身份验证证书是否过期。

如果客户端身份验证证书已过期，请执行以下步骤来解决问题：

1. 验证是否已安装了 Azure 文件同步代理版本 4.0.1.0 或更高版本。
2. 在服务器上运行以下 PowerShell 命令：

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>由于找不到身份验证证书，同步失败。  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT（十进制）** | -2134375896 |
| **错误字符串** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **所需的补救措施** | 是 |

发生此错误是因为用于身份验证的证书未找到。

若要解决此问题，请执行以下步骤：

1. 验证是否已安装了 Azure 文件同步代理版本 4.0.1.0 或更高版本。
2. 在服务器上运行以下 PowerShell 命令：

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**由于找不到身份验证标识，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT（十进制）** | -2134364039 |
| **错误字符串** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **所需的补救措施** | 是 |

出现此错误是因为服务器终结点删除失败，导致该终结点现在处于部分删除状态。 若要解决此问题，请重新尝试删除服务器终结点。

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**服务器终结点所在卷的磁盘空间不足。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT（十进制）** | -1906441711 |
| **错误字符串** | JET_errLogDiskFull |
| **所需的补救措施** | 是 |

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT（十进制）** | -2134375654 |
| **错误字符串** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **所需的补救措施** | 是 |

由于卷没有足够的磁盘空间或已达到磁盘配额限制，同步会话失败，并出现下列其中一项错误。 此错误的常见原因是服务器终结点外部的文件用尽了卷上的空间。 请通过添加更多的服务器终结点、将文件移到其他卷，或增大服务器终结点所在卷的大小，来释放卷上的空间。 若使用[文件服务器资源管理器](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview)或 [NTFS 配额](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-quota)在卷上配置了磁盘配额，会提高配额限制。

<a id="-2134364145"></a><a id="replica-not-ready"></a>**服务尚未准备好与此服务器终结点同步。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT（十进制）** | -2134364145 |
| **错误字符串** | ECS_E_REPLICA_NOT_READY |
| **所需的补救措施** | 否 |

发生此错误的原因是，云终结点是在 Azure 文件共享上已存在内容的情况下创建的。 Azure 文件同步必须先扫描 Azure 文件共享中的所有内容，然后才能允许服务器终结点继续进行初始同步。

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**由于许多单独的文件出现问题，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT（十进制）** | -2134375877 |
| **错误字符串** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **所需的补救措施** | 是 |

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT（十进制）** | -2134375908 |
| **错误字符串** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **所需的补救措施** | 是 |

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT（十进制）** | -2134375853 |
| **错误字符串** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **所需的补救措施** | 是 |

当有许多文件由于存在按项列出的错误而无法同步时，同步会话就会因这些错误而失败。 执行[如何确定是否有特定的文件或文件夹未同步？](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)部分所述的步骤，以修复按项列出的错误。 对于同步错误 ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED，请提交一个支持案例。

> [!NOTE]
> Azure 文件同步每天在服务器上创建临时 VSS 快照一次，以同步包含开放句柄的文件。

<a id="-2134376423"></a>**由于服务器终结点路径出现问题，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT（十进制）** | -2134376423 |
| **错误字符串** | ECS_E_SYNC_INVALID_PATH |
| **所需的补救措施** | 是 |

确保路径存在、位于本地 NTFS 卷上，且不是重新分析点或现有服务器终结点。

<a id="-2134375817"></a>同步失败，因为筛选器驱动程序版本与代理版本不兼容  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT（十进制）** | -2134375817 |
| **错误字符串** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **所需的补救措施** | 是 |

发生此错误的原因是加载的云分层筛选器驱动程序 (StorageSync.sys) 版本与 Storage Sync Agent (FileSyncSvc) 服务不兼容。 如果已升级 Azure 文件同步代理，请重启服务器以完成安装。 如果错误继续发生，请卸载代理，重启服务器并重新安装 Azure 文件同步代理。

<a id="-2134376373"></a>**服务当前不可用。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT（十进制）** | -2134376373 |
| **错误字符串** | ECS_E_SERVICE_UNAVAILABLE |
| **所需的补救措施** | 否 |

此错误的原因是 Azure 文件同步服务不可用。 当 Azure 文件同步服务再次可用时，此错误会自动解决。

> [!Note]  
> 连接到 Azure 文件同步服务的网络连接恢复后，同步可能不会立即恢复。 默认情况下，若在服务器终结点位置未检测到任何更改，Azure 文件同步将每 30 分钟启动一次同步会话。 若要强制执行同步会话，请重启存储同步代理 (FileSyncSvc) 服务，或对服务器终结点位置内的文件或目录进行更改。

<a id="-2146233088"></a>**由于出现异常，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT（十进制）** | -2146233088 |
| **错误字符串** | COR_E_EXCEPTION |
| **所需的补救措施** | 否 |

此错误的原因是同步因异常而失败。 如果此错误持续了数小时，请创建支持请求。

<a id="-2134364045"></a>**由于存储帐户已故障转移到另一区域，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT（十进制）** | -2134364045 |
| **错误字符串** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **所需的补救措施** | 是 |

发生此错误的原因是存储帐户已故障转移到另一区域。 Azure 文件同步不支持存储帐户故障转移功能。 不得对包含 Azure 文件共享且用作 Azure 文件同步中云终结点的存储帐户执行故障转移。 否则，将会导致同步停止，并且可能还会在有新分层文件的情况下导致意外数据丢失。 若要解决此问题，请将帐户移到主要区域。

<a id="-2134375922"></a>**由于同步数据库出现暂时性的问题，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT（十进制）** | -2134375922 |
| **错误字符串** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **所需的补救措施** | 否 |

此错误的原因是同步数据库出现内部问题。 重试同步时，此错误会自行解决。 如果此错误持续了较长时间，请创建支持请求，到时我们将与你取得联系，并帮助解决此问题。

<a id="-2134364024"></a>**由于 Azure Active Directory 租户中的更改，同步失败**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT（十进制）** | -2134364024 | 
| **错误字符串** | ECS_E_INVALID_AAD_TENANT |
| **所需的补救措施** | 是 |

请确保你有最新的 Azure 文件同步代理。 从代理 V10 开始，Azure文件同步支持将订阅移到其他 Azure Active Directory 租户。
 
获得最新的代理版本后，必须为 Microsoft.StorageSync 应用程序提供对存储帐户的访问权限（请参阅[确保 Azure 文件同步有权访问存储帐户](#troubleshoot-rbac)）。

<a id="-2134364010"></a>**由于未配置防火墙和虚拟网络例外，同步失败**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT（十进制）** | -2134364010 | 
| **错误字符串** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **所需的补救措施** | 是 |

如果在存储帐户上启用防火墙和虚拟网络设置，但未勾选“允许受信任的 Microsoft 服务访问此存储帐户”例外，则会发生此错误。 若要解决此问题，请执行部署指南的[配置防火墙和虚拟网络设置](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)部分记录的步骤。

<a id="-2147024891"></a>**由于系统卷信息文件夹的权限不正确，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT（十进制）** | -2147024891 |
| **错误字符串** | ERROR_ACCESS_DENIED |
| **所需的补救措施** | 是 |

如果 NT AUTHORITY\SYSTEM 帐户没有访问服务器终结点所在卷上的 System Volume Information 文件夹的权限，则可能出现此错误。 请注意，如果单个文件同步失败并显示 ERROR_ACCESS_DENIED 错误，请执行[针对每个文件/目录同步错误的故障排除](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors)部分中所述的步骤。

若要解决此问题，请执行以下步骤：

1. 下载 [Psexec](/sysinternals/downloads/psexec) 工具。
2. 在提升的命令提示符处运行以下命令，以使用系统帐户启动命令提示符：`PsExec.exe -i -s -d cmd`
3. 在命令提示符（在系统帐户下运行）处运行以下命令，确认 NT AUTHORITY\SYSTEM 帐户无权访问服务器终结点所在卷上的 System Volume Information 文件夹：`cacls "drive letter:\system volume information" /T /C`
4. 如果 NT AUTHORITY\SYSTEM 帐户无权访问服务器终结点所在卷上的 System Volume Information 文件夹，请运行以下命令：`cacls  "drive letter:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"`
    - 如果步骤 4 失败并出现访问被拒绝错误，请运行以下命令来获取 System Volume Information 文件夹的所有权，然后重复步骤 4：`takeown /A /R /F "drive letter:\System Volume Information"`

<a id="-2134375810"></a>**由于已删除并重新创建 Azure 文件共享，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT（十进制）** | -2134375810 |
| **错误字符串** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **所需的补救措施** | 是 |

发生此错误的原因是，Azure 文件同步不支持在同一同步组中删除 Azure 文件共享并重新创建它。 

若要解决此问题，请执行以下步骤，以便删除同步组并重新创建它：

1. 删除同步组中的所有服务器终结点。
2. 删除云终结点。 
3. 删除同步组。
4. 如果在服务器终结点上启用了云分层，请执行[删除服务器终结点后无法在服务器上访问分层文件](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)部分中所述的步骤，以便删除服务器上的独立分层文件。
5. 重新创建同步组。

<a id="-2134375852"></a>**同步检测到副本已还原到较旧状态**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c80254 |
| **HRESULT（十进制）** | -2134375852 |
| **错误字符串** | ECS_E_SYNC_REPLICA_BACK_IN_TIME |
| **所需的补救措施** | 否 |

不需要执行任何操作。 之所以发生此错误，是因为同步检测到副本已还原到较旧的状态。 现在，同步将进入协调模式；在此模式下，将通过合并 Azure 文件共享的内容和服务器终结点上的数据，重新创建同步关系。 触发协调模式时，进程可能会非常耗时，具体取决于命名空间大小。 在完成协调前，不会进行常规同步，并且 Azure 文件共享和服务器终结点之间的不同文件（上次修改时间或大小）将导致文件冲突。

<a id="-2145844941"></a>**由于重定向了 HTTP 请求，同步失败**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT（十进制）** | -2145844941 |
| **错误字符串** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **所需的补救措施** | 是 |

发生此错误的原因是 Azure 文件同步不支持 HTTP 重定向（3xx 状态代码）。 要解决此问题，请在代理服务器或网络设备上禁用 HTTP 重定向。

<a id="-2134364027"></a>脱机数据传输期间发生超时，但数据传输仍在进行中。  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT（十进制）** | -2134364027 |
| **错误字符串** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **所需的补救措施** | 否 |

数据引入操作超时可引发此错误。 如果正在进行同步（AppliedItemCount 大于 0），则可以忽略此错误。 请参阅[如何监视当前同步会话的进度？](#how-do-i-monitor-the-progress-of-a-current-sync-session)。

<a id="-2134375814"></a>**由于在服务器上找不到服务器终结点路径，同步失败。**  

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80c8027a |
| **HRESULT（十进制）** | -2134375814 |
| **错误字符串** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **所需的补救措施** | 是 |

如果已重命名或已删除用作服务器终结点路径的目录，则会发生此错误。 如果已重命名该目录，请将目录重命名回原始名称，并重启存储同步代理服务 (FileSyncSvc)。

如果已删除该目录，请执行以下步骤，删除现有服务器终结点，并使用新路径创建一个新的服务器终结点：

1. 按照[删除服务器终结点](file-sync-server-endpoint-delete.md)中所述的步骤，在同步组中删除服务器终结点。
1. 按照[添加服务器终结点](file-sync-server-endpoint-create.md)中所述的步骤，在同步组中创建一个新的服务器终结点。

<a id="-2134375783"></a>由于服务器路径为空，服务器终结点预配失败。

| 错误 | 代码 |
|-|-|
| **HRESULT** | 0x80C80299 |
| **HRESULT（十进制）** | -2134375783 |
| **错误字符串** | ECS_E_SYNC_AUTHORITATIVE_UPLOAD_EMPTY_SET |
| **所需的补救措施** | 是 |

如果满足以下条件，服务器终结点预配会失败并出现此错误代码： 
* 此服务器终结点预配了初始同步模式：[服务器授权](file-sync-server-endpoint-create.md#initial-sync-section)
* 本地服务器路径为空或不包含识别为能同步的项。

此预配错误可防止你删除 Azure 文件共享中可能提供的所有内容。 服务器授权上传是一种特殊模式，用于利用服务器位置的更新来了解已设置种子的云位置。 查看此[迁移指南](../files/storage-files-migration-server-hybrid-databox.md)，了解为其而构建此模式的场景。

1. 按照[删除服务器终结点](file-sync-server-endpoint-delete.md)中所述的步骤，在同步组中删除服务器终结点。
1. 按照[添加服务器终结点](file-sync-server-endpoint-create.md)中所述的步骤，在同步组中创建一个新的服务器终结点。

### <a name="common-troubleshooting-steps"></a>常见故障排除步骤
<a id="troubleshoot-storage-account"></a>**验证存储帐户是否存在。**  
# <a name="portal"></a>[门户](#tab/azure-portal)
1. 导航到存储同步服务中的同步组。
2. 选择同步组中的云终结点。
3. 记下打开的窗格中的 Azure 文件共享名称。
4. 选择链接的存储帐户。 如果此链接不起作用，则表示引用的存储帐户已删除。
    ![显示云终结点详细信息窗格（包含存储帐户的链接）的屏幕截图。](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**确保 Azure 文件共享存在。**  
# <a name="portal"></a>[门户](#tab/azure-portal)
1. 在左侧目录中单击“概述”，返回存储帐户主页。
2. 选择“文件”查看文件共享列表。
3. 检查云终结点引用的文件共享是否显示在文件共享列表中（在上述步骤 1 中应已记下此共享名称）。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**确保 Azure 文件同步有权访问存储帐户。**  
# <a name="portal"></a>[门户](#tab/azure-portal)
1. 在左侧的目录上单击“访问控制(IAM)”。
1. 单击“角色分配”选项卡以列出有权访问你的存储帐户的用户和应用程序（*服务主体*）。
1. 使用“读取器和数据访问”角色验证列表中是否显示“Microsoft.StorageSync”或“混合文件同步服务”（旧应用程序名称）。 

    ![存储帐户访问控制选项卡中的“混合文件同步服务”服务主体的屏幕截图](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    如果“Microsoft.StorageSync”或“混合文件同步服务”没有出现在列表中，请执行以下步骤：

    - 单击“添加”。
    - 在“角色”字段中，选择“读者和数据访问”。
    - 在“选择”字段中，键入“Microsoft.StorageSync”，选择角色并单击“保存”  。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

## <a name="cloud-tiering"></a>云分层 
云分层中存在两个故障路径：

- 文件可能无法层，这意味着，Azure 文件同步未成功尝试将某个文件分层到 Azure 文件。
- 文件可能无法撤回，这意味着，当某个用户尝试访问已分层的文件时，Azure 文件同步文件系统筛选器 (StorageSync.sys) 无法下载数据。

通过任一故障路径可能发生两个主要故障类：

- 云存储故障
    - 暂时性存储服务可用性问题。 有关详细信息，请参阅 [Azure 存储的服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)。
    - Azure 文件共享不可访问。 删除仍属于同步组中的云终结点的 Azure 文件共享时，通常会发生此故障。
    - 存储帐户不可访问。 删除仍包含 Azure 文件共享（属于同步组中的云终结点）的存储帐户时，通常会发生此故障。 
- 服务器故障 
  - 未加载 Azure 文件同步文件系统筛选器 (StorageSync.sys)。 为了响应分层/撤回请求，必须加载 Azure 文件同步文件系统筛选器。 可能会出于多种原因而未能加载该筛选器，但最常见的原因是管理员已手动将其卸载。 要使 Azure 文件同步正常运行，始终必须加载 Azure 文件同步文件系统筛选器。
  - 重分析点缺失、损坏或出现其他形式的中断。 重分析点是文件中的特殊数据结构，它由两个部分组成：
    1. 一个重分析标记，向操作系统指示 Azure 文件同步文件系统筛选器 (StorageSync.sys) 可能需要针对文件的 IO 执行某项操作。 
    2. 重分析数据，向文件系统筛选器指示关联云终结点（Azure 文件共享）上的文件的 URI。 
        
       重分析点损坏的最常见原因是管理员尝试修改标记或其数据。 
  - 网络连接问题。 若要分层或撤回文件，服务器必须已建立 Internet 连接。

以下部分说明如何排查云分层问题，并确定问题是云存储问题还是服务器问题。

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>如何监视服务器上的分层活动  
若要监视服务器上的分层活动，请使用遥测事件日志（位于“事件查看器”中的“应用程序和服务\Microsoft\FileSync\代理”下）中的事件 ID 9003、9016 和 9029。

- 事件 ID 9003 提供服务器终结点的错误分布情况。 例如错误总数、错误代码，等等。请注意，将为每个错误代码记录一个事件。
- 事件 ID 9016 提供卷的副本创建结果。 例如，可用空间百分比、会话中创建的文件副本数、无法创建副本的文件数，等等。
- 事件 ID 9029 提供服务器终结点的副本创建会话信息。 例如，会话中尝试的文件数、会话分层的文件数、已分层的文件数，等等。

### <a name="how-to-monitor-recall-activity-on-a-server"></a>如何监视服务器上的重新调用活动
若要监视服务器上的回调活动，请使用遥测事件日志（位于“事件查看器”中的“应用程序和服务\Microsoft\FileSync\代理”下）中的事件 ID 9005、9006、9009 和 9059。

- 事件 ID 9005 提供服务器终结点的重新调用可靠性。 例如，访问的唯一文件总数、访问失败的唯一文件总数，等等。
- 事件 ID 9006 提供服务器终结点的重新调用错误分布情况。 例如，失败的请求总数、错误代码，等等。请注意，将为每个错误代码记录一个事件。
- 事件 ID 9009 提供服务器终结点的回调会话信息。 例如，DurationSeconds、CountFilesRecallSucceeded、CountFilesRecallFailed 等。
- 事件 ID 9059 提供服务器终结点的应用程序回调分布情况。 例如，ShareId、应用程序名称和 TotalEgressNetworkBytes。

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>如何对未能分层的文件进行故障排除
如果文件无法分层到 Azure 文件：

1. 在事件查看器中，查看位于 Applications and Services\Microsoft\FileSync\Agent 下的遥测、操作和诊断事件日志。 
   1. 验证文件是否在 Azure 文件共享中存在。

      > [!NOTE]
      > 文件必须先同步到 Azure 文件共享，然后才能分层。

   2. 验证服务器是否已建立 Internet 连接。 
   3. 验证 Azure 文件同步筛选器驱动程序（StorageSync.sys 和 StorageSyncGuard.sys）是否正在运行。
       - 在权限提升的命令提示符下，运行 `fltmc`。 验证 StorageSync.sys 和 StorageSyncGuard.sys 文件系统筛选器驱动程序是否已列出。

> [!NOTE]
> 如果文件无法分层，则每小时在“遥测”事件日志中记录事件 ID 9003 一次（为每个错误代码记录一个事件）。 检查[分层错误和修正](#tiering-errors-and-remediation)部分，以查看是否列出了针对错误代码的修正步骤。

### <a name="tiering-errors-and-remediation"></a>分层错误和修正

| HRESULT | HRESULT（十进制） | 错误字符串 | 问题 | 补救 |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | 文件分层失败，因为正在进行初始上传。 | 无需采取措施。 完成初始上传后，将对文件进行分层。 |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | 由于文件在使用中，文件分层失败。 | 无需采取措施。 不再使用该文件时，文件同步会对其进行分层。 |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | 由于文件已被同步排除，文件分层失败。 | 无需采取措施。 无法对同步排除列表中的文件进行分层。 |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | 由于在服务器上找不到文件，文件分层失败。 | 无需采取措施。 如果错误仍然存在，请检查服务器上是否存在该文件。 |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | 由于文件已在 Azure 文件共享中删除，文件分层失败。 | 无需采取措施。 当下一个下载同步会话运行时，应在服务器上删除该文件。 |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | 由于网络问题，文件分层失败。 | 无需采取措施。 如果错误仍然存在，请检查与 Azure 文件共享的网络连接。 |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | 由于网络问题，文件分层失败。 | 无需采取措施。 如果错误仍然存在，请检查与 Azure 文件共享的网络连接。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 由于访问被拒绝，文件分层失败。 如果文件位于一个 DFS-R 只读复制文件夹中，则可能发生此错误。 | Azure 文件同步不支持 DFS-R 只读复制文件夹中的服务器终结点。 请参阅[规划指南](file-sync-planning.md#distributed-file-system-dfs)以获取详细信息。 |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | 由于网络问题，文件分层失败。 | 无需采取措施。 如果错误仍然存在，请检查与 Azure 文件共享的网络连接。 |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | 由于文件大小小于支持的大小，文件分层失败。 | 如果代理版本低于 9.0，则支持的最小文件大小为 64kb。 如果代理版本为 9.0 和更高版本，则支持的最小文件大小取决于文件系统群集大小（文件系统群集大小的两倍）。 例如，如果文件系统群集大小为 4kb，则最小文件大小为 8kb。 |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | 由于 Azure 存储问题，文件分层失败。 | 如果此错误持续存在，请创建支持请求。 |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | 由于同时撤回文件，文件分层失败。 | 无需采取措施。 撤回完成且文件不再使用后，将会对文件分层。 |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | 由于文件尚未同步到 Azure 文件共享，文件分层失败。 | 无需采取措施。 当文件同步到 Azure 文件共享后，文件将进行分层。 |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | 由于云分层筛选器驱动程序 (storagesync.sys) 未运行，文件分层失败。 | 要解决此问题，请打开提升的命令提示符并运行以下命令：`fltmc load storagesync`<br>如果在运行 fltmc 命令时无法加载 Azure 文件同步筛选器驱动程序，请卸载 Azure 文件同步代理，重启服务器，并重新安装 Azure 文件同步代理。 |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 由于服务器终结点所在的卷上磁盘空间不足，文件分层失败。 | 若要解决此问题，请在服务器终结点所在的卷上腾出至少 100 MB 的磁盘空间。 |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | 由于文件尚未同步到 Azure 文件共享，文件分层失败。 | 无需采取措施。 当文件同步到 Azure 文件共享后，文件将进行分层。 |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | 由于文件是不受支持的的重分析点，文件分层失败。 | 如果文件是重复数据删除重分析点，请按照 [计划指南](file-sync-planning.md#data-deduplication)中的步骤启用重复数据删除支持。 不支持具有除重复数据删除以外的重分析点的文件，也不会对其进行分层。  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | 由于已对文件进行了修改，文件分层失败。 | 无需采取措施。 当修改后的文件同步到 Azure 文件共享时，文件将进行分层。 |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | 由于文件尚未同步到 Azure 文件共享，文件分层失败。 | 无需采取措施。 当文件同步到 Azure 文件共享后，文件将进行分层。 |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | 由于网络问题，文件分层失败。 | 无需采取措施。 如果错误仍然存在，请检查与 Azure 文件共享的网络连接。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 由于已对文件进行了修改，文件分层失败。 | 无需采取措施。 当修改后的文件同步到 Azure 文件共享时，文件将进行分层。 |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 由于系统资源不足，文件分层失败。 | 如果错误仍然存在，请调查耗尽了系统资源的应用程序或内核模式驱动程序。 |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | 由于写入云分层数据库时出现 I/O 错误，文件分层失败。 | 如果错误仍然存在，请在卷上运行 chkdsk，并检查存储硬件。 |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | 由于云分层数据库未运行，文件分层失败。 | 要解决此问题，请重启 FileSyncSvc 服务或服务器。 如果错误仍然存在，请在卷上运行 chkdsk，并检查存储硬件。 |
| 0x80C80285 | -2160591493 | ECS_E_GHOSTING_SKIPPED_BY_CUSTOM_EXCLUSION_LIST | 无法对文件进行分层，是因为此文件类型已从分层中排除。 | 若要使用此文件类型对文件进行分层，请修改位于 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 下的 GhostingExclusionList 注册表设置。 |
| 0x80C86050 | -2160615504 | ECS_E_REPLICA_NOT_READY_FOR_TIERING | 文件分层失败，是因为当前同步模式为初始上传或协调。 | 无需采取措施。 同步完成初始上传或协调后，文件将分层。 |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>如何对未能召回的文件进行故障排除  
如果无法重新调用文件：
1. 在事件查看器中，查看位于 Applications and Services\Microsoft\FileSync\Agent 下的遥测、操作和诊断事件日志。
    1. 验证文件是否在 Azure 文件共享中存在。
    2. 验证服务器是否已建立 Internet 连接。 
    3. 打开服务 MMC 管理单元，然后验证存储同步代理服务 (FileSyncSvc) 是否正在运行。
    4. 验证 Azure 文件同步筛选器驱动程序（StorageSync.sys 和 StorageSyncGuard.sys）是否正在运行。
        - 在权限提升的命令提示符下，运行 `fltmc`。 验证 StorageSync.sys 和 StorageSyncGuard.sys 文件系统筛选器驱动程序是否已列出。

> [!NOTE]
> 如果文件无法重新调用，则每小时在“遥测”事件日志中记录事件 ID 9006 一次（为每个错误代码记录一个事件）。 检查[撤回错误和修正](#recall-errors-and-remediation)部分，查看是否列出了针对错误代码的修正步骤。

### <a name="recall-errors-and-remediation"></a>撤回错误和修正

| HRESULT | HRESULT（十进制） | 错误字符串 | 问题 | 补救 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | 由于 I/O 超时，文件撤回失败。 此问题可能是由以下几个原因造成的：服务器资源约束、网络连接不良或 Azure 存储问题（例如存在限制）。 | 无需采取措施。 如果此错误持续数小时，请提交一个支持案例。 |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | 由于网络问题，文件撤回失败。  | 如果错误仍然存在，请检查与 Azure 文件共享的网络连接。 |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | 由于已删除服务器终结点，文件撤回失败。 | 若要解决此问题，请参阅[在删除服务器终结点后无法访问服务器上的分层文件](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 由于访问被拒绝，文件撤回失败。 如果存储帐户启用了防火墙和虚拟网络设置，且服务器无权访问存储帐户，则可能出现此问题。 | 若要解决此问题，按照部署指南中的[配置防火墙和虚拟网络设置](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)部分中所述的步骤添加服务器 IP 地址或虚拟网络。 |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | 由于在 Azure 文件共享中无法访问文件，文件撤回失败。 | 若要解决此问题，请验证文件是否存在于 Azure 文件共享中。 如果文件存在于 Azure 文件共享中，请升级到最新的 Azure 文件同步[代理版本](file-sync-release-notes.md#supported-versions)。 |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | 由于存储帐户授权失败，文件撤回失败。 | 若要解决此问题，请确保 [Azure 文件同步有权访问存储帐户](?tabs=portal1%252cazure-portal#troubleshoot-rbac)。 |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | 由于无法访问 Azure 文件共享，文件撤回失败。 | 请验证文件共享是否存在并且可访问。 如果已删除并重新创建了文件共享，请执行[由于已删除并重新创建了 Azure 文件共享，同步失败](?tabs=portal1%252cazure-portal#-2134375810)部分中所述的步骤，以删除并重新创建同步组。 |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 由于系统资源不足，文件撤回失败。 | 如果错误仍然存在，请调查耗尽了系统资源的应用程序或内核模式驱动程序。 |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | 由于内存不足，文件撤回失败。 | 如果错误仍然存在，请调查导致内存不足的应用程序或内核模式驱动程序。 |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 由于磁盘空间不足，文件撤回失败。 | 要解决此问题，请将文件移到其他卷以释放卷的空间、增加卷大小，或使用 Invoke-StorageSyncCloudTiering cmdlet 强制文件分层。 |
| 0x80072f8f | -2147012721 | WININET_E_DECODING_FAILED | 由于服务器无法解码来自 Azure 文件同步服务的响应，因此未能召回文件。 | 若代理正在修改来自 Azure 文件同步服务的响应，通常会出现此错误。 请检查你的代理配置。 |
| 0x80090352 | -2146892974 | SEC_E_ISSUING_CA_UNTRUSTED | 若组织使用 TLS 终止代理，或恶意实体正在截获服务器与 Azure 文件同步服务之间的通信，则会出现此错误。 | 若你确定这是正常情况（因为你的组织使用的是 TLS 终止代理)，请按照 [CERT_E_UNTRUSTEDROOT](https://docs.microsoft.com/azure/storage/file-sync/file-sync-troubleshoot?tabs=portal1%2Cazure-portal#-2146762487) 错误中所述的步骤来解决此问题。 |
| 0x80c86047 | -2134351801 | ECS_E_AZURE_SHARE_SNAPSHOT_NOT_FOUND | 文件无法撤回，因为其引用的文件版本在 Azure 文件共享中不再存在。 | 若分层文件是从备份中还原的，可能出现此问题。 若要解决此问题，请提出支持请求。 |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>在删除服务器终结点后无法访问服务器上的分层文件
如果没有在删除服务器终结点之前撤回分层的文件，则服务器上的这些文件会变得不可访问。

如果分层文件不可访问，则记录错误
- 同步文件时，将在 ItemResults 事件日志中记录错误代码 -2147942467 (0x80070043 - ERROR_BAD_NET_NAME)
- 撤回文件时，将在 RecallResults 事件日志中记录错误代码 -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND)

如果满足以下条件，则可以恢复对分层文件的访问：
- 服务器终结点是在过去 30 天内被删除的
- 未删除云终结点 
- 未删除文件共享
- 未删除同步组

如果满足上述条件，则可以在 30 天内在同一同步组内服务器上的同一路径上重新创建服务器终结点，从而恢复对服务器上的文件的访问。 

如果不满足上述条件，则无法恢复访问，因为服务器上的这些分层文件现已孤立。 按照以下说明删除孤立的分层文件。

**说明**
- 当无法访问服务器上的分层文件时，如果直接访问 Azure 文件共享，则完整文件应仍可访问。
- 若要防止将来出现孤立的分层文件，请在删除服务器终结点时按照[删除服务器终结点](file-sync-server-endpoint-delete.md)中所述的步骤进行操作。

<a id="get-orphaned"></a>**如何获取孤立分层文件的列表** 

1. 验证是否已安装了 Azure 文件同步代理版本 v5.1 或更高版本。
2. 运行以下 PowerShell 命令以列出孤立的分层文件：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. 保存 OrphanTieredFiles.txt 输出文件，以防文件在删除后需要从备份中还原。

<a id="remove-orphaned"></a>**如何删除孤立的分层文件** 

*选项 1：删除孤立的分层文件*

此选项将删除 Windows Server 上孤立的分层文件，但如果服务器终结点在 30 天后重新创建，或已连接到其他同步组，则需将其删除。 如果在重新创建服务器终结点之前在 Windows Server 或 Azure 文件共享上更新了文件，则会发生文件冲突。

1. 验证是否已安装了 Azure 文件同步代理版本 v5.1 或更高版本。
2. 备份 Azure 文件共享和服务器终结点位置。
3. 按照[删除服务器终结点](file-sync-server-endpoint-delete.md)中所述的步骤，在同步组中删除服务器终结点（如果存在）。

> [!Warning]  
> 如果在使用 Remove-StorageSyncOrphanedTieredFiles cmdlet 之前未删除服务器终结点，则删除服务器上孤立的分层文件将删除 Azure 文件共享中的完整文件。 

4. 运行以下 PowerShell 命令以列出孤立的分层文件：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. 保存 OrphanTieredFiles.txt 输出文件，以防文件在删除后需要从备份中还原。
6. 运行以下 PowerShell 命令以删除孤立的分层文件：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**说明** 
- 服务器上已修改但未同步到 Azure 文件共享的分层文件将被删除。
- 不会删除可访问的分层文件（非孤立文件）。
- 非分层文件将保留在服务器上。

7. 可选：如果在步骤 3 中删除了服务器终结点，请重新创建。

*选项 2：装载 Azure 文件共享，并在本地复制服务器上孤立的文件*

此选项不需要删除服务器终结点，但需要足够的磁盘空间，以在本地复制完整文件。

1. 在包含孤立分层文件的 Windows Server 上[装载](../files/storage-how-to-use-files-windows.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) Azure 文件共享。
2. 运行以下 PowerShell 命令以列出孤立的分层文件：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. 使用 OrphanTieredFiles.txt 输出文件标识服务器上孤立的分层文件。
4. 通过将完整文件从 Azure 文件共享复制到 Windows Server 来覆盖孤立的分层文件。

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>如何对服务器上意外重新调用文件的问题进行故障排除  
读取大量文件的防病毒、备份和其他应用程序会导致意外的重新调用，除非这些应用程序遵循脱机属性并跳过读取这些文件的内容。 跳过支持此选项的产品的脱机文件可帮助避免在执行防病毒软件扫描或备份作业等操作时出现意外的重新调用。

请咨询软件供应商，了解如何配置其解决方案以跳过读取脱机文件。

在其他一些情况下（例如，在文件资源管理器中浏览文件时），也可能出现意外的重新调用。 在服务器上的文件资源管理器中打开包含云分层文件的文件夹可能导致意外的重新调用。 在服务器上启用防病毒解决方案时更是如此。

> [!NOTE]
>使用遥测事件日志中的事件 ID 9059 确定导致重新调用的应用程序。 此事件为服务器终结点提供应用程序重新调用分发，并每小时记录一次。

### <a name="process-exclusions-for-azure-file-sync"></a>Azure 文件同步的进程排除项

若要将防病毒或其他应用程序配置为跳过对 Azure 文件同步可访问文件的扫描，请配置以下进程排除项：

- C:\Program Files\Azure\StorageSyncAgent\AfsAutoUpdater.exe
- C:\Program Files\Azure\StorageSyncAgent\FileSyncSvc.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\MonAgentLauncher.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\MonAgentHost.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\MonAgentManager.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\MonAgentCore.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\Extensions\XSyncMonitoringExtension\AzureStorageSyncMonitor.exe

### <a name="tls-12-required-for-azure-file-sync"></a>Azure 文件同步需要 TLS 1.2

可以通过查看[注册表设置](/windows-server/security/tls/tls-registry-settings)来查看服务器上的 TLS 设置。 

如果使用的是代理，请查阅代理文档并确保将其配置为使用 TLS 1.2。

## <a name="general-troubleshooting"></a>常规故障排除
如果在服务器上遇到 Azure 文件同步问题，请先完成以下步骤：
1. 在事件查看器中，查看遥测、操作和诊断事件日志。
    - 同步、分层和重新调用问题记录在 Applications and Services\Microsoft\FileSync\Agent 下的遥测、诊断和操作事件日志中。
    - 与管理服务器相关的（例如配置设置）问题记录在 Applications and Services\Microsoft\FileSync\Management 下的操作和诊断事件日志中。
2. 验证 Azure 文件同步服务是否正在服务器上运行。
    - 打开服务 MMC 管理单元，然后验证存储同步代理服务 (FileSyncSvc) 是否正在运行。
3. 验证 Azure 文件同步筛选器驱动程序（StorageSync.sys 和 StorageSyncGuard.sys）是否正在运行。
    - 在权限提升的命令提示符下，运行 `fltmc`。 验证 StorageSync.sys 和 StorageSyncGuard.sys 文件系统筛选器驱动程序是否已列出。

如果问题未得到解决，请运行 AFSDiag 工具，并将其 .zip 文件输出发送到分配给你的用例的支持工程师做进一步诊断。

要运行 AFSDiag，请执行以下步骤。

对于代理版本 v11 和更高版本：
1. 打开权限提升的 PowerShell 窗口并运行以下命令（在每条命令后面按 Enter）：

    > [!NOTE]
    >AFSDiag 会在收集日志之前在其中创建输出目录和临时文件夹，并将在执行后删除临时文件夹。 指定不包含数据的输出位置。
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. 重现问题。 完成后，输入 **D**。
3. 随即会将一个包含日志和跟踪文件的 .zip 文件保存到指定的输出目录。 

对于代理版本 v10 和更早版本：
1. 创建用于保存 AFSDiag 输出的目录（例如，C:\Output）。
    > [!NOTE]
    >AFSDiag 会在收集日志之前删除输出目录中的所有内容。 指定不包含数据的输出位置。
2. 打开权限提升的 PowerShell 窗口并运行以下命令（在每条命令后面按 Enter）：

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. 对于 Azure 文件同步内核模式跟踪级别，请输入 **1**（除非指定为创建更详细的跟踪）并按 Enter。
4. 对于 Azure 文件同步用户模式跟踪级别，请输入 **1**（除非指定为创建更详细的跟踪）并按 Enter。
5. 重现问题。 完成后，输入 **D**。
6. 随即会将一个包含日志和跟踪文件的 .zip 文件保存到指定的输出目录。


## <a name="see-also"></a>另请参阅
- [监视 Azure 文件同步](file-sync-monitoring.md)
- [在 Windows 中排查 Azure 文件问题](../files/storage-troubleshoot-windows-file-connection-problems.md)
- [在 Linux 中排查 Azure 文件问题](../files/storage-troubleshoot-linux-file-connection-problems.md)
