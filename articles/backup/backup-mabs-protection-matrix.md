---
title: MABS（Azure 备份服务器）V3 UR1 保护矩阵
description: 本文提供了一个支持矩阵，列出了受 Azure 备份服务器保护的所有工作负荷、数据类型和安装。
ms.date: 07/27/2021
ms.topic: conceptual
ms.openlocfilehash: c95f9564a12b430795a1a000a074af8869adce43
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720699"
---
# <a name="mabs-azure-backup-server-v3-ur1-and-later-protection-matrix"></a>MABS（Azure 备份服务器）V3 UR1（及更高版本）的保护矩阵

本文列出可以使用 Azure 备份服务器保护的各种服务器和工作负荷。 以下矩阵列出了可使用 Azure 备份服务器保护的内容。

对于 MABS v3 UR1（及更高版本），请使用以下矩阵：

* 工作负荷 - 技术的工作负荷类型。

* 版本 – 工作负荷支持的 MABS 版本。

* MABS 安装 – 要在其中安装 MABS 的计算机/位置。

* 保护和恢复 - 列出工作负荷的详细信息，如支持的存储容器或支持的部署。

>[!NOTE]
>MABS v3 UR1（及更高版本）不再支持 32 位保护代理。 请参阅[弃用 32 位保护代理](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation)。

## <a name="protection-support-matrix"></a>保护支持矩阵

以下部分详细介绍了 MABS 的保护支持矩阵：

