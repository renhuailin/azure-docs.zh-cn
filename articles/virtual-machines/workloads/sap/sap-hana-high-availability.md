---
title: SLES 上 Azure VM 中 SAP HANA 的高可用性 | Microsoft Docs
description: SUSE Linux Enterprise Server 上 Azure VM 中 SAP HANA 的高可用性
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 079cf4fde03f2e9eeafc77a35f03c16e4b8c241b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112285996"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server 上 Azure VM 中 SAP HANA 的高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

对于本地开发，可以使用 HANA 系统复制或共享存储来建立 SAP HANA 的高可用性。
在 Azure 虚拟机 (VM) 上，Azure 上的 HANA 系统复制是唯一受支持的高可用性功能。 SAP HANA 复制由一个主节点和至少一个辅助节点组成。 对主节点上数据所做的更改以同步或异步方式复制到辅助节点。

本文介绍如何部署和配置虚拟机、安装群集框架，以及安装和配置 SAP HANA 系统复制。
在示例配置和安装命令中，使用了实例编号 **03** 和 HANA 系统 ID **HN1**。

请先阅读以下 SAP 说明和文档：

* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表。
  * Azure VM 大小的重要容量信息。
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合。
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本。
* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2205917] 包含适用于 SUSE Linux Enterprise Server for SAP Applications 的推荐 OS 设置。
* SAP 说明 [1944799] 包含适用于 SUSE Linux Enterprise Server for SAP Applications 的 SAP HANA 准则。
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1984787] 包含有关 SUSE Linux Enterprise Server 12 的一般信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* SAP 说明 [401162] 包含有关在设置 HANA 系统复制时如何避免“地址已在使用”的信息。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [针对 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]指南。
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]（本文）。
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]指南。
* [SUSE Linux Enterprise Server for SAP Applications 12 SP3 最佳做法指南][sles-for-sap-bp]
  * 设置 SAP HANA SR 性能优化的基础结构 (SLES for SAP Applications 12 SP1)。 本指南包含为本地开发设置 SAP HANA 系统复制的所需的全部信息。 请使用本指南作为基准。
  * 设置 SAP HANA SR 成本优化的基础结构 (SLES for SAP Applications 12 SP1)

## <a name="overview"></a>概述

为实现高可用性，SAP HANA 安装在两台虚拟机上。 数据将使用 HANA 系统复制进行复制。

