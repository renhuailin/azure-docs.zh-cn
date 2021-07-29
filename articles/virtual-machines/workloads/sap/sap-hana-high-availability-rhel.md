---
title: RHEL 上 Azure VM 中 SAP HANA 的高可用性 | Microsoft Docs
description: 在 Azure 虚拟机 (VM) 上建立 SAP HANA 的高可用性。
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
ms.openlocfilehash: 435528f7338657bc7e7d486a481cdf0ce48f4d38
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108142884"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上 Azure VM 中 SAP HANA 的高可用性

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

对于本地开发，可以使用 HANA 系统复制或共享存储来建立 SAP HANA 的高可用性。
在 Azure 虚拟机 (VM) 上，Azure 上的 HANA 系统复制是唯一受支持的高可用性功能。
SAP HANA 复制由一个主节点和至少一个辅助节点组成。 对主节点上数据所做的更改以同步或异步方式复制到辅助节点。

本文介绍如何部署和配置虚拟机、安装群集框架，以及安装和配置 SAP HANA 系统复制。
在示例配置和安装命令中，使用了实例编号 **03** 和 HANA 系统 ID **HN1**。

请先阅读以下 SAP 说明和文档：

* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表。
  * Azure VM 大小的重要容量信息。
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合。
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本。
* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2002167] 包含适用于 Red Hat Enterprise Linux 的建议 OS 设置
* SAP 说明 [2009879] 包含适用于 Red Hat Enterprise Linux 的 SAP HANA 准则
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署（本文）][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101)（Pacemaker 群集中的 SAP HANA 系统复制）
* 通用 RHEL 文档
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（高可用性附加产品管理）
  * [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure 特定的 RHEL 文档：
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/solutions/3193782)（在 Red Hat Enterprise Linux 上安装要在 Microsoft Azure 中使用的 SAP HANA）

## <a name="overview"></a>概述

为实现高可用性，SAP HANA 安装在两台虚拟机上。 数据将使用 HANA 系统复制进行复制。

