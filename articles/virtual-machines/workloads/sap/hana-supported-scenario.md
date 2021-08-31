---
title: Azure SAP HANA（大型实例）的支持方案 | Microsoft Docs
description: 了解 Azure SAP HANA（大型实例）的支持方案及其体系结构详细信息。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9ebd38cbff7d28515a7f60554a2ef755bdf6d04a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467751"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 大型实例的支持方案
本文介绍了 HANA 大型实例 (HLI) 的支持方案和体系结构详细信息。

>[!NOTE]
>如果此文未提及你的方案，请与 Microsoft 服务管理团队联系以评估需求。
设置 HLI 单元之前，请先验证 SAP 或服务实现合作伙伴的设计。

## <a name="terms-and-definitions"></a>术语和定义
我们来一起了解本文中使用的术语和定义：

- **SID**：HANA 系统的系统标识符。
- **HLI**：Hana 大型实例。
- **DR**：灾难恢复 (DR)。
- **常规 DR**：一种系统设置，具有仅用于 DR 目的的专用资源。
- **多用途 DR**：配置为使用非生产环境的 DR 站点系统，以及为 DR 事件配置的生产实例。 
- **单个 SID**：安装了一个实例的系统。
- **多 SID**：配置了多个实例的系统；也称为 MCOS 环境。
- **HSR**：SAP HANA 系统复制。

## <a name="overview"></a>概述
HANA 大型实例支持多种体系结构，可帮助满足你的业务需求。 以下部分介绍体系结构方案及其配置详细信息。 

派生的体系结构设计完全是从基础结构的角度出发的。 请咨询 SAP 或实现合作伙伴，了解 HANA 部署。 如果你的方案未在本文中列出，请联系 Microsoft 帐户团队查看体系结构并为你派生解决方案。

> [!NOTE]
> 这些体系结构完全符合定制的数据集成 (TDI) 设计，并受 SAP 支持。

本文介绍了每个受支持的体系结构中的两个组件的详细信息：

- 以太网
- 存储

### <a name="ethernet"></a>以太网

每个预配的服务器都预配置了一组以太网接口。 每个 HLI 单元配置的以太网接口分为四种类型：

- **A**：用于或由客户端访问。
- **B**：用于节点到节点通信。 无论请求什么拓扑，都会在所有服务器上配置此接口。 然而，它仅用于横向扩展方案。
- **C**：用于节点到存储连接。
- **D**：用于 STONITH 设置的节点到 iSCSI 设备连接。 仅当请求 HSR 设置时才配置此接口。  

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点|
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | STONITH |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 节点到节点|
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

基于 HLI 单元上配置的拓扑来选择接口。 例如，接口“B”设置为用于节点到节点的通信，这在配置横向扩展拓扑时非常有用。 此接口不用于单节点纵向扩展配置。 有关接口使用情况的详细信息，请查看所需的方案（本文档的后文）。 

如果需要，可自行定义更多 NIC 卡。 但是，无法更改现有 NIC 的配置。

>[!NOTE]
>你可能会发现其他接口是物理接口或捆绑。 对于用例，应仅考虑前面提到的接口。 忽略任何其他接口。

分配有两个 IP 地址的单元的分布应如下所示：

- 以太网“A”应具有一个位于提交给 Microsoft 的服务器 IP 池地址范围内的已分配 IP 地址。 此 IP 地址应在操作系统 (OS) 的 /etc/hosts 目录中进行维护。

- 以太网“C”应具有一个用于与 NFS 通信的已分配 IP 地址。 不需要在 etc/hosts 目录中维护此地址以允许租户内的实例到实例流量。 

对于 HANA 系统复制或 HANA 横向扩展部署，不适合使用分配有两个 IP 地址的边栏选项卡配置。 如果只有两个分配的 IP 地址，并且想要部署此类配置，请联系 Azure 服务管理上的 SAP HANA。 他们可以在第三个 VLAN 中为你分配第三个 IP 地址。 对于在三个 NIC 端口上分配了三个 IP 地址的 HANA 大型实例，请注意以下使用规则：

- 以太网“A”应具有一个位于提交给 Microsoft 的服务器 IP 池地址范围以外的已分配 IP 地址。 此 IP 地址不应在 OS 的 etc/hosts 目录中进行维护。

- 以太网“B”应专门在 etc/hosts 目录中进行维护，以便在各个实例之间进行通信。 在横向扩展 HANA 配置中维护这些 IP 地址，并作为 HANA 用于节点间配置的 IP 地址进行维护。

