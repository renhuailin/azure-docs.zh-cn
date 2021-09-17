---
title: 从 AWS 和其他平台迁移到 Azure 中的托管磁盘
description: 使用从 AWS 等其他云或其他虚拟化平台上传的 VHD 在 Azure 中创建 VM，并使用 Azure 托管磁盘。
author: roygara
manager: twooley
ms.service: storage
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68aaf58230ebadd7283e62baf232b84743bba535
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692412"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>从 Amazon Web Services (AWS) 和其他平台迁移到 Azure 中的托管磁盘

**适用于：** :heavy_check_mark: Windows VM 

可将 VHD 文件从 AWS 或本地虚拟化解决方案上传到 Azure，以创建可使用托管磁盘的虚拟机 (VM)。 Azure 托管磁盘不需要为 Azure IaaS VM 管理存储帐户。 只需指定所需的磁盘类型和大小，Azure 会为你创建和管理磁盘。 

可以上传通用和专用 VHD。 
- **通用 VHD** - 已使用 Sysprep 删除所有个人帐户信息。 
- “专用 VHD” - 维护来自原始 VM 的用户帐户、应用程序和其他状态数据。 

> [!IMPORTANT]
> 将任何 VHD 上传到 Azure 之前，应该遵循[准备要上传到 Azure 的 Windows VHD 或 VHDX](prepare-for-upload-vhd-image.md)
>
>


| 方案                                                                                                                         | 文档                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| 拥有要使用托管磁盘迁移至 Azure VM 的现有 AWS EC2 实例。                              | [将 VM 从 Amazon Web Services (AWS) 移到 Azure](aws-to-azure.md)                           |
| 拥有要用作映像的来自其他虚拟化平台的 VM，以创建多个 Azure VM。 | [上传通用 VHD 并用其在 Azure 中新建 VM](upload-generalized-managed.md) |
| 有一个唯一自定义 VM，你想要在 Azure 中重新创建它。                                                      | [将专用 VHD 上传到 Azure 并创建新 VM](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>托管磁盘概述

Azure 托管磁盘无需管理存储帐户，从而简化 VM 管理。 可用性集中 VM 的更高可靠性使托管磁盘受益。 这可确保将可用性集中不同 VM 的磁盘最大限度地彼此独立，以避免单点故障。 它会自动将可用性集中不同 VM 的磁盘置于不同的存储缩放单元（戳），限制由于硬件和软件故障引起的单个存储缩放单元故障影响。
可根据需要，从存储选项的四种类型中进行选择。 若要了解可用的磁盘类型，请参阅[选择磁盘类型](../disks-types.md)一文。

## <a name="plan-for-the-migration-to-managed-disks"></a>计划迁移到托管磁盘

本部分可帮助你针对 VM 和磁盘类型做出最佳决策。

如果打算从非托管磁盘迁移到托管磁盘，则应注意具有[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色的用户不能更改 VM 大小（因为它们可以预转换）。 这是因为包含托管磁盘的 VM 要求用户对 OS 磁盘具有 Microsoft.Compute/disks/write 权限。

### <a name="location"></a>位置

选取 Azure 托管磁盘可用的位置。 如果要迁移到高级 SSD，还请确保高级存储在计划迁移到的目标区域中可用。 有关可用位置的最新信息，请参阅 [Azure 服务（按区域）](https://azure.microsoft.com/regions/#services) 。

### <a name="vm-sizes"></a>VM 大小

如果要迁移到高级 SSD，必须将 VM 的大小更新为该 VM 所在区域中提供的支持高级存储的大小。 查看支持高级存储的 VM 大小。 [虚拟机大小](../sizes.md)中列出了 Azure VM 大小规范。
查看适用于高级存储的虚拟机的性能特征并选择最适合工作负载的 VM 大小。 确保 VM 上有足够的带宽来驱动磁盘通信。

### <a name="disk-sizes"></a>磁盘大小

有关可用磁盘类型和大小的信息，请参阅 [Azure 有哪些可用的磁盘类型](../disks-types.md)。

### <a name="disk-caching-policy"></a>磁盘缓存策略 

**高级托管磁盘**

默认情况下，所有高级数据磁盘的磁盘缓存策略都是“只读”，所有附加到 VM 的高级操作系统都是“读写”。 为使应用程序的 IO 达到最佳性能，建议使用此配置设置。 对于频繁写入或只写的磁盘（例如 SQL Server 日志文件），禁用磁盘缓存可获得更佳的应用程序性能。

### <a name="pricing"></a>定价

查看[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。


## <a name="next-steps"></a>后续步骤

- 将任何 VHD 上传到 Azure 之前，应该遵循[准备要上传到 Azure 的 Windows VHD 或 VHDX](prepare-for-upload-vhd-image.md)