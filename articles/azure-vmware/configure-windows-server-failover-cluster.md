---
title: 配置 Azure VMware 解决方案 vSAN 上的 Windows Server 故障转移群集
description: 了解如何使用本机共享磁盘配置 Azure VMware 解决方案 vSAN 上的 Windows Server 故障转移群集 (WSFC)。
ms.topic: how-to
ms.date: 05/04/2021
ms.openlocfilehash: fcde65b98b3774ee1ef9b15bfa6da3836aaa8a1b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323186"
---
# <a name="configure-windows-server-failover-cluster-on-azure-vmware-solution-vsan"></a>配置 Azure VMware 解决方案 vSAN 上的 Windows Server 故障转移群集

在本文中，你将了解如何使用本机共享磁盘配置 Azure VMware 解决方案 vSAN 上的 [Windows Server 故障转移群集](/windows-server/failover-clustering/failover-clustering-overview)。 

>[!IMPORTANT]
>本文中的实现用于进行概念证明和试验。 我们建议使用 Cluster-in-a-Box (CIB) 配置，直到放置策略可用。

Windows Server 故障转移群集（以前称为 Microsoft 服务群集服务 (MSCS)）是一种 Windows Server 操作系统 (OS) 功能。 WSFC 是一项业务关键型功能，许多应用程序都需要此功能。 例如，以下配置都需要 WSFC：

- SQL Server 的如下配置：
  - Always On 故障转移群集实例，用于实现实例级高可用性。
  - Always On 可用性组 (AG)，用于实现数据库级高可用性。
- Windows 文件服务：
  - 在活动群集节点上运行的一般文件共享。
  - 横向扩展文件服务器 (SOFS)，用于将文件存储在群集共享卷 (CSV) 中。
  - 存储空间直通 (S2D)；用于在不同的群集节点中创建存储池的本地磁盘。

你可以将 WSFC 群集托管在不同的 Azure VMware 解决方案实例上，此配置称为 Cluster-Across-Box (CAB)。 也可以将 WSFC 群集放置在单个 Azure VMware 解决方案节点上。 此配置称为 Cluster-in-a-Box (CIB)。 不建议将 CIB 解决方案用于生产实现。 如果单个 Azure VMware 解决方案节点发生故障，所有 WSFC 群集节点都将关闭电源，并且应用程序将会出现停机。 Azure VMware 解决方案要求私有云群集中至少有三个节点。

一定要部署受支持的 WSFC 配置。 你的解决方案需要在 vSphere 和 Azure VMware 解决方案中受支持。 有关 vSphere 6.7 上的 WSFC 的详细信息，请参阅 VMware 提供的文档[设置故障转移群集和 Microsoft 群集服务](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf)。

