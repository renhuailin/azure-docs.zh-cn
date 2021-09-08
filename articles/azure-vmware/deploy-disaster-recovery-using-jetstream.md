---
title: 使用 JetStream 部署灾难恢复
description: 了解如何为 Azure VMware 解决方案私有云和本地 VMware 工作负载部署 JetStream 灾难恢复 (DR)。
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: a26cbaa4f8c37f3148a86005a637b006572685dc
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315117"
---
# <a name="deploy-disaster-recovery-using-jetstream"></a>使用 JetStream 部署灾难恢复

[JetStream 灾难恢复 (DR)](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) 安装在 VMware vSphere 环境中，并通过 vCenter 插件设备进行管理。 它提供云原生持续数据保护 (CDP)，不断地复制虚拟机 (VM) I/O 操作。 它不定期捕获快照，而是在数据写入主存储时连续捕获和复制数据，对正在运行的应用程序影响最小。  因此，可通过它快速恢复 VM 及其数据，从而达到较低的恢复点目标 (RPO)。  

使用 Azure VMware 解决方案，可直接将数据存储到 vSAN 中的恢复群集或附加的文件系统（如 Azure NetApp 文件）。 数据通过在 vSphere 中运行的 I/O 筛选器捕获。 基础数据存储可以是 VMFS、VSAN、vVol 或任何 HCI 平台。 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-disaster-recovery-diagram.png" alt-text="关系图显示了 JetStream DR 和 Azure VMware 解决方案集成。" border="false":::

JetStream DR 软件由三个主要组件组成： 

- 管理服务器虚拟设备 (MSA) 在 DR 保护之前进行安装和配置。 

- DR 虚拟设备 (DRVA) 是 JetStream DR MSA 自动部署的 .ISO 映像。

- 主机组件（IO 筛选器包） 

MSA 用于在计算群集上安装和配置主机组件，然后管理 JetStream DR 软件。 DRVA 运行数据路径 DR 组件。 多个 DRVA 可以同时运行，提高可伸缩性。 每个 DRVA 都有一个或多个作为 iSCSI LUN 或低延迟 VDISK 附加的专用分区。 分区用于维护持久性元数据的复制日志和存储库。

在本文中，你将在 Azure VMware 解决方案私有云和本地 VMware 工作负载中部署 JetStream DR，并了解如何使用它。


## <a name="supported-scenarios"></a>支持的方案
根据所需的保护服务和受保护的私有云类型，可通过两种方式部署 JetStream DR：

- 本地到云

- 云到云

### <a name="on-premises-to-cloud-deployment"></a>本地到云的部署
在组织的基于 VMware 的数据中心中运行的 VM 会被持续复制到 Microsoft Azure。 因此，如果本地数据中心内发生事件，VM 可在 Azure VMware 解决方案中恢复运行。 当 VM 在恢复环境中运行时，它们会继续复制数据以实现持续保护。 还原本地数据中心后，VM 及其数据（包括恢复环境中 VM 生成的任何新数据）可以不间断地返回到其原始数据中心。

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="关系图显示了本地到 Azure VMware 解决方案私有云的 JetStream 部署。" border="false":::

### <a name="cloud-to-cloud-deployment"></a>云到云的部署

在此配置中，Azure VMware 解决方案在一个数据中心托管你的主环境。 它通过持续地将 VM 和数据复制到其另一个数据中心的另一个私有云来保护它们。 如果发生事件，VM 和数据将在第二个数据中心恢复。 此保护可以是双向的 - 数据中心“A”保护数据中心“B”，反之亦然。

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="关系图显示了 Azure VMware 解决方案私有云到私有云的 JetStream 部署。" border="false":::


## <a name="prerequisites"></a>先决条件

- 部署为次要区域的 Azure VMware 解决方案私有云。 

- 一个与 Azure VMware 解决方案私有云建立了 ExpressRoute 连接的 Ubuntu Linux 跳转盒。 

- 安装在 Linux 跳转盒上的最新 PowerShell。

- 安装了 PowerShell 库的最新第三方模块。

### <a name="protected-site"></a>受保护的站点

受保护的站点托管一个服务群集，用于管理服务，例如 vCenter、DNS、Active Directory 和运行受保护的业务线应用程序的计算机群集 。 受保护的站点可以位于本地，也可以托管在 Azure VMware 解决方案中。

可使用以下任一类型：

- Azure Blob 存储

- Azure VMware 解决方案 vSAN

- Azure VMware 解决方案附加文件系统，例如 Azure NetApp 文件


