---
title: 配置存储策略
description: 了解如何为 Azure VMware 解决方案虚拟机配置存储策略。
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: b9535c5765b2ff024537ff44e2e24a76c992dd35
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123304049"
---
# <a name="configure-storage-policy"></a>配置存储策略

vSAN 存储策略定义了虚拟机 (VM) 的存储要求。 这些策略保证了 VM 所需的服务级别，因为它们可确定如何将存储分配给 VM。 部署到 vSAN 数据存储的每个 VM 都分配有至少一个 VM 存储策略。

可以在 VM 的初始部署中或在执行其他 VM 操作（例如克隆或迁移）时分配 VM 存储策略。 部署后 cloudadmin 用户或等效角色不能更改 VM 的默认存储策略。 但是，允许按磁盘更改 VM 存储策略。 

运行命令允许授权用户将默认或现有 VM 存储策略更改为部署后 VM 的可用策略。 没有对磁盘级别 VM 存储策略进行任何更改。 始终可以根据要求更改磁盘级别的 VM 存储策略。


>[!NOTE]
>运行命令按提交的顺序一次执行一个。


在本操作指南中，你将学习如何：

> [!div class="checklist"]
> * 列出所有存储策略
> * 设置 VM 的存储策略
> * 指定群集的存储策略



## <a name="prerequisites"></a>先决条件

确保[满足最低级别的主机](https://docs.vmware.com/en/VMware-Cloud-on-AWS/services/com.vmware.vsphere.vmc-aws-manage-data-center-vms.doc/GUID-EDBB551B-51B0-421B-9C44-6ECB66ED660B.html)。

|  **RAID 配置** | **允许的故障数 (FTT)** | **需要的最少主机数** |
| --- | :---: | :---: |
| RAID-1（镜像） <br />默认设置。  | 1  | 3  |
| RAID-5（擦除编码）  | 1  | 4  |
| RAID-1（镜像）  | 2  | 5  |
| RAID-6（擦除编码）  | 2  | 6  |
| RAID-1（镜像）  | 3  | 7  |


 

## <a name="list-storage-policies"></a>列出存储策略

你将运行 `Get-StoragePolicy` cmdlet，列出可在 VM 上设置的基于 vSAN 的存储策略。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“运行命令” > “包” > “Get-StoragePolicies”。

   :::image type="content" source="media/run-command/run-command-overview-storage-policy.png" alt-text="显示如何访问可用的存储策略运行命令的屏幕截图。" lightbox="media/run-command/run-command-overview-storage-policy.png":::

1. 提供所需的值或更改默认值，然后选择“运行”。

   :::image type="content" source="media/run-command/run-command-get-storage-policy.png" alt-text="显示如何列出可用的存储策略的屏幕截图。":::
   
   | **字段** | 值 |
   | --- | --- |
   | 最长保留时间  | cmdlet 输出的保持期。 默认值为 60。  |
   | 指定用于执行的名称  | 字母数字名称，例如 Get-StoragePolicies-Exec1。 |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。




## <a name="set-storage-policy-on-vm"></a>在 VM 上设置存储策略

你将运行 `Set-AvsVMStoragePolicy` cmdlet 来修改单个 VM 上基于 vSAN 的存储策略。 

>[!NOTE]
>不能使用 vSphere 客户端更改 VM 的默认存储策略或任何现有存储策略。 

1. 选择“运行命令” > “包” > “Set-AvsVMStoragePolicy”。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | **VMName** | 目标 VM 的名称。 |
   | **StoragePolicyName** | 要设置的存储策略的名称。 例如，RAID-FTT-1。 |
   | 最长保留时间  | cmdlet 输出的保持期。 默认值为 60。  |
   | 指定用于执行的名称  | 字母数字名称，例如 changeVMStoragePolicy。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。


## <a name="specify-storage-policy-for-a-cluster"></a>指定群集的存储策略

你将运行 `Set-ClusterDefaultStoragePolicy` cmdlet 来指定群集的默认存储策略。

1. 选择“运行命令” > “包” > “Set-ClusterDefaultStoragePolicy”。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | **ClusterName** | 群集的名称。 |
   | **StoragePolicyName** | 要设置的存储策略的名称。 例如，RAID-FTT-1。 |
   | 最长保留时间  | cmdlet 输出的保持期。 默认值为 60。  |
   | 指定用于执行的名称  | 字母数字名称，例如 Set-ClusterDefaultStoragePolicy-Exec1。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。



## <a name="next-steps"></a>后续步骤

现在你已经了解了如何配置 vSAN 存储策略，可以详细了解：

- [如何将磁盘池附加到 Azure VMware 解决方案主机（预览版）](attach-disk-pools-to-azure-vmware-solution-hosts.md)- 可以使用磁盘作为 Azure VMware 解决方案的持久性存储以实现最佳成本和性能。

- [如何为 vCenter 配置外部标识](configure-identity-source-vcenter.md) - vCenter 具有一个名为 cloudadmin 的内置本地用户，并为该用户分配了 CloudAdmin 角色。 本地 cloudadmin 用户用于在 Active Directory (AD) 中设置用户。 借助运行命令功能，可以为 vCenter 配置通过 LDAP 或 LDAPS 的 Active Directory 作为外部标识源。