本文重点介绍了 Windows Server 2016 和 Windows Server 2019 上的 WSFC。 遗憾的是，较早的 Windows Server 版本不在[主要支持](https://support.microsoft.com/lifecycle/search?alpha=windows%20server)范围内，因此本文中不探讨这些版本。

首先需要[创建一个 WSFC](/windows-server/failover-clustering/create-failover-cluster)。 随后使用本文中的信息在 Azure VMware 解决方案上指定 WSFC 部署。

## <a name="prerequisites"></a>先决条件

- Azure VMware 解决方案环境
- Microsoft Windows Server 操作系统安装介质

## <a name="reference-architecture"></a>参考体系结构

Azure VMware 解决方案为虚拟化 WSFC 提供本机支持。 它在虚拟磁盘级别支持 SCSI-3 永久保留 (SCSI3PR)。 WSFC 需要通过此支持来仲裁节点之间对共享磁盘的访问。 通过 SCSI3PR 支持，可以在 vSAN 数据存储上以本机方式配置使用 VM 间共享的磁盘资源的 WSFC。

下图说明了 Azure VMware 解决方案私有云上 WSFC 虚拟节点的体系结构。 它显示了 Azure VMware 解决方案的驻留位置（就更广泛的 Azure 平台而言），其中包括 WSFC 虚拟服务器（红框）。 此图展示了一个典型的中心辐射型体系结构，但使用 Azure 虚拟 WAN 也可以实现类似设置。 两种设置都可以提供其他 Azure 服务能带给你的所有价值。

:::image type="content" source="media/windows-server-failover-cluster/windows-server-failover-architecture.svg" alt-text="Azure VMware 解决方案私有云上 Windows Server 故障转移群集虚拟节点的关系图。" border="false" lightbox="media/windows-server-failover-cluster/windows-server-failover-architecture.svg":::

## <a name="supported-configurations"></a>支持的配置

目前支持的配置包括：

- Microsoft Windows Server 2012 或更高版本
- 每个群集最多五个故障转移群集节点
- 每个 VM 最多四个 PVSCSI 适配器
- 每个 PVSCSI 适配器最多 64 个磁盘

## <a name="virtual-machine-configuration-requirements"></a>虚拟机配置要求

### <a name="wsfc-node-configuration-parameters"></a>WSFC 节点配置参数

- 在每个 WSFC 节点上安装最新的 VMware 工具。
- 不支持在单个虚拟 SCSI 适配器上混合使用非共享磁盘和共享磁盘。 例如，如果系统磁盘（驱动器 C:）连接到 SCSI0:0，则第一个共享磁盘会连接到 SCSI1:0。 WSFC 的 VM 节点可拥有的虚拟 SCSI 控制器数量上限与普通 VM 相同 - 最多拥有四 (4) 个虚拟 SCSI 控制器。
- 虚拟光盘 SCSI ID 应在托管同一个 WSFC 的节点的所有 VM 之间保持一致。

| **组件** | **要求** |
| --- | --- |
| VM 硬件版本 | 版本 11 或更高版本，以支持实时 vMotion。 |
| 虚拟 NIC | VMXNET3 半虚拟化网络接口卡 (NIC)；在虚拟 NIC 上启用来宾内置 Windows 接收方缩放 (RSS)。 |
| 内存 | 将整个 VM 预留内存用于 WSFC 群集中的节点。 |
| 增加每个 WSFC 节点的 I/O 超时时间。 | 将 HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet 修改为 60 秒或更大值。 （如果重新创建群集，此值可能会重置为其默认值，因此必须再次更改它。） |
| Windows 群集运行状况监视 | 必须将 Windows 群集运行状况监视的 SameSubnetThreshold 参数的值修改为至少允许丢失 10 个检测信号。 这是 [Windows Server 2016 中的默认设置](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834)。 此建议适用于使用 WSFC 的所有应用程序，其中包括共享磁盘和非共享磁盘。 |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>WSFC 节点 - 启动磁盘配置参数


| **组件** | **要求** |
| --- | --- |
| SCSI 控制器类型 | LSI 逻辑 SAS |
| 磁盘模式 | 虚拟 |
| SCSI 总线共享 | 无 |
| 修改托管启动设备的虚拟 SCSI 控制器的高级设置。 | 向每个 WSFC 节点添加以下高级设置：<br /> scsiX.returnNoConnectDuringAPD = "TRUE"<br />scsiX.returnBusyOnNoConnectStatus = "FALSE"<br />其中 X 是启动设备 SCSI 总线控制器 ID 编号。 默认情况下，X 设置为 0。 |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>WSFC 节点 - 共享磁盘配置参数


| **组件** | **要求** |
| --- | --- |
| SCSI 控制器类型 | VMware 半虚拟化 (PVSCSI) |
| 磁盘模式 | 独立 - 永久性（见下图中的步骤 2）。 使用此设置，可确保从快照中排除所有磁盘。 基于 WSFC 的 VM 不支持快照。 |
| SCSI 总线共享 | 物理（见下图中的步骤 1） |
| 多编写器标志 | 未使用 |
| 磁盘格式 | 厚预配。 （vSAN 不要求使用厚置备置零 (EZT)。） |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="显示虚拟硬盘的“编辑设置”页的屏幕截图。":::

## <a name="non-supported-scenarios"></a>不支持的场景

Azure VMware 解决方案上的 WSFC 不支持以下功能：

- NFS 数据存储
- 存储空间
- 使用 iSCSI 服务的 vSAN
- vSAN 拉伸群集
- 增强的 vMotion 兼容性 (EVC)
- vSphere 容错 (FT)
- 快照
- 实时（在线）存储 vMotion
- N 端口 ID 虚拟化 (NPIV)

对虚拟机硬件进行的热更改可能会中断 WSFC 节点之间的检测信号。

以下活动不受支持，它们可能会导致 WSFC 节点故障转移：

- 热添加内存
- 热添加 CPU
- 使用快照
- 增加共享磁盘的大小
- 暂停和恢复虚拟机状态
- 内存过度配置会导致 ESXi 交换或 VM 内存扩展
- 热扩展本地 VMDK 文件（即使该文件未与 SCSI 总线共享控制器关联）

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>在 Azure VMware 解决方案 vSAN 上为 WSFC 配置共享磁盘

1. 确保 Active Directory 环境可用。
2. 在 vSAN 数据存储上创建虚拟机 (VM)。
3. 开启所有 VM，配置主机名和 IP 地址，将所有 VM 加入 Active Directory 域，并安装最新的操作系统更新。
4. 安装最新的 VMware 工具。
5. 在每个 VM 上启用并配置 Windows Server 故障转移群集功能。
6. 配置群集仲裁见证（可以使用文件共享见证）。
7. 关闭 WSFC 群集所有节点的电源。
8. 将一个或多个半虚拟 SCSI 控制器（最多四个）添加到 WSFC 的每个 VM 中。 使用前面段落中的设置。
9. 在第一个群集节点上，通过“添加新设备” > “硬盘”添加所需的所有共享磁盘。 将磁盘共享保留为“未指定”（默认），磁盘模式应保留为“独立 - 永久性” 。 然后将共享磁盘附加到在前面步骤中创建的控制器。
10. 在剩余的 WSFC 节点上继续操作。 通过选择“添加新设备” > “现有硬盘”，添加在上一个步骤中创建的磁盘。 请务必在所有 WSFC 节点上保留相同的磁盘 SCSI ID。
11. 开启第一个 WSFC 节点；登录并打开磁盘管理控制台 (MMC)。 确保已添加的共享磁盘可以由操作系统进行管理并已初始化。 设置磁盘的格式并分配一个驱动器号。
12. 开启其他 WSFC 节点。
13. 通过“添加磁盘”向导将磁盘添加到 WSFC 群集中，并将它们添加到群集共享卷。
14. 通过“移动磁盘”向导测试故障转移，并确保使用共享磁盘的 WSFC 群集正常工作。
15. 运行“验证群集”向导，确认群集及其节点是否正常工作。

    一定要注意群集验证测试中的以下特定项目：

       - 验证存储空间永久保留。 如果你的集群没有使用存储空间（以 Azure VMware 解决方案 vSAN 为例），则此测试不适用。 可以忽略“验证存储空间永久保留”测试的任何结果，包括此警告。 若要避免出现警告，可以排除此测试。
        
      - 验证网络通信。 群集验证测试会显示一条警告，指出每个群集节点都只有一个网络接口可用。 你可以忽略此警告。 Azure VMware 解决方案会提供所需的可用性和性能，因为节点连接到其中一个 NSX-T 段。 但是，请将此项保留为群集验证测试的一部分，因为它会验证网络通信的其他方面。

16. 创建一个 DRS 规则，以将 WSFC VM 放置在同一个 Azure VMware 解决方案节点上。 为此，你需要创建一个主机到 VM 的关联规则。 这样，群集节点将在同一个 Azure VMware 解决方案主机上运行。 同样，此操作用于试验目的，直到放置策略可用。

    >[!NOTE]
    > 为此，你需要创建一个支持请求票证。 我们的 Azure 支持组织将能够为你提供帮助。

## <a name="related-information"></a>相关信息

- [Windows Server 中的故障转移群集](/windows-server/failover-clustering/failover-clustering-overview)
- [vSphere 上的 Microsoft 群集准则 (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [设置故障转移群集和 Microsoft 群集服务的相关知识 (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6.7 U3 - 使用共享磁盘的 WSFC &amp; SCSI-3 永久保留 (vmware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Azure VMware 解决方案限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>后续步骤

了解如何在 Azure VMware 解决方案中设置 WSFC 后，接下来你可能想要了解以下内容：

- 通过添加需要 WSFC 功能的更多应用程序来设置新的 WSFC。 例如，SQL Server 和 SAP ASCS。
- 设置备份解决方案。
  - [为 Azure VMware 解决方案设置 Azure 备份服务器](set-up-backup-server-for-azure-vmware-solution.md)
  - [适用于 Azure VMware 解决方案虚拟机的备份解决方案](backup-azure-vmware-solution-virtual-machines.md)