- 以太网“C”应具有一个用于与 NFS 存储通信的已分配 IP 地址。 此类型的地址不应在 etc/hosts 目录中进行维护。

- 应以独占方式使用以太网“D”访问 Pacemaker 的 STONITH 设备。 配置 HANA 系统复制并且希望使用基于 SBD 的设备在操作系统上实现自动故障转移时，需要此接口。


### <a name="storage"></a>存储
根据请求的拓扑结构预配置存储。 根据服务器和 SKU 的数量和配置的拓扑，卷大小和装入点会有所不同。 有关详细信息，请查看所需的方案（本文档的后文）。 如果需要更多存储空间，可以按 1-TB 的增量购买。

>[!NOTE]
>装入点 /usr/sap/\<SID> 是 /hana/shared 装入点的符号链接。


## <a name="supported-scenarios"></a>支持的方案

下一节中的体系结构关系图使用以下表示法：

[ ![体系结构关系图表](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

下面是受支持的方案：

* 具有一个 SID 的单节点
* 单节点 MCOS
* 具有 DR（常规）的单节点
* 具有 DR（多用途）的单节点
* 具有 STONITH 的 HSR
* 具有 DR（常规/多用途）的 HSR 
* 主机自动故障转移 (1+1) 
* 使用备用节点的横向扩展
* 不使用备用节点的横向扩展
* 使用 DR 的横向扩展

## <a name="single-node-with-one-sid"></a>具有一个 SID 的单节点

此拓扑支持具有一个 SID 的纵向扩展配置中的一个节点。

### <a name="architecture-diagram"></a>体系结构关系图  

![具有一个 SID 的单节点](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID | HANA 安装 | 
|/hana/data/SID/mnt00001 | 数据文件安装 | 
|/hana/log/SID/mnt00001 | 日志文件安装 | 
|/hana/logbackups/SID | 重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。

## <a name="single-node-mcos"></a>单节点 MCOS

此拓扑支持具有多个 SID 的纵向扩展配置中的一个节点。

### <a name="architecture-diagram"></a>体系结构关系图  

![单节点 MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID1 | SID1 的 HANA 安装 | 
|/hana/data/SID1/mnt00001 | SID1 的数据文件安装 | 
|/hana/log/SID1/mnt00001 | SID1 的日志文件安装 | 
|/hana/logbackups/SID1 | SID1 的重做日志 |
|/hana/shared/SID2 | SID2 的 HANA 安装 | 
|/hana/data/SID2/mnt00001 | SID2 的数据文件安装 | 
|/hana/log/SID2/mnt00001 | SID2 的日志文件安装 | 
|/hana/logbackups/SID2 | SID2 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](./hana-overview-architecture.md)。

## <a name="single-node-with-dr-using-storage-replication"></a>使用存储复制进行 DR 的单节点
 
此拓扑支持具有一个或多个 SID 的纵向扩展配置中的一个节点。 到 DR 站点的基于存储的复制用于主 SID。 在关系图中，主站点上只显示了一个单一 SID 系统，但同时还支持 MCOS 系统。

### <a name="architecture-diagram"></a>体系结构关系图  

![使用存储复制进行 DR 的单节点](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID | SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | SID 的日志文件安装 | 
|/hana/logbackups/SID | SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](./hana-overview-architecture.md)。
- 在 DR 站点：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。 
- 在 DR 站点：通过生产站点的快照复制数据、日志备份和共享卷（标记为“存储复制”）。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](./hana-overview-high-availability-disaster-recovery.md)。
- SKU 第 I 类级别的启动卷将复制到 DR 节点。


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>使用存储复制进行 DR（多用途）的单节点
 
此拓扑支持具有一个或多个 SID 的纵向扩展配置中的一个节点。 到 DR 站点的基于存储的复制用于主 SID。 

在关系图中，主站点上只显示了一个单一 SID 系统，但同时还支持多 SID (MCOS) 系统。 在 DR 站点，HLI 单元用于 QA 实例。 生产操作从主站点运行。 在 DR 故障转移（或故障转移测试）期间，DR 站点上的 QA 实例将会关闭。

### <a name="architecture-diagram"></a>体系结构关系图  

![使用存储复制进行 DR（多用途）的单节点](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](./hana-overview-architecture.md)。
- 在 DR 站点：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。 
- 在 DR 站点：通过生产站点的快照复制数据、日志备份和共享卷（标记为“存储复制”）。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](./hana-overview-high-availability-disaster-recovery.md)。 
- 在 DR 站点：为 QA 实例安装配置 QA 的数据、日志备份、日志和共享卷（标记为“QA 实例安装”）。
- SKU 第 I 类级别的启动卷将复制到 DR 节点。

## <a name="hsr-with-stonith-for-high-availability"></a>带有 STONITH 的高可用性 HSR
 
此拓扑支持 HANA 系统复制配置的两个节点。 仅节点上的单个 HANA 实例支持此配置。 不支持 MCOS 方案。

> [!NOTE]
> 截至 2019 年 12 月，SUSE 操作系统仅支持此体系结构。


### <a name="architecture-diagram"></a>体系结构关系图  

![带有 STONITH 的高可用性 HSR](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 用于 STONITH |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 用于 STONITH |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在辅助节点上**|
|/hana/shared/SID | 辅助 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 辅助 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 辅助 SID 的日志文件安装 | 
|/hana/logbackups/SID | 辅助 SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](./hana-overview-architecture.md)。
- STONITH：为 STONITH 设置配置 SBD。 但是，STONITH 的使用是可选的。


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>将 HSR 和 DR 与存储复制配合使用来实现高可用性
 
此拓扑支持 HANA 系统复制配置的两个节点。 支持常规和多用途 DR。 这些配置仅适用于节点上的单个 HANA 实例。 这些配置 *不* 支持 MCOS 方案。

在图中，DR 站点显示了一个多用途方案，其中 HLI 单元用于 QA 实例。 生产操作从主站点运行。 在 DR 故障转移（或故障转移测试）期间，DR 站点上的 QA 实例将会关闭。 

### <a name="architecture-diagram"></a>体系结构关系图  

![将 HSR 和 DR 与存储复制配合使用来实现高可用性](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 用于 STONITH |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 用于 STONITH |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点的主节点上**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在主站点的辅助节点上**|
|/hana/shared/SID | 辅助 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 辅助 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 辅助 SID 的日志文件安装 | 
|/hana/logbackups/SID | 辅助 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](./hana-overview-architecture.md)。
- STONITH：为 STONITH 设置配置 SBD。 但是，STONITH 的使用是可选的。
- 在 DR 站点：主节点和辅助节点复制需要两组存储卷。
- 在 DR 站点：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。 
- 在 DR 站点：通过生产站点的快照复制数据、日志备份和共享卷（标记为“存储复制”）。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](./hana-overview-high-availability-disaster-recovery.md)。 
- 在 DR 站点：为 QA 实例安装配置 QA 的数据、日志备份、日志和共享卷（标记为“QA 实例安装”）。
- SKU 第 I 类级别的启动卷将复制到 DR 节点。