![SAP HANA 高可用性概述](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA 系统复制设置使用专用的虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 以下列表显示负载均衡器的配置：

* 前端配置：hn1-db 的 IP 地址 10.0.0.13
* 后端配置：连接到应当作为 HANA 系统复制的一部分的所有虚拟机的主网络接口
* 探测端口：端口 62503
* 负载均衡规则：30313 TCP、30315 TCP、30317 TCP、30340 TCP、30341 TCP、30342 TCP

## <a name="deploy-for-linux"></a>为 Linux 部署

Azure 市场中包含适用于 SUSE Linux Red Hat Enterprise Linux 7.4 for SAP HANA 的映像，可以用于部署新的虚拟机。

### <a name="deploy-with-a-template"></a>使用模板进行部署

可以使用 GitHub 上的某个快速入门模板部署全部所需的资源。 该模板将部署虚拟机、负载均衡器、可用性集，等等。
若要部署模板，请执行以下步骤：

1. 在 Azure 门户中打开[数据库模板][template-multisid-db]。
1. 输入以下参数：
    * **SAP 系统 ID**：输入要安装的 SAP 系统的 SAP 系统 ID。 该 ID 将用作所要部署的资源的前缀。
    * **OS 类型**：选择一个 Linux 发行版。 对于此示例，请选择“RHEL 7”。
    * **数据库类型**：选择“HANA”。
    * **SAP 系统大小**：输入新系统将提供的 SAPS 数量。 如果不确定系统需要多少 SAPS，请咨询 SAP 技术合作伙伴或系统集成商。
    * **系统可用性**：选择“HA”。
    * **管理员用户名、管理员密码或 SSH 密钥**：创建可用于登录计算机的新用户。
    * **子网 ID**：如果要将 VM 部署到现有 VNet 中，并且该 VNet 中已定义了 VM 应分配到的子网，请指定该特定子网的 ID。 ID 通常类似于“/subscriptions/\<subscription ID>/resourceGroups/\<resource group name>/providers/Microsoft.Network/virtualNetworks/\<virtual network name>/subnets/\<subnet name>”。 如果要创建新的虚拟网络，请将其留空

### <a name="manual-deployment"></a>手动部署

1. 创建资源组。
1. 创建虚拟网络。
1. 创建可用性集。  
   设置最大更新域。
1. 创建负载均衡器（内部）。 建议使用[标准负载均衡器](../../../load-balancer/load-balancer-overview.md)。
   * 选择在步骤 2 中创建的虚拟网络。
1. 创建虚拟机 1。  
   最低使用 Red Hat Enterprise Linux 7.4 for SAP HANA。 本示例使用 Red Hat Enterprise Linux 7.4 for SAP HANA 映像 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 选择在步骤 3 中创建的可用性集。
1. 创建虚拟机 2。  
   最低使用 Red Hat Enterprise Linux 7.4 for SAP HANA。 本示例使用 Red Hat Enterprise Linux 7.4 for SAP HANA 映像 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 选择在步骤 3 中创建的可用性集。
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
      1. 将“空闲超时”增大到 30 分钟。
      1. 确保 **启用浮动 IP**。
      1. 选择“确定”。


1. 或者，如果你的方案指示使用基本负载均衡器，请执行以下配置步骤：
   1. 配置负载均衡器。 首先创建前端 IP 池：

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

## <a name="install-sap-hana"></a>安装 SAP HANA

本部分中的步骤使用以下前缀：

* **[A]** ：该步骤适用于所有节点。
* **[1]** ：该步骤仅适用于节点 1。
* **[2]** ：该步骤仅适用于 Pacemaker 群集的节点 2。

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

1. **[A]** RHEL for HANA 配置

   如 <https://access.redhat.com/solutions/2447641> 和以下 SAP 说明中所述配置 RHEL：  
   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)（2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置）
   - [2777782 - SAP HANA DB: Recommended OS Settings for RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)（2777782 - SAP HANA DB：RHEL 8 的建议 OS 设置）
   - [2455582 - Linux: Running SAP applications compiled with GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582)（2455582 - Linux：运行使用 GCC 6.x 编译的 SAP 应用程序）
   - [2593824 - Linux: Running SAP applications compiled with GCC 7.x](https://launchpad.support.sap.com/#/notes/2593824)（2593824 - Linux：运行使用 GCC 7.x 编译的 SAP 应用程序） 
   - [2886607 - Linux: Running SAP applications compiled with GCC 9.x](https://launchpad.support.sap.com/#/notes/2886607)（2886607 - Linux：运行使用 GCC 9.x 编译的 SAP 应用程序）

1. **[A]** 安装 SAP HANA

   要安装 SAP HANA 系统复制，请按照 <https://access.redhat.com/articles/3004101> 执行操作。

   * 从 HANA DVD 运行 hdblcm 程序。 在提示符下输入以下值：
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

1. **[A]** 配置防火墙

   为 Azure 负载均衡器探测端口创建防火墙规则。

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>配置 SAP HANA 2.0 系统复制

本部分中的步骤使用以下前缀：

* **[A]** ：该步骤适用于所有节点。
* **[1]** ：该步骤仅适用于节点 1。
* **[2]** ：该步骤仅适用于 Pacemaker 群集的节点 2。

1. **[A]** 配置防火墙

   创建防火墙规则以允许 HANA 系统复制和客户端流量。 [所有 SAP 产品的 TCP/IP 端口](https://help.sap.com/viewer/ports)上均列出了所需端口。 以下命令是允许 HANA 2.0 系统复制和到数据库 SYSTEMDB、HN1 和 NW1 的流量的示例。

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** 创建租户数据库。

   如果使用的是 SAP HANA 2.0 或 MDC，请为 SAP NetWeaver 系统创建一个租户数据库。 将 **NW1** 替换为 SAP 系统的 SID。

   以 < hanasid\>adm 身份执行以下命令：

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

1. **[1]** 检查复制状态

   检查复制状态并等待，直到所有数据库都保持同步。如果状态一直“未知”，请检查防火墙设置。

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>配置 SAP HANA 1.0 系统复制

本部分中的步骤使用以下前缀：

* **[A]** ：该步骤适用于所有节点。
* **[1]** ：该步骤仅适用于节点 1。
* **[2]** ：该步骤仅适用于 Pacemaker 群集的节点 2。

1. **[A]** 配置防火墙

   创建防火墙规则以允许 HANA 系统复制和客户端流量。 [所有 SAP 产品的 TCP/IP 端口](https://help.sap.com/viewer/ports)上均列出了所需端口。 以下命令是允许 HANA 2.0 系统复制的示例。 针对 SAP HANA 1.0 安装对其进行调整。

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>创建 Pacemaker 群集

按照[在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker](high-availability-guide-rhel-pacemaker.md) 的步骤，创建适用于此 HANA 服务器的基本 Pacemaker。

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>实现 Python 系统复制挂钩 SAPHanaSR

这是优化与群集的集成并在需要进行群集故障转移时改进检测的重要步骤。 强烈建议配置 SAPHanaSR python 挂钩。    

1. [A] 安装 HANA“系统复制挂钩”。 需要在两个 HANA DB 节点上安装挂钩。           

   > [!TIP]
   > 只能对 HANA 2.0 实现 python 挂钩。        

   1. 以 `root` 身份准备挂钩。  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
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
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

有关 SAP HANA 系统复制挂钩实现的更多详细信息，请参阅[启用 SAP HA/DR 提供程序挂钩](https://access.redhat.com/articles/3004101#enable-srhook)。  
 
## <a name="create-sap-hana-cluster-resources"></a>创建 SAP HANA 群集资源

在所有节点上安装 SAP HANA 资源代理。 确保启用包含程序包的存储库。 如果正在使用已启用 RHEL 8.x HA 的映像，则不需要启用其他存储库。  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

接下来，创建 HANA 拓扑。 在其中一个 Pacemaker 群集节点上运行以下命令：

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

接着，创建 HANA 资源。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。

如果在 RHEL 7.x 上构建群集，请使用以下命令：  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

如果在 RHEL 8.x 上构建群集，请使用以下命令：  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable meta notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

> [!NOTE]
> 上述配置中的超时只是示例，可能需要根据特定的 HANA 设置进行调整。 例如，如果启动 SAP HANA 数据库需要较长时间，则可能需要增加启动超时。  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>


## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>在 Pacemaker 群集中配置启用 HANA 活动/读取的系统复制

从 SAP HANA 2.0 SPS 01 开始，SAP 允许对 SAP HANA 系统复制使用启用活动/读取的设置，在这种情况下，SAP HANA 系统复制的辅助系统可积极用于读取密集型工作负载。 若要在群集中支持此类设置，需要提供第二个虚拟 IP 地址，以便客户端能够访问启用了辅助读取的 SAP HANA 数据库。 若要确保辅助复制站点在接管后仍可以访问，群集需要将虚拟 IP 地址与 SAPHana 资源的辅助地址一起移动。

本部分介绍使用第二个虚拟 IP 在 Red Hat 高可用性群集中管理启用 HANA 活动/读取系统复制所需的其他步骤。    

在继续下一步之前，请确保已完全配置 Red Hat 高可用性群集管理 SAP HANA 数据库，如上文所述。  

![具备启用读取辅助的 SAP HANA 高可用性](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>在 Azure 负载均衡器中进行其他设置，以实现启用活动/读取设置

若要继续执行预配第二个虚拟 IP 的其他步骤，请确保已配置[手动部署](#manual-deployment)部分所述的 Azure 负载均衡器。

1. 对于“标准”负载均衡器，请在前面部分中创建的同一负载平衡器上按照以下附加步骤进行操作。

   a. 创建第二个前端 IP 池： 

   - 打开负载均衡器，选择 **前端 IP 池**，然后选择“添加”。
   - 输入第二个新前端 IP 池的名称（例如“hana-secondaryIP”）。
   - 将“分配”设置为“静态”并输入 IP 地址（例如，“10.0.0.14”）  。
   - 选择“确定”。
   - 创建新前端 IP 池后，请记下池 IP 地址。

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
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.40.0.16"

pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603

pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

RHEL 8.x: 
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-clone 4000
RHEL 7.x:
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-master 4000

pcs property set maintenance-mode=false
```
请确保群集状态正常，并且所有资源都已启动。 第二个虚拟 IP 将与 SAPHana 辅助资源一起在辅助站点上运行。

```
sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full List of Resources:
#   rsc_hdb_azr_agt     (stonith:fence_azure_arm):      Started hn1-db-0
#   Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]:
#     Started: [ hn1-db-0 hn1-db-1 ]
#   Clone Set: SAPHana_HN1_03-clone [SAPHana_HN1_03] (promotable):
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
#   Resource Group: g_ip_HN1_03:
#     nc_HN1_03         (ocf::heartbeat:azure-lb):      Started hn1-db-0
#     vip_HN1_03        (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#   Resource Group: g_secip_HN1_03:
#     secnc_HN1_03      (ocf::heartbeat:azure-lb):      Started hn1-db-1
#     secvip_HN1_03     (ocf::heartbeat:IPaddr2):       Started hn1-db-1
```

在下一部分中，可以找到要执行的典型故障转移测试组。

请注意第二个虚拟 IP 的行为，同时测试使用已启用读取辅助配置的 HANA 群集：

1. 将“SAPHana_HN1_HDB03”群集资源迁移到“hn1-db-1”时，第二个虚拟 IP 将移到另一个服务器“hn1-db-0”。 如果已配置 AUTOMATED_REGISTER = "false"，且未自动注册 HANA 系统复制，则第二个虚拟 IP 将在“hn1-db-0”上运行，因为服务器可用且群集服务处于联机状态。  

2. 测试服务器故障时，第二个虚拟 IP 资源 (“rsc_secip_HN1_HDB03”) 和 Azure 负载均衡器端口资源 (“rsc_secnc_HN1_HDB03”) 将在主服务器上与主虚拟 IP 资源一起运行。  当辅助服务器关闭时，连接到启用读取的 HANA 数据库的应用程序将连接到主 HANA 数据库。 此行为是预期行为，因为当时间辅助服务器不可用时，不希望连接到已启用读取的 HANA 数据库的应用程序无法访问。

3. 当辅助服务器可用且群集服务处于联机状态时，第二个虚拟 IP 和端口资源会自动转移到辅助服务器，即使 HANA 系统复制可能无法注册为辅助服务器。 你需要确保在启动该服务器上的群集服务之前，将辅助 HANA 数据库注册为已启用读取。 可以通过设置参数 AUTOMATED_REGISTER = true 来将 HANA 实例群集资源配置为自动注册辅助副本。
   
4. 在故障转移和回退过程中，使用第二个虚拟 IP 连接到 HANA 数据库的应用程序的现有连接可能会中断。  

## <a name="test-the-cluster-setup"></a>测试群集设

本部分介绍如何测试设置。 在开始测试之前，请确保 Pacemaker 没有任何失败的操作（通过 pcs 状态检查）、没有任何意外的位置约束（例如迁移测试的遗留内容），并且 HANA 处于同步状态，例如，使用 systemReplicationStatus：

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>测试迁移

开始测试之前的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

可通过执行以下命令来迁移 SAP HANA 主控节点：

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

如果设置了 `AUTOMATED_REGISTER="false"`，则此命令应将 SAP HANA 主控节点以及包含虚拟 IP 地址的组迁移到 hn1-db-1。

完成迁移后，“sudo pcs status”输出将如下所示

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

hn1-db-0 上的 SAP HANA 资源将停止。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

迁移过程将创建位置约束，需要再次删除这些约束：

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

使用“pcs status”监视 HANA 资源的状态。 在 hn1-db-0 上启动 HANA 后，输出应如下所示

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>测试 Azure 隔离代理

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。  

开始测试之前的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

可以通过禁用节点上的网络接口（SAP HANA 作为主控节点运行）来测试 Azure 隔离代理的设置。
如需了解如何模拟网络故障，请参阅 [Red Hat 知识库文章 79523](https://access.redhat.com/solutions/79523)。 此示例使用 net_breaker 脚本阻止对网络的所有访问。

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

现在，虚拟机应会根据群集配置重启或停止。
如果将 `stonith-action` 设置为 off，则虚拟机会停止，资源将迁移到正在运行的虚拟机。

如果设置 `AUTOMATED_REGISTER="false"`，则再次启动虚拟机后，SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

测试之后的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>测试手动故障转移

开始测试之前的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

可以通过停止 hn1-db-0 节点上的群集来测试手动故障转移：

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

故障转移后，可以再次启动该群集。 如果设置了 `AUTOMATED_REGISTER="false"`，则 hn1-db-0 节点上的 SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

测试之后的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>测试手动故障转移

开始测试之前的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

可以通过停止 hn1-db-0 节点上的群集来测试手动故障转移：

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP HANA VM 存储配置](./hana-vm-operations-storage.md)