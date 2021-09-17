---
title: 有关 Azure NetApp 文件的常见问题解答 | Microsoft Docs
description: 查看有关 Azure NetApp 文件（如网络、安全性、性能、容量管理和数据迁移/保护）的常见问题解答。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: b-juche
ms.openlocfilehash: 119cf21f90102f7ebccd8e4e06cd5e5dee3c4bfe
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427977"
---
# <a name="faqs-about-azure-netapp-files"></a>有关 Azure NetApp 文件的常见问题解答

本文解答了有关 Azure NetApp 文件的常见问题 (FAQ)。 

## <a name="networking-faqs"></a>网络常见问题解答

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>NFS 或 SMB 数据路径是否通过 Internet？  

不是。 NFS 或 SMB 数据路径不通过 Internet。 Azure NetApp 文件是一项 Azure 本机服务，已部署到可以使用该服务的 Azure 虚拟网络 (VNet) 中。 Azure NetApp 文件使用委托子网，并直接在 VNet 中预配网络接口。 

有关详细信息，请参阅 [Azure NetApp 文件网络规划指南](./azure-netapp-files-network-topologies.md)。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>是否可以将已创建的 VNet 连接到 Azure NetApp 文件服务？

是的，可以将创建的 VNet 连接到该服务。 

有关详细信息，请参阅 [Azure NetApp 文件网络规划指南](./azure-netapp-files-network-topologies.md)。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>是否可以使用 DNS FQDN 名称装载 Azure NetApp 文件的 NFS 卷？

是的，如果创建了所需的 DNS 条目，可以这样做。 Azure NetApp 文件提供预配卷的服务 IP。 

> [!NOTE] 
> Azure NetApp 文件可根据需要部署服务的其他 IP。  DNS 条目可能需要定期更新。

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>能否为 Azure NetApp 文件卷设置或选择我自己的 IP 地址？  

不是。 对 Azure NetApp 文件卷的 IP 分配是动态的。 不支持静态 IP 分配。 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp 文件是否支持双堆栈（IPv4 和 IPv6）VNet？

否，Azure NetApp 文件目前不支持双堆栈（IPv4 和 IPv6）VNet。  

### <a name="is-the-number-of-the-ip-addresses-using-azure-vmware-solutions-for-guest-os-mounts-limited-to-1000"></a>使用 Azure VMWare 解决方案进行来宾 OS 装载的 IP 地址数是否[限于 1000 个](azure-netapp-files-resource-limits.md#resource-limits)？

否。 Azure VMWare 解决方案位于 ER 网关后面，这使得它的行为类似于本地系统。 AVS“主机”和“来宾”的数量对于 Azure NetApp 文件不可见，1000 个 IP 地址限制不适用。
 
## <a name="security-faqs"></a>安全常见问题

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>能否对 Azure VM 和存储之间的网络流量进行加密？

根据设计，Azure NetApp 文件数据流量本质上是安全的，因为它不提供公共终结点，并且数据流量仍在客户拥有的 VNet 中。 默认情况下，不会对传输中数据进行加密。 但是，从 Azure VM（运行 NFS 或 SMB 客户端）到 Azure NetApp 文件的数据流量与任何其他 Azure VM 到 VM 流量一样安全。 

NFSv3 协议不支持加密，因此无法对此传输中数据进行加密。 不过，也可以选择启用 NFSv4.1 和 SMB3 传输中数据加密。 可以使用 Kerberos AES-256 加密对 NFSv4.1 客户端和 Azure NetApp 文件卷之间的数据流量进行加密。 有关详细信息，请参阅[为 Azure NetApp 文件配置 NFSv4.1 Kerberos 加密](configure-kerberos-encryption.md)。 SMB3 客户端和 Azure NetApp 文件卷之间的数据流量可使用 SMB 3.0 的 AES-CCM 算法和 SMB 3.1.1 连接的 AES-GCM 算法进行加密。 有关详细信息，请参阅[创建用于 Azure NetApp 文件的 SMB 卷](azure-netapp-files-create-volumes-smb.md)。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>能否对存储进行静态加密？

