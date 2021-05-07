---
title: Windows 虚拟桌面 FSLogix 配置文件容器存储 - Azure
description: 用于在 Azure 存储中存储 Windows 虚拟桌面 FSLogix 配置文件的选项。
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ec166c1df9727052d4980f5d5758ece8c499880
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99526596"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Windows 虚拟桌面中 FSLogix 配置文件容器的存储选项

Azure 提供了多个存储解决方案，可用于存储 FSLogix 配置文件容器。 本文比较了 Azure 提供的用于 Windows 虚拟桌面 FSLogix 用户配置文件容器的几种存储解决方案。 建议大多数客户将 FSLogix 配置文件容器存储在 Azure 文件存储中。

Windows 虚拟桌面服务提供 FSLogix 配置文件容器作为推荐的用户配置文件解决方案。 FSLogix 设计用于在远程计算环境（如 Windows 虚拟桌面）中漫游配置文件。 登录时，此容器将动态附加到使用原生支持的虚拟硬盘 (VHD) 和 Hyper-V 虚拟硬盘 (VHDX) 的计算环境。 用户配置文件随时可用并在系统中显示，就像本机用户配置文件一样。

下表比较了适用于 Windows 虚拟桌面 FSLogix 配置文件容器用户配置文件的 Azure 存储服务的存储解决方案。

## <a name="azure-platform-details"></a>Azure 平台详细信息

|功能|Azure 文件|Azure NetApp 文件|存储空间直通|
|--------|-----------|------------------|---------------------|
|用例|常规用途|超高性能或从 NetApp 本地迁移|跨平台|
|平台服务|是，Azure 本机解决方案|是，Azure 本机解决方案|否，自托管|
|区域可用性|所有区域|[选择区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|所有区域|
|冗余|本地冗余/区域冗余/异地冗余/异地区域冗余|本地冗余|本地冗余/区域冗余/异地冗余|
|层和性能| 标准（事务优化）<br>高级<br>每个共享（10 GBps）最多 100K IOPS，约 3 毫秒延迟|Standard<br>高级<br>超高性能<br>最多 320k (16K) IOPS，每卷 4.5 GBps，大约 1 毫秒延迟|标准 HDD：每个磁盘限制为最多 500 IOPS<br>标准 SSD：每个磁盘限制为最多 4k IOPS<br>高级 SSD：每个磁盘限制为最多 20K IOPS<br>建议存储空间直通使用高级磁盘|
|容量|每个共享 100 TiB，每个常规用途帐户最多 5 PiB |每卷 100 TiB ，每个订阅最多 12.5 PiB|每个磁盘最多 32 TiB|
|要求的基础设施|最小共享大小 1 GiB|最小容量池 4 TiB，最小卷大小 100 GiB|Azure IaaS 上的两个 VM（+ 云见证）或至少三个 VM，磁盘不含，以及磁盘费用|
|协议|SMB 3.0/2.1，NFSv 4.1（预览版），REST|NFSv3，NFSv 4.1（预览版），SMB 3.x/2.x|NFSv3，NFSv4.1，SMB 3.1|

## <a name="azure-management-details"></a>Azure 管理详细信息

|功能|Azure 文件|Azure NetApp 文件|存储空间直通|
|--------|-----------|------------------|---------------------|
|Access|云，本地和混合（Azure 文件同步）|云，本地（通过 ExpressRoute 连接）|云，本地|
|备份|Azure 备份快照集成|Azure NetApp 文件快照|Azure 备份快照集成|
|安全性与符合性|[所有 Azure 支持的证书](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|已完成 ISO 认证|[所有 Azure 支持的证书](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory 集成|[Native Active Directory 和 Azure Active Directory 域服务](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory 域服务和 Native Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|仅 Native Active Directory 或 Azure Active Directory 域服务支持|

选择存储方法后，请查看 [Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)，了解有关定价计划的信息。

## <a name="next-steps"></a>后续步骤

若要了解有关 FSLogix 配置文件容器、用户配置文件磁盘和其他用户配置文件技术的详细信息，请参阅 [FSLogix 配置文件容器和 Azure 文件](fslogix-containers-azure-files.md)中的表。

如果已准备好创建自己的 FSLogix 配置文件容器，请开始学习以下其中一个教程：

- [Windows 虚拟桌面 Azure 文件存储上的 FSLogix 配置文件容器入门指南](create-file-share.md)
- [使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器](create-fslogix-profile-container.md)
- 使用 FSLogix 配置文件容器而不是用户配置文件磁盘时，[在 Azure 中部署 UPD 存储的双节点存储空间直通横向扩展文件服务器](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)中的说明同样适用

另外，还可以从最初阶段开始，并在 [Windows 虚拟桌面创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)中设置自己的 Windows 虚拟桌面解决方案。