![SAP HANA 高可用性概述](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA 系统复制设置使用专用的虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 以下列表显示负载均衡器的配置：

* 前端配置：hn1-db 的 IP 地址 10.0.0.13
* 后端配置：连接到应当作为 HANA 系统复制的一部分的所有虚拟机的主网络接口
* 探测端口：端口 62503
* 负载均衡规则：30313 TCP、30315 TCP、30317 TCP

## <a name="deploy-for-linux"></a>为 Linux 部署

SUSE Linux Enterprise Server for SAP Applications 中已随附 SAP HANA 的资源代理。
Azure 市场中包含适用于 SUSE Linux Enterprise Server for SAP Applications 12 的映像，可以用于部署新的虚拟机。

### <a name="deploy-with-a-template"></a>使用模板进行部署

可以使用 GitHub 上的某个快速入门模板部署全部所需的资源。 该模板将部署虚拟机、负载均衡器、可用性集，等等。
若要部署模板，请执行以下步骤：

1. 在 Azure 门户中打开[数据库模板][template-multisid-db]或[聚合模板][template-converged]。 
    数据库模板仅创建数据库的负载均衡规则。 聚合模板还会创建 ASCS/SCS 和 ERS（仅限 Linux）实例的负载均衡规则。 如果打算安装基于 SAP NetWeaver 的系统，并想要在同一台计算机上安装 ASCS/SCS 实例，请使用[聚合模板][template-converged]。

1. 输入以下参数：
    - **SAP 系统 ID**：输入要安装的 SAP 系统的 SAP 系统 ID。 该 ID 将用作所要部署的资源的前缀。
    - **堆叠类型**：（仅当使用聚合模板时，此参数才适用。）选择 SAP NetWeaver 堆叠类型。
    - **OS 类型**：选择一个 Linux 发行版。 对于本示例，请选择“SLES 12”。
    - **数据库类型**：选择“HANA”。
    - **SAP 系统大小**：输入新系统将提供的 SAPS 数量。 如果不确定系统需要多少 SAPS，请咨询 SAP 技术合作伙伴或系统集成商。
    - **系统可用性**：选择“HA”。
    - **管理员用户名和管理员密码**：创建的可用于登录计算机的新用户。
    - **新子网或现有子网**：确定是要创建新的虚拟网络和子网，还是使用现有子网。 如果已有连接到本地网络的虚拟网络，请选择“现有”。
    - **子网 ID**：如果要将 VM 部署到现有 VNet 中，并且该 VNet 中已定义了 VM 应分配到的子网，请指定该特定子网的 ID。 ID 通常类似于“/subscriptions/\<subscription ID>/resourceGroups/\<resource group name>/providers/Microsoft.Network/virtualNetworks/\<virtual network name>/subnets/\<subnet name>”。

### <a name="manual-deployment"></a>手动部署

> [!IMPORTANT]
> 请确保你选择的 OS 已经过 SAP 针对你使用的特定 VM 类型上的 SAP HANA 进行的认证。 可以在[经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中查找获得 SAP HANA 认证的 VM 类型和这些 VM 类型的 OS 版本列表。 请确保单击进入列出的 VM 类型的详细信息，以获取特定 VM 类型的 OS 版本支持的 SAP HANA 完整列表
>  

1. 创建资源组。
1. 创建虚拟网络。
1. 创建可用性集。
   - 设置最大更新域。
1. 创建负载均衡器（内部）。 建议使用[标准负载均衡器](../../../load-balancer/load-balancer-overview.md)。
   - 选择在步骤 2 中创建的虚拟网络。
1. 创建虚拟机 1。
   - 在 Azure 库中使用所选 VM 类型上 SAP HANA 支持的 SLES4SAP 映像。
   - 选择在步骤 3 中创建的可用性集。
1. 创建虚拟机 2。
   - 在 Azure 库中使用所选 VM 类型上 SAP HANA 支持的 SLES4SAP 映像。
   - 选择在步骤 3 中创建的可用性集。 
1. 添加数据磁盘。

> [!IMPORTANT]
> 负载均衡方案中的 NIC 辅助 IP 配置不支持浮动 IP。 有关详细信息，请参阅 [Azure 负载均衡器限制](../../../load-balancer/load-balancer-multivip-overview.md#limitations)。 如果你需要为 VM 提供其他 IP 地址，请部署第二个 NIC。   

> [!Note]
> 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)。  

1. 如果使用标准负载均衡器，请执行以下配置步骤：
   1. 首先创建前端 IP 池：
   
      1. 打开负载均衡器，选择 **前端 IP 池**，然后选择“添加”。
      1. 输入新前端 IP 池的名称（例如 **hana-frontend**）。
      1. 将“分配”设置为“静态”并输入 IP 地址（例如 **10.0.0.13**）。 
      1. 选择“确定”。
      1. 创建新前端 IP 池后，请记下池 IP 地址。
   
   1. 接下来创建后端池：
   
      1. 打开负载均衡器，选择 **后端池**，然后选择“添加”。
      1. 输入新后端池的名称（例如 **hana-backend**）。
      1. 选择“虚拟网络”。
      1. 选择“添加虚拟机”。
      1. 选择“虚拟机”。
      1. 选择 SAP HANA 群集的虚拟机及其 IP 地址。
      1. 选择 **添加**。
   
   1. 接下来创建运行状况探测：
   
      1. 打开负载均衡器，选择 **运行状况探测**，然后选择“添加”。
      1. 输入新运行状况探测的名称（例如 **hana-hp**）。
      1. 选择“TCP”作为协议，并选择端口 625 **03**。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。 
      1. 选择“确定”。
   
   1. 接下来，创建负载均衡规则：
   
      1. 打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
      1. 输入新负载均衡器规则的名称（例如 hana-lb）。
      1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 hana-frontend、hana-backend 和 hana-hp）。
      1. 选择“HA 端口”。
      1. 确保 **启用浮动 IP**。
      1. 选择“确定”。

1. 或者，如果你的方案指示使用基本负载均衡器，请执行以下配置步骤：
   1. 首先创建前端 IP 池：
   
      1. 打开负载均衡器，选择 **前端 IP 池**，然后选择“添加”。
      1. 输入新前端 IP 池的名称（例如 **hana-frontend**）。
      1. 将“分配”设置为“静态”并输入 IP 地址（例如 **10.0.0.13**）。 
      1. 选择“确定”。
      1. 创建新前端 IP 池后，请记下池 IP 地址。
   
   1. 接下来创建后端池：
   
      1. 打开负载均衡器，选择 **后端池**，然后选择“添加”。
      1. 输入新后端池的名称（例如 **hana-backend**）。
      1. 选择“添加虚拟机”。
      1. 选择在步骤 3 中创建的可用性集。
      1. 选择 SAP HANA 群集的虚拟机。
      1. 选择“确定”。
   
   1. 接下来创建运行状况探测：
   
      1. 打开负载均衡器，选择 **运行状况探测**，然后选择“添加”。
      1. 输入新运行状况探测的名称（例如 **hana-hp**）。
      1. 选择“TCP”作为协议，并选择端口 625 **03**。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。 
      1. 选择“确定”。
   
   1. 对于 SAP HANA 1.0，请创建负载均衡规则：
   
      1. 打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
      1. 输入新负载均衡器规则的名称（例如 hana-lb-3 **03** 15）。
      1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
      1. 将“协议”保留设置为“TCP”，输入端口 3 **03** 15。 
      1. 将“空闲超时”增大到 30 分钟。
      1. 确保 **启用浮动 IP**。
      1. 选择“确定”。
      1. 针对端口 3 **03** 17 重复上述步骤。
   
   1. 对于 SAP HANA 2.0，请为系统数据库创建负载均衡规则：
   
      1. 打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
      1. 输入新负载均衡器规则的名称（例如 hana-lb-3 **03** 13）。
      1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
      1. 将“协议”保留设置为“TCP”，输入端口 3 **03** 13。 
      1. 将“空闲超时”增大到 30 分钟。
      1. 确保 **启用浮动 IP**。
      1. 选择“确定”。
      1. 针对端口 3 **03** 14 重复上述步骤。
   
   1. 对于 SAP HANA 2.0，请先为租户数据库创建负载均衡规则：
   
      1. 打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
      1. 输入新负载均衡器规则的名称（例如 hana-lb-3 **03** 40）。
      1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
      1. 将“协议”保留设置为“TCP”，输入端口 3 **03** 40。 
      1. 将“空闲超时”增大到 30 分钟。
      1. 确保 **启用浮动 IP**。
      1. 选择“确定”。
      1. 针对端口 3 **03** 41 和 3 **03** 42 重复上述步骤。

   有关 SAP HANA 所需端口的详细信息，请参阅 [SAP HANA 租户数据库](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6)指南中的[连接到租户数据库](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html)一章或 [SAP 说明 2388694][2388694]。

> [!IMPORTANT]
> 请勿在放置于 Azure 负载均衡器之后的 Azure VM 上启用 TCP 时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数“net.ipv4.tcp_timestamps”设置为“0”。 有关详细信息，请参阅[负载均衡器运行状况探测](../../../load-balancer/load-balancer-custom-probe-overview.md)。
> 另请参阅 SAP 说明 [2382421](https://launchpad.support.sap.com/#/notes/2382421)。 

## <a name="create-a-pacemaker-cluster"></a>创建 Pacemaker 群集

按照[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](high-availability-guide-suse-pacemaker.md) 中的步骤为此 HANA 服务器创建一个基本 Pacemaker 群集。 还可对 SAP HANA 和 SAP NetWeaver (A)SCS 使用同一 Pacemaker 群集。

## <a name="install-sap-hana"></a>安装 SAP HANA

本部分中的步骤使用以下前缀：
- **[A]** ：该步骤适用于所有节点。
- **[1]** ：该步骤仅适用于节点 1。
- **[2]** ：该步骤仅适用于 Pacemaker 群集的节点 2。

1. **[A]** 设置磁盘布局：**逻辑卷管理器 (LVM)** 。

   我们建议对存储数据和日志文件的卷使用 LVM。 以下示例假设虚拟机上附加了四个用于创建两个卷的数据磁盘。

   列出所有可用的磁盘：

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   示例输出：

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   为想要使用的所有磁盘创建物理卷：

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   为数据文件创建卷组。 将一个卷组用于日志文件，将另一个卷组用于 SAP HANA 的共享目录：

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   创建逻辑卷。 线性卷是使用不带 `-i` 开关的 `lvcreate` 创建的。 我们建议你创建一个带区卷，以获得更好的 I/O 性能，并将带区大小与 [SAP HANA VM 存储配置](./hana-vm-operations-storage.md)中记录的值保持一致。 `-i` 参数应表示基础物理卷的数量，`-I` 参数则表示带区大小。 在本文档中，两个物理卷用于数据卷，因此 `-i` 开关参数设置为 **2**。 数据卷的带区大小为 256 KiB。 一个物理卷用于日志卷，因此，`-i` 或 `-I` 开关不会显式用于日志卷命令。  

   > [!IMPORTANT]
   > 对每个数据、日志或共享卷使用多个物理卷时，请使用 `-i` 开关，并将其设置为基础物理卷的数量。 创建带区卷时，请使用 `-I` 开关来指定带区大小。  
   > 有关建议的存储配置，包括带区大小和磁盘数量，请参阅 [SAP HANA VM 存储配置](./hana-vm-operations-storage.md)。  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   创建装载目录，并复制所有逻辑卷的 UUID：

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   创建三个逻辑卷的 `fstab` 条目：       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   将以下行插入 `/etc/fstab` 文件：      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   装载新卷：

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** 设置磁盘布局：**无格式磁盘**。

   对于演示系统，可将 HANA 数据和日志文件放在一个磁盘上。 在 /dev/disk/azure/scsi1/lun0 上创建一个分区，并使用 XFS 格式化该分区：

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   将以下行插入 /etc/fstab 文件：

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   创建目标目录并装载磁盘：

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** 为所有主机设置主机名解析。

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts 文件。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名：

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   将以下行插入 /etc/hosts 文件。 根据环境更改 IP 地址和主机名：

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** 安装 SAP HANA 高可用性包：

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

若要安装 SAP HANA 系统复制，请遵循 [SAP HANA SR 性能优化方案指南](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)的第 4 章。

1. **[A]** 从 HANA DVD 运行 **hdblcm** 程序。 在提示符下输入以下值：
   * 选择安装：输入 **1**。
   * 选择要安装的其他组件：输入 **1**。
   * 输入安装路径 [/hana/shared]：选择 Enter。
   * 输入本地主机名 [..]：选择 Enter。
   * 是否要将其他主机添加到系统? (y/n) [n]：选择 Enter。
   * 输入 SAP HANA 系统 ID：输入 HANA 的 SID，例如：**HN1**。
   * 输入实例编号 [00]：输入 HANA 实例编号。 如果使用了 Azure 模板或者遵循了本文的手动部署部分，请输入 **03**。
   * 选择数据库模式 / 输入索引 [1]：选择 Enter。
   * 选择系统用途/输入索引 [4]：选择系统用途值。
   * 输入数据卷的位置 [/hana/data/HN1]：选择 Enter。
   * 输入日志卷的位置 [/hana/log/HN1]：选择 Enter。
   * 是否限制最大内存分配? [n]：选择 Enter。
   * 输入主机 '...' [...] 的证书主机名：选择 Enter。
   * 输入 SAP 主机代理用户 (sapadm) 密码：输入主机代理用户密码。
   * 确认 SAP 主机代理用户 (sapadm) 密码：再次输入主机代理用户密码以进行确认。
   * 输入系统管理员 (hdbadm) 密码：输入系统管理员密码。
   * 确认系统管理员 (hdbadm) 密码：再次输入系统管理员密码以进行确认。
   * 输入系统管理员主目录 [/usr/sap/HN1/home]：选择 Enter。
   * 输入系统管理员登录 Shell [/ bin/sh]：选择 Enter。
   * 输入系统管理员用户 ID [1001]：选择 Enter。
   * 输入用户组 (sapsys) 的 ID [79]：选择 Enter。
   * 输入数据库用户 (SYSTEM) 密码：输入数据库用户密码。
   * 确认数据库用户 (SYSTEM) 密码：再次输入数据库用户密码以进行确认。
   * 重新引导计算机后是否重新启动系统? [n]：选择 Enter。
   * 是否继续? (y/n)：验证摘要。 按 **y** 继续。

1. **[A]** 升级 SAP 主机代理。

   从 [SAP 软件中心][sap-swcenter]下载最新的 SAP 主机代理存档，并运行以下命令来升级代理。 替换存档的路径，使其指向已下载的文件：

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>配置 SAP HANA 2.0 系统复制

本部分中的步骤使用以下前缀：

* **[A]** ：该步骤适用于所有节点。
* **[1]** ：该步骤仅适用于节点 1。
* **[2]** ：该步骤仅适用于 Pacemaker 群集的节点 2。

1. **[1]** 创建租户数据库。

   如果使用的是 SAP HANA 2.0 或 MDC，请为 SAP NetWeaver 系统创建一个租户数据库。 将 **NW1** 替换为 SAP 系统的 SID。

   以 <hanasid\>adm 身份执行以下命令：

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** 在第一个节点上配置系统复制：

   以 < hanasid\>adm 身份备份数据库：

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   将系统 PKI 文件复制到辅助站点：

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   创建主站点：

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** 在第二个节点上配置系统复制：
    
   注册第二个节点以启动系统复制。 以 <hanasid\>adm 身份运行以下命令：

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>配置 SAP HANA 1.0 系统复制

本部分中的步骤使用以下前缀：

* **[A]** ：该步骤适用于所有节点。
* **[1]** ：该步骤仅适用于节点 1。
* **[2]** ：该步骤仅适用于 Pacemaker 群集的节点 2。

1. **[1]** 创建所需的用户。

   以 root 身份运行以下命令。 确保将粗体字符串（HANA 系统 ID **HN1** 和实例编号 **03**）替换为 SAP HANA 安装的值。

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** 创建密钥存储条目。

   以 root 身份运行以下命令来创建新的密钥存储条目：

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** 备份数据库。

   以 root 身份备份数据库：

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   如果使用的是多租户安装，则还需要备份租户数据库：

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** 在第一个节点上配置系统复制。

   以 <hanasid\>adm 身份创建主站点：

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** 在第二个节点上配置系统复制。

   以 <hanasid\>adm 身份注册辅助站点：

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>实现 Python 系统复制挂钩 SAPHanaSR

这是优化与群集的集成并在需要进行群集故障转移时改进检测的重要步骤。 强烈建议配置 SAPHanaSR python 挂钩。    

1. [A] 安装 HANA“系统复制挂钩”。 需要在两个 HANA DB 节点上安装挂钩。           

   > [!TIP]
   > 验证包 SAPHanaSR 至少为版本 0.153，以便能够使用 SAPHanaSR Python 挂钩功能。       
   > 只能对 HANA 2.0 实现 python 挂钩。        

   1. 以 `root` 身份准备挂钩。  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. 在两个节点上停止 HANA。 以 <sid\>adm 身份执行：  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. 在每个群集节点上调整 `global.ini`。  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. [A] 群集需要在每个群集节点上为 <sid\>adm 配置 sudoers。 在此示例中，通过创建新文件来实现此目的。 以 `root` 身份执行命令。    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```
有关实现 SAP HANA 系统复制挂钩的更多详细信息，请参阅[设置 HANA HA/DR 提供程序](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-sr-guide-PerfOpt-12/index.html#_set_up_sap_hana_hadr_providers)。  

3. [A] 在两个节点上启动 SAP HANA。 以 <sid\>adm 身份执行。  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** 验证是否安装了挂钩。 在活动 HANA 系统复制站点上以 <sid\>adm 的身份执行。   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-08 22:18:15.877583 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:18:46.531564 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:21:26.816573 ha_dr_SAPHanaSR SOK

    ```

## <a name="create-sap-hana-cluster-resources"></a>创建 SAP HANA 群集资源

首先，创建 HANA 拓扑。 在其中一个 Pacemaker 群集节点上运行以下命令：

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

接着，创建 HANA 资源：

> [!IMPORTANT]
> 最近的测试表明，由于积压工作 (backlog) 及其仅处理一个连接的限制，netcat 停止响应请求。 netcat 资源停止侦听 Azure 负载均衡器请求，并且浮动 IP 变为不可用。  
> 对于现有 Pacemaker 群集，我们过去建议将 netcat 替换为 socat。 当前，我们建议使用 azure-lb 资源代理，它是包 resource-agents 的一部分，具有以下包版本要求：
> - 对于 SLES 12 SP4/SP5，版本必须至少为 resource-agents-4.3.018.a7fb5035-3.30.1。  
> - 对于 SLES 15/15 SP1，版本必须至少为 resource-agents-4.3.0184.6ee15eb2-4.13.1。  
>
> 请注意，更改将需要短暂的停机时间。  
> 对于现有的 Pacemaker 群集，如果已经按照 [Azure 负载平衡器检测强化](https://www.suse.com/support/kb/doc/?id=7024128)中所述将配置更改为使用 socat，则无需立即切换到 azure-lb 资源代理。


> [!NOTE]
> 本文包含对术语“主”和“从”的引用，Microsoft 不再使用这些术语 。 当这些术语在软件中被删除后，我们会将它们从本文中删除。

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>在 Pacemaker 群集中配置启用 HANA 活动/读取的系统复制

从 SAP HANA 2.0 SPS 01 开始，SAP 允许对 SAP HANA 系统复制设置活动/读取启用，在这种情况下，SAP HANA 系统复制的辅助系统可主动用于读取密集型工作负荷。 若要在群集中支持此类设置，需要提供第二个虚拟 IP 地址，以便客户端能够访问启用辅助启用读取的 SAP HANA 数据库。 若要确保辅助复制站点在接管后仍可以访问，群集需要将虚拟 IP 地址与 SAPHana 资源的辅助地址一起移动。

本部分介绍使用第二个虚拟 IP 在 SUSE 高可用性群集中管理启用 HANA 活动/读取系统复制所需的其他步骤。    
在继续下一步之前，请确保已完全配置 SUSE 高可用性群集管理 SAP HANA 数据库，如上文所述。  

![具备启用读取辅助的 SAP HANA 高可用性](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>在 Azure 负载均衡器中进行其他设置，以实现启用活动/读取设置

若要继续执行预配第二个虚拟 IP 的其他步骤，请确保已配置[手动部署](#manual-deployment)部分所述的 Azure 负载均衡器。

1. 对于“标准”负载均衡器，请在前面部分中创建的同一负载平衡器上按照以下附加步骤进行操作。

   a. 创建第二个前端 IP 池： 

   - 打开负载均衡器，选择 **前端 IP 池**，然后选择“添加”。
   - 输入第二个新前端 IP 池的名称（例如“hana-secondaryIP”）。
   - 将“分配”设置为“静态”并输入 IP 地址（例如，“10.0.0.14”）  。
   - 选择“确定”。
   - 创建新前端 IP 池后，请记下前端 IP 地址。

   b. 接下来创建运行状况探测：

   - 打开负载均衡器，选择 **运行状况探测**，然后选择“添加”。
   - 输入新运行状况探测的名称（例如“hana-secondaryhp”）。
   - 选择“TCP”作为协议，并选择端口“62603” 。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。 
   - 选择“确定”。

   c. 接下来，创建负载均衡规则：

   - 打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
   - 输入新负载均衡器规则的名称（例如“hana-secondarylb”）。
   - 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如“hana-secondaryIP”、“hana-backend”和“hana-secondaryhp”）。
   - 选择“HA 端口”。
   - 将“空闲超时”增大到 30 分钟。
   - 确保 **启用浮动 IP**。
   - 选择“确定”。

### <a name="configure-hana-activeread-enabled-system-replication"></a>配置启用 HANA 活动/读取的系统复制

[配置 SAP HANA 2.0 系统复制](#configure-sap-hana-20-system-replication)部分介绍了配置 HANA 系统复制的步骤。 如果部署已启用读取的辅助方案，则在第二个节点上配置系统复制的同时，请以“hanasid”adm 身份执行以下命令：

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>为已启用活动/读取设置添加辅助虚拟 IP 地址资源

可以通过以下命令配置第二个虚拟 IP 和适当的归置约束：

```
crm configure property maintenance-mode=true

crm configure primitive rsc_secip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
 meta target-role="Started" \
 operations \$id="rsc_secip_HN1_HDB03-operations" \
 op monitor interval="10s" timeout="20s" \
 params ip="10.0.0.14"

crm configure primitive rsc_secnc_HN1_HDB03 azure-lb port=62603 \
 meta resource-stickiness=0

crm configure group g_secip_HN1_HDB03 rsc_secip_HN1_HDB03 rsc_secnc_HN1_HDB03

crm configure colocation col_saphana_secip_HN1_HDB03 4000: g_secip_HN1_HDB03:Started \
 msl_SAPHana_HN1_HDB03:Slave 

crm configure property maintenance-mode=false
```
请确保群集状态正常，并且所有资源都已启动。 第二个虚拟 IP 将与 SAPHana 辅助资源一起在辅助站点上运行。

```
sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
# Resource Group: g_secip_HN1_HDB03:
#     rsc_secip_HN1_HDB03       (ocf::heartbeat:IPaddr2):        Started hn1-db-1
#     rsc_secnc_HN1_HDB03       (ocf::heartbeat:azure-lb):       Started hn1-db-1

```

在下一部分中，可以找到要执行的典型故障转移测试组。

请注意第二个虚拟 IP 的行为，同时测试使用已启用读取辅助配置的 HANA 群集：

1. 将“SAPHana_HN1_HDB03”群集资源迁移到“hn1-db-1”时，第二个虚拟 IP 将移到另一个服务器“hn1-db-0”。 如果已配置 AUTOMATED_REGISTER = "false"，且未自动注册 HANA 系统复制，则第二个虚拟 IP 将在“hn1-db-0”上运行，因为服务器可用且群集服务处于联机状态。  

2. 测试服务器故障时，第二个虚拟 IP 资源 (“rsc_secip_HN1_HDB03”) 和 Azure 负载均衡器端口资源 (“rsc_secnc_HN1_HDB03”) 将在主服务器上与主虚拟 IP 资源一起运行。 当辅助服务器关闭时，连接到启用读取的 HANA 数据库的应用程序将连接到主 HANA 数据库。 此行为是预期行为，因为当辅助服务器不可用时，不希望连接到已启用读取的 HANA 数据库的应用程序无法访问。
  
3. 当辅助服务器可用且群集服务处于联机状态时，第二个虚拟 IP 和端口资源会自动转移到辅助服务器，即使 HANA 系统复制可能无法注册为辅助服务器。 你需要确保在启动该服务器上的群集服务之前，将辅助 HANA 数据库注册为已启用读取。 可以通过设置参数 AUTOMATED_REGISTER = true 来将 HANA 实例群集资源配置为自动注册辅助副本。       

4. 在故障转移和回退过程中，使用第二个虚拟 IP 连接到 HANA 数据库的应用程序的现有连接可能会中断。  

## <a name="test-the-cluster-setup"></a>测试群集设

本部分介绍如何测试设置。 每项测试都假设你是 root 用户，且 SAP HANA 主机在 **hn1-db-0** 虚拟机上运行。

### <a name="test-the-migration"></a>测试迁移

在开始测试之前，请确保 Pacemaker 没有任何失败的操作（通过 crm_mon -r 检查）、没有任何意外的位置约束（例如迁移测试的遗留内容），并且 HANA 处于同步状态，例如，使用 SAPHanaSR-showAttr：

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr
Sites    srHook
----------------
SITE2    SOK

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

可通过执行以下命令来迁移 SAP HANA 主控节点：

<pre><code>crm resource move msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b> force
</code></pre>

如果设置了 `AUTOMATED_REGISTER="false"`，则此命令序列应将 SAP HANA 主控节点以及包含虚拟 IP 地址的组迁移到 hn1-db-1。

完成迁移后，crm_mon -r 输出将如下所示：

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

hn1-db-0 上的 SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

迁移过程将创建位置约束，需要再次删除这些约束：

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource clear msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

此外，还需要清理辅助节点资源的状态：

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

使用 crm_mon -r 监视 HANA 资源的状态。 在 hn1-db-0 上启动 HANA 后，输出应如下所示

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>测试 Azure 隔离代理（非 SBD）

可以通过禁用 hn1-db-0 节点上的网络接口来测试 Azure 隔离代理的设置。

<pre><code>sudo ifdown eth0
</code></pre>

现在，虚拟机应会根据群集配置重启或停止。
如果将 `stonith-action` 设置为 off，则虚拟机会停止，资源将迁移到正在运行的虚拟机。

如果设置 `AUTOMATED_REGISTER="false"`，则再次启动虚拟机后，SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>测试 SBD 隔离

可以通过终止 inquisitor 进程来测试 SBD 的设置。

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

应重新启动群集节点 hn1-db-0。 Pacemaker 服务随后可能不会启动。 请务必将其重新启动。

### <a name="test-a-manual-failover"></a>测试手动故障转移

可以通过停止 hn1-db-0 节点上的 `pacemaker` 服务来测试手动故障转移：

<pre><code>service pacemaker stop
</code></pre>

故障转移后，可以再次启动该服务。 如果设置了 `AUTOMATED_REGISTER="false"`，则 hn1-db-0 节点上的 SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE 测试

> [!IMPORTANT]
> 请确保你选择的 OS 已经过 SAP 针对你使用的特定 VM 类型上的 SAP HANA 进行的认证。 可以在[经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中查找获得 SAP HANA 认证的 VM 类型和这些 VM 类型的 OS 版本列表。 请确保单击进入列出的 VM 类型的详细信息，以获取特定 VM 类型的 OS 版本支持的 SAP HANA 完整列表

根据具体的用例，运行“SAP HANA SR 性能优化方案”或“SAP HANA SR 成本优化方案”指南中列出的所有测试用例。 可在 [SLES for SAP 最佳做法页][sles-for-sap-bp]上找到这些指南。

以下测试是“SUSE Linux Enterprise Server for SAP Applications 12 SP1 的 SAP HANA SR 性能优化方案”指南测试说明的副本。 如需最新版本，另请阅读指南本身。 在开始测试之前，始终确保 HANA 已同步，另请确保 Pacemaker 的配置正确。

以下测试说明假设 PREFER_SITE_TAKEOVER ="true"，AUTOMATED_REGISTER ="false"。
注意：以下测试需按顺序运行，并依赖于先前测试的退出状态。

1. 测试 1：在节点 1 上停止主数据库

   开始测试之前的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   在节点 hn1-db-0 上，以 <hanasid\>adm 的身份运行以下命令：

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker 应检测到已停止的 HANA 实例，并故障转移到另一个节点。 完成故障转移后，节点 hn1-db-0 上的 HANA 实例将会停止，因为 Pacemaker 不会自动将该节点注册为 HANA 辅助节点。

   运行以下命令将节点 hn1-db-0 注册为辅助节点，并清理已失败的资源。

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   测试之后的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 测试 2：在节点 2 上停止主数据库

   开始测试之前的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   在节点 hn1-db-1 上，以 <hanasid\>adm 的身份运行以下命令：

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker 应检测到已停止的 HANA 实例，并故障转移到另一个节点。 完成故障转移后，节点 hn1-db-1 上的 HANA 实例将会停止，因为 Pacemaker 不会自动将该节点注册为 HANA 辅助节点。

   运行以下命令将节点 hn1-db-1 注册为辅助节点，并清理已失败的资源。

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   测试之后的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 测试 3：在节点上崩溃主数据库

   开始测试之前的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   在节点 hn1-db-0 上，以 <hanasid\>adm 的身份运行以下命令：

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker 应检测到已终止的 HANA 实例，并故障转移到另一个节点。 完成故障转移后，节点 hn1-db-0 上的 HANA 实例将会停止，因为 Pacemaker 不会自动将该节点注册为 HANA 辅助节点。

   运行以下命令将节点 hn1-db-0 注册为辅助节点，并清理已失败的资源。

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   测试之后的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 测试 4：在节点 2 上崩溃主数据库

   开始测试之前的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   在节点 hn1-db-1 上，以 <hanasid\>adm 的身份运行以下命令：

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker 应检测到已终止的 HANA 实例，并故障转移到另一个节点。 完成故障转移后，节点 hn1-db-1 上的 HANA 实例将会停止，因为 Pacemaker 不会自动将该节点注册为 HANA 辅助节点。

   运行以下命令将节点 hn1-db-1 注册为辅助节点，并清理已失败的资源。

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   测试之后的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 测试 5：崩溃主站点节点（节点 1）

   开始测试之前的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   在节点 hn1-db-0 上，以 root 身份运行以下命令：

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker 应检测到已终止的群集节点，并隔离该节点。 隔离节点后，Pacemaker 将触发 HANA 实例的接管。 重新启动隔离的节点后，Pacemaker 不会自动启动。

   运行以下命令启动 Pacemaker，清理节点 hn1-db-0 的 SBD 消息，将节点 hn1-db-0 注册为辅助节点，并清理已失败的资源。

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   测试之后的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 测试 6：崩溃辅助站点节点（节点 2）

   开始测试之前的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   在节点 hn1-db-1 上，以 root 身份运行以下命令：

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker 应检测到已终止的群集节点，并隔离该节点。 隔离节点后，Pacemaker 将触发 HANA 实例的接管。 重新启动隔离的节点后，Pacemaker 不会自动启动。

   运行以下命令启动 Pacemaker，清理节点 hn1-db-1 的 SBD 消息，将节点 hn1-db-1 注册为辅助节点，并清理已失败的资源。

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   测试之后的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 测试 7：停止节点 2 上的辅助数据库

   开始测试之前的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   在节点 hn1-db-1 上，以 <hanasid\>adm 的身份运行以下命令：

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker 将检测到已停止的 HANA 实例，并将节点 hn1-db-1 上的资源标记为失败。 Pacemaker 应会自动重启 HANA 实例。 运行以下命令来清理失败状态。

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   测试之后的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 测试 8：崩溃节点 2 上的辅助数据库

   开始测试之前的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   在节点 hn1-db-1 上，以 <hanasid\>adm 的身份运行以下命令：

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker 将检测到已终止的 HANA 实例，并将节点 hn1-db-1 上的资源标记为失败。 运行以下命令来清理失败状态。 然后，Pacemaker 应会自动重启 HANA 实例。

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   测试之后的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 测试 9：崩溃运行辅助 HANA 数据库的辅助站点节点（节点 2）

   开始测试之前的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   在节点 hn1-db-1 上，以 root 身份运行以下命令：

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker 应检测到已终止的群集节点，并隔离该节点。 重新启动隔离的节点后，Pacemaker 不会自动启动。

   运行以下命令启动 Pacemaker，清理节点 hn1-db-1 的 SBD 消息，并清理已失败的资源。

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   测试之后的资源状态：

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]