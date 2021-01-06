---
title: Windows 虚拟桌面设置文件共享 .MSIX 应用附加预览版-Azure
description: 如何为 Windows 虚拟桌面设置 .MSIX 应用附加的文件共享。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930780"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>设置用于 .MSIX 应用附加 (预览的文件共享) 

> [!IMPORTANT]
> .MSIX 应用附加当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

所有 .MSIX 映像都必须存储在网络共享上，该网络共享可供具有只读权限的主机池中的用户访问。

.MSIX 应用附加 (预览) 与文件共享使用的存储构造类型无关。 .MSIX 应用附加共享的注意事项与 FSLogix 共享的注意事项相同。 若要了解有关存储要求的详细信息，请参阅 [Windows 虚拟桌面中的 FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

## <a name="performance-requirements"></a>性能要求

.MSIX 应用附加了你的系统的映像大小限制取决于你用来存储 VHD 或 VHDx 文件的存储类型，以及 VHD、VHSD 或 CIM 文件和文件系统的大小限制。

下表提供了一个示例，该示例演示了单个 1 GB .MSIX 映像中包含一个 .MSIX 应用的每个 VM 所需的资源：

| 资源             | 要求 |
|----------------------|--------------|
| 稳定状态 IOPs    | 1 IOPs       |
| 计算机启动登录 | 10 IOPs      |
| 延迟              | 400 ms       |

要求可能会有很大的不同，具体取决于 .MSIX 映像中存储了多少 .MSIX 打包的应用程序。 对于更大的 .MSIX 映像，需要分配更多的带宽。

### <a name="storage-recommendations"></a>有关存储的建议

Azure 提供了多个存储选项，可用于 MISX 应用附加。 建议使用 Azure 文件或 Azure NetApp 文件，因为这些选项提供成本和管理开销的最佳价值。 [Windows 虚拟桌面中的 FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)一文比较了 Windows 虚拟桌面环境中 Azure 提供的不同托管存储解决方案。

### <a name="optimize-msix-app-attach-performance"></a>优化 .MSIX 应用附加性能

下面是建议优化 .MSIX 应用附加性能的一些其他事项：

- 用于 .MSIX 应用附加的存储解决方案应位于与会话主机相同的数据中心位置。
- 若要避免性能瓶颈，请从防病毒扫描中排除以下 VHD、VHDX 和 CIM 文件：
   
    - <MSIXAppAttachFileShare \> \* 。硬盘
    - <MSIXAppAttachFileShare \> \* 。VHDX
    - \\\\storageaccount.file.core.windows.net \\ 共享 \* \* 。硬盘
    - \\\\storageaccount.file.core.windows.net \\ 共享 \* \* 。VHDX
    - <MSIXAppAttachFileShare>.CIM
    - \\\\storageaccount.file.core.windows.net \\ 共享 \* \* 。CIM

- 将 .MSIX 应用的存储构造与 FSLogix 配置文件容器分离。
- 所有 VM 系统帐户和用户帐户都必须具有访问该文件共享的只读权限。
- 适用于 Windows 虚拟桌面的任何灾难恢复计划必须包括将 .MSIX 应用附加文件共享复制到辅助故障转移位置。 若要了解有关灾难恢复的详细信息，请参阅 [设置业务连续性和灾难恢复计划](disaster-recovery.md)。

## <a name="how-to-set-up-the-file-share"></a>如何设置文件共享

.MSIX 应用附加文件共享的设置过程在很大程度上与 [FSLogix 配置文件共享的安装过程](create-host-pools-user-profile.md)相同。 但是，需要为用户分配不同的权限。 .MSIX 应用附加需要只读权限才能访问文件共享。

如果要将 .MSIX 应用程序存储在 Azure 文件中，则对于你的会话主机，你将需要为所有会话主机 Vm 分配存储帐户基于角色的访问控制 (RBAC) 和文件共享新技术文件系统 (NTFS) 对共享的权限。

| Azure 对象                      | 必需的角色                                     | Role 函数                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| 会话主机 (VM 计算机对象) | 存储文件数据 SMB 共享参与者          | 读取和执行、读取、列出文件夹内容  |
| 文件共享上的管理员              | 存储文件数据 SMB 共享特权参与者 | 完全控制                                  |
| 文件共享上的用户               | 存储文件数据 SMB 共享参与者          | 读取和执行、读取、列出文件夹内容  |

为存储帐户和文件共享分配会话主机 Vm 权限：

1. ) 安全组中创建一个 Active Directory 域服务 (AD DS。

2. 将所有会话主机 Vm 的计算机帐户添加为该组的成员。

3. 将 AD DS 组同步到 Azure Active Directory (Azure AD) 。

4. 创建存储帐户。

5. 按照 [创建 Azure 文件共享](../storage/files/storage-how-to-create-file-share.md#create-file-share)中的说明，在存储帐户下创建文件共享。

6. 按照 [第一部分：为 Azure 文件共享启用 AD DS 身份验证](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)中的说明，将存储帐户加入到 AD DS。

7. 将同步的 AD DS 组分配给 Azure AD，并为存储帐户分配存储文件数据 SMB 共享参与者角色。

8. 按照 [第二部分：向标识分配共享级权限](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)中的说明，将文件共享装载到任何会话主机。

9. 向 AD DS 组授予对文件共享的 NTFS 权限。

10. 设置用户帐户的 NTFS 权限。 你将需要一个 (OU) 的运营单位，该单位来源于 VM 中的帐户所属的 AD DS。

将标识分配到存储后，请按照 [后续步骤](#next-steps) 中的文章中的说明向已分配给 vm 的标识授予其他所需权限。

还需要确保会话主机 Vm 具有新的技术文件系统 (NTFS) 权限。 你必须具有源自 Active Directory 域服务 (AD DS) 的操作单元容器，并且你的用户必须是该操作单元的成员才能使用这些权限。

## <a name="next-steps"></a>后续步骤

下面是在设置文件共享之后需要执行的其他操作：

- 了解如何设置 Azure Active Directory 域服务 (AD DS) [使用 Azure 文件和 AD DS 创建配置文件容器](create-file-share.md)。
- 了解如何 [使用 Azure 文件和 AZURE AD ds 在创建配置文件容器](create-profile-container-adds.md)时设置 azure 文件和 Azure AD ds。
- 了解如何在 [使用 Azure Netapp 文件和 AD DS 创建配置文件容器](create-fslogix-profile-container.md)中设置 .msix 应用附加的 Azure NetApp 文件。
- 了解如何使用基于虚拟机的文件共享，以便 [使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。

完成后，你可能会发现以下其他资源：

- 请在 [Windows 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)上咨询有关此功能的社区问题。
- 还可以在 [Windows 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Windows 虚拟桌面的反馈。
- [.MSIX 应用附加术语表](app-attach-glossary.md)
- [.MSIX 应用附加常见问题解答](app-attach-faq.md)