## <a name="host-auto-failover-11"></a>主机自动故障转移 (1+1)
 
此拓扑支持主机自动故障转移配置中的两个节点。 其中一个节点具有主/辅助角色，另一个节点作为备用节点。 SAP 仅支持将此方案用于 S/4 HANA。 有关详细信息，请参阅 [OSS 说明 2408419 - SAP S/4HANA - 多节点支持](https://launchpad.support.sap.com/#/notes/2408419)。



### <a name="architecture-diagram"></a>体系结构关系图  

![主机自动故障转移 (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点和备用节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |



### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在备用节点上：在备用单元上为 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。
 

## <a name="scale-out-with-standby"></a>使用备用节点的横向扩展
 
此拓扑支持横向扩展配置中的多个节点。 其中一个节点具有主角色，一个或多个节点具有辅助角色，一个或多个节点作为备用节点。 但是，在任何给定时间，只能有一个主节点。


### <a name="architecture-diagram"></a>体系结构关系图  

![使用备用节点的横向扩展](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点、工作器节点和备用节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


## <a name="scale-out-without-standby"></a>不使用备用节点的横向扩展
 
此拓扑支持横向扩展配置中的多个节点。 其中一个节点具有主角色，一个或多个节点具有辅助角色。 但是，在任何给定时间，只能有一个主节点。


### <a name="architecture-diagram"></a>体系结构关系图  

![不使用备用节点的横向扩展](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点和工作器节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。

## <a name="scale-out-with-dr-using-storage-replication"></a>使用存储复制进行 DR 横向扩展
 
此拓扑支持使用 DR 的横向扩展中的多个节点。 支持常规和多用途 DR。 在该关系图中，仅显示单一目的 DR。 可在有或没有备用节点的情况下请求此拓扑。


### <a name="architecture-diagram"></a>体系结构关系图  

![使用存储复制进行 DR 横向扩展](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
-  在 DR 站点：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。 
- 在 DR 站点：通过生产站点的快照复制数据、日志备份和共享卷（标记为“存储复制”）。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](./hana-overview-high-availability-disaster-recovery.md)。 
- SKU 第 I 类级别的启动卷将复制到 DR 节点。


## <a name="single-node-with-dr-using-hsr"></a>使用 HSR 进行 DR 的单节点
 
此拓扑支持具有一个 SID（具有主 SID 的 DR 站点的HANA 系统复制）的纵向扩展配置中的一个节点。 在关系图中，主站点上只显示了一个单一 SID 系统，但同时还支持多 SID (MCOS) 系统。

### <a name="architecture-diagram"></a>体系结构关系图  

![使用 HSR 进行 DR 的单节点](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点在主 HLI 单元和 DR HLI 单元上进行预配置：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID | SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | SID 的日志文件安装 | 
|/hana/logbackups/SID | SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](./hana-overview-architecture.md)。
- 主节点通过使用 HANA 系统复制与 DR 节点同步。 
- 使用 [Global Reach](../../../expressroute/expressroute-global-reach.md)，可以将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络。



## <a name="single-node-hsr-to-dr-cost-optimized"></a>单节点 HSR 到 DR（成本优化） 
 
 此拓扑支持具有一个 SID 的纵向扩展配置中的一个节点。 到 DR 站点的 HANA 系统复制用于主 SID。 在关系图中，主站点上只显示了一个单一 SID 系统，但同时还支持多 SID (MCOS) 系统。 在 DR 站点，HLI 单元用于 QA 实例。 生产操作从主站点运行。 在 DR 故障转移（或故障转移测试）期间，DR 站点上的 QA 实例将会关闭。

### <a name="architecture-diagram"></a>体系结构关系图  

![单节点 HSR 到 DR（成本优化）](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](./hana-overview-architecture.md)。
- 在 DR 站点：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“DR 站点中的 PROD 实例”）。 
- 在 DR 站点：为 QA 实例安装配置 QA 的数据、日志备份、日志和共享卷（标记为“QA 实例安装”）。
- 主节点通过使用 HANA 系统复制与 DR 节点同步。 
- 使用 [Global Reach](../../../expressroute/expressroute-global-reach.md)，可以将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络。

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>使用 HSR 实现高可用性和灾难恢复 
 
 该拓扑支持用于 HANA 系统复制配置的两个节点，以实现本地区域的高可用性。 对于 DR，DR 区域的第三个节点通过使用 HSR（异步模式）与主站点同步。 

### <a name="architecture-diagram"></a>体系结构关系图  

![使用 HSR 实现高可用性和灾难恢复](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在 DR 站点：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“PROD DR 实例”）。 
- 主站点节点通过使用 HANA 系统复制与 DR 节点同步。 
- 使用 [Global Reach](../../../expressroute/expressroute-global-reach.md)，可以将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络。

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>使用 HSR 实现高可用性和灾难恢复（成本优化）
 
 该拓扑支持用于 HANA 系统复制配置的两个节点，以实现本地区域的高可用性。 对于 DR，DR 区域中的第三个节点通过使用 HSR（异步模式）与主站点同步，而另一个实例（例如 QA）则是在 DR 节点中运行。 

### <a name="architecture-diagram"></a>体系结构关系图  

![使用 HSR 实现高可用性和灾难恢复（成本优化）](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在 DR 站点：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“PROD DR 实例”）。 
- 在 DR 站点：为 QA 实例安装配置 QA 的数据、日志备份、日志和共享卷（标记为“QA 实例安装”）。
- 主站点节点通过使用 HANA 系统复制与 DR 节点同步。 
- 使用 [Global Reach](../../../expressroute/expressroute-global-reach.md)，可以将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络。

## <a name="scale-out-with-dr-using-hsr"></a>使用 HSR 进行 DR 横向扩展
 
此拓扑支持使用 DR 的横向扩展中的多个节点。 可在有或没有备用节点的情况下请求此拓扑。 主站点节点通过使用 HANA 系统复制（异步模式）与 DR 站点节点同步。


### <a name="architecture-diagram"></a>体系结构关系图  

[ ![使用 HSR 进行 DR 横向扩展](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo> | team0.tenant | Client-to-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在 DR 站点：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点。 
- 主站点节点通过使用 HANA 系统复制与 DR 节点同步。 
- 使用 [Global Reach](../../../expressroute/expressroute-global-reach.md)，可以将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络。


## <a name="next-steps"></a>后续步骤

了解如何部署 HANA 大型实例。

> [!div class="nextstepaction"]
> [SAP HANA（大型实例）部署](./hana-overview-infrastructure-connectivity.md)
