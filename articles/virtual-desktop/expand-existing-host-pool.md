---
title: 利用新的会话主机扩展现有的主机池-Azure
description: 如何使用 Azure 虚拟桌面中的新会话主机扩展现有的主机池。
author: Heidilohr
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f37e7e18fd32c3ad0b06f1189c57f44d72dced7a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760854"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>利用新的会话主机扩展现有的主机池

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md)。

当你在主机池内提高使用率时，可能需要使用新的会话主机扩展现有的主机池以处理新负载。

本文将介绍如何使用新的会话主机扩展现有的主机池。

## <a name="what-you-need-to-expand-the-host-pool"></a>扩展主机池所需的条件

在开始之前，请确保已使用以下方法之一创建了主机池和会话主机虚拟机 (Vm)：

- [Azure 门户](./create-host-pools-azure-marketplace.md)
- [使用 PowerShell 创建主机池](./create-host-pools-powershell.md)

当你首次创建主机池和会话主机 Vm 时，还需要以下信息：

- VM 大小、映像和名称前缀
- 域加入管理员凭据
- 虚拟网络名称和子网名称

## <a name="add-virtual-machines-with-the-azure-portal"></a>添加具有 Azure 门户的虚拟机

通过添加虚拟机扩展主机池：

1. 登录到 Azure 门户。

2. 搜索并选择“Azure 虚拟桌面”。

3. 在屏幕左侧的菜单中，选择 " **主机池**"，然后选择要将虚拟机添加到的主机池的名称。

4. 在屏幕左侧的菜单中选择 " **会话主机** "。

5. 选择 " **+ 添加** " 开始创建主机池。

6. 忽略“基本信息”选项卡，然后选择“VM 详细信息”选项卡。在这里，你可以查看和编辑要添加到主机池的虚拟机 (VM) 的详细信息。

7. 选择要在下面创建 VM 的资源组，然后选择区域。 你可以选择当前使用的区域或新区域。

8. 输入要添加到主机池的会话主机数，并将其添加到 **VM 的数目**。 例如，如果要将主机池扩展到五台主机，请输入 **5**。

    >[!NOTE]
    >尽管可以编辑 VM 的映像和前缀，但如果 VM 在同一主机池中具有不同的映像，则不建议对其进行编辑。 仅当你计划从受影响的主机池中删除包含旧映像的 VM 时，才编辑映像和前缀。

9. 对于 **虚拟网络信息**，请选择要将虚拟机加入到的虚拟网络和子网。 你可以选择现有计算机当前使用的相同虚拟网络，或选择更适用于在步骤7中选择的区域的其他虚拟网络。

10. 对于“要加入的域”，选择是要将虚拟机加入 Active Directory 还是 [Azure Active Directory](deploy-azure-ad-joined-vm.md)。 选择“在 Intune 中注册 VM”会自动在 Intune 中注册虚拟机。 主机池中的所有虚拟机都应加入同一个域或 Azure AD 租户。

11. 对于“AD 域加入 UPN”，输入与你选择的域关联的 Active Directory 域用户名和密码。 这些凭据将用于将虚拟机加入 Active Directory 域。

      >[!NOTE]
      >确保你的管理员名称符合本文提供的信息。 并且不会对该帐户启用 MFA。

12. 对于“虚拟机管理员帐户”，输入要用于所有虚拟机的本地管理员帐户信息。

13. 如果你有要将虚拟机分组到其中的任何标记，请选择“标记”选项卡。 否则，请跳过此选项卡。

14. 选择“查看 + 创建”选项卡。查看你的选择，如果一切正常，请选择“创建” 。

## <a name="next-steps"></a>后续步骤

在扩展现有主机池后，可以登录到 Azure 虚拟桌面客户端，将其作为用户会话的一部分进行测试。 你可以使用以下任一客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](./user-documentation/connect-windows-7-10.md)
- [使用 Web 客户端进行连接](./user-documentation/connect-web.md)
- [使用 Android 客户端进行连接](./user-documentation/connect-android.md)
- [使用 macOS 客户端进行连接](./user-documentation/connect-macos.md)
- [使用 iOS 客户端进行连接](./user-documentation/connect-ios.md)
