---
title: 规划 Azure 文件部署 | Microsoft Docs
description: 了解规划 Azure 文件部署。 可以直接装载 Azure 文件共享，也可以使用 Azure 文件同步在本地缓存 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 1d3688e4051f2883c5316a13e59f5629481799e2
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864558"
---
# <a name="planning-for-an-azure-files-deployment"></a>规划 Azure 文件部署
[Azure 文件存储](storage-files-introduction.md)的部署主要有两种方式：直接装载无服务器 Azure 文件共享，或使用 Azure 文件同步功能在本地缓存 Azure 文件共享。所选择的部署方式决定了规划部署时需要考虑的事项。 

- 直接装载 Azure 文件共享：由于 Azure 文件存储提供服务器消息块 (SMB) 或网络文件系统 (NFS) 访问，因此你可以使用 OS 中提供的标准 SMB 或 NFS（预览版）客户端在本地或云中装载 Azure 文件共享。 由于 Azure 文件共享是无服务器的，因此针对生产方案进行部署不需要管理文件服务器或 NAS 设备。 这意味着无需应用软件修补程序或换出物理磁盘。 

- **使用 Azure 文件同步在本地缓存 Azure 文件共享**：借助 Azure 文件同步，可以在 Azure 文件存储中集中管理组织的文件共享，同时又能保留本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将本地（或云中的）Windows Server 转换为 Azure SMB 文件共享的快速缓存。 

本文主要阐述有关部署可供本地或云客户端直接装载的 Azure 文件共享时的部署注意事项。 若要规划 Azure 文件同步部署，请参阅[规划 Azure 文件同步部署](../file-sync/file-sync-planning.md)。

## <a name="available-protocols"></a>可用的协议
Azure 文件存储提供两种用于装载 Azure 文件共享的行业标准协议：[服务器消息块 (SMB)](files-smb-protocol.md) 协议和[网络文件系统 (NFS)](files-nfs-protocol.md) 协议。 Azure 文件存储可让你选择最适合你工作负载的文件系统协议。 尽管可以在同一存储帐户中创建 SMB 和 NFS Azure 文件共享，但 Azure 文件共享不支持在同一个文件共享中同时使用 SMB 和 NFS 协议。 目前只有新的 FileStorage 存储帐户类型中支持 NFS 4.1（仅限高级文件共享）。

对于 SMB 和 NFS 文件共享，Azure 文件存储提供企业级文件共享，这些共享可以纵向扩展以满足你的存储需求，并且可同时由数千个客户端访问。

