---
title: 使用 JetStream DR 部署灾难恢复（预览版）
description: 了解如何为 Azure VMware 解决方案私有云和本地 VMware 工作负载实施 JetStream DR。
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: references_regions
ms.openlocfilehash: f43d2a8db4ef29588ad5260c583faab31bd01228
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279063"
---
# <a name="deploy-disaster-recovery-using-jetstream-dr-preview"></a>使用 JetStream DR 部署灾难恢复（预览版）

>[!IMPORTANT]
>Azure VMware 解决方案（预览版）上的 JetStream DR 目前以公共预览版提供。
>此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[JetStream DR](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) 是一种云原生灾难恢复解决方案，旨在最大限度地减少发生灾难时虚拟机 (VM) 的停机时间。 JetStream DR 的实例部署在受保护站点和恢复站点。 

JetStream 的平台以连续数据保护 (CDP) 为基础构建，采用 [VMware VAIO 框架](https://core.vmware.com/resource/vmware-vsphere-apis-io-filtering-vaio)，可实现最小或接近零数据丢失。 JetStream DR 为许多业务关键型和任务关键型应用程序提供所需的保护级别。 它还通过在 DR 站点使用最少的资源并使用具有成本效益的云存储（例如 [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）来实现具有成本效益的 DR。

在本文中，你将为 Azure VMware 解决方案私有云和本地 VMware 工作负载实施 JetStream DR。

若要详细了解 JetStream DR，请参阅：

- [JetStream 解决方案简介](https://www.jetstreamsoft.com/2020/09/28/solution-brief-disaster-recovery-for-avs/)

- [Azure Marketplace 上的 JetStream DR](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020)

- [JetStream 知识库文章](https://www.jetstreamsoft.com/resources/knowledge-base/)


## <a name="core-components-of-the-jetstream-dr-solution"></a>JetStream DR 解决方案的核心组件

| 项 | 描述 |
| --- | --- |
| JetStream 管理服务器虚拟设备 (MSA)  | MSA 同时启用第 0 天和第 2 天配置，例如主站点、保护域和恢复 VM。  MSA 由云管理员安装在 vSphere 节点上。MSA 实现了一个 vCenter 插件，可用于从 vCenter 本地管理 JetStream DR。 MSA 不处理受保护 VM 的复制数据。  | 
| JetStream DR 虚拟设备 (DRVA)  | 基于 Linux 的虚拟机设备从源 ESXi 主机接收受保护的 VM 复制数据。 它负责将复制数据存储在 DR 站点（通常位于对象存储中，例如 Azure Blob 存储）。 根据受保护的 VM 数量和要复制的存储量，私有云管理员可以创建一个或多个 DRVA 实例。  | 
| JetStream ESXi 主机组件（IO 筛选器包）  | JetStream 软件安装在为 JetStream DR 配置的每个 ESXi 主机上。 主机驱动程序会截获 vSphere VM 的 IO，并将复制数据发送到 DRVA。   | 
| JetStream 保护域  | 将使用相同的策略和 runbook 一起保护的 VM 的逻辑组。 保护域中所有 VM 的数据存储在同一 Azure Blob 容器实例中。 同一 DRVA 实例处理对保护域中所有 VM 的远程 DR 存储的复制。   | 
| **Azure Blob 存储容器**  | 受保护的 VM 复制数据存储在 Azure Blob 中。 JetStream 软件为每个 JetStream 保护域创建一个 Azure Blob 容器实例。    | 



## <a name="jetstream-scenarios-on-azure-vmware-solution"></a>Azure VMware 解决方案上的 JetStream 方案
对于以下两种方案，可以将 JetStream DR 与 Azure VMware 解决方案一起使用：  

- 本地 VMware 到 Azure VMware 解决方案 DR

- Azure VMware 解决方案到 Azure VMware 解决方案 DR

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>方案 1：本地 VMware 到 Azure VMware 解决方案 DR

在这种方案中，主站点是本地 VMware 环境，DR 站点是 Azure VMware 解决方案私有云。

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="关系图显示了本地到 Azure VMware 解决方案私有云的 JetStream 部署。" border="false" lightbox="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png":::

### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>方案 2：Azure VMware 解决方案到 Azure VMware 解决方案 DR

在此方案中，主站点是一个 Azure 区域中的 Azure VMware 解决方案私有云。 灾难恢复站点是另一个 Azure 区域中的 Azure VMware 解决方案私有云。 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="关系图显示了 Azure VMware 解决方案私有云到私有云的 JetStream 部署。" border="false" lightbox="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png":::


## <a name="prerequisites"></a>先决条件


### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>方案 1：本地 VMware 到 Azure VMware 解决方案 DR 

- 在目标 DR 区域中部署了至少三个节点的 Azure VMware 解决方案私有云。  


   :::image type="content" source="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png" alt-text="显示 Azure VMware 解决方案上灾难恢复解决方案的第一个必备条件的示意图。" lightbox="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png":::


- 在主站点 JetStream 设备和 Azure 存储 Blob 实例之间配置的网络连接。 

- 从 Azure 市场[设置和订阅 JetStream DR](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) 以下载 JetStream DR 软件。

- 使用标准或高级性能层级创建的 [Azure Blob 存储帐户](../storage/common/storage-account-create.md)。 对于[访问层级，选择“热”](../storage/blobs/access-tiers-overview.md)。 

   >[!NOTE]
   >不支持 Blob 上的“启用分层命名空间选项”。   

- 在 Azure VMware 解决方案私有云上配置的 NSX-T 网段，并可选择在该网段上为 JetStream 虚拟设备启用 DHCP。  

- DNS 服务器配置为解析 Azure VMware 解决方案 vCenter、Azure VMware 解决方案 ESXi 主机、Azure 存储帐户和 JetStream 虚拟设备的 JetStream 市场服务的 IP 地址。 



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>方案 2：Azure VMware 解决方案到 Azure VMware 解决方案 DR

- Azure VMware 解决方案私有云在主要和次要区域中均至少部署了三个节点。  
 
- 在主站点 JetStream 设备和 Azure 存储 Blob 实例之间配置的网络连接。 

- 从 Azure 市场[设置和订阅 JetStream DR](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) 以下载 JetStream DR 软件。

- 使用标准或高级性能层级创建的 [Azure Blob 存储帐户](../storage/common/storage-account-create.md)。 对于[访问层级，选择“热”](../storage/blobs/access-tiers-overview.md)。 

   >[!NOTE]
   >不支持 Blob 上的“启用分层命名空间选项”。   

- 在 Azure VMware 解决方案私有云上配置的 NSX-T 网段，并可选择在该网段上为 JetStream 虚拟设备启用 DHCP。  

- 主站点和 DR 站点上的 DNS 服务器配置为解析 Azure VMware 解决方案 vCenter、Azure VMware 解决方案 ESXi 主机、Azure 存储帐户和 JetStream 虚拟设备的 JetStream 市场服务的 IP 地址。 


有关更多本地 JetStream DR 必备条件，请参阅 [JetStream 预安装指南](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/pre-installation-guidelines/)。





## <a name="install-jetstream-dr-on-azure-vmware-solution"></a>在 Azure VMware 解决方案上安装 JetStream DR  
 
可以针对这两种受支持的方案执行这些步骤。 
 
1. 在本地数据中心，按照 [JetStream 文档](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/installing-jetstream-dr-software/)安装 JetStream DR。  

1. 在 Azure VMware 解决方案私有云中，使用“Run”命令安装 JetStream DR。 从 [Azure 门户](https://portal.azure.com)中，选择“运行命令” > “包” > “JSDR.Configuration”。  

   :::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="屏幕截图显示了如何访问可用的 JetStream 运行命令。" lightbox="media/run-command/run-command-overview-jetstream.png":::
 
   >[!NOTE]
   >Azure VMware 解决方案中的默认 CloudAdmin 用户没有足够的权限来安装 JetStream DR。  Azure VMware 解决方案通过调用 JetStream DR 的 Azure VMware 解决方案运行命令来简化和自动安装 JetStream DR。  
 

1. 运行 Invoke-PreflightJetDRInstall cmdlet 以检查是否满足安装 JetStream DR 的先决条件。 例如，该 cmdlet 会验证所需数量的主机、群集名称和唯一的 VM 名称。 

1. 提供所需的值或更改默认值，然后选择“运行”。

   | 字段 | 值 |
   | --- | --- |
   | **Network**  | 必须在其中部署 JetStream MSA 的 NSX-T 网段的名称。  |
   | **数据存储**  | 将在其中部署 MSA 的数据存储的名称。  |
   | ProtectedCluster | 要保护的 Azure VMware 解决方案私有云群集的名称，例如 Cluster-1。  只能提供一个群集名称。 |
   | **Cluster** | 部署 JetStream MSA 的 Azure VMware 解决方案专用群集的名称，例如 Cluster-1。 |
   | **VMName** | JetStream MSA VM 的名称，例如 jetstreamServer。 |
   | 指定用于执行的名称  | 执行的字母数字名称，例如 Invoke-PreflightJetDRInstall-Exec1。 用于验证 cmdlet 是否成功运行。 |
   | **超时**  | 一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. [查看执行的状态](concepts-run-command.md#view-the-status-of-an-execution)。
   

## <a name="install-the-jetstream-dr-msa"></a>安装 JetStream DR MSA

Azure VMware 解决方案支持使用静态 IP 地址或基于 DHCP 的 IP 地址安装 JetStream。  

### <a name="static-ip-address"></a>静态 IP 地址

1. 选择“运行命令” > “包” > “Install-JetDRWithStaticIP”  。

1. 提供所需的值或更改默认值，然后选择“运行”。


   | 字段 | **值** |
   | --- | --- |
   | ProtectedCluster | 要保护的 Azure VMware 解决方案私有云群集的名称，例如 Cluster-1。  在安装期间，只能提供一个群集名称。 |
   | **数据存储**  | 将在其中部署 JetStream MSA 的数据存储的名称。  |
   | **VMName** | JetStream MSA VM 的名称，例如 jetstreamServer。 |
   | **Cluster** | 部署 JetStream MSA 的 Azure VMware 解决方案专用群集的名称，例如 Cluster-1。 |
   | 网络掩码 | 要部署的 MSA 的网络掩码，例如 22 或 24。  |
   | MSIp | JetStream MSA 虚拟机的 IP 地址。   |
   | **Dns** | JetStream MSA VM 应使用的 DNS IP。   |
   | **网关** | JetStream MSA VM 的网络网关的 IP 地址。  |
   | **凭据**  |  JetStream MSA VM 根用户的凭据。   |
   | **HostName** | JetStream MSA VM 的主机名 (FQDN)。  |
   | **Network**  | 必须在其中部署 JetStream MSA 的 NSX-T 网段的名称。 |
   | 指定用于执行的名称  | 执行的字母数字名称，例如 Install-JetDRWithStaticIP-Exec1。   用于验证 cmdlet 是否成功运行。  |


1. [查看执行的状态](concepts-run-command.md#view-the-status-of-an-execution)。


### <a name="dhcp-based-ip-address"></a>基于 DHCP 的 IP 地址

此步骤还会在需要 DR 保护的集群上安装 JetStream vSphere 安装包 (VIB)。 

1. 选择“运行命令” > “包” > “Install-JetDRWithDHCP”  。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | 字段 | **值** |
   | --- | --- |
   | ProtectedCluster | 要保护的 Azure VMware 解决方案私有云群集的名称，例如 Cluster-1。  在安装期间，只能提供一个群集名称。 |
   | **数据存储**  | 将在其中部署 JetStream MSA 的数据存储的名称。  |
   | **VMName** | JetStream MSA VM 的名称，例如 jetstreamServer。 |
   | **Cluster** | 部署 JetStream MSA 的 Azure VMware 解决方案专用群集的名称，例如 Cluster-1。 |
   | **凭据**  |  JetStream MSA VM 根用户的凭据。   |
   | **HostName** | JetStream MSA VM 的主机名 (FQDN)。  |
   | **Network**  | 必须在其中部署 JetStream MSA 的 NSX-T 网段的名称。 |
   | 指定用于执行的名称  | 执行的字母数字名称，例如 Install-JetDRWithDHCP-Exec1。   用于验证 cmdlet 是否成功运行。  |
 
 
1. [查看执行的状态](concepts-run-command.md#view-the-status-of-an-execution)。
 
 
## <a name="add-jetstream-dr-to-new-azure-vmware-solution-clusters"></a>将 JetStream DR 添加到新的 Azure VMware 解决方案群集  


1. 选择“运行命令” > “包” > “Enable-JetDRForCluster”。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | 字段 | **值** |
   | --- | --- |
   | ProtectedCluster | 要保护的 Azure VMware 解决方案私有云群集的名称，例如 Cluster-1。  在安装期间，只能提供一个群集名称。 |
   | **凭据**  |  JetStream MSA VM 根用户的凭据。   |
   | MSIp | JetStream MSA 虚拟机的 IP 地址。   |
   | 指定用于执行的名称  | 执行的字母数字名称，例如 Enable-JetDRForCluster-Exec1。   用于验证 cmdlet 是否成功运行。  |
  
1. [查看执行的状态](concepts-run-command.md#view-the-status-of-an-execution)。
 
 
 

## <a name="configure-jetstream-dr"></a>配置 JetStream DR 
 
本节仅概述了配置 JetStream DR 所需的步骤。  有关详细说明和步骤，请参阅[配置 JetStream DR](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/configuring-jetstream-dr/) 文档。 
 
在 Azure VMware 解决方案群集上安装 JetStream DR MSA 和 JetStream VIB 后，请使用 JetStream 门户完成剩余的配置步骤。 



1. 从 vCenter 设备访问 JetStream 门户。

1. [添加外部存储站点](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/add-a-storage-site/)。  

1. [部署 JetStream DRVA 设备](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/deploy-a-dr-virtual-appliance/)。 

1. 使用 Azure VMware 解决方案群集可用的其中一种数据存储[创建 JetStream 复制日志存储卷](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-replication-log-store-volume/)。 

   >[!TIP]
   >快速本地存储（例如 vSAN 数据存储）是复制日志的首选。 
 
1. [创建 JetStream 保护域](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-protected-domain/)。 你将提供在前面的步骤中创建的 Azure Blob 存储站点、JetStream DRVA 实例和复制日志。 

1. [选择要保护的 VM](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/select-vms-for-protection/)，然后[启动 VM 保护](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/start-vm-protection/)。

 
有关 JetStream DR 的其余配置步骤，例如创建故障转移 Runbook、调用到 DR 站点的故障转移以及调用到主站点的故障恢复，请参阅 [JetStream 管理指南文档](https://www.jetstreamsoft.com/portal/jetstream-article-categories/product-manual/)。  
 
## <a name="disable-jetstream-dr-on-an-azure-vmware-solution-cluster"></a>在 Azure VMware 解决方案群集上禁用 JetStream DR  
 
此 cmdlet 仅对其中一个群集禁用 JetStream DR，而不会完全卸载 JetStream DR。 

1. 选择“运行命令” > “包” > “Disable-JetDRForCluster”。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | 字段 | **值** |
   | --- | --- |
   | ProtectedCluster | 要保护的 Azure VMware 解决方案私有云群集的名称，例如 Cluster-1。  在安装期间，只能提供一个群集名称。 |
   | **凭据**  |  JetStream MSA VM 根用户的凭据。   |
   | MSIp | JetStream MSA 虚拟机的 IP 地址。   |
   | 指定用于执行的名称  | 执行的字母数字名称，例如 Disable-JetDRForCluster-Exec1。   用于验证 cmdlet 是否成功运行。  |

1. [查看执行的状态](concepts-run-command.md#view-the-status-of-an-execution)。


 
## <a name="uninstall-jetstream-dr"></a>卸载 JetStream DR  

1. 选择“运行命令” > “包” > “Invoke-PreflightJetDRUninstall”  。 此 cmdlet 检查群集是否至少有四个主机（最少需要四个主机）。 

1. 提供所需的值或更改默认值，然后选择“运行”。

   | 字段 | **值** |
   | --- | --- |
   | ProtectedCluster | 要保护的 Azure VMware 解决方案私有云群集的名称，例如 Cluster-1。  在安装期间，只能提供一个群集名称。 |
   | **凭据**  |  JetStream MSA VM 根用户的凭据。   |
   | MSIp | JetStream MSA 虚拟机的 IP 地址。   |
   | 指定用于执行的名称  | 执行的字母数字名称，例如 Invoke-PreflightJetDRUninstall-Exec1。   用于验证 cmdlet 是否成功运行。  |

1. [查看执行的状态](concepts-run-command.md#view-the-status-of-an-execution)。

1. 预检 cmdlet 成功完成后，选择“Uninstall-JetDR”，提供所需的值或更改默认值，然后选择“运行”。 

   | 字段 | **值** |
   | --- | --- |
   | ProtectedCluster | 要保护的 Azure VMware 解决方案私有云群集的名称，例如 Cluster-1。  在安装期间，只能提供一个群集名称。 |
   | **凭据**  |  JetStream MSA VM 根用户的凭据。   |
   | MSIp | JetStream MSA 虚拟机的 IP 地址。   |
   | 指定用于执行的名称  | 执行的字母数字名称，例如 Uninstall-JetDR-Exec1。   用于验证 cmdlet 是否成功运行。  |

 1. [查看执行的状态](concepts-run-command.md#view-the-status-of-an-execution)。


## <a name="region-availability"></a>上市区域

适用于 Azure VMware 解决方案的 JetStream DR 可在以下区域使用： 

- 美国东部 

- 北欧 

- 美国南部 

- 西欧 

- 美国中部 

- 美国西部 

- 东亚 

- Japan East 

- Brazil South 

- 加拿大东部 

- 英国西部 


 
## <a name="support"></a>支持  
 
JetStream DR 是 [JetStream Software](https://www.jetstreamsoft.com/) 支持的解决方案。 有关 JetStream 的任何产品或支持问题，请联系 support-avs@jetstreamsoft.com。  
 
Azure VMware 解决方案使用“run”命令（预览版）自动安装和卸载 JetStream DR。 有关 run 命令的任何问题，请联系 Microsoft 支持部门。 有关 JetStream 安装和卸载 cmdlet 的问题，请联系 JetStream 获得支持。 



## <a name="next-steps"></a>后续步骤

- [基础结构设置：适用于 Azure VMware 解决方案的 JetStream DR](https://vimeo.com/480574312/b5386a871c) 

- [适用于 Azure VMware 解决方案的 JetStream DR（完整演示）](https://vimeo.com/475620858/2ce9413248)
   
   - [适用于 Azure VMware 解决方案的 JetStream DR 入门](https://vimeo.com/491880696/ec509ff8e3)

   - [配置和保护 VM](https://vimeo.com/491881616/d887590fb2)

   - [故障转移到 Azure VMware 解决方案](https://vimeo.com/491883564/ca9fc57092)

   - [故障回复到本地](https://vimeo.com/491884402/65ee817b60)

