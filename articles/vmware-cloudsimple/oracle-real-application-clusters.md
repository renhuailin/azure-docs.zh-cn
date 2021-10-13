---
title: Azure VMware Solution by CloudSimple - 为 Oracle RAC 优化 CloudSimple 私有云
description: 介绍如何为 Oracle Real Application Cluster (RAC) 的安装和配置部署新群集并优化 VM
author: suzizuber
ms.author: v-szuber
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c6413d6237cb8bab903b3c98a844fe05c943539e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616282"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>优化 CloudSimple 私有云以安装 Oracle RAC

可以在 CloudSimple 私有云环境中部署 Oracle Real Application Clusters (RAC)。 本指南介绍了如何为 Oracle RAC 解决方案部署新群集并优化 VM。 完成本主题中的步骤后，即可安装和配置 Oracle RAC。

## <a name="storage-policy"></a>存储策略

成功实现 Oracle RAC 需要群集中有足够数量的节点。  在 vSAN 存储策略中，故障容许 (FTT) 被应用于用来存储数据库、日志和重做磁盘的数据磁盘。  有效容许故障所需的节点数量为 2N + 1，其中 N 是 FTT 的值。

示例：如果所需的 FTT 为2，则群集中的节点总数必须为 2*2+1 = 5。

## <a name="overview-of-deployment"></a>部署概述

以下部分介绍如何为 Oracle RAC 设置 CloudSimple 私有云环境。

1. 磁盘配置最佳做法
2. 部署 CloudSimple 私有云 vSphere 群集
3. 为 Oracle RAC 设置网络
4. 设置 vSAN 存储策略
5. 创建 Oracle VM 并创建共享 VM 磁盘
6. 设置 VM 到主机的关联规则

## <a name="best-practices-for-disk-configuration"></a>磁盘配置最佳做法

Oracle RAC 虚拟机有多个用于特定功能的磁盘。  共享磁盘装载在 Oracle RAC 群集使用的所有虚拟机上。  操作系统和软件安装磁盘仅在单个虚拟机上装载。  

![Oracle RAC 虚拟机磁盘概述](media/oracle-vm-disks-overview.png)

以下示例使用下表中定义的磁盘。

| 磁盘                                      | 目的                                       | 共享磁盘 |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | 操作系统磁盘                         | 否          |
| GRID                                      | Oracle Grid 软件的安装位置     | 否          |
| DATABASE                                  | Oracle 数据库软件的安装位置 | 否          |
| ORAHOME                                   | Oracle 数据库二进制文件的基位置    | 否          |
| DATA1, DATA2, DATA3, DATA4                | Oracle 数据库文件的存储磁盘   | 是         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | 重做日志磁盘                                | 是         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | 投票磁盘                                  | 是         |
| FRA1, FRA2                                | 快速恢复区域磁盘                      | 是         |

