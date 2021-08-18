---
title: Azure 虚拟桌面 FSLogix 配置文件容器文件 - Azure
description: 本文介绍 Azure 虚拟桌面和 Azure 文件存储中的 FSLogix 配置文件容器。
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 013dea780c1f5819d9b9caefeec3b66066d1f44c
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033544"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 配置文件容器和 Azure 文件

Azure 虚拟桌面服务建议将 FSLogix 配置文件容器用作用户配置文件解决方案。 FSLogix 设计用于在远程计算环境（如 Azure 虚拟桌面）中漫游配置文件。 它将完整的用户配置文件存储在单个容器中。 登录时，此容器将动态附加到使用原生支持的虚拟硬盘 (VHD) 和 Hyper-V 虚拟硬盘 (VHDX) 的计算环境。 用户配置文件随时可用并在系统中显示，就像本机用户配置文件一样。 本文介绍如何在 Azure 虚拟桌面中将 FSLogix 配置文件容器与 Azure 文件存储功能配合使用。

>[!NOTE]
>如果你正在寻找有关 Azure 上不同 FSLogix 配置文件容器存储选项的比较资料，请参阅 [FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

## <a name="user-profiles"></a>用户配置文件

用户配置文件包含有关个人的数据元素，其中包括桌面设置、持久性网络连接和应用程序设置等配置信息。 默认情况下，Windows 会创建一个与操作系统紧密集成的本地用户配置文件。

远程用户配置文件在用户数据和操作系统之间提供分区。 使用该配置文件可以在不影响用户数据的情况下替换或更改操作系统。 在远程桌面会话主机 (RDSH) 和虚拟桌面基础结构 (VDI) 中，可能出于以下原因而替换操作系统：

- 升级操作系统
- 替换现有的虚拟机 (VM)
- 某个用户成为共用（非持久性）RDSH 或 VDI 环境的一部分

Microsoft 产品是利用多种适用于远程用户配置文件的技术运行的，其中包括以下技术：
- 漫游用户配置文件 (RUP)
- 用户配置文件磁盘 (UPD)
- 企业状态漫游 (ESR)

UPD 和 RUP 是远程桌面会话主机 (RDSH) 和虚拟硬盘 (VHD) 环境中最广泛使用的用户配置文件技术。

### <a name="challenges-with-previous-user-profile-technologies"></a>旧的用户配置文件技术面临的挑战

用户配置文件的现有及旧式 Microsoft 解决方案面临着各种挑战。 没有任何旧的解决方案能够处理 RDSH 或 VDI 环境中存在的所有用户配置文件需求。 例如，UPD 无法处理大型 OST 文件，而 RUP 不会持久保存新式设置。

#### <a name="functionality"></a>功能

下表显示了旧用户配置文件技术的优势和局限性。

| 技术 | 新式设置 | Win32 设置 | OS 设置 | 用户数据 | 在服务器 SKU 上受支持 | Azure 上的后端存储 | 本地后端存储 | 版本支持 | 后续登录时间 |备注|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **用户配置文件磁盘 (UPD)** | 是 | 是 | 是 | 是 | 是 | 否 | 是 | Win 7+ | 是 | |
| **漫游用户配置文件 (RUP)，维护模式** | 否 | 是 | 是 | 是 | 是| 否 | 是 | Win 7+ | 否 | |
| **企业状态漫游 (ESR)** | 是 | 否 | 是 | 否 | 请参阅说明 | 是 | 否 | Win 10 | 否 | 在服务器 SKU 上起作用，但不提供支持性用户界面 |
| **用户体验虚拟化 (UE-V)** | 是 | 是 | 是 | 否 | 是 | 否 | 是 | Win 7+ | 否 |  |
| **OneDrive 云文件** | 否 | 否 | 否 | 是 | 请参阅说明 | 请参阅说明  | 请参阅说明 | Win 10 RS3 | 否 | 未在服务器 SKU 上进行测试。 Azure 上的后端存储依赖于同步客户端。 本地后端存储需要一个同步客户端。 |

#### <a name="performance"></a>性能

UPD 需要使用[存储空间直通 (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) 来满足性能要求。 UPD 使用服务器消息块 (SMB) 协议。 它将配置文件复制到用户正在登录的 VM。

#### <a name="cost"></a>成本

虽然 S2D 群集能够实现所需的性能，但成本对于大企业客户而言较为高昂，对于中小企业 (SMB) 客户而言特别高昂。 对于此解决方案，企业需要支付存储磁盘费用，以及将磁盘用作共享的 VM 的费用。

#### <a name="administrative-overhead"></a>管理开销

S2D 群集需要一个已修补、已更新并保持处于安全状态的操作系统。 这些流程以及设置 S2D 灾难恢复时存在的复杂性，使得 S2D 仅适用于拥有专职 IT 人员的企业。

## <a name="fslogix-profile-containers"></a>FSLogix 配置文件容器

2018 年 11 月 19 日，[Microsoft 收购了 FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/)。 FSLogix 解决了许多方面的配置文件容器难题。 其中包括以下关键方面：

- **性能：** [FSLogix 配置文件容器](/fslogix/configure-profile-container-tutorial/)具有很高的性能，解决了在过去会阻止缓存交换模式的性能问题。
- **OneDrive：** 如果没有 FSLogix 配置文件容器，则非持久性 RDSH 或 VDI 环境中不支持 OneDrive for Business。 [OneDrive VDI 支持页](/onedrive/sync-vdi-support)介绍了两者的交互方式。 有关详细信息，请参阅[在虚拟桌面上使用同步客户端](/deployoffice/rds-onedrive-business-vdi/)。
- **更多的文件夹：** FSLogix 提供扩展用户配置文件以包含更多文件夹的功能。

收购 FSLogix 后，Microsoft 已开始将现有用户配置文件解决方案（例如 UPD）替换为 FSLogix 配置文件容器。

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure 文件存储与 Azure Active Directory 域服务的集成

FSLogix 配置文件容器的性能和功能利用了云。 2019 年 8 月 7 日，Microsoft Azure 文件存储宣告正式发布[使用 Azure Active Directory 域服务 (AD DS) 进行 Azure 文件存储身份验证](../storage/files/storage-files-active-directory-overview.md)的功能。 使用 Azure AD DS 身份验证的 Azure 文件存储满足了成本和管理开销方面的需求，是适用于 Azure 虚拟桌面服务中用户配置文件的高级解决方案。

## <a name="best-practices-for-azure-virtual-desktop"></a>有关 Azure 虚拟桌面的最佳做法

Azure 虚拟桌面提供对客户所用 VM 的大小、类型和计数的完全控制。 有关详细信息，请参阅[什么是 Azure 虚拟桌面？](overview.md)。

若要确保 Azure 虚拟桌面环境遵循最佳做法：

- Azure 文件存储帐户必须位于会话主机 VM 所在的同一区域中。
- Azure 文件存储权限应与[要求 - 配置文件容器](/fslogix/fslogix-storage-config-ht)中所述的权限匹配。
- 每个主机池 VM 必须是基于同一主映像使用相同类型和大小的 VM 构建的。
- 每个主机池 VM 必须位于同一资源组中，以帮助进行管理、缩放和更新。
- 为了获得最佳性能，存储解决方案和 FSLogix 配置文件容器应在同一数据中心位置。
- 包含主映像的存储帐户必须位于要在其中预配 VM 的同一区域和订阅中。

## <a name="next-steps"></a>后续步骤

使用以下指南来设置 Azure 虚拟桌面环境。

- 若要开始构建桌面虚拟化解决方案，请参阅[在 Azure 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)。
- 若要在 Azure 虚拟桌面租户中创建主机池，请参阅[通过 Azure 市场创建主机池](create-host-pools-azure-marketplace.md)。
- 若要在云中设置完全托管的文件共享，请参阅[设置 Azure 文件存储共享](/azure/storage/files/storage-files-active-directory-enable/)。
- 若要配置 FSLogix 配置文件容器，请参阅[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。
- 若要将用户分配到主机池，请参阅[管理 Azure 虚拟桌面的应用组](manage-app-groups.md)。
- 若要通过 Web 浏览器访问 Azure 虚拟桌面资源，请参阅[连接到 Azure 虚拟桌面](./user-documentation/connect-web.md)。