所有 Azure NetApp 文件卷都使用 FIPS 140-2 标准进行加密。 所有密钥均由 Azure NetApp 文件服务托管。 

### <a name="is-azure-netapp-files-cross-region-replication-traffic-encrypted"></a>Azure NetApp 文件跨区域复制流量是加密的吗？

Azure NetApp 文件跨区域复制使用 TLS 1.2 AES-256 GCM 加密来加密源卷和目标卷之间传输的所有数据。 这种加密是对所有 Azure 流量（包括 Azure NetApp 文件跨区域复制）默认启用的 [Azure MACSec 加密](../security/fundamentals/encryption-overview.md)的补充。 

### <a name="how-are-encryption-keys-managed"></a>如何托管加密密钥？ 

Azure NetApp 文件的密钥管理由服务处理。 为每个卷生成唯一的 XTS-AES-256 数据加密密钥。 加密密钥层次结构用于加密和保护所有卷密钥。 这些加密密钥永远不会以未加密的格式显示或报告。 删除卷时，会立即删除加密密钥。

在美国东部、美国中南部、美国西部 2 和 US Gov 弗吉尼亚州地区，可以在受控的基础上提供对使用 Azure 专用 HSM 的客户管理的密钥（创建自己的密钥）的支持。 可以通过 [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) 请求访问权限。 容量可用时，请求将获得批准。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>能否配置 NFS 导出策略规则来控制对 Azure NetApp 文件服务装载目标的访问？

能，你可以在单个 NFS 导出策略中配置最多五个规则。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp 文件是否支持网络安全组？

不支持，当前无法将网络安全组应用于 Azure NetApp 文件的委托子网或服务创建的网络接口。

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>能否在 Azure NetApp 文件中使用 Azure RBAC？

能，Azure NetApp 文件支持 Azure RBAC 功能。 除了使用内置的 Azure 角色以外，还可为 Azure NetApp 文件[创建自定义角色](../role-based-access-control/custom-roles.md)。 

有关 Azure NetApp 文件权限的完整列表，请参阅针对 [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp) 的 Azure 资源提供程序操作。

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Azure NetApp 文件是否支持 Azure 活动日志？

Azure NetApp 文件是 Azure 原生的服务。 将会记录针对 Azure NetApp 文件的所有 PUT、POST 和 DELETE API 操作。 例如，日志中会显示谁创建了快照、谁修改了卷等活动。

有关 API 操作的完整列表，请参阅 [Azure NetApp 文件 REST API](/rest/api/netapp/)。

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>是否可以在 Azure NetApp 文件中使用 Azure 策略？

是的，可以创建[自定义 Azure 策略](../governance/policy/tutorials/create-custom-policy-definition.md)。 