* [应用程序备份](#applications-backup)
* [VM 备份](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>应用程序备份

| **工作负载**               | **版本**                                                  | **Azure 备份服务器安装**                       | **支持的 Azure 备份服务器** | **保护和恢复**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| 客户端计算机（64 位） | Windows 10                                                  | 物理服务器  <br><br>    Hyper-V 虚拟机   <br><br>   VMware 虚拟机 | V3 UR1 和 V3 UR2                            | 卷、共享、文件夹、文件、已删除重复数据的卷   <br><br>   受保护的卷必须采用 NTFS 格式。 不支持 FAT 和 FAT32。  <br><br>    卷必须至少有 1 GB 空间。 Azure 备份服务器使用卷影复制服务 (VSS) 来创建数据快照；仅当卷至少有 1 GB 空间时，快照才能正常工作。 |
| 服务器（64 位）          | Windows Server 2019、2016、2012 R2、2012                    | Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）  <br><br>    物理服务器  <br><br>    Hyper-V 虚拟机 <br><br>     VMware 虚拟机  <br><br>    Azure Stack | V3 UR1 和 V3 UR2                            | 卷、共享、文件夹、文件 <br><br>    已删除重复数据的卷（仅限 NTFS） <br><br>当使用在 Windows Server 2019 上运行的 MABS v3 保护 WS 2016 NTFS 已删除重复数据卷时，恢复可能会受到影响。 我们具有以非去重方式进行恢复的修补程序，该修补程序将成为 MABS 更高版本的一部分。 如果 MABS v3 UR1 上需要此修补程序，请联系 MABS 支持。<br><br> 当使用在 Windows Server 2016 上运行的 MABS v3 保护 WS 2019 NTFS 已删除重复数据卷时，备份和还原都将为非重复数据。 这意味着备份将比原始 NTFS 已去重卷占用 MABS 服务器上更多的空间。   <br><br>   系统状态和裸机（当工作负荷作为 Azure 虚拟机运行时不支持） |
| 服务器（64 位）          | Windows Server 2008 R2 SP1、Windows Server 2008 SP2（需安装 [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)） | 物理服务器  <br><br>    Hyper-V 虚拟机  <br><br>      VMware 虚拟机  <br><br>   Azure Stack | V3 UR1 和 V3 UR2                           | 卷、共享、文件夹、文件、系统状态/裸机        |
| SQL Server                | SQL Server 2019、2017、2016 和[支持的 SP](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016)、2014 和支持的 [SP](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | 物理服务器  <br><br>     Hyper-V 虚拟机   <br><br>     VMware 虚拟机  <br><br>   Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）  <br><br>     Azure Stack | V3 UR1 和 V3 UR2                            | 所有部署方案：数据库       <br><br>    MABS v3 UR2 及更高版本支持备份存储在群集共享卷上的 SQL 数据库。     <br><br>  MABS v3 UR1 支持备份基于 ReFS 卷的 SQL 数据库     <br><br>     MABS 不支持在 Windows Server 2012 横向扩展文件服务器 (SOFS)上托管 SQL Server 数据库。 <br><br>   MABS 无法保护 SQL Server 分布式可用性组 (DAG) 或可用性组 (AG)，其中故障转移群集上的角色名称不同于 SQL 上的命名 AG。       |
| Exchange                   | Exchange 2019、2016                                         | 物理服务器   <br><br>   Hyper-V 虚拟机  <br><br>      VMware 虚拟机  <br><br>   Azure Stack  <br><br>    Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时） | V3 UR1 和 V3 UR2                            | 保护（所有部署方案）：独立 Exchange 服务器、数据库可用性组 (DAG) 下的数据库  <br><br>    恢复（所有部署方案）：邮箱、DAG 下的邮箱数据库    <br><br>  MABS v3 UR1 支持备份基于 ReFS 的 Exchange |
| SharePoint                 | 带最新 SP 的 SharePoint 2019、2016                       | 物理服务器  <br><br>    Hyper-V 虚拟机 <br><br>    VMware 虚拟机  <br><br>   Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）   <br><br>   Azure Stack | V3 UR1 和 V3 UR2                            | 保护（所有部署方案）：场、前端 Web 服务器内容  <br><br>    恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器  <br><br>    对于将 SQL Server 2012 AlwaysOn 功能用于内容数据库的 SharePoint 场，不支持对其进行保护。 |

## <a name="vm-backup"></a>VM 备份

| **工作负载**                                                 | **版本**                                             | **Azure 备份服务器安装**                      | **支持的 Azure 备份服务器** | **保护和恢复**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 MABS 保护代理 | Windows Server 2019、2016、2012 R2、2012               | 物理服务器  <br><br>    Hyper-V 虚拟机 <br><br>    VMware 虚拟机 | V3 UR1 和 V3 UR2                                 | 保护：虚拟机、群集共享卷 (CSV)  <br><br>    恢复：虚拟机恢复，以及文件和文件夹（仅适用于 Windows）、卷、虚拟硬盘的项级恢复 |
| Azure Stack HCI  |       V1 和 20H2      |    物理服务器        <br><br>     Hyper-V/Azure Stack HCI 虚拟机     <br><br>    VMware 虚拟机     |    V3 UR2 及更高版本   | 保护：虚拟机、群集共享卷 (CSV)      <br><br>     恢复：虚拟机恢复，以及文件和文件夹（仅适用于 Windows）、卷、虚拟硬盘的项级恢复 |
| VMware VM                                                  | VMware 服务器 5.5、6.0、6.5 或 6.7（经许可版本） | Hyper-V 虚拟机  <br><br>   VMware 虚拟机         | V3 UR1                             | 保护：群集共享卷 (CSV)、NFS 和 SAN 存储中的 VMware VM   <br><br>     恢复：虚拟机恢复，以及文件和文件夹（仅适用于 Windows）、卷、虚拟硬盘的项级恢复 <br><br>    不支持 VMware vApp。 |
| VMware VM      |   VMware 服务器 7.0、6.7、6.5 或 6.0（经许可版本）     |    Hyper-V 虚拟机     <br><br>    VMware 虚拟机    |   V3 UR2 及更高版本    |   保护：群集共享卷 (CSV)、NFS 和 SAN 存储上的 VMware VM     <br><br>    恢复：虚拟机恢复，以及文件和文件夹（仅适用于 Windows）、卷、虚拟硬盘的项级恢复     <br><br>    不支持 VMware vApp。  |

>[!NOTE]
> MABS 不支持对使用传递磁盘或远程 VHD 的虚拟机进行备份。 建议在这些方案中使用具有 MABS 的来宾级别备份，并在虚拟机上安装代理来备份数据。

## <a name="linux"></a>Linux

| **工作负载** | **版本**                               | **Azure 备份服务器安装**                      | **支持的 Azure 备份服务器** | **保护和恢复**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | 以 [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) 或 [VMware](backup-azure-backup-server-vmware.md) 来宾身份运行的 Linux | 物理服务器、本地 Hyper-V VM、VMware 中的 Windows VM | V3 UR1 和 V3 UR2                             | Hyper-V 必须在 Windows Server 2012 R2、Windows Server 2016 或 Windows Server 2019 上运行。 保护：整个虚拟机   <br><br>   恢复：整个虚拟机   <br><br>    仅支持文件一致的快照。    <br><br>   有关支持的 Linux 发行版和版本的完整列表，请参阅 [Azure 认可的发行版中的 Linux](../virtual-machines/linux/endorsed-distros.md) 一文。 |

## <a name="azure-expressroute-support"></a>Azure ExpressRoute 支持

可以使用公共对等互连（适用于旧线路）和 Microsoft 对等互连通过 Azure ExpressRoute 备份数据。 不支持通过专用对等互连进行备份。

使用公共对等互连：确保访问以下域/地址：

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP 地址
  * 20.190.128.0/18
  * 40.126.0.0/18

使用 Microsoft 对等互连，选择以下服务/区域和相关社区值：

* Azure Active Directory (12076:5060)
* Microsoft Azure 区域（根据恢复服务保管库的位置）
* Azure 存储（根据恢复服务保管库的位置）

有关详细信息，请参阅 [ExpressRoute 路由要求](../expressroute/expressroute-routing.md)。

>[!NOTE]
>对于新线路，公共对等互连已弃用。

## <a name="cluster-support"></a>群集支持

Azure 备份服务器可以保护以下群集应用程序中的数据：

* 文件服务器

* SQL Server

* Hyper-V - 如果使用横向扩展的 MABS 保护代理来保护 Hyper-V 群集，则无法为受保护的 Hyper-V 工作负荷添加辅助保护。

* Exchange Server - Azure 备份服务器可以保护受支持 Exchange Server 版本的非共享磁盘群集（群集连续复制），而且还可以保护针对本地连续复制配置的 Exchange Server。

* SQL Server - Azure 备份服务器不支持备份托管在群集共享卷 (CSV) 上的 SQL Server 数据库。

>[!NOTE]
>MABS 仅支持群集共享卷 (CSV) 上 Hyper-V 虚拟机的保护。 不支持保护 CSV 上托管的其他工作负荷。

Azure 备份服务器可以保护 MABS 服务器所在的同一个域中以及子域或受信任域中的群集工作负荷。 若要保护不受信任的域或工作组中的数据源，请对单个服务器使用 NTLM 或证书身份验证，或者只对群集使用证书身份验证。

## <a name="data-protection-issues"></a>数据保护问题

* MABS 无法使用共享驱动器（可能附加到其他 VM）备份 VM，因为 Hyper-V VSS 编写器无法备份由共享 VHD 备份的卷。

* 在保护共享文件夹时，共享文件夹的路径包括卷上的逻辑路径。 如果移动共享文件夹，保护将失败。 如果必须移动受保护的共享文件夹，请将其从保护组中删除，然后在移动后将其添加到保护。  此外，如果更改使用加密文件系统 (EFS) 的卷上某个保护的数据源的路径，且新文件路径超过 5120 个字符，则数据保护将失败。

* 无法更改受保护计算机的域并在不中断的情况下继续保护。 此外，无法更改受保护计算机的域，并将现有副本和恢复点与重新保护后的计算机关联。 如果必须更改受保护计算机的域，则首先将计算机上的数据源从保护中删除。 然后在它采用新域之后再保护计算机上的数据源。

* 无法更改受保护计算机的名称并在不中断的情况下继续保护。 此外，无法更改受保护计算机的名称，并将现有副本和恢复点与重新保护后的计算机关联。 如果必须更改受保护计算机的名称，则首先将计算机上的数据源从保护中删除。 然后在它采用新名称之后再保护计算机上的数据源。

* MABS 会在安装保护代理的过程中自动确定受保护计算机的时区。 如果在配置了保护后将受保护计算机转移到其他时区，请确保在控制面板中更改计算机时间。 然后更新 MABS 数据库中的时区。

* MABS 可以保护位于与 MABS 服务器相同的域中的工作负载，或是位于子域和受信任的域中的工作负载。 还可以使用 NTLM 或证书身份验证保护工作组和不受信任的域中的以下工作负载：

  * SQL Server
  * 文件服务器
  * Hyper-V

  这些工作负荷可以在单台服务器上或群集配置中运行。 要保护不在受信任的域中的工作负载，请参阅[在工作组和不受信任的域中准备计算机](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019&preserve-view=true#supported-scenarios)，了解支持的对象以及需要的身份验证的准确详细信息。

## <a name="unsupported-data-types"></a>不支持的数据类型

MABS 不支持保护以下数据类型：

* 硬链接

* 重新分析点，包括 DFS 链接和交接点

* 装入点元数据 - 保护组可以包含具有装入点的数据。 在这种情况下，DPM 会保护作为装入点目标的已装入卷，但是它不保护装入点元数据。 恢复包含装入点的数据时，需要手动重新创建装入点层次结构。

* 已装入卷中的数据

* 回收站

* 页面文件

* 系统卷信息文件夹。 要保护计算机的系统信息，需要将计算机的系统状态选择为保护组成员。

* 非 NTFS 卷

* 包含来自 Windows Vista 的硬链接或符号链接的文件。

* 承载 UPD（用户配置文件磁盘）的文件共享上的数据

* 具有以下任何特性组合的文件：

  * 加密和重新分析

  * 加密和单实例存储 (SIS)

  * 加密和区分大小写

  * 加密和稀疏

  * 区分大小写和 SIS

  * 压缩和 SIS

## <a name="next-steps"></a>后续步骤

* [使用 Microsoft Azure 备份服务器或 System Center DPM 进行备份时的支持矩阵](backup-support-matrix-mabs-dpm.md)