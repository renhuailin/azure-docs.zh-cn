---
title: Azure 文件存储与 Azure NetApp 文件的比较 | Microsoft Docs
description: Azure 文件存储与 Azure NetApp 文件的比较。
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 5/25/2021
ms.author: jeffpatt
ms.openlocfilehash: 7cd1b08b572dc000ac3346e493302437eb32ae98
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015433"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Azure 文件存储与 Azure NetApp 文件的比较

本文将 Azure 文件存储与 Azure NetApp 文件进行了比较。 

大多数需要云文件存储的工作负载在 Azure 文件存储或 Azure NetApp 文件上都能正常工作。 为了帮助确定哪种服务最适合你的工作负载，请查看本文中提供的信息。 有关详细信息，请查看 [Azure 文件存储](./index.yml)和 [Azure NetApp 文件](../../azure-netapp-files/index.yml)文档以及[所有企业文件工作负载的共享存储](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s)会话，该会话介绍了如何在 Azure 文件存储和 Azure NetApp 文件之间进行选择。

## <a name="features"></a>功能

| 类别 | Azure 文件 | Azure NetApp 文件 |
|---------|-------------------------|---------|
| 说明 | [Azure 文件存储](https://azure.microsoft.com/services/storage/files/)是一项完全托管的高可用性企业级服务，它通过就地数据更新针对随机访问工作负载进行了优化。<br><br> Azure 文件存储与其他服务（例如 Azure Blob）构建在相同的 Azure 存储平台上。 | [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)是一项完全托管、高度可用的企业级 NAS 服务，它可处理需要高级数据管理功能的要求最严格、高性能和低延迟工作负载。 它可实现工作负载的迁移；如果不使用它，这些工作负载会被视为“不可迁移”。<br><br>  ANF 构建在 NetApp 的裸机之上，有 ONTAP 存储 OS 在 Azure 数据中心内运行来提供一致的 Azure 体验和类似本地的性能。 |
| 协议 | 高级<br><ul><li>SMB 2.1、3.0、3.1.1</li><li>NFS 4.1（预览版）</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2.1、3.0、3.1.1</li><li>REST</li></ul><br> 若要了解详细信息，请查看[可用的文件共享协议](./storage-files-planning.md#available-protocols)。 | 所有层级<br><ul><li>SMB 2.x、3.x</li><li>NFS 3.0、4.1</li><li>双重协议访问 (NFSv3/SMB)</li></ul><br> 若要了解详细信息，请查看如何创建 [NFS](../../azure-netapp-files/azure-netapp-files-create-volumes.md)、[SMB](../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md) 或[双重协议](../../azure-netapp-files/create-volumes-dual-protocol.md)卷。 |
| 区域可用性 | 高级<br><ul><li>超过 30 个区域</li></ul><br>Standard<br><ul><li>所有区域</li></ul><br> 有关详细信息，请参阅[各区域的可用产品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。 | 所有层级<br><ul><li>超过 25 个区域</li></ul><br> 有关详细信息，请参阅[各区域的可用产品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。 |
| 冗余 | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> 若要了解详细信息，请查看[冗余](./storage-files-planning.md#redundancy)。 | 所有层级<br><ul><li>内置本地高可用性 (HA)</li><li>[跨区域复制](../../azure-netapp-files/cross-region-replication-introduction.md)</li></ul> |
| 服务级别协议 (SLA)<br><br> 请注意，Azure 文件存储和 Azure NetApp 文件的 SLA 计算方式不同。 | [Azure 文件存储的 SLA](https://azure.microsoft.com/support/legal/sla/storage/) | [Azure NetApp 文件的 SLA](https://azure.microsoft.com/support/legal/sla/netapp) |  
| 基于标识的身份验证和授权 | SMB<br><ul><li>Active Directory 域服务 (AD DS)</li><li>Azure Active Directory 域服务 (Azure AD DS)</li></ul><br> 请注意，仅在使用 SMB 协议时才支持基于标识的身份验证。 若要了解详细信息，请查看[常见问题解答](./storage-files-faq.md#security-authentication-and-access-control)。 | SMB<br><ul><li>Active Directory 域服务 (AD DS)</li><li>Azure Active Directory 域服务 (Azure AD DS)</li></ul><br> NFS/SMB 双重协议<ul><li>ADDS/LDAP 集成</li></ul><br>NFSv3/NFSv4.1<ul><li>ADDS/LDAP 集成（即将推出）</li><li>NFS 扩展组（即将推出）</li></ul><br> 若要了解详细信息，请查看[常见问题解答](../../azure-netapp-files/azure-netapp-files-faqs.md)。 |
| 加密 | 所有协议<br><ul><li>采用客户或 Microsoft 管理的密钥的静态加密 (AES 256)</li></ul><br>SMB<br><ul><li>采用 AES 256 或 RC4-HMAC 的 Kerberos 加密</li><li>传输数据的加密</li></ul><br>REST<br><ul><li>传输数据的加密</li></ul><br> 若要了解详细信息，请查看[安全性和网络](files-nfs-protocol.md#security-and-networking)。 | 所有协议<br><ul><li>采用 Microsoft 管理的密钥的静态加密 (AES 256) </li></ul><br>NFS 4.1<ul><li>采用 Kerberos 和 AES 256 的传输中加密</li></ul><br> 若要了解详细信息，请查看[安全性常见问题解答](../../azure-netapp-files/azure-netapp-files-faqs.md#security-faqs)。 |
| 访问选项 | <ul><li>Internet</li><li>安全 VNet 访问</li><li>VPN 网关</li><li>ExpressRoute</li><li>Azure 文件同步</li></ul><br> 若要了解详细信息，请查看[网络注意事项](./storage-files-networking-overview.md)。 | <ul><li>安全 VNet 访问</li><li>VPN 网关</li><li>ExpressRoute</li><li>[全局文件缓存](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC 缓存](../../hpc-cache/hpc-cache-overview.md)</li></ul><br> 若要了解详细信息，请查看[网络注意事项](../../azure-netapp-files/azure-netapp-files-network-topologies.md)。 |
| 数据保护  | <ul><li>增量快照</li><li>文件/目录用户自助还原</li><li>还原到新位置</li><li>就地还原</li><li>共享级软删除</li><li>Azure 备份集成</li></ul><br> 若要了解详细信息，请查看 [Azure 文件存储增强数据保护功能](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/)。 | <ul><li>快照（255/卷）</li><li>文件/目录用户自助还原</li><li>还原为新卷</li><li>就地还原</li><li>[跨区域复制](../../azure-netapp-files/cross-region-replication-introduction.md)（公共预览版）</li></ul><br> 若要了解详细信息，请查看 [Azure NetApp 文件快照的工作原理](../../azure-netapp-files/snapshots-introduction.md)。 |
| 迁移工具  | <ul><li>Azure Data Box</li><li>Azure 文件同步</li><li>存储迁移服务</li><li>AzCopy</li><li>Robocopy</li></ul><br> 若要了解详细信息，请查看[迁移到 Azure 文件共享](./storage-files-migration-overview.md)。 | <ul><li>[全局文件缓存](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service)、[XCP](https://xcp.netapp.com/)</li><li>存储迁移服务</li><li>AzCopy</li><li>Robocopy</li><li>基于应用程序（例如 HSR、Data Guard、AOAG）</li></ul> |
| 层 | <ul><li>高级</li><li>事务优化</li><li>热</li><li>冷</li></ul><br> 若要了解详细信息，请查看[存储层](./storage-files-planning.md#storage-tiers)。 | <ul><li>超高性能</li><li>高级</li><li>Standard</li></ul><br> 所有层级都提供亚毫秒级最低延迟。<br><br> 若要了解详细信息，请查看[服务级别](../../azure-netapp-files/azure-netapp-files-service-levels.md)和[性能注意事项](../../azure-netapp-files/azure-netapp-files-performance-considerations.md)。 |
| 定价 | [Azure 文件存储定价](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp 文件定价](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>可伸缩性和性能

| 类别 | Azure 文件 | Azure NetApp 文件 |
|---------|---------|---------|
| 最小共享/卷大小 | 高级<br><ul><li>100 GiB</li></ul><br>Standard<br><ul><li>无最小值。</li></ul> | 所有层级<br><ul><li>100 GiB（最小容量池大小：4 TiB）</li></ul> |
| 最大共享/卷大小 | 100 TiB | 所有层级<br><ul><li>100 TiB（容量池上限：500 TiB）</li></ul><br>每个 Azure NetApp 帐户最高 12.5 PiB。 |
| 最大共享/卷 IOPS | 高级<br><ul><li>最高 100,000</li></ul><br>Standard<br><ul><li>最高 10,000</li></ul> | 超高和高级<br><ul><li>最高 450,000 </li></ul><br>Standard<br><ul><li>最高 320,000</li></ul> |
| 最大共享/卷吞吐量 | 高级<br><ul><li>最高 10 GiB/秒</li></ul><br>Standard<br><ul><li>最高 300 MiB/秒</li></ul> | 超高和高级<br><ul><li>最高 4.5 GiB/秒</li></ul><br>Standard<br><ul><li>最高 3.2GiB/秒</li></ul> |
| 文件大小上限 | 4 TiB | 16 TiB |
| 每个文件的最大 IOPS | 高级<br><ul><li>最高 8,000</li></ul><br>Standard<br><ul><li>1,000</li></ul> | 所有层级<br><ul><li>不超过卷上限</li></ul> |
| 每个文件的最大吞吐量 | 高级<br><ul><li>300 MiB/秒（使用 SMB 多通道时，最高 1 GiB/秒）</li></ul><br>Standard<br><ul><li>60 MiB/秒</li></ul> | 所有层级<br><ul><li>不超过卷上限</li></ul> |
| SMB 多通道 | 是（[预览版](./storage-files-smb-multichannel-performance.md)） | 是 |
| 延迟 | 个位数毫秒级最低延迟（小型 IO 的为 2-3 毫秒） | 亚毫秒级最低延迟（随机 IO 的小于 1 毫秒）<br><br>若要了解详细信息，请查看[性能基准](../../azure-netapp-files/performance-benchmarks-linux.md)。 |

有关可伸缩性和性能目标的详细信息，请查看 [Azure 文件存储](./storage-files-scale-targets.md#azure-files-scale-targets)和 [Azure NetApp 文件](../../azure-netapp-files/azure-netapp-files-resource-limits.md)。

## <a name="next-steps"></a>后续步骤
* [Azure 文件文档](./index.yml)
* [Azure NetApp 文件文档](../../azure-netapp-files/index.yml)
* [所有企业文件工作负载的共享存储会话](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s)