| 功能 | SMB | NFS（预览版） |
|---------|-----|---------------|
| 支持的协议版本 | SMB 3.1.1、SMB 3.0、SMB 2.1 | NFS 4.1 |
| 推荐的操作系统 | <ul><li>Windows 10 版本 21H1+</li><li>Windows Server 2019+</li><li>Linux 内核版本 5.3+</li></ul> | Linux 内核版本 4.3+ |
| [可用层](storage-files-planning.md#storage-tiers)  | 高级、事务优化、热和冷 | 高级 |
| 计费模式 | <ul><li>[高级文件共享的预配容量](./understanding-billing.md#provisioned-model)</li><li>[标准文件共享的即用即付计费模式](./understanding-billing.md#pay-as-you-go-model)</li></ul> | [预配的容量](./understanding-billing.md#provisioned-model) |
| [冗余](storage-files-planning.md#redundancy) | LRS、ZRS、GRS、GZRS | LRS、ZRS |
| 文件系统语义 | Win32 | POSIX |
| 身份验证 | 基于标识的身份验证 (Kerberos)、共享密钥身份验证 (NTLMv2) | 基于主机的身份验证 |
| 授权 | Win32 样式访问控制列表 (ACL) | UNIX 样式权限 |
| 事例敏感性 | 不区分大小写，但保留大小写 | 区分大小写 |
| 删除或修改打开的文件 | 仅使用锁定 | 是 |
| 文件共享 | [Windows 共享模式](/windows/win32/fileio/creating-and-opening-files) | 字节范围的公告网络锁管理器 |
| 硬链接支持 | 不支持 | 支持 |
| 符号链接支持 | 不支持 | 支持 |
| （可选）可通过 Internet 访问 | 是（仅限 SMB 3.0+） | 否 |
| 支持 FileREST | 是 | 子网： <br /><ul><li>[针对 `FileService` 的操作](/rest/api/storageservices/operations-on-the-account--file-service-)</li><li>[针对 `FileShares` 的操作](/rest/api/storageservices/operations-on-shares--file-service-)</li><li>[针对 `Directories` 的操作](/rest/api/storageservices/operations-on-directories)</li><li>[针对 `Files` 的操作](/rest/api/storageservices/operations-on-files)</li></ul> |

## <a name="management-concepts"></a>管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

将 Azure 文件共享部署到存储帐户时，我们建议：

- 仅将 Azure 文件共享部署到已包含其他 Azure 文件共享的存储帐户。 尽管 GPv2 存储帐户允许使用混合用途的存储帐户，但由于 Azure 文件共享和 Blob 容器等存储资源共享存储帐户的限制，因此，将资源混合在一起可能会导致将来更难以排查性能问题。 

- 部署 Azure 文件共享时请注意存储帐户的 IOPS 限制。 理想情况下，应该以 1:1 的形式将文件共享与存储帐户相映射，但由于组织和 Azure 施加的各种限制和制约，不一定总能实现这种映射。 无法在一个存储帐户中部署一个文件共享时，请考虑哪些共享高度活跃，哪些共享不够活跃，以确保不会将访问量最大的文件共享一起放到同一存储帐户中。

- 仅部署 GPv2 帐户和 FileStorage 帐户，在环境中找到 GPv1 帐户和经典存储帐户时对其进行升级。 

## <a name="identity"></a>标识
若要访问某个 Azure 文件共享，该文件共享的用户必须完成身份验证，并已获得授权。 这种授权是根据访问文件共享的用户的标识完成的。 Azure 文件存储与三个主要标识提供者集成：
- **本地 Active Directory 域服务（AD DS 或本地 AD DS）** ：Azure 存储帐户可以是已加入客户拥有的 Active Directory 域服务的域，例如 Windows Server 文件服务器或 NAS 设备。 你可以在本地、在 Azure VM 中部署域控制器，甚至可以部署为其他云服务提供商中的 VM；Azure 文件存储与域控制器的托管位置无关。 存储帐户加入域后，最终用户可以使用其登陆电脑的用户帐户装载文件共享。 基于 AD 的身份验证使用 Kerberos 身份验证协议。
- **Azure Active Directory 域服务 (Azure AD DS)** ：Azure AD DS 提供了可用于 Azure 资源的 Microsoft 托管域控制器。 将你的存储帐户加入 Azure AD DS 的域，可获得加入客户拥有的 Active Directory 的域类似的好处。 此部署选项最适用于需要基于 AD 的权限的应用程序直接迁移方案。 由于 Azure AD DS 提供了基于 AD 的身份验证，因此选项还使用 Kerberos 身份验证协议。
- **Azure 存储帐户密钥**：还可以使用 Azure 存储帐户密钥装载 Azure 文件共享。 若要以这种方式装载文件共享，需使用存储帐户名称作为用户名，使用存储帐户密钥作为密码。 使用存储帐户密钥装载 Azure 文件共享实际上是一项管理员操作，因为装载的文件共享对其上的所有文件和文件夹拥有完全权限，即使对这些文件和文件夹应用了 ACL。 使用存储帐户密钥通过 SMB 装载时，将使用 NTLMv2 身份验证协议。

对于从本地文件服务器迁移的客户，或在 Azure 文件存储中创建新的文件共享旨在使其行为方式类似于 Windows 文件服务器或 NAS 设备，建议选择将存储帐户加入到“客户拥有的 Active Directory”域。 若要详细了解如何将存储帐户“域加入”到客户拥有的 Active Directory，请参阅 [Azure 文件存储 Active Directory 概述](storage-files-active-directory-overview.md)。

如果要使用存储帐户密钥来访问 Azure 文件共享，我们建议使用[网络](#networking)部分所述的服务终结点。

## <a name="networking"></a>网络
可在任意位置通过存储帐户的公共终结点访问 Azure 文件共享。 这意味着，已经过身份验证的请求（例如已由用户登录标识授权的请求）可以安全地从 Azure 内部或外部发起。 在许多客户环境中，最初在本地工作站上装载 Azure 文件共享的操作会失败，尽管可以成功地从 Azure VM 装载。 其原因是，许多组织和 Internet 服务提供商 (ISP) 阻止 SMB 用来通信的端口 445。 如需大致了解允许或禁止从端口 445 进行访问的 ISP，请访问 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)。

若要取消阻止对 Azure 文件共享的访问，可以采用两种做法：

- 对组织的本地网络取消阻止端口 445。 在外部，只能使用 Internet 安全协议（例如 SMB 3.x 和 FileREST API）通过公共终结点访问 Azure 文件共享。 这是从本地访问 Azure 文件共享的最简单方法，因为它不需要进行高级网络配置，而只需更改组织的出站端口规则；但是，我们建议删除传统的已弃用版本的 SMB 协议，即 SMB 1.0。 若要了解如何执行此操作，请参阅[保护 Windows/Windows Server](/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3)和[保护 Linux](files-remove-smb1-linux.md)。

- 通过 ExpressRoute 或 VPN 连接访问 Azure 文件共享。 通过网络隧道访问 Azure 文件共享时，可以像装载本地文件共享一样装载 Azure 文件共享，因为 SMB 流量不会通过组织边界。   

尽管从技术角度讲，通过公共终结点装载 Azure 文件共享要容易得多，但我们预期大多数客户会选择通过 ExpressRoute 或 VPN 连接装载其 Azure 文件共享。 可以通过 SMB 和 NFS 共享来装载这些选项。 为此，需要为环境配置以下设置：  

- **使用 ExpressRoute、站点到站点或点到站点 VPN 的网络隧道**：通过隧道连接到虚拟网络后，即使端口 445 已被阻止，也能从本地访问 Azure 文件共享。
- **专用终结点**：专用终结点在虚拟网络的地址空间中为存储帐户指定了一个专用 IP 地址。 这样，无需打开本地网络就可以通过网络隧道连接到 Azure 存储群集拥有的所有 IP 地址范围。 
- **DNS 转发**：配置本地 DNS，以将存储帐户的名称（用于公有云区域的 `storageaccount.file.core.windows.net`）解析为专用终结点的 IP 地址。

> [!Important]  
> Azure 文件存储支持多个网络路由选项。 默认选项“Microsoft 路由”适用于所有 Azure 文件存储配置。 Internet 路由选项不支持 AD 域加入方案或 Azure 文件同步。

若要规划与 Azure 文件共享部署相关的网络，请参阅 [Azure 文件存储网络注意事项](storage-files-networking-overview.md)。

## <a name="encryption"></a>Encryption
Azure 文件存储支持两种不同类型的加密：传输中加密（与装载/访问 Azure 文件共享时使用的加密相关），以及静态加密（与存储在磁盘中的数据的加密方式相关）。 

### <a name="encryption-in-transit"></a>传输中加密

> [!IMPORTANT]
> 本部分介绍 SMB 共享的传输中加密详细信息。 有关通过 NFS 共享进行传输中加密的详细信息，请参阅[安全性和网络](files-nfs-protocol.md#security-and-networking)。

默认情况下，所有 Azure 存储帐户均已启用传输中加密。 即通过 SMB 装载文件共享或通过 FileREST 协议（例如，通过 Azure门户、PowerShell/CLI 或 Azure SDK）访问文件共享时，Azure 文件存储仅允许通过加密或 HTTPS 使用 SMB 3.x 及更高版本建立的连接。 如果启用了传输中加密，则不支持 SMB 3.x 的客户端或支持 SMB 3.x 但不支持 SMB 加密的客户端将无法装载 Azure 文件共享。 要详细了解哪些操作系统支持具有加密功能的 SMB 3.x，请参阅适用于 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和 [Linux](storage-how-to-use-files-linux.md) 的详细文档。 PowerShell、CLI 和 SDK 的所有当前版本均支持 HTTPS。  

可以为 Azure 存储帐户禁用传输中加密。 禁用加密后，Azure 文件存储还将允许没有加密功能的 SMB 2.1、SMB 3.x，以及通过 HTTP 进行的未加密 FileREST API 调用。 禁用传输中加密的主要原因是为了支持必须在更低版本的操作系统（例如，Windows Server 2008 R2 或更低版本的 Linux 发行版）上运行的旧版应用程序。 Azure 文件存储仅允许在与 Azure 文件共享相同的 Azure 区域内建立 SMB 2.1 连接；Azure 文件共享的 Azure 区域之外的 SMB 2.1 客户端（例如，本地或其他 Azure 区域）将无法访问文件共享。

强烈建议启用传输中数据加密。

有关传输中加密的详细信息，请参阅[要求在 Azure 存储中进行安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="encryption-at-rest"></a>静态加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>数据保护
Azure 文件有一种多层方法来确保数据得到备份、恢复以及不受安全威胁。

### <a name="soft-delete"></a>软删除
Azure 文件共享的软删除是一种存储帐户级别设置，使你在意外删除文件共享时对其进行恢复。 已删除的文件共享会过渡到软删除状态，而非被永久擦除。 可配置软删除数据被永久删除前的可恢复时间，并在此保留期内随时取消删除共享。 

我们建议对大多数文件共享启用软删除。 如果你的工作流中共享删除是常见且预期的，那么你可能会决定很短的保留期，或者根本不启用软删除。

有关软删除的详细信息，请参见[防止意外数据删除](./storage-files-prevent-file-share-deletion.md)。

### <a name="backup"></a>备份
可以通过[共享快照](./storage-snapshots-files.md)备份 Azure 文件共享，这些快照是共享的只读时间点副本。 快照是增量的，这意味着它们只包含自上一个快照以来更改的数据量。 每个文件共享最多可以有 200 个快照，并将其保留长达 10 年。 可以通过 PowerShell 或命令行接口 (CLI) 在 Azure 门户中手动获取这些快照，或使用 [Azure 备份](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 快照存储在文件共享中，这意味着如果删除文件共享，快照也将删除。 若要保护快照备份不被意外删除，请确保为共享启用软删除。

[Azure 文件共享的 Azure 备份](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)处理快照的计划和保留。 其祖父-父-子 (GFS) 功能意味着你可以生成每日、每周、每月和每年的快照，每个快照都具有自己不同的保持期。 Azure 备份还会协调启用软删除，并在存储帐户中任何文件共享配置为用于备份时，立即对存储帐户添加删除锁定。 最后，Azure 备份提供某些关键的监视和警报功能，使客户能够获得其备份空间的合并视图。

可以使用 Azure 备份在 Azure 门户中执行项目级和共享级还原。 只需选择还原点（特定快照）、特定文件或目录（如相关），然后选择要还原到的位置（原始或备用）。 备份服务会处理复制快照数据，并在门户中显示还原进度。

有关备份的详细信息，请参阅[关于 Azure 文件共享备份](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="azure-defender-for-azure-files"></a>适用于 Azure 文件存储的 Azure Defender 
适用于 Azure 存储的 Azure Defender（之前为适用于 Azure 存储的高级威胁防护）提供额外的安全智能层，当其检测到你的存储帐户中存在异常活动（例如异常访问尝试）时，将发出警报。 它还会运行恶意软件哈希信誉分析，并对已知的恶意软件发出警报。 你可以通过 Azure 安全中心在订阅或存储帐户级别配置 Azure Defender。 

有关详细信息，请参阅[用于存储的 Azure Defender 简介](../../security-center/defender-for-storage-introduction.md)。

## <a name="storage-tiers"></a>存储层
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

#### <a name="limitations"></a>限制
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>冗余
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>迁移
在很多情况下，你不想要为组织建立全新的文件共享，而是将现有文件共享从本地文件服务器或 NAS 设备迁移到 Azure 文件存储。 为你的场景选择正确的迁移策略和工具对于迁移的成功非常重要。 

[迁移概述文章](storage-files-migration-overview.md)简要介绍了基础知识，并包含一个表格，指引你查看适用于你的方案的迁移指南。

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](../file-sync/file-sync-planning.md)
* [部署 Azure 文件](./storage-how-to-create-file-share.md)
* [部署 Azure 文件同步](../file-sync/file-sync-deployment-guide.md)
* [查看迁移概述文章，了解适用于你的方案的迁移指南](storage-files-migration-overview.md)