![Oracle 虚拟机磁盘配置](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>虚拟机配置

* 每个虚拟机都配置有四个 SCSI 控制器。
* SCSI 控制器类型设置为 VMware 半虚拟。
* 创建多个虚拟磁盘 (.vmdk)。
* 磁盘装载到不同 SCSI 控制器上。
* 为共享群集磁盘设置多编写器共享类型。
* 定义 vSAN 存储策略，以确保磁盘的高可用性。

### <a name="operating-system-and-software-disk-configuration"></a>操作系统和软件磁盘配置

每个 Oracle 虚拟机均配置有多个磁盘，用于主机操作系统、交换、软件安装和其他 OS 功能。  这些磁盘不会在虚拟机之间共享。  

* 每个虚拟机的三个磁盘配置为虚拟磁盘，并装载在 Oracle RAC 虚拟机上。
    * OS 磁盘
    * 用于存储 Oracle Grid 安装文件的磁盘
    * 用于存储 Oracle 数据库安装文件的磁盘
* 磁盘可以配置为“精简预配”。
* 每个磁盘都装载在第一个 SCSI 控制器上 (SCSI0)。  
* 共享设置为“无共享”。
* 使用 vSAN 策略在存储上定义冗余。  

![显示 Oracle RAC OS 磁盘物理配置的示意图。](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>数据磁盘配置

数据磁盘主要用于存储数据库文件。  

* 四个磁盘配置为虚拟磁盘，并装载在所有 Oracle RAC 虚拟机上。
* 每个磁盘都装载到不同的 SCSI 控制器上。
* 每个虚拟磁盘均配置为“Thick Provision Eager Zeroed”。  
* 共享设置为“多编写器”。  
* 磁盘必须配置为自动存储管理 (ASM) 磁盘组。  
* 使用 vSAN 策略在存储上定义冗余。  
* ASM 冗余设置为“外部”冗余。

![Oracle RAC 数据磁盘组配置](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>重做日志磁盘配置

重做日志文件用于存储数据库所做更改的副本。  如果在发生任何故障后需要恢复数据，则使用日志文件。

* 重做日志磁盘必须配置为多个磁盘组。  
* 六个磁盘在所有 Oracle RAC 虚拟机上创建并装载。
* 磁盘装载到不同 SCSI 控制器上
* 每个虚拟磁盘均配置为“Thick Provision Eager Zeroed”。
* 共享设置为“多编写器”。  
* 磁盘必须配置为两个 ASM 磁盘组。
* 每个 ASM 磁盘组都包含三个磁盘，它们位于不同的 SCSI 控制器上。  
* ASM 冗余设置为“一般”冗余。
* 五个重做日志文件在两个 ASM 重做日志组上创建

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC 重做日志磁盘组配置](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle 投票磁盘配置

投票磁盘提供仲裁磁盘功能作为额外信道，以避免出现任何拆分情况。

* 五个磁盘在所有 Oracle RAC 虚拟机上创建并装载。
* 磁盘装载到一个 SCSI 控制器上
* 每个虚拟磁盘均配置为“Thick Provision Eager Zeroed”。
* 共享设置为“多编写器”。  
* 磁盘必须配置为一个 ASM 磁盘组。  
* ASM 冗余设置为“高”冗余。

![Oracle RAC 投票磁盘组配置](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle 快速恢复区域磁盘配置（可选）

快速恢复区域 (FRA) 是 Oracle ASM 磁盘组管理的文件系统。  FRA 为备份和恢复文件提供共享存储位置。 Oracle 在快速恢复区域中创建存档日志和闪回日志。 Oracle 恢复管理器 (RMAN) 可以选择在快速恢复区域中存储其备份集和映像副本，并在媒体恢复期间还原文件时进行使用。

* 两个磁盘在所有 Oracle RAC 虚拟机上创建并装载。
* 磁盘装载到不同 SCSI 控制器上
* 每个虚拟磁盘均配置为“Thick Provision Eager Zeroed”。
* 共享设置为“多编写器”。  
* 磁盘必须配置为一个 ASM 磁盘组。  
* ASM 冗余设置为“外部”冗余。

![显示 Oracle RAC 投票磁盘组配置的关系图。](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>部署 CloudSimple 私有云 vSphere 群集

若要在私有云上部署 vSphere 群集，请遵循以下过程：

1. 在 CloudSimple 门户中，[创建私有云](create-private-cloud.md)。 CloudSimple 在新创建的私有云中创建一个名为“cloudowner”的默认 vCenter 用户。 有关默认私有云用户和权限模型的详细信息，请参阅[了解私有云权限模型](learn-private-cloud-permissions.md)。  此步骤将为私有云创建主管理群集。

2. 在 CloudSimple 门户中，使用新群集[展开私有云](expand-private-cloud.md)。  此群集将用于部署 Oracle RAC。  根据所需的容错（最少三个节点）选择节点数。

## <a name="set-up-networking-for-oracle-rac"></a>为 Oracle RAC 设置网络

1. 在私有云中，[创建两个 VLAN ](create-vlan-subnet.md)，分别用于 Oracle 公用网络和 Oracle 专用网络，并分配适当的子网 CIDR。
2. 创建 VLAN 后，[在私有云 vCenter 上创建分布式端口组](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)。
3. 在管理群集上为 Oracle 环境设置 [DHCP 和 DNS 服务器虚拟机](dns-dhcp-setup.md)。
4. 在私有云上安装的[ DNS 服务器上配置 DNS 转发](on-premises-dns-setup.md#create-a-conditional-forwarder)。

## <a name="set-up-vsan-storage-policies"></a>设置 vSAN 存储策略

vSAN 策略为 VM 磁盘上存储的数据定义故障容许和磁盘条带化。  创建 VM 时，必须在 VM 磁盘上应用创建的存储策略。

1. [登录到私有云的 vSphere 客户端](./vcenter-access.md)。
2. 在顶部菜单中，选择“策略和配置文件”。
3. 在左侧菜单中，选择“VM 存储策略”，然后选择“创建 VM 存储策略”。
4. 输入有意义的策略名称，然后单击“下一步”。
5. 在“策略结构”部分，选择“启用 vSAN 存储的规则”，然后单击“下一步”。
6. 在“vSAN” > “可用性”部分中，为“站点灾难容错”选择“无”。 对于故障容许，请为所需的 FTT 选择“RAID 镜像”选项。
    ![vSAN 设置](media/oracle-rac-storage-wizard-vsan.png)。
7. 在“高级”部分中，选择每个对象的磁盘条带数。 对于对象空间保留，请选择“厚预配”。 选择“禁用对象校验和”。 单击“下一步”。
8. 按照屏幕上的说明查看兼容 vSAN 数据存储的列表，检查设置，然后完成安装。

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>创建 Oracle VM 并为 Oracle 创建共享 VM 磁盘

若要为 Oracle 创建 VM，请克隆现有 VM 或创建新的 VM。  本部分介绍如何创建新的 VM，然后将其克隆，以便在安装基本操作系统后创建另一个 VM。  创建 VM 后，可以为其创建添加磁盘。  Oracle 群集使用共享磁盘来存储数据、日志和重做日志。

### <a name="create-vms"></a>创建 VM

1. 在 vCenter 中，单击“主机和群集”图标。 选择为 Oracle 创建的群集。
2. 右键单击该群集，然后选择“新建虚拟机”。
3. 选择“新建虚拟机”，然后选择“下一步”。 
4. 为虚拟机命名，选择 Oracle VM 的位置，然后单击“下一步”。
5. 选择群集资源，然后单击“下一步”。
6. 选择群集的 vSAN 数据存储，然后单击“下一步”。
7. 保留默认的 ESXi 6.5 兼容性选择，并单击“下一步”。
8. 选择要创建的 VM 的 ISO 来宾操作系统，然后单击“下一步”。
9. 选择安装操作系统所需的硬盘大小。
10. 若要在另一台设备上安装应用程序，请单击“添加新设备”。
11. 选择网络选项并分配为公用网络创建的分布式端口组。
12. 若要添加其他网络接口，请单击“添加新设备”，然后选择为专用网络创建的分布式端口组。
13. 对于新 DC/DVD 驱动器，请选择包含用于首选操作系统安装的 ISO 的数据存储 ISO 文件。 选择之前上传到 ISP 和 Templates 文件夹的文件，然后单击“确定”。
14. 检查设置，然后单击“确定”以创建新 VM。
15. 打开 VM。 安装操作系统和所需的任何更新

安装操作系统后，可以克隆第二个 VM。 右键单击 VM 条目，然后选择“克隆”选项。

### <a name="create-shared-disks-for-vms"></a>为 VM 创建共享磁盘

Oracle 使用共享磁盘来存储数据、日志和重做日志文件。  可以在 vCenter 上创建共享磁盘，并将其装载到两个 VM 上。  为了获得更高性能，请将数据磁盘放置到不同 SCSI 控制器 以下步骤显示如何在 vCenter 上创建共享磁盘，并将其附加到虚拟机。 vCenter Flash 客户端用于修改 VM 属性。

#### <a name="create-disks-on-the-first-vm"></a>在第一个 VM 上创建磁盘

1. 在 vCenter 中，右键单击其中一个 Oracle VM，然后选择“编辑设置”。
2. 在“新设备”部分中，选择“SCSI 控制器”，并单击“添加”。
3. 在“新设备”部分中，选择“新建硬盘”，然后单击“添加”。
4. 展开“新建硬盘”的属性。
5. 指定硬盘的大小。
6. 将 VM 存储策略指定为前面定义的 vSAN 存储策略。
7. 选择位置作为 vSAN 数据存储的文件夹。 此位置有助于浏览磁盘并将磁盘附加第二个 VM。
8. 将磁盘设置选为“Thick provision eager zeroed”。
9. 将共享指定为“多编写器”。
10. 将虚拟设备节点选为步骤 2 中创建的新 SCSI 控制器。

    ![突出显示在第一个 VM 上创建磁盘所需字段的屏幕截图。](media/oracle-rac-new-hard-disk.png)

对于 Oracle 数据、日志和重做日志文件所需的所有新磁盘，重复步骤 2 到 10。

#### <a name="attach-disks-to-second-vm"></a>将磁盘附加到第二个 VM

1. 在 vCenter 中，右键单击其中一个 Oracle VM，然后选择“编辑设置”。
2. 在“新设备”部分中，选择“SCSI 控制器”，并单击“添加”。
3. 在“新设备”部分中，选择“现有硬盘”，然后单击“添加”。
4. 浏览到为第一个 VM 创建磁盘的位置，并选择 VMDK 文件。
5. 将 VM 存储策略指定为前面定义的 vSAN 存储策略。
6. 将磁盘设置选为“Thick provision eager zeroed”。
7. 将共享指定为“多编写器”。
8. 将虚拟设备节点选为步骤 2 中创建的新 SCSI 控制器。

    ![在第一个 VM 上创建磁盘](media/oracle-rac-existing-hard-disk.png)

对于 Oracle 数据、日志和重做日志文件所需的所有新磁盘，重复步骤 2 到 7。

## <a name="set-up-vm-host-affinity-rules"></a>设置 VM 主机关联规则

VM 到主机的关联规则确保 VM 在所需主机上运行。  可以在 vCenter 上定义规则，以确保 Oracle VM 在具有足够资源的主机上运行，并满足任何特定许可要求。

1. 在 CloudSimple 门户中，[提升 cloudowner 用户的权限](escalate-private-cloud-privileges.md)。
2. 登录到私有云的 vSphere 客户端。
3. 在 vSphere 客户端中，选择部署 Oracle VM 的群集，然后单击“配置”。
4. 在“配置“下，选择“VM/主机组”。
5. 单击“+”  。
6. 添加 VM 组。 选择“VM 组”作为类型。 输入该组的名称。 选择 VM，然后单击“确定”以创建组。
6. 添加主机组。 选择“主机组”作为类型。 输入该组的名称。 选择将运行 VM 的主机，然后单击“确定”以创建组。
7. 若要创建规则，请单击“VM/主机规则”。
8. 单击“+”  。
9. 输入规则的名称，并选中“启用”。
10. 对于“规则类型”，选择“虚拟机到主机”。
11. 选择包含 Oracle VM 的 VM 组。
12. 选择“必须在此组中的主机上运行”。
13. 选择所创建的主机组。
14. 单击“确定”创建规则  。

## <a name="references"></a>参考

* [关于 vSAN 策略](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [共享 VMDK 的 VMware 多编写器属性](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
