---
title: SAP HANA Azure 虚拟机 ANF 配置 | Microsoft Docs
description: 适用于 SAP HANA 的 Azure NetApp 文件存储建议。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp 文件, 快照
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6b0fa5e1af60b65c513fd3fa6250dba2a978879
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965891"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷

Azure NetApp 文件提供本机 NFS 共享，可用于 /hana/shared、/hana/data 和 /hana/log 卷  。 对 /hana/data 和 /hana/log 卷使用基于 ANF 的 NFS 共享需要使用 v4.1 NFS 协议 。 当共享基于 ANF 时，NFS 协议 v3 不支持使用 /hana/data和 /hana/log 卷 。 


> [!IMPORTANT]
> 在 Azure NetApp 文件上实现的 NFS v3 协议不支持用于 /hana/data 和 /hana/log  。 从功能角度来看，对于 /hana/data 和 /hana/log 卷，NFS 4.1 是必需的 。 而对于 /hana/shared 卷，则可以从功能角度使用 NFS v3 或 NFS v4.1 协议。

## <a name="important-considerations"></a>重要注意事项

在考虑将 Azure NetApp 文件用于 SAP Netweaver 和 SAP HANA 时，请注意以下重要注意事项：

- 最小容量池为 4 TiB  
- 最小卷大小为 100 GiB
- Azure NetApp 文件和所有虚拟机（将装载 Azure NetApp 文件卷）必须位于同一 Azure 虚拟网络中或同一区域中的[对等虚拟网络](../../../virtual-network/virtual-network-peering-overview.md)中
- 将虚拟机部署在靠近 Azure NetApp 存储的位置以降低延迟，这一点非常重要。  
- 所选的虚拟网络必须具有一个委派给 Azure NetApp 文件的子网
- 请确保从数据库服务器到 ANF 卷的延迟得到测量并低于 1 毫秒
- Azure NetApp 卷的吞吐量是卷配额和服务级别的函数，如 [Azure NetApp 文件服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量满足 HANA 系统要求
- 请尝试“合并”卷以在更大卷中实现更高性能，例如，在可能时将一个卷用于 /sapmnt、/usr/sap/trans… 。  
- Azure NetApp 文件提供[导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)：你可以对允许的客户端、访问类型（读写、只读等）进行控制。 
- Azure NetApp 文件功能尚没有区域感知性。 当前，Azure NetApp 文件功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。   
- 虚拟机上 sidadm 的用户 ID 和 `sapsys` 的组 ID 必须与 Azure NetApp 文件中的配置相匹配<b></b>。 

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在邻近的地方部署。  

> [!IMPORTANT]
> 如果在虚拟机和 Azure NetApp 配置之间，sidadm 的用户 ID 和 `sapsys` 组 ID 不匹配，VM 上装载的 Azure NetApp 卷上的文件权限会显示为 `nobody`<b></b>。 在[载入新系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)到 Azure NetApp 文件时，请确保指定的 sidadm 的用户 ID 和 `sapsys` 的组 ID<b></b> 准确无误。


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>调整 Azure NetApp 文件上的 HANA 数据库的大小

Azure NetApp 卷的吞吐量是卷大小和服务级别的函数，如 [Azure NetApp 文件服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 

重要的是了解性能与大小的关系，服务的存储终结点存在物理限制。 每个存储终结点将在创建卷时动态注入 [Azure NetApp 文件委托子网](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)，并接收 IP 地址。 Azure NetApp 文件卷可以共享存储终结点，具体取决于可用的容量和部署逻辑

下表表明创建大型“标准”卷来存储备份是有意义的，而创建大于 12 TB 的“超级”卷是没有意义的，因为会超过单个卷的最大物理带宽容量。 

卷和单个 Linux 会话的最大写入吞吐量介于 1.2 与 1.4 GB/秒之间。 如果需要将更大的吞吐量用于 /hana/data，则可以使用 SAP HANA 数据卷分区在数据重新加载期间使 I/O 活动条带化，或者跨位于多个 NFS 共享上的多个 HANA 数据文件使 HANA 保存点条带化。 有关 HANA 数据卷条带化的更多详细信息，请阅读以下文章：

- [HANA 管理员指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [有关 SAP HANA 的博客 - 分区数据卷](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP 说明 #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP 说明 #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| 大小  | 标准吞吐量 | 高级吞吐量 | 超级吞吐量 |
| --- | --- | --- | --- |
| 1 TB | 16 MB/秒 | 64 MB/秒 | 128 MB/秒 |
| 2 TB | 32 MB/秒 | 128 MB/秒 | 256 MB/秒 |
| 4 TB | 64 MB/秒 | 256 MB/秒 | 512 MB/秒 |
| 10 TB | 160 MB/秒 | 640 MB/秒 | 1,280 MB/秒 |
| 15 TB | 240 MB/秒 | 960 MB/秒 | 1,400 MB/秒 |
| 20 TB | 320 MB/秒 | 1,280 MB/秒 | 1,400 MB/秒 |
| 40 TB | 640 MB/秒 | 1,400 MB/秒 | 1,400 MB/秒 |

重要的是需要了解，数据会写入存储后端中的相同 SSD。 创建容量池中的性能配额是为了能够管理环境。
存储 KPI 对于所有 HANA 数据库大小都是等同的。 在几乎所有情况下，这种假设并不能反映现实和客户期望。 HANA 系统的大小不一定意味着小型系统需要较低的存储吞吐量 – 而大型系统需要较高的存储吞吐量。 但一般而言，对于较大的 HANA 数据库实例，我们可以期望更高的吞吐量要求。 由于 SAP 对底层硬件的大小调整规则，此类较大 HANA 实例在实例重新启动后加载数据等任务中也可提供更多的 CPU 资源和更高的并行性。 因此，应根据客户期望和要求采用卷大小。 而不仅仅将单纯的容量要求作为驱动因素。

在 Azure 中为 SAP 设计基础结构时，应了解 SAP 的一些最低存储吞吐量要求（对于生产系统），这可转化为以下最低吞吐量特征：

| 卷类型和 I/O 类型 | SAP 所需的最小 KPI | “高级”服务级别 | “超级”服务级别 |
| --- | --- | --- | --- |
| 日志卷写入 | 250 MB/秒 | 4 TB | 2 TB |
| 数据卷写入 | 250 MB/秒 | 4 TB | 2 TB |
| 数据量读取 | 400 MB/秒 | 6.3 TB | 3.2 TB |

由于所有这三个 KPI 都是必需的，因此需要将 /hana/data 卷的大小调整为更大的容量，以满足最低读取要求。

对于不需要高带宽的 HANA 系统，ANF 卷大小可以较小。 如果 HANA 系统需要更多吞吐量，则可以通过联机重设容量大小来调整卷。 没有为备份卷定义 KPI。 但是，备份卷吞吐量对于执行良好的环境是必不可少的。 日志和数据卷性能必须按照客户期望进行设计。

> [!IMPORTANT]
> 无论在单个 NFS 卷上部署的容量如何，预计吞吐量都将 1.2-1.4 GB/秒的带宽范围（由单个会话中的使用者利用）内稳定下来。 这与 ANF 产品/服务的基础体系结构以及围绕 NFS 的相关 Linux 会话限制有关。 [Azure NetApp 文件的性能基准测试结果](../../../azure-netapp-files/performance-benchmarks-linux.md)一文中记录的性能和吞吐量数针对具有多个客户端 VM 的一个共享 NFS 卷而执行，因此具有多个会话。 这种情况不同于我们在 SAP 中测量的情况。 我们针对 NFS 卷衡量单个 VM 的吞吐量的位置。 托管在 ANF 上。

为了满足 SAP 数据和日志的最低吞吐量要求，并根据 /hana/shared 准则，建议的大小如下所示：

| 数据量(Volume) | 大小<br /> 高级存储层 | 大小<br /> 超级存储层 | 支持的 NFS 协议 |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared scale-up | 最小值（1 TB，1 x RAM）  | 最小值（1 TB，1 x RAM） | v3 或 v4.1 |
| /hana/shared scale-out | 1 x 工作器节点 RAM<br /> 每四个工作器节点  | 1 x 工作器节点 RAM<br /> 每四个工作器节点  | v3 或 v4.1 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | v3 或 v4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 或 v4.1 |

对于所有卷，强烈建议使用 NFS v4.1

备份卷的大小是估算值。 需要根据工作负载和操作过程来定义精确要求。 对于备份，可以将不同 SAP HANA 实例的多个卷整合为一个（或两个）较大卷，这可能会具有较低服务级别的 ANF。

> [!NOTE]
> 本文档中所述 Azure NetApp 文件大小调整建议的目的在于满足 SAP 向其基础架构提供商表达的最低要求。 在实际客户部署和工作负载场景中，这可能还不够。 请将这些建议作为起点，并根据具体工作负载的要求进行调整。  

因此，可以考虑对已为超级磁盘存储列出的 ANF 卷部署类似的吞吐量。 同时请考虑为不同 VM SKU 的卷列出的大小情况，正如超级磁盘表中所做的那样。

> [!TIP]
> 可以动态调整 Azure NetApp 文件卷的大小，而无需 `unmount` 卷、停止虚拟机或停止 SAP HANA。 这具有灵活性，可同时满足应用程序的预期和不可预见的吞吐量需求。

有关如何使用 ANF 中托管的 NFS v4.1 卷部署带备用节点的 SAP HANA 横向扩展配置的文档，已在以下位置发布：[使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上进行带备用节点的 SAP HANA 横向扩展](./sap-hana-scale-out-standby-netapp-files-suse.md)。


## <a name="availability"></a>可用性
ANF 系统更新和升级可在不影响客户环境的情况下进行应用。 定义的 [SLA 为 99.99%](https://azure.microsoft.com/support/legal/sla/netapp/)。


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>卷以及 IP 地址和容量池
对于 ANF，必须了解底层基础结构的构建方式。 容量池只是一个构造，它根据容量池服务级别提供容量和性能预算以及计费单位。 容量池与底层基础结构没有物理关系。 在服务上创建卷时，将创建存储终结点。 将单个 IP 地址分配给此存储终结点，以提供对卷的数据访问。 如果创建多个卷，则所有卷将分布在底层裸机机群中，并绑定到此存储终结点。 ANF 具有一个逻辑，可在已配置存储的卷或/和容量达到内部预定义级别后自动分布客户工作负载。 你可能会注意到这种情况，因为会自动创建具有新 IP 地址的新存储终结点以访问卷。 ANF 服务不提供客户对此分发逻辑的控制。

## <a name="log-volume-and-log-backup-volume"></a>日志卷和日志备份卷
“日志卷” (/hana/log) 用于写入联机重做日志。 因而有打开的文件位于此卷中，对此卷拍摄快照没什么意义。 联机重做日志文件已满或执行重做日志备份后，联机重做日志文件会存档或备份到日志备份卷。 若要提供合理的备份性能，日志备份卷需要良好的吞吐量。 若要优化存储成本，合并多个 HANA 实例的日志备份卷会十分有意义。 这样多个 HANA 实例可使用相同卷，并将其备份写入不同目录。 使用此类合并可以获得更大的吞吐量，因为你需要使卷更大一些。 

这同样适用于将完整 HANA 数据库备份写入的卷。  
 

## <a name="backup"></a>备份
除了流式处理备份以及备份 SAP HANA 数据库的 Azure 备份服务（如[Azure 虚拟机上的 SAP HANA 备份指南](../../../backup/sap-hana-db-about.md)一文中所述）之外，通过 Azure NetApp 文件还可以执行基于存储的快照备份。 

SAP HANA 支持：

- 基于存储的快照备份（从 SAP HANA 1.0 SPS7 开始）
- 适用于多数据库容器 (MDC) HANA 环境的基于存储的快照备份支持（从 SAP HANA 2.0 SPS4 开始）


创建基于存储的快照备份是简单的四步骤过程， 
1. 创建 HANA（内部）数据库快照 - 你或工具需要执行的活动 
1. SAP HANA 将数据写入数据文件以在存储中创建一致的状态 - HANA 在创建 HANA 快照后执行此步骤
1. 在存储中的 /hana/data 卷上创建快照 - 你或工具需要执行的步骤。 无需在 /hana/log 卷上执行快照
1. 删除 HANA（内部）数据库快照并恢复正常操作 - 你或工具需要执行的步骤

> [!WARNING]
> 缺少最后一个步骤或未能执行最后一个步骤会对 SAP HANA 的内存需求产生严重影响，可能会导致 SAP HANA 停止

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![SAP HANA 的 ANF 快照备份](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![SAP HANA 的 ANF 快照备份第 2 部分](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

可以使用各种工具，通过多种方式来管理此快照备份过程。 一个示例是 GitHub [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) 上提供的 python 脚本“ntaphana_azure.py”。这是示例代码，“按原样”提供，没有任何维护或支持。



> [!CAUTION]
> 快照本身不是受保护的备份，因为它与你刚拍摄快照的卷位于相同物理存储中。 必须每天将至少一个快照“保护”到不同位置。 这可以在同一环境、远程 Azure 区域或 Azure Blob 存储中进行。


对于 Commvault 备份产品的用户，第二个选项是 Commvault IntelliSnap V.11.21 和更高版本。 此版本或更高版本的 Commvault 可提供 Azure NetApp 文件支持。 [Commvault IntelliSnap 11.21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) 一文提供了详细信息。


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>使用 Azure blob 存储备份快照
备份到 Azure blob 存储是一种经济高效且快速的方法，可用于保存基于 ANF 的 HANA 数据库存储快照备份。 若要将快照保存到 Azure Blob 存储，AzCopy 工具是首选工具。 下载此工具的最新版本并安装（例如在安装 GitHub 中的 python 脚本的 bin 目录中）。
下载最新 AzCopy 工具：

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

最高级的功能是同步选项。 如果使用同步选项，则 azcopy 会使源目录和目标目录保持同步。 参数 --delete-destination 的使用十分重要。 如果没有此参数，则 azcopy 不会删除目标站点上的文件，目标端的空间利用率会增长。 在 Azure 存储帐户中创建块 Blob 容器。 随后为 blob 容器创建 SAS 密钥，并将快照文件夹同步到 Azure Blob 容器。

例如，如果每日快照应同步到 Azure blob 容器以保护数据。 只应保留一个快照，可以使用以下命令。

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>后续步骤
阅读文章：

- [Azure 虚拟机的 SAP HANA 高可用性](./sap-hana-availability-overview.md)