| **项目**  | **描述**  |  
| --- | --- |  
| **vCenter Server**  | <ul><li>受支持的版本：6.7</li><li>HTTPS 端口：如果使用防火墙，则必须打开 HTTPS 端口 443。</li><li>连接性：必须能够从 vCenter 访问 JetStream DR 管理服务器设备 FQDN。 否则，插件安装将失败。</li><li>时间：vCenter 时钟和 JetStream DR MSA 时钟必须同步。</li></ul>  |
| 分布式资源计划程序 (DRS)  | 为实现资源均衡，建议在计算群集中使用。  |
| **群集**  | vSphere 主机：要由 JetStream DR 保护的 VM 必须属于群集的一部分。  |
| vSphere 主机  | <ul><li>支持的版本：6.7U1（版本 #10302608）或更高版本</li><li>连接性：必须能够从主机访问 vCenter Server FQDN。 否则，主机配置将失败。</li><li>时间：vSphere 主机时钟和 JetStream DR MSA 时钟必须同步。</li><li>CIM 服务：必须启用 CIM 服务器，这是默认设置。</li></ul>  |
| JetStream DR MSA  | <ul><li>CPU：64 位，4 个 vCPU</li><li>内存:4 GB</li><li>磁盘空间：60 GB</li><li>网络：可使用静态的或动态分配的 (DHCP) IP 地址。 FQDN 必须向 DNS 注册。</li><li>DNS：vSphere 虚拟主机和 vCenter Server 的 DNS 名称解析</li></ul>  |
| JetStream DRVA  | <ul><li>CPU：4 核</li><li>内存：8 GB</li><li>网络：可使用静态的或动态分配的 (DHCP) IP 地址。</li></ul>  |
| 复制日志存储  | 受保护的站点应公开主机在群集中共享以实现最佳性能的低延迟闪存设备。 此设备可以由 JetStream DR 软件控制，也可以由第三方提供。 它用作复制日志的存储库。 DRVA 和 ESXi 主机必须能够通过 iSCSI 直接访问此存储。  |
| **端口**  | 安装 JetStream DR 软件后，源 ESXi 主机上会自动打开一系列端口。 因此，对于大多数用户，无需执行更多操作。 但是，如果本地/源设置具有阻止这些端口的特殊防火墙规则，则需手动打开这些端口。<br /><br />端口范围：32873-32878  |



### <a name="recovery-site"></a>恢复站点

建立了一个 Azure VMware 解决方案试点轻量群集，用于故障转移恢复。 尽管恢复站点是在安装过程中创建的，但在正常操作期间，恢复站点群集并未完全填充或使用。 为了响应灾难事件，可按需将已进行故障转移的计算群集添加到恢复站点。

### <a name="network"></a>网络

必须在受保护的站点和恢复站点之间建立具有以下特征的网络。

| **项** | **说明** |
| --- | --- |
| JetStream DR MSA  | MSA 需要管理网络。 此网络用于访问 JetStream DR RESTful API 并进行其他数据路径调用。 如果专用网络可用于连接到对象存储，则此专用网络应作为单独的网络添加到 MSA VM。 如果没有可用的专用网络，请确保可使用管理网络连接到对象存储。 <br /><br />专用外部网络可用于对象存储访问；否则，数据流量通过管理网络进行发送。  | 
| JetStream DRVA  | 如果使用的唯一网络是管理网络，请确保它有权访问 IO 筛选器和对象存储。 如果群集中存在多个网络，则所有网络都必须附加到 DRVA VM。  | 
| 从对象云虚拟设备恢复 (RocVA)  | 如果使用的唯一网络是管理网络，请确保它有权访问 ESXi 主机和对象存储。 如果群集中存在多个网络，则所有网络都必须附加到 RocVA VM。 RocVA 是一个临时 VM，在需要用于 VM 恢复时自动创建，然后在不再需要时删除。  | 
| 对象存储/Blob 存储  | 对象存储/Blob 存储应可供受保护的站点和恢复站点访问。  | 
| 复制日志存储  | DRVA 和 ESXi 主机必须能够通过 iSCSI 直接访问该存储。  | 


## <a name="install-jetstream-dr"></a>安装 JetStream DR

JetStream DR 安装可通过 Azure VMware 解决方案门户中的“运行命令”功能进行。 分三步完成安装。

:::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="屏幕截图显示了如何访问可用的 JetStream 运行命令。" lightbox="media/run-command/run-command-overview-jetstream.png":::


>[!NOTE]
>运行命令按提交的顺序一次执行一个。

### <a name="check-the-current-state-of-the-system"></a>检查系统的当前状态

运行 `Invoke-PreflightJetDRSystemCheck` cmdlet 来检查系统的状态，以及是否满足脚本的最低要求。 它还检查用于执行其他 cmdlet 的所需 vCenter 配置。  

该 cmdlet 会检查：

- PowerShell
- vCenter FQDN
- CloudAdmin 角色
- VMware 模块

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“运行命令” > “包” > “Invoke-PreflightJetDRSystemCheck”  。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | 最长保留时间  | cmdlet 输出的保持期。 默认值为 60。  |
   | 指定用于执行的名称  | 字母数字名称，例如 checkDRsystem。  |
   | **超时**  | 一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。 



### <a name="check-cluster-configuration"></a>检查群集配置

运行 `Invoke-PreflightJetDRInstall` cmdlet 来检查以下群集配置：

- 群集详细信息是否正确 
- 至少有四个主机（最少）
- 是否提供有同名的 VM 用于安装 MSA
- vCenter 中是否存在任何 jetdr 插件