但是，不能在 Azure NetApp 文件界面上创建 Azure 策略（自定义命名策略）。 请参阅 [Azure NetApp 文件网络规划指导原则](azure-netapp-files-network-topologies.md#considerations)。

### <a name="when-i-delete-an-azure-netapp-files-volume-is-the-data-deleted-safely"></a>删除 Azure NetApp 文件卷时，是否会安全地删除数据？ 

Azure NetApp 文件卷的删除操作以编程方式执行，并会立即生效。 删除操作包括删除用于加密静态数据的密钥。 成功执行删除操作（通过 Azure 门户 和 API 等接口）后，任何情形下都无法恢复已删除的卷。

## <a name="performance-faqs"></a>性能常见问题解答

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>我应该如何优化或调整 Azure NetApp 文件性能？

你可以根据性能要求执行以下操作： 
- 请确保虚拟机大小合适。
- 为 VM 启用加速网络。
- 为容量池选择所需的服务级别和大小。
- 创建具有所需容量配额大小和性能的卷。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>如何将 Azure NetApp 文件的基于吞吐量的服务级别转换为 IOPS？

可以使用以下公式将 MB/秒转换为 IOPS：  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>如何更改卷的服务级别？

可以通过将现有卷移到使用所需卷[服务级别](azure-netapp-files-service-levels.md)的另一个容量池中，来更改该卷的服务级别。 请参阅[动态更改卷的服务级别](dynamic-change-volume-service-level.md)。 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>如何监视 Azure NetApp 文件性能？

Azure NetApp 文件提供卷性能指标。 你还可以使用 Azure Monitor 来监视 Azure NetApp 文件的使用指标。  有关 Azure NetApp 文件的性能指标列表，请参阅 [Azure Netapp 文件的指标](azure-netapp-files-metrics.md)。

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>对 NFSv4.1 上的 Kerberos 有哪些性能影响？

有关 NFSv4.1 的安全选项、测试的性能向量以及预期性能影响的信息，请参阅[对 NFSv4.1 卷上的 Kerberos 的性能影响](performance-impact-kerberos.md)。 

### <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp 文件是否支持 SMB Direct？

不支持，Azure NetApp 文件不支持 SMB Direct。 

### <a name="is-nic-teaming-supported-in-azure"></a>Azure 中是否支持 NIC 组合？

Azure 中不支持 NIC 组合。 虽然 Azure 虚拟机支持多个网络接口，但它们表示的是一种逻辑构造，而不是物理构造。 因此 Azure 虚拟机不提供容错功能。  此外，Azure 虚拟机的可用带宽是针对虚拟机本身计算的，而不是针对任何单个网络接口计算。

### <a name="are-jumbo-frames-supported"></a>是否支持 jumbo 帧？

Azure 虚拟机不支持 jumbo 帧。

## <a name="nfs-faqs"></a>NFS 常见问题解答

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>我希望在启动或重新启动 Azure VM 时自动装载卷。  如何针对永久 NFS 卷配置我的主机？

若要使 NFS 卷在 VM 启动或重新启动时自动装载，请在主机上的 `/etc/fstab` 文件中添加一个条目。 

有关详细信息，请参阅[为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。  

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp 文件支持哪个 NFS 版本？

Azure NetApp 文件支持 NFSv3 和 NFSv4.1。 可以使用任一 NFS 版本[创建卷](azure-netapp-files-create-volumes.md)。 

### <a name="how-do-i-enable-root-squashing"></a>如何启用根压缩？

你可以使用卷的导出策略指定根帐户是否可以访问该卷。 有关详细信息，请参阅[为 NFS 卷配置导出策略](azure-netapp-files-configure-export-policy.md)。

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>是否可以将同一文件路径（卷创建令牌）用于多个卷？

可以。 但是，文件路径必须在不同的订阅或不同的区域中使用。   

例如，创建一个名为 `vol1` 的卷。 然后，在不同的容量池中但在相同的订阅和区域中创建另一个也称为 `vol1` 的卷。 在这种情况下，使用相同的卷名称 `vol1` 将导致错误。 若要使用相同的文件路径，名称必须位于不同的区域或订阅中。

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>尝试通过 Windows 客户端访问 NFS 卷时，为什么客户端需要很长时间来搜索文件夹和子文件夹？

确保在 Windows 客户端上启用 `CaseSensitiveLookup` 以加快文件夹和子文件夹的查找：

1. 使用以下 PowerShell 命令启用 CaseSensitiveLookup：   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. 在 Windows Server 上装载卷。   
    示例：   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Azure NetApp 文件如何支持 NFSv4.1 文件锁定？ 

对于 NFSv4.1 客户端，Azure NetApp 文件支持 NFSv4.1 文件锁定机制，该机制在基于租用的模型下维护所有文件锁定的状态。 

根据 RFC 3530，Azure NetApp 文件为 NFS 客户端拥有的所有状态定义了一个租用期限。 如果客户端未在定义的期限内续订其租用时间，则服务器将释放与客户端的租用关联的所有状态。  

例如，如果装载卷的客户端在超时后无响应或崩溃，则将释放锁定。 客户端可以通过执行诸如读取文件之类的操作来显式或隐式地续订其租用时间。   

宽限期定义了特殊处理的期限，在此期间，客户端可以尝试在服务器恢复期间重新获得其锁定状态。 租用的默认超时为 30 秒，宽限期为 45 秒。 在此之后，将释放客户端的租用。   

## <a name="smb-faqs"></a>SMB 常见问题解答

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Azure NetApp 文件支持哪些 SMB 版本？

Azure NetApp 文件支持 SMB 2.1 和 SMB 3.1（其中包括对 SMB 3.0 的支持）。    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 访问是否需要 Active Directory 连接？ 

需要，必须在部署 SMB 卷之前创建 Active Directory 连接。 若要成功连接，Azure NetApp 文件的委托子网必须可以访问指定的域控制器。  有关详细信息，请参阅[创建 SMB 卷](./azure-netapp-files-create-volumes-smb.md)。 

### <a name="how-many-active-directory-connections-are-supported"></a>支持多少个 Active Directory 连接？

对于每个订阅和每个区域，只能配置一个 Active Directory (AD) 连接。 有关更多信息，请参阅 [Active Directory 连接的要求](create-active-directory-connections.md#requirements-for-active-directory-connections)。 

但是，可以将同一订阅和同一区域下的多个 NetApp 帐户映射到其中一个 NetApp 帐户中创建的通用 AD 服务器。 请参阅[将同一订阅和区域中的多个 NetApp 帐户映射到 AD 连接](create-active-directory-connections.md#shared_ad)。 

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp 文件是否支持 Azure Active Directory？ 

[Azure Active Directory (AD) 域服务](../active-directory-domain-services/overview.md)和 [Active Directory 域服务 (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 均受支持。 可以将现有 Active Directory 域控制器与 Azure NetApp 文件一起使用。 域控制器可以作为虚拟机驻留在 Azure 中，也可以通过 ExpressRoute 或 S2S VPN 驻留在本地。 目前，Azure NetApp 文件不支持 [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) 的 AD 联接。

如果将 Azure NetApp 文件与 Azure Active Directory 域服务一起使用，则在为 NetApp 帐户配置 Active Directory 时，组织单位路径为 `OU=AADDC Computers`。

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>支持哪些版本的 Windows Server Active Directory？

Azure NetApp 文件支持 Windows Server 2008r2SP1-2019 版本的 Active Directory 域服务。

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>连接到我的 SMB 共享时出现问题。 应采取何种操作？

最佳做法是将计算机时钟同步的最大容差设置为 5 分钟。 有关详细信息，请参阅[计算机时钟同步的最大容差](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11))。 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>是否可以通过计算机管理控制台 (MMC) 管理 `SMB Shares`、`Sessions` 和 `Open Files`？

目前不支持通过计算机管理控制台 (MMC) 管理 `SMB Shares`、`Sessions` 和 `Open Files`。

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>如何通过门户获取 SMB 卷的 IP 地址？

使用卷概述窗格中的“JSON 视图”链接，并查看“properties” -> “mountTargets”下的“startIp”标识符   。

### <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Azure NetApp 文件 SMB 共享是否可以充当 DFS 命名空间 (DFS-N) 根？

否。 但是，Azure NetApp 文件 SMB 共享可以充当 DFS 命名空间 (DFS-N) 文件夹目标。   
若要使用 Azure NetApp 文件 SMB 共享作为 DFS-N 文件夹目标，请使用 [DFS 添加文件夹目标](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target)过程提供 Azure NETAPP 文件 SMB 共享的通用命名约定 (UNC) 装载路径。  

### <a name="can-the-smb-share-permissions-be-changed"></a>是否可以更改 SMB 共享权限？   

不行，无法更改共享权限。 但是，可以使用 [NTFS 文件和文件夹权限](azure-netapp-files-create-volumes-smb.md#ntfs-file-and-folder-permissions)过程更改 `root` 卷的 NTFS 权限。 

## <a name="capacity-management-faqs"></a>容量管理常见问题解答

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>如何监视 Azure NetApp 文件的容量池和卷的使用情况？ 

Azure NetApp 文件提供容量池和卷的使用指标。 你还可以使用 Azure Monitor 来监视 Azure NetApp 文件的使用情况。 有关详细信息，请参阅 [Azure NetApp 文件的指标](azure-netapp-files-metrics.md)。 

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>如何确定目录是否即将达到限制大小？

可以从客户端使用 `stat` 命令来查看目录是否即将达到目录元数据的[最大大小限制](azure-netapp-files-resource-limits.md#resource-limits) (320 MB)。
请参阅 [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md#directory-limit)，了解限制和计算。 

<!-- 
You can use the `stat` command from a client to see whether a directory is approaching the maximum size limit for directory metadata (320 MB).   

For a 320-MB directory, the number of blocks is 655360, with each block size being 512 bytes.  (That is, 320x1024x1024/512.)  This number translates to approximately 4 million files maximum for a 320-MB directory. However, the actual number of maximum files might be lower, depending on factors such as the number of files containing non-ASCII characters in the directory. As such, you should use the `stat` command as follows to determine whether your directory is approaching its limit.  

Examples:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```
--> 

### <a name="does-snapshot-space-count-towards-the-usable--provisioned-capacity-of-a-volume"></a>快照空间是否计入卷的可用/预配容量？

是，[已用快照容量](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)将计入卷中预配的空间。 如果卷已满，请考虑采取以下措施：

* [调整卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)。
* [删除较旧的快照](azure-netapp-files-manage-snapshots.md#delete-snapshots)，以释放宿主卷中的空间。 

### <a name="does-azure-netapp-files-support-auto-grow-for-volumes-or-capacity-pools"></a>Azure NetApp 文件支持卷或容量池的自动增长吗？

不支持， Azure NetApp 文件的卷和容量池在填满后不会自动增长。 请参阅 [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)。   

你可以使用社区支持的[逻辑应用 ANFCapacityManager 工具](https://github.com/ANFTechTeam/ANFCapacityManager)来管理基于容量的预警规则。 该工具可以自动增加卷大小，以防止卷用尽空间。

### <a name="does-the-destination-volume-of-a-replication-count-towards-hard-volume-quota"></a>复制的目标卷是否会计入硬卷配额？  

不会，复制的目标卷不会计入硬卷配额。

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>能否通过 Azure 存储资源管理器管理 Azure NetApp 文件？

不是。 Azure 存储资源管理器不支持 Azure NetApp 文件。

## <a name="data-migration-and-protection-faqs"></a>数据迁移和保护常见问题解答

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>如何将数据迁移到 Azure NetApp 文件？
Azure NetApp 文件提供 NFS 和 SMB 卷。  你可以使用任何基于文件的复制工具将数据迁移到服务。 

NetApp 提供基于 SaaS 的解决方案，即 [NetApp 云同步](https://cloud.netapp.com/cloud-sync-service)。通过该解决方案，你能够将 NFS 或 SMB 数据复制到 Azure NetApp 文件 NFS 导出或 SMB 共享。 

你还可以使用各种各样的免费工具来复制数据。 对于 NFS，你可以使用工作负载工具（如 [rsync](https://rsync.samba.org/examples.html)）将源数据复制和同步到 Azure NetApp 文件卷中。 对于 SMB，你可以采用相同的方式使用工作负载 [robocopy](/windows-server/administration/windows-commands/robocopy)。  这些工具还可以复制文件或文件夹权限。 

从本地到 Azure NetApp 文件的数据迁移要求如下所示： 

- 确保 Azure NetApp 文件在目标 Azure 区域中可用。
- 验证源和 Azure NetApp 文件目标卷 IP 地址之间的网络连接。 通过 ExpressRoute 支持本地和 Azure NetApp 文件服务之间的数据传输。
- 创建目标 Azure NetApp 文件卷。
- 使用首选的文件复制工具将源数据传输到目标卷。

### <a name="where-does-azure-netapp-files-store-customer-data"></a>Azure NetApp 文件将客户数据存储在何处？   

默认情况下，你的数据保留在部署 Azure NetApp 文件卷的区域内。 不过，你可选择使用[跨区域复制](cross-region-replication-introduction.md)将数据逐卷复制到可用目标区域。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>如何在另一个 Azure 区域中创建 Azure NetApp 文件卷的副本？
    
Azure NetApp 文件提供 NFS 和 SMB 卷。  可以使用任何基于文件的复制工具在 Azure 区域之间复制数据。 

通过[跨区域复制](cross-region-replication-introduction.md)功能，你可以以异步方式将数据从一个区域中的 Azure NetApp 文件卷（源）复制到另一个区域中的 Azure NetApp 文件卷（目标）。  此外，还可以[使用现有卷的快照创建新卷](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume)。

NetApp 提供基于 SaaS 的解决方案，即 [NetApp 云同步](https://cloud.netapp.com/cloud-sync-service)。通过该解决方案，你能够将 NFS 或 SMB 数据复制到 Azure NetApp 文件 NFS 导出或 SMB 共享。 

你还可以使用各种各样的免费工具来复制数据。 对于 NFS，你可以使用工作负载工具（如 [rsync](https://rsync.samba.org/examples.html)）将源数据复制和同步到 Azure NetApp 文件卷中。 对于 SMB，你可以采用相同的方式使用工作负载 [robocopy](/windows-server/administration/windows-commands/robocopy)。  这些工具还可以复制文件或文件夹权限。 

将 Azure NetApp 文件卷复制到其他 Azure 区域的要求如下所示： 
- 确保 Azure NetApp 文件在目标 Azure 区域中可用。
- 验证每个区域中 VNet 之间的网络连接。 目前，不支持 VNet 之间的全局对等互连。  可以通过与 ExpressRoute 线路链接或使用 S2S VPN 连接来建立 VNet 之间的连接。 
- 创建目标 Azure NetApp 文件卷。
- 使用首选的文件复制工具将源数据传输到目标卷。

### <a name="is-migration-with-azure-data-box-supported"></a>是否支持使用 Azure Data Box 进行迁移？

不是。 Azure Data Box 目前不支持 Azure NetApp 文件。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>是否支持通过 Azure 导入/导出服务进行迁移？

不是。 Azure 导入/导出服务目前不支持 Azure NetApp 文件。

## <a name="product-faqs"></a>产品常见问题解答

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>能否将 Azure NetApp 文件 NFS 或 SMB 卷与 Azure VMware 解决方案 (AVS) 一起使用？

可以在 AVS Windows VM 或 Linux VM 上装载 Azure NetApp 文件 NFS 卷。 可以在 AVS Windows VM 上映射 Azure NetApp 文件 SMB 共享。 有关更多详细信息，请参阅[采用 Azure VMware 解决方案的 Azure NetApp 文件]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md)。  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>哪些区域支持将 Azure NetApp 文件 NFS 或 SMB 卷与 Azure VMware 解决方案 (AVS) 一起使用？

[所有启用 AVS 和 ANF 的区域](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware,netapp)都支持将 Azure NetApp 文件 NFS 或 SMB 卷用于 AVS 以进行来宾 OS 装载。

### <a name="does-azure-netapp-files-work-with-azure-policy"></a>Azure NetApp 文件是否适用于 Azure Policy？

是的。 Azure NetApp 文件是第一方服务。 其完全遵循 Azure 资源提供程序标准。 因此，可以通过自定义策略定义将 Azure NetApp 文件集成到 Azure Policy 中。 有关如何为 Azure NetApp 文件实现自定义策略的信息，请参阅 Microsoft 技术社区中的 [Azure Netapp 文件现可使用的 Azure Policy](https://techcommunity.microsoft.com/t5/azure/azure-policy-now-available-for-azure-netapp-files/m-p/2282258)。 

### <a name="which-unicode-character-encoding-is-supported-by-azure-netapp-files-for-the-creation-and-display-of-file-and-directory-names"></a>Azure NetApp 文件支持哪种 Unicode 字符编码来创建和显示文件和目录名称？   

Azure NetApp 文件仅支持使用 UTF-8 Unicode 字符编码格式对 NFS 和 SMB 卷进行编码的文件和目录名称。

如果尝试创建的文件或目录的名称使用补充字符或代理项对（如 UTF-8 不支持的非常规字符和表情符号），则操作会失败。 在这种情况下，来自 Windows 客户端的错误可能会显示“指定的文件名无效或太长。 请指定另一文件名。” 

## <a name="next-steps"></a>后续步骤  

- [Microsoft Azure ExpressRoute 常见问题解答](../expressroute/expressroute-faqs.md)
- [Microsoft Azure 虚拟网络常见问题解答](../virtual-network/virtual-networks-faq.md)
- [如何创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [有关 Azure NetApp 文件的 SMB 性能的常见问题解答](azure-netapp-files-smb-performance.md)
