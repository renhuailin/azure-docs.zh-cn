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
ms.date: 08/18/2021
ms.author: b-juche
ms.openlocfilehash: 0bf972cd5b597d4cf0fb608eee8481cb72080425
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769334"
---
# <a name="whats-new-in-azure-netapp-files"></a>Azure NetApp 文件中的新增功能

Azure NetApp 文件会定期更新。 本文总结了最新的新功能和增强功能。 

## <a name="august-2021"></a>2021 年 8 月

* 支持[在现有 SMB 卷上启用连续可用性](enable-continuous-availability-existing-SMB.md)

    已经可以在[新建 SMB 卷](azure-netapp-files-create-volumes-smb.md#continuous-availability)时启用 SMB 持续可用性 (CA) 功能。 现在还可以在现有 SMB 卷上启用 SMB CA。 请参阅[在现有 SMB 卷上启用连续可用性](enable-continuous-availability-existing-SMB.md)。

* [快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)现已正式发布 (GA)  

    快照策略功能现已正式发布。 使用该功能前无需再进行注册。

* [NFS `Chown Mode` 导出策略和 UNIX 导出权限](configure-unix-permissions-change-ownership-mode.md)（预览版）   

    对于具有 Unix 安全样式的 Azure NetApp 文件 NFS 卷或双协议卷，现在可以选择设置“Unix 权限”和“更改所有权模式”(`Chown Mode`) 选项。 可以在创建卷时或创建卷后指定这些设置。   

    使用“更改所有权模式”(`Chown Mode`) 功能，可以设置文件和目录的所有权管理功能。 可以在卷的导出策略下指定或修改该设置。 `Chown Mode` 有两个选项可用：“受限”（默认，其中只有根用户才能更改文件和目录的所有权），以及“非限制”（其中非根用户可以更改其拥有的文件和目录的所有权）。   

    使用 Azure NetApp 文件的“Unix 权限”功能，可以为装载路径指定更改权限。 

    这些新功能提供了一些选项，可用于将某些文件和目录的访问控制移交给数据用户（而不是服务操作员）。   

* [双协议（NFSv4.1 和 SMB）卷](create-volumes-dual-protocol.md)（预览版）   

    从 [2020 年 7 月](#july-2020)起，Azure NetApp 文件已支持对 NFSv3 和 SMB 卷进行双协议访问。 现在可以创建 Azure NetApp 文件卷。该卷允许进行同步双协议（NFSv4.1 和 SMB）访问，并支持 LDAP 用户映射。 此功能支持以下用例：你可能具有使用 NFSv4.1 进行访问的基于 Linux 的工作负载，该工作负载在 Azure NetApp 文件卷中生成和存储数据。 同时，你的员工可能需要使用基于 Windows 的客户端和软件来分析同一 Azure NetApp 文件卷中新生成的数据。 有了同步双协议访问功能，就不必将工作负荷生成的数据复制到使用不同协议的单独卷中进行后期分析，从而节省了存储成本和运营时间。 此功能免费（常规 Azure NetApp 文件存储成本仍然适用），并已正式发布。 有关详细信息，请参阅[同步双协议 NFSv4.1/SMB 访问](create-volumes-dual-protocol.md)文档。

## <a name="june-2021"></a>2021 年 6 月  

* [Azure NetApp 文件存储服务加载项](storage-service-add-ons.md)

    新的 Azure NetApp 文件的“存储服务加载项”菜单选项针对 Azure NetApp 文件存储服务提供了一个 Azure 门户启动板，可启动可用的第三方生态系统加载项。 使用这个新的门户菜单选项，您可以通过单击加载项磁贴来进入登陆页，以便快速访问该外接程序。  

    NetApp 加载项是存储服务加载项下面引入的第一类加载项。 它提供了对 NetApp 云合规性的访问。 单击“NetApp 云合规性”磁贴将打开一个新浏览器，并将你引导到加载项安装页面。 

* [手动 QoS 容量池](manual-qos-capacity-pool-introduction.md)现已正式发布 (GA)   

    手动 QoS 容量池功能现已正式发布。 使用该功能前无需再进行注册。 

* [支持共享 AD，在每个区域的每个订阅中，可以让多个帐户访问一个 Active Directory](create-active-directory-connections.md#shared_ad)（预览）   

    到目前为止，Azure NetApp 文件在每个区域仅支持一个 Active Directory (AD)，其中只能将一个 NetApp 配置为访问 AD。 新的 **共享 AD** 功能使所有 NetApp 帐户共享一个 AD 连接，该连接是由属于同一订阅和同一区域的某个 NetApp 帐户创建。 例如，使用此功能，同一订阅和区域中的所有 NetApp 帐户均可使用常见 AD 配置来创建 SMB 卷、NFSv 4.1 Kerberos 卷或双协议卷。 使用此功能时，AD 连接将在同一订阅和同一区域下的所有 NetApp 帐户中可见。

## <a name="may-2021"></a>2021 年 5 月 

* Azure NetApp 文件应用程序一致快照工具 [(AzAcSnap)](azacsnap-introduction.md) 现已正式发布。 

    AzAcSnap 是一款命令行工具，你可用它来简化 Linux 环境（例如 SUSE 和 RHEL）中第三方数据库 (SAP HANA) 的数据保护。 有关该工具的最新更改，请参阅 [AzAcSnap 发行说明](azacsnap-release-notes.md)。   

* [支持容量池计费标记](manage-billing-tags.md)   

    Azure NetApp 文件现在支持计费标记，可帮助你在业务单元或其他内部使用者方面交叉引用成本。 计费标记是在容量池级别分配，而不是数据量级别分配，计费标记将显示在客户发票中。

* [ADDS LDAP over TLS](configure-ldap-over-tls.md)（预览） 

    默认情况下，客户端和服务器应用程序之间的 LDAP 通信不会被加密。 这意味着，可以使用网络监视设备或软件来查看 LDAP 客户端与服务器计算机之间的通信。 使用 LDAP 简单绑定时，这一方案在非隔离或共享的 VNet 中可能会出现问题，因为用于将 LDAP 客户端绑定到 LDAP 服务器的凭据（用户名和密码）通过未加密的网络传递。 LDAP over TLS（也称为 LDAPS）是一种协议，该协议使用 TLS 来保护 LDAP 客户端与 LDAP 服务器之间的通信。 Azure NetApp 文件现在通过 LDAP over TLS 来支持 Active Directory 域服务器 (ADDS) 之间的安全通信。 现在，Azure NetApp 文件可以使用 LDAP over TLS 在 Active Directory 集成的 LDAP 服务器之间设置经过身份验证的会话。 可以为 NFS、SMB 和双协议卷启用 LDAP over TLS 功能。 默认情况下，LDAP over TLS 在 Azure NetApp 文件中处于禁用状态。  

* 支持吞吐量[指标](azure-netapp-files-metrics.md)    

    Azure NetApp 文件增加了对以下指标的支持：   
    * 容量池吞吐量指标
        * 分配给卷的池的吞吐量
        * 池消耗的吞吐量
        * 分配给卷的池的吞吐量百分比
        * 池消耗的吞吐量百分比
    * 卷吞吐量指标
        * 卷分配的吞吐量
        * 池消耗的吞吐量
        * 卷消耗的吞吐量百分比

* 支持对复制卷[动态更改服务级别](dynamic-change-volume-service-level.md)   

    Azure NetApp 文件现在支持动态更改复制源卷和目标卷的服务级别。

## <a name="april-2021"></a>2021 年 4 月

* [手动卷和容量池管理](volume-quota-introduction.md)（硬配额） 

    Azure NetApp 文件卷和容量池预配的行为已更改为手动且可控的机制。 卷的存储容量限制为卷的设定大小（配额）。 当卷消耗量达到最大时，卷和基础容量池都不会自动增长。 相反，该卷将收到显示“空间不足”的条件。 不过，可以根据需要[调整容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)。 应主动[监视卷](monitor-volume-capacity.md)和基础容量池的容量。

    这一行为变化是许多用户指示的以下关键请求的结果：

    * 以前，在使用 OS 空间或容量监视工具时，VM 客户端会看到任何给定卷的容量都是精简预配的 (100 TiB)。  这种情况可能会导致客户端或应用程序端的容量显示不准确。 此行为现已更正。  
    * 容量之前的自动增大行为让应用程序所有者无法控制预配的容量池空间（以及关联的成本）。 在可能快速填满“失控进程”和增大预配容量的环境中，这一行为特别讨厌。 此行为已更正。  
    * 用户希望看到并维持卷大小（配额）与性能之间的直接关联。 以前的行为允许（隐式）超额订阅卷（容量），而容量池会自动增大。 因此，在主动设置或重置卷配额之前，用户无法进行直接关联。 此行为现已更正。

    用户已请求直接控制预配的容量。 用户想要控制和平衡存储容量与利用率。 他们还想要控制成本，及其应用程序卷的可用、已用和预配容量与性能的应用程序端和客户端可见性。 借助这一新行为，现在可以完整启用整个容量。

* [SMB 连续可用性 (CA) 共享支持 FSLogix 用户配置文件容器](azure-netapp-files-create-volumes-smb.md#continuous-availability)（预览版）  

    [FSLogix](/fslogix/overview) 是一组可优化、启用和简化非持久性 Windows 计算环境的解决方案。 FSLogix 解决方案适用于公有云和私有云中的虚拟环境。 使用物理设备时，FSLogix 解决方案还可用于创建更多的可移植计算会话。 FSLogix 可用于提供对存储在 SMB 共享网络存储（包括 Azure NetApp 文件）上的持久性用户配置文件容器的动态访问。 为了进一步增强 FSLogix 对存储服务维护事件的复原能力，Azure NetApp 文件通过适用于用户配置文件容器的 [Azure NetApp 文件上的 SMB 连续可用性 (CA) 共享](azure-netapp-files-create-volumes-smb.md#continuous-availability)扩展了对 SMB 透明故障转移的支持。 有关其他信息，请参阅 Azure NetApp 文件 [Azure 虚拟桌面解决方案](azure-netapp-files-solution-architectures.md#windows-virtual-desktop)。  

* [SMB3 协议加密](azure-netapp-files-create-volumes-smb.md#smb3-encryption)（预览版） 

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

* [LDAP 签名](create-active-directory-connections.md#create-an-active-directory-connection)（预览版）   

    Azure NetApp 文件现在支持 LDAP 签名，以在 Azure NetApp 文件服务和用户指定的 Active Directory 域服务域控制器之间进行安全 LDAP 查找。 此功能目前处于预览状态。

* [用于 AD 身份验证的 AES 加密](create-active-directory-connections.md#create-an-active-directory-connection)（预览版）

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

* [动态卷服务级别更改](dynamic-change-volume-service-level.MD)（预览） 

    云保证了 IT 支出的灵活性。 现在，可以通过将现有 Azure NetApp 文件卷移到使用所需卷服务级别的另一个容量池中，来更改该卷的服务级别。 这种针对卷的就地服务级别更改不需要迁移数据， 也不会影响对卷的数据平面访问。 你可以将现有卷更改为使用更高的服务级别（提升性能），或使用更低的服务级别（优化成本）。 此功能免费（常规 [Azure NetApp 文件存储成本](https://azure.microsoft.com/pricing/details/netapp/)仍然适用）。 此功能当前处于预览状态。 你可以按照[动态卷服务级别更改文档](dynamic-change-volume-service-level.md)注册该预览版功能。

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
