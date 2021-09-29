---
title: Azure 虚拟桌面设置文件共享 MSIX 应用附加 - Azure
description: 如何为 Azure 虚拟桌面设置用于 MSIX 应用附加的文件共享。
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b568b11b7e0e630dcceef53e4c0f513dc0a7732c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547550"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>设置用于 MSIX 应用附加的文件共享

所有 MSIX 映像都必须存储在主机池中具有只读权限的用户可以访问的网络共享上。

MSIX 应用附加不依赖于文件共享使用的存储构造的类型。 MSIX 应用附加共享的注意事项与 FSLogix 共享的注意事项相同。 若要详细了解存储要求，请参阅 [Azure 虚拟桌面中的 FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

## <a name="performance-requirements"></a>性能要求

系统的 MSIX 应用附加映像大小限制取决于用来存储 VHD 或 VHDx 文件的存储类型，以及 VHD、VHDX 或 CIM 文件和文件系统的大小限制。

下表举例说明了对于每个 VM，其内具有一个 MSIX 应用的单个 1 GB MSIX 映像需要多少资源：

| 资源             | 要求 |
|----------------------|--------------|
| 稳定状态 IOPS    | 1 IOPS       |
| 计算机启动登录 | 10 IOPS      |
| 延迟              | 400 毫秒       |

要求可能会有很大的不同，具体取决于 MSIX 映像中存储了多少 MSIX 打包的应用程序。 对于更大的 MSIX 映像，你需要分配更多的带宽。

### <a name="storage-recommendations"></a>有关存储的建议

Azure 提供了可用于 MISX 应用附加的多个存储选项。 建议使用 Azure 文件存储或 Azure NetApp 文件，因为这些选项在成本和管理开销之间提供了最佳价值平衡。 [Azure 虚拟桌面中的 FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)一文比较了 Azure 在 Azure 虚拟桌面环境中提供的各种托管存储解决方案。

### <a name="optimize-msix-app-attach-performance"></a>优化 MSIX 应用附加性能

下面是建议你执行的用于优化 MSIX 应用附加性能的一些其他操作：

- 用于 MSIX 应用附加的存储解决方案应与会话主机位于同一数据中心位置。
- 若要避免性能瓶颈，请从防病毒扫描中排除以下 VHD、VHDX 和 CIM 文件：
   
    - `<MSIXAppAttachFileShare\>\*.VHD`
    - `<MSIXAppAttachFileShare\>\*.VHDX`
    - `\\storageaccount.file.core.windows.net\share*.VHD`
    - `\\storageaccount.file.core.windows.net\share*.VHDX`
    - `<MSIXAppAttachFileShare>.CIM`
    - `\\storageaccount.file.core.windows.net\share**.CIM`

- 将用于 MSIX 应用附加的存储构造与 FSLogix 配置文件容器分离。
- 所有 VM 系统帐户和用户帐户都必须具有访问文件共享的只读权限。
- 针对 Azure 虚拟机的任何灾难恢复计划都必须包括将 MSIX 应用附加文件共享复制到辅助故障转移位置的操作。 若要详细了解灾难恢复，请参阅[设置业务连续性和灾难恢复计划](disaster-recovery.md)。

## <a name="how-to-set-up-the-file-share"></a>如何设置文件共享

MSIX 应用附加文件共享的设置过程在很大程度上与 [FSLogix 配置文件文件共享的设置过程](create-host-pools-user-profile.md)相同。 但是，你需要为用户分配不同的权限。 MSIX 应用附加需要用于访问文件共享的只读权限。

如果你将 MSIX 应用程序存储在 Azure 文件存储中，则对于会话主机，你需要为所有会话主机 VM 分配存储帐户的基于角色的访问控制 (RBAC) 和对共享的文件共享新技术文件系统 (NTFS) 权限。

| Azure 对象                      | 所需角色                                     | 角色功能                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| 会话主机（VM 计算机对象）| 存储文件数据 SMB 共享参与者          | 读取和执行、读取、列出文件夹内容  |
| 文件共享上的管理员              | 存储文件数据 SMB 共享特权参与者 | 完全控制                                  |
| 文件共享上的用户               | 存储文件数据 SMB 共享参与者          | 读取和执行、读取、列出文件夹内容  |

若要为会话主机 VM 分配对存储帐户和文件共享的权限，请执行以下操作：

1. 创建一个 Active Directory 域服务 (AD DS) 安全组。

2. 将所有会话主机 VM 的计算机帐户添加为该组的成员。

3. 将 AD DS 组同步到 Azure Active Directory (Azure AD)。

4. 创建存储帐户。

5. 按照[创建 Azure 文件共享](../storage/files/storage-how-to-create-file-share.md#create-a-file-share)中的说明，在存储帐户下创建一个文件共享。

6. 按照[第一部分：为 Azure 文件共享启用 AD DS 身份验证](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)中的说明，将存储帐户加入到 AD DS。

7. 将同步后的 AD DS 组分配给 Azure AD，并为存储帐户分配存储文件数据 SMB 共享参与者角色。

8. 按照[第二部分：为标识分配共享级权限](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)中的说明，将文件共享装载到任何会话主机。

9. 向 AD DS 组授予对文件共享的 NTFS 权限。

10. 设置用户帐户的 NTFS 权限。 你需要一个来源于 VM 中帐户所属的 AD DS 的操作单位 (OU)。

将标识分配给存储后，请按照[后续步骤](#next-steps)中的文章中的说明，向已分配给 VM 的标识授予其他所需权限。

你还需要确保会话主机 VM 具有新技术文件系统 (NTFS) 权限。 若要使用这些权限，你必须有一个来源于 Active Directory 域服务 (AD DS) 的操作单位容器，并且你的用户必须是该操作单位的成员。

## <a name="next-steps"></a>后续步骤

下面是在设置文件共享之后需要执行的其他操作：

- 从[使用 Azure 文件存储和 AD DS 创建配置文件容器](create-file-share.md)了解如何设置 Azure Active Directory 域服务 (AD DS)。
- 从[使用 Azure 文件存储和 Azure AD DS 创建配置文件容器](create-profile-container-adds.md)了解如何设置 Azure 文件存储和 Azure AD DS。
- 从[使用 Azure NetApp 文件和 AD DS 创建配置文件容器](create-fslogix-profile-container.md)了解如何设置用于 MSIX 应用附加的 Azure NetApp 文件。
- 从[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)了解如何使用基于虚拟机的文件共享。

完成后，你可以查看下面的一些可能有用的其他资源：

- 请在 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)询问有关此功能的问题。
- 还可以在 [Azure 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Azure 虚拟桌面的反馈。
- [MSIX 应用附加术语表](app-attach-glossary.md)
- [MSIX 应用附加常见问题解答](app-attach-faq.yml)
