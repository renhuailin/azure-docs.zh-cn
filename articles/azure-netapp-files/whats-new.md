---
title: Azure NetApp 文件中的新增功能 | Microsoft Docs
description: 总结 Azure NetApp 文件的最新新功能和增强功能。
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
ms.topic: overview
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: 3c6da2137f2db43284ce7a533ff763e9ef157f35
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726639"
---
# <a name="whats-new-in-azure-netapp-files"></a>Azure NetApp 文件中的新增功能

Azure NetApp 文件会定期更新。 本文总结了最新的新功能和增强功能。 

## <a name="april-2021"></a>2021 年 4 月

* [SMB3 协议加密](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)（预览版） 

    现在可以在 Azure NetApp 文件 SMB 和双协议卷上启用 SMB3 协议加密。 此功能使用 [SMB 3.0 上的 AES-CCM 算法和 SMB 3.1.1 上的 AES-GCM 算法](/windows-server/storage/file-server/file-server-smb-overview#features-added-in-smb-311-with-windows-server-2016-and-windows-10-version-1607)连接，对传送中的 SMB3 数据启用加密。 未使用 SMB3 加密的 SMB 客户端无法访问此卷。 无论此设置如何，静态数据都会加密。 SMB 加密进一步增强了安全性。 但是，它可能会对客户端产生影响（加密和解密消息的 CPU 开销）。 它还可能会影响存储资源利用率（降低吞吐量）。 在将工作负载部署到生产环境之前，应测试对应用程序的加密性能影响。

* [Active Directory 域服务 (ADDS) LDAP 用户映射到 NFS 扩展组](configure-ldap-extended-groups.md)（预览版）   

    默认情况下，Azure NetApp 文件在处理 NFS 用户凭据时最多支持 16 个组 ID，如 [RFC 5531](https://tools.ietf.org/html/rfc5531) 中所定义。 利用这一新功能，如果用户人数超过群组的默认成员数量，则现在可以将最大值增加到 1,024。 若要支持此功能，现在还可以向 ADDS LDAP 添加 NFS 卷，这会使的具有扩展组条目（有多达 1,024 组）的 Active Directory LDAP 用户可以访问该卷。 

## <a name="march-2021"></a>2021 年 3 月
 
* [SMB 连续可用性 (CA) 共享](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)（预览）  

    SMB 透明故障转移可在 Azure NetApp 文件服务上实现维护操作，并且依然保持连接到存储和访问 SMB 卷上的数据的服务器应用程序。 为支持 SMB 透明故障转移，Azure NetApp 文件现在支持 SMB 连续可用性共享选项，适用于在 Azure 虚拟机上运行的基于 SMB 的 SQL Server 应用程序。 目前 Windows SQL Server 支持此功能。 目前 Linux SQL Server 不支持此功能。 启用这一功能可为[单实例、Always-On 故障转移群集实例和 Always-On 可用性组部署](azure-netapp-files-solution-architectures.md#sql-server)带来显著的 SQL Server 性能改进以及规模和成本效益。 请参阅[使用 Azure NetApp 文件进行 SQL Server 部署的好处](solutions-benefits-azure-netapp-files-sql-server.md)。

* [自动调整跨区域复制目标卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    在跨区域复制关系中，目标卷会根据源卷的大小而自动调整大小。 因此，无需单独调整目标卷的大小。 当卷处于活动的复制关系中时，或者当复制对等互连与重新同步操作断开时，这种自动调整大小行为适用。 要使此功能正常工作，需要确保源卷和目标卷的容量池中都有足够的空余空间。

## <a name="december-2020"></a>2020 年 12 月

* [Azure 应用程序一致性快照工具](azacsnap-introduction.md)（预览版）    

    Azure 应用程序一致性快照工具 (AzAcSnap) 是一款命令行工具，你可用它来简化 Linux 环境（例如 SUSE 和 RHEL）中第三方数据库 (SAP HANA) 的数据保护。   

    AzAcSnap 会使用 Azure NetApp 文件和 Azure 大型实例中的卷快照和复制功能。 它提供以下优势：

    * 应用程序一致性数据保护 
    * 数据库目录管理 
    * 即席卷保护 
    * 克隆存储卷 
    * 支持灾难恢复 

## <a name="november-2020"></a>2020 年 11 月

* [快照还原](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    快照还原功能使你能够快速将卷还原到创建特定快照时的状态。 在大多数情况下，还原卷比将单个文件从快照还原到活动文件系统要快得多。 与将快照还原到新卷相比，此方法也更节省空间。

## <a name="september-2020"></a>2020 年 9 月

* [Azure NetApp 文件跨区域复制](cross-region-replication-introduction.md)（预览版）

  Azure NetApp 文件现在支持跨区域复制。 借助这项新的灾难恢复功能，可以快速且经济高效地将 Azure NetApp 文件卷从一个 Azure 区域复制到另一个 Azure 区域，从而保护数据免受不可预见的区域故障的影响。 Azure NetApp 文件跨区域复制采用 NetApp SnapMirror® 技术；只有已更改的块才以压缩、有效的格式通过网络发送。 这项专有技术最大程度地减少了跨区域复制所需的数据量，从而节省了数据传输成本。 它还可以缩短复制时间，让你可以实现较小的还原点目标 (RPO)。

* [手动 QoS 容量池](manual-qos-capacity-pool-introduction.md)（预览版）  

    在手动 QoS 容量池中，可以单独为卷分配容量和吞吐量。 使用手动 QoS 容量池创建的所有卷的总吞吐量受池总吞吐量的限制。 它由池大小和服务级别吞吐量共同决定。 另外，容量池的 [QoS 类型](azure-netapp-files-understand-storage-hierarchy.md#qos_types)也可以是自动，这是默认值。 在自动 QoS 容量池中，吞吐量会自动分配到池中的卷，并与分配给这些卷的大小配额成正比。

* [LDAP 签名](azure-netapp-files-create-volumes-smb.md)（预览版）   

    Azure NetApp 文件现在支持 LDAP 签名，以在 Azure NetApp 文件服务和用户指定的 Active Directory 域服务域控制器之间进行安全 LDAP 查找。 此功能目前处于预览状态。

* [用于 AD 身份验证的 AES 加密](azure-netapp-files-create-volumes-smb.md)（预览版）

    Azure NetApp 文件现在支持对与 DC 的 LDAP 连接进行 AES 加密，以便为 SMB 卷启用 AES 加密。 此功能目前处于预览状态。 

* 新[指标](azure-netapp-files-metrics.md)：   

    * 新卷指标： 
        * *卷分配大小*：卷的预配大小
    * 新池指标： 
        * *池分配大小*：池的预配大小 
        * *池的总快照大小*：池中所有卷的快照大小总和

## <a name="july-2020"></a>2020 年 7 月

* [双协议（NFSv3 和 SMB）卷](create-volumes-dual-protocol.md)

    现在可以创建 Azure NetApp 文件卷。该卷允许进行同步双协议（NFS v3 和 SMB）访问，并支持 LDAP 用户映射。 此功能支持以下用例：你可能具有基于 Linux 的工作负荷，该工作负荷在 Azure NetApp 文件卷中生成和存储数据。 同时，你的员工需要使用基于 Windows 的客户端和软件来分析同一 Azure NetApp 文件卷中新生成的数据。 有了同步双协议访问功能，就不必将工作负荷生成的数据复制到使用不同协议的单独卷中进行后期分析，从而节省了存储成本和运营时间。 此功能免费（常规 [Azure NetApp 文件存储成本](https://azure.microsoft.com/pricing/details/netapp/)仍然适用），并已正式发布。 有关详细信息，请参阅[同步双协议访问文档](create-volumes-dual-protocol.MD)。

* [NFS v4.1 Kerberos 传输中加密](configure-kerberos-encryption.MD)

    Azure NetApp 文件现在支持使用 AES-256 加密算法在 Kerberos 模式（krb5、krb5i 和 krb5p）下加密 NFS 客户端，从而提供额外的数据安全性。 此功能免费（常规 [Azure NetApp 文件存储成本](https://azure.microsoft.com/pricing/details/netapp/)仍然适用），并已正式发布。 有关详细信息，请参阅 [NFS v4.1 Kerberos 加密文档](configure-kerberos-encryption.MD)。

* [动态卷服务级别更改](dynamic-change-volume-service-level.MD)

    云保证了 IT 支出的灵活性。 现在，可以通过将现有 Azure NetApp 文件卷移到使用所需卷服务级别的另一个容量池中，来更改该卷的服务级别。 这种针对卷的就地服务级别更改不需要迁移数据， 也不会影响对卷的数据平面访问。 你可以将现有卷更改为使用更高的服务级别（提升性能），或使用更低的服务级别（优化成本）。 此功能免费（常规 [Azure NetApp 文件存储成本](https://azure.microsoft.com/pricing/details/netapp/)仍然适用），并且目前提供公共预览版。 你可以按照[动态卷服务级别更改文档](dynamic-change-volume-service-level.md)注册该预览版功能。

* [卷快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)（预览版） 

    Azure NetApp 文件可用于创建卷的时间点快照。 现在，你可以创建快照策略，使 Azure NetApp 文件按你选择的频率自动创建卷快照。 你可以安排以每小时、每天、每周或每月为周期拍摄快照。 还可以指定作为快照策略一部分保留的最大快照数。 此功能免费（常规 [Azure NetApp 文件存储成本](https://azure.microsoft.com/pricing/details/netapp/)仍然适用），并且目前提供预览版。 你可以按照[卷快照策略文档](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)注册该预览版功能。

* [NFS 根访问导出策略](azure-netapp-files-configure-export-policy.md)

    Azure NetApp 文件现在允许指定根帐户是否可以访问卷。 

* [隐藏快照路径](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp 文件现在允许指定用户是否可以查看和访问已装载卷上的 `.snapshot` 目录（NFS 客户端）或 `~snapshot` 文件夹（SMB 客户端）。

## <a name="may-2020"></a>2020 年 5 月

* [备份策略用户](create-active-directory-connections.md)（预览版）

    Azure NetApp 文件允许包含其他帐户，这些帐户要求对创建用于 Azure NetApp 文件的计算机帐户具有提升的权限。 将允许指定的帐户在文件或文件夹级别更改 NTFS 权限。 例如，你可以指定一个非特权服务帐户，用于将数据迁移到 Azure NetApp 文件中的 SMB 文件共享。 备份策略用户功能目前提供公共预览版。

## <a name="next-steps"></a>后续步骤
* [什么是 Azure NetApp 文件](azure-netapp-files-introduction.md)
* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md) 