1. 选择“运行命令” > “包” > “Invoke-PreflightJetDRInstall”  。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | **VMName** | MSA VM 的名称。 例如 jetstreamServer。 |
   | **群集** | 将部署 MSA 的群集的名称。 例如 Cluster-1。 |
   | ProtectedCluster | 要保护的群集。 例如 Cluster-1。 |
   | 最长保留时间  | cmdlet 输出的保持期。 默认值为 60。  |
   | 指定用于执行的名称  | 字母数字名称，例如 check_jetserverdetails。  |
   | **超时**  | 一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。  

1. 如果报告了错误，可转到下一步来部署 JetDR MSA。


### <a name="deploy-jetdr-msa"></a>部署 JetDR MSA

运行 `Install-JetDR` cmdlet 来部署 JetDR MSA、将 vCenter 注册到 JetDR MSA 并配置群集。 该部署从 Microsoft Server Media (MMS) 下载 JetDR 捆绑包，并创建分配有提升的权限的新用户。 

1. 选择“运行命令” > “包” > “Install-JetDR”  。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | **Network** | 要部署的 MSA 的网络映射。 例如 VM 网络。  |
   | **HostName** | 要部署的 MSA 的主机名 (FQDN)。  |
   | **凭据** | 要部署的 MSA 的根用户凭据。   |
   | **网关** | 要部署的 MSA 的网关。 保留空白表示使用 DHCP。  |
   | **Dns** | MSA 应使用的 DNS IP。 保留空白表示使用 DHCP。  |
   | MSIp | 要部署的 MSA 的 IP 地址。 保留空白表示使用 DHCP。  |
   | 网络掩码 | 要部署的 MSA 的网络掩码。 保留空白表示使用 DHCP。  |
   | **群集** | 将部署 MSA 的群集的名称。 例如 Cluster-1。 |
   | **VMName** | MSA VM 的名称。 例如 jetstreamServer。 |
   | **数据存储** | 将部署 MSA 的数据存储。  |
   | ProtectedCluster | 要保护的群集。 例如 Cluster-1。 |
   | RegisterWithIp | 使用 IP 而不是主机名来注册 MSA。 <ul><li>如果 MSA 的主机名未注册 DNS，则为 True。</li><li>如果 MSA 的主机名已注册 DNS，则为 False。 </li></ul> |
   | 最长保留时间  | cmdlet 输出的保持期。 默认值为 60。  |
   | 指定用于执行的名称  | 字母数字名称，例如 check_jetserverdetails。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。  


## <a name="uninstall-jetstream-dr"></a>卸载 JetStream DR

分两步卸载 JetStream DR。 


### <a name="check-current-state-of-the-jetstream-appliance"></a>检查 JetStream 设备的当前状态

运行 `Invoke-PreflightJetDRUninstall` cmdlet 以诊断现有 MSA VM 和群集配置。 它检查 JetStream DR 设备的当前状态，以及是否满足脚本的最低要求：

- 群集详细信息是否正确 
- 至少有四个主机（最少）
- vCenter 是否已注册到 MSA

1. 选择“运行命令” > “包” > “Invoke-PreflightJetDRUninstall”  。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | MSIp | MSA VM 的 IP 地址。  |
   | **凭据** | MSA VM 的根用户的凭据。 它必须与安装时提供的凭据相同。  |
   | ProtectedCluster | 受保护的群集的名称，例如 Cluster-1。  它必须是安装时提供的群集。 |
   | 最长保留时间  | cmdlet 输出的保持期。 默认值为 60。  |
   | 指定用于执行的名称  | 字母数字名称，例如 uninstallcheck_jetserverdetails。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。  




### <a name="uninstall-jetdr"></a>卸载 JetDR

运行 `Uninstall-JetDR` cmdlet 以卸载 JetStream DR 及其组件。  它会取消配置群集，从 MSA 中注销 vCenter，然后删除该用户。

1. 选择“运行命令” > “包” > “Uninstall-JetDR”  。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | MSIp | MSA VM 的 IP 地址。  |
   | **凭据** | MSA VM 的根用户的凭据。 它必须与安装时提供的凭据相同。   |
   | ProtectedCluster | 受保护的群集的名称，例如 Cluster-1。  它必须是安装时提供的群集。 |
   | 最长保留时间  | cmdlet 输出的保持期。 默认值为 60。  |
   | 指定用于执行的名称  | 字母数字名称，例如 uninstallcheck_jetserverdetails。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。  


## <a name="next-steps"></a>后续步骤

- [Azure VMware 解决方案的 JetStream DR - 完整演示](https://vimeo.com/475620858/2ce9413248)
   
   - [Azure VMware 解决方案的 JetStream DR 入门](https://vimeo.com/491880696/ec509ff8e3)

   - [配置和保护 VM](https://vimeo.com/491881616/d887590fb2)

   - [故障转移到 Azure VMware 解决方案](https://vimeo.com/491883564/ca9fc57092)

   - [故障回复到本地](https://vimeo.com/491884402/65ee817b60)

- [Azure VMware 解决方案的 JetStream DR 基础结构设置](https://vimeo.com/480574312/b5386a871c)（技术详细信息，无音轨）
