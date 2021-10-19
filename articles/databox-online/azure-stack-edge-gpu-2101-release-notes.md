---
title: Azure Stack Edge Pro 2101 发行说明
description: 介绍运行 2101 版的 Azure Stack Edge Pro 的关键未结问题和解决方案。
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 44aa20dcdf4cc4d87fa868ce38855b525caa8a98
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707497"
---
# <a name="azure-stack-edge-2101-release-notes"></a>Azure Stack Edge 2101 发行说明

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

以下发行说明明确了 2101 版 Azure Stack Edge 的重要未结问题和已解决问题。 这些发行说明适用于Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 设备。 与特定模型相对应的功能和问题会在适当的位置进行调用。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署设备之前，请仔细查看本发行说明中包含的信息。

本文适用于“Azure Stack Edge 2101”版本，该版本映射到软件版本号“2.2.1473.2521” 。

## <a name="whats-new"></a>新变化

Azure Stack Edge 2101 版中提供了以下新功能。 

- **正式发布 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 设备** - 从此发布版本开始，将可以使用 Azure Stack Edge pro R 和 Azure Stack Edge Mini R 设备。 有关详细信息，请参阅[什么是 Azure Stack Edge Pro R](azure-stack-edge-pro-r-overview.md) 以及[什么是 Azure Stack Edge Mini R](azure-stack-edge-mini-r-overview.md)。  
- **虚拟机的云管理** - 从此版本开始，可以通过 Azure 门户在设备上创建和管理虚拟机。 有关详细信息，请参阅[部署 Azure SQL Edge](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)。
- **与 Azure Monitor 的集成** - 现在可以使用 Azure Monitor 来监视设备上运行的计算应用程序中的容器。 在此版本中不支持 Azure Monitor 指标存储。 有关详细信息，请参阅如何[在设备上启用 Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md)。
- **Edge 容器注册表** - 在此版本中，提供了一个边缘容器注册表，可用于在设备的边缘提供存储库。 你可以使用此注册表来存储和管理容器映像。 有关详细信息，请参阅[启用 Edge 容器注册表](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。 
- **虚拟专用网络 (VPN)**  - 使用 VPN 为设备和云之间流动的数据提供额外一层加密。 VPN 仅在 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 上可用。有关详细信息，请参阅如何[在设备上配置 VPN](azure-stack-edge-mini-r-configure-vpn-powershell.md)。 
- **轮换静态加密密钥** - 现在可以轮换用于保护设备上驱动器的静态加密密钥了。 此功能仅适用于 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 设备。 有关详细信息，请参阅[轮换静态加密密钥](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#manage-access-to-device-data)。
- **主动日志记录** - 从此版本开始，可以根据系统运行状况指示符在设备上启用主动日志收集，从而帮助高效解决任何设备问题。 有关详细信息，请参阅[设备上的主动日志收集](azure-stack-edge-gpu-proactive-log-collection.md)。


## <a name="known-issues-in-2101-release"></a>2101 版本中的已知问题

下表提供了 2101 版本中已知问题的摘要。

| 不是。 | 功能 | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
|**1.**|预览功能 |在此版本中提供了以下功能的预览版：本地 Azure 资源管理器、VM、VM 的云管理、已启用 Azure Arc 的 Kubernetes、用于 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 的 VPN、用于 Azure Stack Edge Pro GPU 的多进程服务 (MPS)。  |将在以后的版本中正式推出这些功能。 |
|**2.**|Kubernetes 仪表板 | 不支持带 SSL 证书的 Kubernetes 仪表板的 Https 终结点。 | |
|**3.**|Kubernetes |启用 Web 代理时，Edge 容器注册表不起作用。|未来的版本中将提供该功能。 |
|**4.**|Kubernetes |Edge 容器注册表不适用于 IoT Edge 模块。| |
|**5.**|Kubernetes |Kubernetes 在 .NET 应用程序使用的环境变量名称中不支持“:”。 这也是事件网格 IoT Edge 模块在 Azure Stack Edge 设备和其他应用程序上运行时所必需的。 有关详细信息，请参阅 [ASP.NET 核心文档](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables)。|将“:”替换为双下划线。 有关详细信息，请参阅 [Kubernetes 问题](https://github.com/kubernetes/kubernetes/issues/53201)|
|**6.** |Azure Arc + Kubernetes 群集 |默认情况下，从 Git 存储库中删除资源 `yamls` 时，不会从 Kubernetes 群集中删除相应的资源。  |若要允许从 git 存储库删除资源时同时删除群集中的相应资源，请在 Arc OperatorParams 中设置 `--sync-garbage-collection`。 有关详细信息，请参阅[删除配置](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters)。 |
|**7.**|NFS |那些使用设备上的 NFS 共享装载来写入数据的应用程序应使用独占写入。 这可确保数据写入磁盘。| |
|**8.**|计算配置 |如果网络配置中的网关或交换机或路由器为网络上不存在的系统响应地址解析协议 (ARP) 请求，计算配置在网络配置中会失败。| |
|**9.**|计算和 Kubernetes |如果先在设备上设置了 Kubernetes，它会声明获取所有可用 GPU。 因此在设置 Kubernetes 后就无法使用 GPU 创建 Azure 资源管理器 VM 了。 |如果设备有 2 个 GPU，可以先创建 1 个使用 GPU 的 VM，然后再配置 Kubernetes。 在这种情况下，Kubernetes 将使用剩余的可用 1 GPU。 |


## <a name="known-issues-from-previous-releases"></a>以前版本中的已知问题

下表总结了以前发行版中遗留的已知问题。

| 不是。 | 功能 | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
|**1.**|Azure Stack Edge Pro + Azure SQL | 创建 SQL 数据库需要管理员访问权限。 |执行以下步骤，而不是[教程：使用 SQL Server 数据库在 Edge 存储数据](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database)中的步骤 1-2。 <ul><li>在设备的本地 UI 中，启用计算接口。 选择“计算”>“端口 #”>“启用计算”>“应用”。</li><li>从 [sqlcmd 实用工具](/sql/tools/sqlcmd-utility)中将 `sqlcmd` 下载到客户端计算机 </li><li>连接到计算接口 IP 地址（已启用的端口），在地址末尾添加 `,1401`。</li><li>最终命令如下所示：`sqlcmd -S {Interface IP},1401 -U SA -P "Strong!Passw0rd"`。</li>完成此操作后，当前文档中的步骤 3-4 应相同。 </li></ul> |
|**2.**|刷新| 不支持对通过“刷新”还原的 blob 进行增量更改 |对于 Blob 终结点，刷新后对 blob 进行部分更新可能导致更新不会上载到云中。 例如类似于以下的操作序列：<ul><li>在云中创建 blob。 或从设备中删除之前上传的 blob。</li><li>使用刷新功能，将 blob 从云中刷新到设备中。</li><li>使用 Azure SDK REST API 仅更新部分 blob。</li></ul>这些操作可能导致 blob 的更新部分不在云中更新。 <br>**解决方法**：使用 robocopy 等工具或通过资源管理器或命令行执行的常规文件复制操作来替换整个 blob。|
|**3.**|限制|在限制期间，如果不允许对设备执行新写入，NFS 客户端写入会失败并出现“权限被拒绝”错误。| 错误如下所示：<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir：无法创建目录“test”：权限被拒绝|
|**4.**|Blob 存储引入|使用 AzCopy 版本 10 进行 Blob 存储引入时，请使用以下参数运行 AzCopy：`Azcopy <other arguments> --cap-mbps 2000`| 如果没有为 AzCopy 提供这些限制，前者可能会将大量请求发送到设备，导致服务出现问题。|
|**5.**|分层存储帐户|使用分层存储帐户时，以下内容适用：<ul><li> 仅支持块 blob。 页 blob 不受支持。</li><li>不提供快照或复制 API 支持。</li><li> 不支持通过 `distcp` 引入 Hadoop 工作负载，因为前者会大量使用复制操作。</li></ul>||
|**6.**|NFS 共享连接|如果将多个进程复制到相同的共享，并且未使用 `nolock` 属性，则在复制过程中可能会出现错误。|必须将 `nolock` 属性传递给 mount 命令，才能将文件复制到 NFS 共享。 例如：`C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`。|
|**7.**|Kubernetes 群集|在运行 kubernetes 群集的设备上应用更新时，kubernetes 虚拟机将重新启动并重新引导。 在这种情况下，只会在更新后自动还原使用指定副本部署的 pod。  |如果在未指定副本集的情况下在复制控制器之外创建了单独的 pod，则在设备更新后，不会自动还原这些 pod。 需要还原这些 pod。<br>系统会使用副本集替换因任何原因被删除或终止的 pod（例如节点故障或中断性节点升级）。 出于此原因，建议使用副本集，即使你的应用程序只需要一个 Pod。|
|**8.**|Kubernetes 群集|只有 Helm v3 或更高版本支持 Azure Stack Edge Pro 上的 Kubernetes。 有关详细信息，请参阅[常见问题解答：删除 Tiller](https://v3.helm.sh/docs/faq/)。|
|**9.**|已启用 Azure Arc 的 Kubernetes |对于正式发布版，已启用 Azure Arc 的 Kubernetes 已从版本 0.1.18 更新为 0.2.9。 由于 Azure Stack Edge 设备上不支持已启用 Azure Arc 的 Kubernetes 更新，因此需要重新部署已启用 Azure Arc 的 Kubernetes。|按照以下步骤操作：<ol><li>[应用设备软件和 Kubernetes 更新](azure-stack-edge-gpu-install-update.md)。</li><li>连接到[设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md)。</li><li>删除现有的 Azure Arc 代理。 键入：`Remove-HcsKubernetesAzureArcAgent`。</li><li>[将 Azure Arc 部署到新资源](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。 不要使用现有的 Azure Arc 资源。</li></ol>|
|**10.**|已启用 Azure Arc 的 Kubernetes|如果在 Azure Stack Edge Pro 设备上配置了 web 代理，则不支持 Azure Arc 部署。||
|**11.**|Kubernetes |端口 31000 保留给 Kubernetes 仪表板。 端口 31001 保留给 Edge 容器注册表。 同样，在默认配置中，保留了 IP 地址 172.28.0.1 和172.28.0.10，分别用于 Kubernetes 服务和核心 DNS 服务。|不要使用保留 IP。|
|**12.**|Kubernetes |Kubernetes 当前不允许多协议 LoadBalancer 服务。 例如，需要同时侦听 TCP 和 UDP 的 DNS 服务。 |若要解决 Kubernetes 与 MetalLB 的这一限制，可以在同一 pod 选择器上创建两个服务（一个用于 TCP，一个用于 UDP）。 这些服务使用相同的共享密钥和 loadBalancerIP 来共享相同的 IP 地址。 如果服务数多于可用 IP 地址，则还可以共享 IP。 <br> 有关详细信息，请参阅 [IP 地址共享](https://metallb.universe.tf/usage/#ip-address-sharing)|
|**13.**|Kubernetes 群集|现有 Azure IoT Edge marketplace 模块可能需要修改才能在 Azure Stack Edge 设备上的 IoT Edge 中运行。|有关详细信息，请参阅“修改市场中的 Azure IoT Edge 模块，使其能在 Azure Stack Edge 设备上运行”。<!-- insert link-->|
|**14.**|Kubernetes |Azure Stack Edge 设备上的 Kubernetes 上的 Azure IoT Edge 不支持基于文件的绑定装载。|IoT Edge 使用转换层将 `ContainerCreate` 选项转换为 Kubernetes 构造。 将 `Binds` 映射创建到 `hostpath` 目录，因此基于文件的绑定装载不能绑定到 IoT Edge 容器中的路径。 如果可能，将映射父目录。|
|**15.**|Kubernetes |如果你为 IoT Edge 自带证书，并在设备上配置了计算后将这些证书添加到 Azure Stack Edge 设备上，则不会提取新证书。|若要解决此问题，应在设备上配置计算之前上传证书。 如果已配置计算，请[连接到设备的 PowerShell 接口，并运行 IoT Edge 命令](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands)。 重新启动 `iotedged` 和 `edgehub` pod。|
|**16.**|证书 |在某些情况下，本地 UI 中的证书状态可能需要几秒钟才能更新。 |本地 UI 中的以下方案可能会受到影响。<ul><li>“证书”页上的“状态”列 。</li><li>“入门”页面上的“安全性”磁贴 。</li><li>“概述”页面上的“配置”磁贴 。</li></ul>  |
|17.|IoT Edge |通过 IoT Edge 部署的模块不能使用主机网络。 | |
|**18.**|计算 + Kubernetes |计算/Kubernetes 不支持 NTLM web 代理。 ||
|**19.**|Kubernetes + 更新 |以前的软件版本（如 2008 版本）存在一个争用条件更新问题，该问题导致更新失败并出现 ClusterConnectionException。 |使用较新版本应有助于避免此问题。 如果仍出现此问题，解决方法是重试升级，此方法应能奏效。|
|**20**|Internet Explorer|如果启用了增强的安全功能，你可能无法访问本地 web UI 页。 | 禁用增强的安全性，并重新启动浏览器。|


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>后续步骤

- [更新设备](azure-stack-edge-gpu-install-update.md)
