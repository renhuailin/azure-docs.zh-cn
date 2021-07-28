---
title: 使用 Azure Site Recovery 保护文件服务器
description: 本文介绍如何使用 Azure Site Recovery 保护文件服务器
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: mvc
ms.openlocfilehash: 5209e715fab422a50e31810b5eb0d370d5fc61cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792518"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>使用 Azure Site Recovery 保护文件服务器 

[Azure Site Recovery](site-recovery-overview.md) 通过在计划内和计划外中断期间使商业应用程序保持启动和运行状态，有助于实施业务连续性和灾难恢复 (BCDR) 策略。 Site Recovery 管理并安排本地计算机和 Azure 虚拟机 (VM) 的灾难恢复。 灾难恢复包括各种工作负荷的复制、故障转移和恢复。

本文介绍如何使用 Site Recovery 保护文件服务器，以及适合各种环境的其他建议。 

- [复制 Azure IaaS 文件服务器计算机](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [使用 Site Recovery 复制本地文件服务器](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>文件服务器体系结构
开放分布式文件共享系统的目标是提供可让一组地理分散的用户进行协作的环境，使他们能够高效地处理文件，并保证实施他们的完整性要求。 支持大量并发用户和大量内容项的典型本地文件服务器生态系统使用分布式文件系统复制 (DFSR) 来计划复制和限制带宽。 

DFSR 使用称为远程差分压缩 (RDC) 的压缩算法，该算法可用于通过带宽有限的网络有效更新文件。 它可以检测文件中数据的插入、删除和重新排列。 在更新文件时，DFSR 只会复制已更改的文件块。 此外还有文件服务器环境，其中会在非高峰时间创建每日备份，可迎合灾难恢复的需求。 不需要实施 DFSR。

下图演示了已实施 DFSR 的文件服务器环境。
        
![DFSR 体系结构](media/site-recovery-file-server/dfsr-architecture.JPG)

在上图中，多台文件服务器称为成员，它们积极跨复制组的文件复制。 即使某个成员脱机，已复制的文件夹中的内容也可供向任一成员发送请求的所有客户端使用。

## <a name="disaster-recovery-recommendations-for-file-servers"></a>文件服务器的灾难恢复建议

* **使用 Site Recovery 复制文件服务器**：可以使用 Site Recovery 将文件服务器复制到 Azure。 无法访问一个或多个本地文件服务器时，可在 Azure 中启动恢复 VM。 然后，只要 Azure 中配置了站点到站点 VPN 连接和 Active Directory，该 VM 就能在本地为客户端发出的请求提供服务。 在已配置 DFSR 的环境或者不带 DFSR 的简单文件服务器环境中可以使用此方法。 

* **将 DFSR 扩展到 Azure IaaS VM**：在已实施 DFSR 的群集化文件服务器环境中，可将本地 DFSR 扩展到 Azure。 然后，可以启用 Azure VM 来执行文件服务器角色。 

    * 处理站点到站点 VPN 连接与 Active Directory 的依赖项并部署 DFSR 之后，如果无法访问一个或多个本地文件服务器，客户端可连接到为请求提供服务的 Azure VM。

    * 如果 VM 包含不受 Site Recovery 支持的配置，可以使用此方法。 经常在文件服务器环境中使用的共享群集磁盘就是这样一个例子。 DFSR 也很适合在变动率中等的低带宽环境中工作。 还需要考虑到一直保持运行某个 Azure VM 所产生的附加成本。 

* 使用 Azure 文件同步来复制文件：如果打算使用云或已使用 Azure VM，则可以使用 Azure 文件同步。Azure 文件同步可以同步完全托管于云中的文件共享，这些文件共享可通过行业标准的[服务器消息块](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) (SMB) 协议加以访问。 然后，Azure 文件共享可由云或者 Windows、Linux 和 macOS 的本地部署同时装载。 

下图可帮助你决定要对文件服务器环境使用哪种策略。

![决策树](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>做出灾难恢复到 Azure 的决策时要考虑的因素

|环境  |建议  |考虑的要点 |
|---------|---------|---------|
|带有或不带 DFSR 的文件服务器环境|   [使用 Site Recovery 进行复制](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery 不支持共享磁盘群集或网络附加存储 (NAS)。 如果环境使用上述任何配置，请相应地使用其他任何方法。 <br> Site Recovery 不支持 SMB 3.0。 仅当已在文件原始位置更新了对文件所做的更改时，复制的 VM 才会合并更改。<br>  Site Recovery 提供了近乎同步的数据复制过程，因此在发生计划外故障转移情况时，可能会丢失数据，并且可能会产生 USN 不匹配问题。
|带有 DFSR 的文件服务器环境     |  [将 DFSR 扩展到 Azure IaaS 虚拟机](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |    DFSR 可在带宽严重不足的环境中正常工作。 此方法要求一直保持运行某个 Azure VM。 需要在规划中考虑该 VM 的成本。         |
|Azure IaaS VM     |     文件同步    |     如果在灾难恢复场景中使用文件同步，则在故障转移期间，需要采取手动操作来确保可在客户端计算机上以透明方式访问文件共享。 文件同步要求在客户端计算机上开放端口 445。     |


### <a name="site-recovery-support"></a>Site Recovery 支持
由于 Site Recovery 复制不区分应用程序，因此这些建议应该也适用于以下场景。

| 源  |到辅助站点  |到 Azure
|---------|---------|---------|
|Azure|  -|是|
|Hyper-V|  是  |是
|VMware  |是|  是
|物理服务器|  是  |是
 

> [!IMPORTANT]
> 在继续使用以下三种方法中的任何一种之前，请确保已处理好这些依赖项。



**站点到站点连接**：必须在本地站点与 Azure 网络之间建立直接连接，以便能够在服务器之间通信。 与用作灾难恢复站点的 Azure 虚拟网络建立安全的站点到站点 VPN 连接。 有关详细信息，请参阅[在本地站点与 Azure 虚拟网络之间建立站点到站点 VPN 连接](../vpn-gateway/tutorial-site-to-site-portal.md)。

**Active Directory**：DFSR 依赖于 Active Directory。 这意味着，具有本地域控制器的 Active Directory 林将扩展到 Azure 中的灾难恢复站点。 即使未使用 DFSR，但如果需要授予/验证目标用户的访问权限，则也要执行这些步骤。 有关详细信息，请参阅[将本地 Active Directory 扩展到 Azure](./site-recovery-active-directory.md)。

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Azure IaaS 虚拟机的灾难恢复建议

若要配置和管理托管在 Azure IaaS VM 上的文件服务器的灾难恢复，可以根据是否要转移到 [Azure 文件](../storage/files/storage-files-introduction.md)，在两个选项之间做出选择。

* [使用文件同步](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [使用 Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>使用文件同步来复制托管在 IaaS 虚拟机上的文件

可以使用 Azure 文件来完全取代或补充传统的本地文件服务器或 NAS 设备。 此外，可以使用文件同步将 Azure 文件共享复制到本地或云中的 Windows Server，在使用位置对数据进行高性能的分布式缓存。 以下步骤说明了其功能与传统文件服务器相同的 Azure VM 的灾难恢复建议：
* 使用 Site Recovery 保护计算机。 遵循[将 Azure VM 复制到另一个 Azure 区域](azure-to-azure-quickstart.md)中的步骤。
* 使用文件同步将充当文件服务器的 VM 中的文件复制到云中。
* 使用 Site Recovery 的[恢复计划](site-recovery-create-recovery-plans.md)功能来添加脚本，以[装载 Azure 文件共享](../storage/files/storage-how-to-use-files-windows.md)并访问虚拟机中的共享。

以下步骤简要说明了如何使用文件同步：

1. [在 Azure 中创建存储帐户](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json)。 如果为存储帐户选择了读取访问异地冗余存储，则发生灾难时，可以从次要区域对数据进行读取访问。 有关详细信息，请参阅[灾难恢复和存储帐户故障转移](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)。
2. [创建文件共享](../storage/files/storage-how-to-create-file-share.md)。
3. 在 Azure 文件服务器上[启动文件同步](../storage/file-sync/file-sync-deployment-guide.md)。
4. 创建一个同步组。 同步组中的终结点保持彼此同步。 同步组中必须至少包含一个表示 Azure 文件共享的云终结点。 此外，同步组必须包含一个表示 Windows Server 上的路径的服务器终结点。
5. 现在，文件会在 Azure 文件共享和本地服务器中保持同步。
6. 本地环境中发生灾难时，可使用[恢复计划](site-recovery-create-recovery-plans.md)执行故障转移。 添加脚本来[装载 Azure 文件共享](../storage/files/storage-how-to-use-files-windows.md)和访问虚拟机中的共享。

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>使用 Site Recovery 复制 IaaS 文件服务器虚拟机

如果本地客户端需要访问 IaaS 文件服务器虚拟机，请执行以下所有步骤。 否则请跳到步骤 3。

1. 在本地站点与 Azure 网络之间建立站点到站点 VPN 连接。
2. 扩展本地 Active Directory。
3. 将 IaaS 文件服务器计算机[设置为灾难恢复到次要区域](azure-to-azure-tutorial-enable-replication.md)。


有关灾难恢复到次要区域的详细信息，请参阅[此文](./azure-to-azure-architecture.md)。


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>使用 Site Recovery 复制本地文件服务器

以下步骤说明 VMware VM 的复制。 有关复制 Hyper-V VM 的步骤，请参阅[此教程](./hyper-v-azure-tutorial.md)。

1. [准备 Azure 资源](tutorial-prepare-azure.md)以复制本地计算机。
2. 在本地站点与 Azure 网络之间建立站点到站点 VPN 连接。 
3. 扩展本地 Active Directory。
4. [准备本地 VMware 服务器](./vmware-azure-tutorial-prepare-on-premises.md)。
5. 将本地 VM [设置为灾难恢复到 Azure](./vmware-azure-tutorial.md)。

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>将 DFSR 扩展到 Azure IaaS 虚拟机

1. 在本地站点与 Azure 网络之间建立站点到站点 VPN 连接。 
2. 扩展本地 Active Directory。
3. 在 Azure 虚拟网络中[创建并预配文件服务器 VM](../virtual-machines/windows/quick-create-portal.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)。
确保将虚拟机添加到已与本地环境建立交叉连接的同一个 Azure 虚拟网络。 
4. 在 Windows Server 上安装并[配置 DFSR](https://techcommunity.microsoft.com/t5/storage-at-microsoft/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of/ba-p/424877)。
5. [实现 DFS 命名空间](/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces)。
6. 实现 DFS 命名空间后，可以通过更新 DFS 命名空间文件夹目标，将共享文件夹从生产环境故障转移到灾难恢复站点。 通过 Active Directory 复制这些 DFS 命名空间更改后，用户会以透明方式连接到相应的文件夹目标。

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>使用文件同步复制本地文件
可以使用文件同步将文件复制到云中。 在发生灾难和本地文件服务器不可用时，可以从云中装载所需的文件位置，并继续为来自客户端计算机的请求提供服务。
将文件同步与 Site Recovery 集成：

* 使用 Site Recovery 保护文件服务器计算机。 遵循[此教程](./vmware-azure-tutorial.md)中的步骤。
* 使用文件同步将充当文件服务器的计算机中的文件复制到云中。
* 使用 Site Recovery 中的恢复计划功能来添加脚本，以便在 Azure 中已故障转移的文件服务器 VM 上装载 Azure 文件共享。

遵循以下步骤来使用文件同步：

1. [在 Azure 中创建存储帐户](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json)。 如果为存储帐户选择了读取访问异地冗余存储（建议），则发生灾难时，可以从次要区域对数据进行读取访问。 有关详细信息，请参阅[灾难恢复和存储帐户故障转移](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)。
2. [创建文件共享](../storage/files/storage-how-to-create-file-share.md)。
3. 在本地文件服务器中[部署文件同步](../storage/file-sync/file-sync-deployment-guide.md)。
4. 创建一个同步组。 同步组中的终结点保持彼此同步。 同步组中必须至少包含一个表示 Azure 文件共享的云终结点。 此外，同步组必须包含一个表示本地 Windows Server 上的路径的服务器终结点。
5. 现在，文件会在 Azure 文件共享和本地服务器中保持同步。
6. 本地环境中发生灾难时，可使用[恢复计划](site-recovery-create-recovery-plans.md)执行故障转移。 添加脚本来装载 Azure 文件共享和访问虚拟机中的共享。

> [!NOTE]
> 确保端口 445 已打开。 Azure 文件使用 SMB 协议。 SMB 通过 TCP 端口 445 通信。 请查看防火墙是否未阻止客户端计算机上的 TCP 端口 445。


## <a name="do-a-test-failover"></a>执行测试故障转移

1. 转到 Azure 门户并选择恢复服务保管库。
2. 选择为文件服务器环境创建的恢复计划。
3. 选择“测试故障转移”  。
4. 选择恢复点和 Azure 虚拟网络，启动测试故障转移过程。
5. 辅助环境启动后，请执行验证。
6. 完成验证后，在恢复计划中单击“清理测试故障转移”，这样即可清理测试故障转移环境。

有关如何执行测试故障转移的详细信息，请参阅[到 Site Recovery 的测试故障转移](site-recovery-test-failover-to-azure.md)。

有关针对 Active Directory 和 DNS 执行测试故障转移的指导，请参阅 [Active Directory 和 DNS 测试故障转移注意事项](site-recovery-active-directory.md)。

## <a name="do-a-failover"></a>执行故障转移

1. 转到 Azure 门户并选择恢复服务保管库。
2. 选择为文件服务器环境创建的恢复计划。
3. 选择“故障转移”。
4. 选择恢复点启动故障转移过程。

有关如何执行故障转移的详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。