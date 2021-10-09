---
title: 在 Azure 虚拟桌面 (Azure) 中部署联接 Azure AD 的 VM - Azure
description: 如何在 Azure 虚拟桌面中配置和部署联接 Azure AD 的 VM。
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
ms.openlocfilehash: e6325c6511c6df9c3f3c021bc24a3f66b2e56c0f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207728"
---
# <a name="deploy-azure-ad-joined-virtual-machines-in-azure-virtual-desktop"></a>在 Azure 虚拟桌面中部署联接 Azure AD 的虚拟机

本文将介绍在 Azure 虚拟桌面中部署和访问联接 Azure Active Directory 的虚拟机的过程。 联接 Azure AD 的 VM 无需具有从 VM 到本地的或虚拟化的 Active Directory 域控制器 (DC) 的视觉线，也无需部署 Azure AD 域服务 (Azure AD DS)。 在某些情况下，它可以完全消除 DC 的需求，从而简化环境的部署和管理。 这些 VM 还可以在 Intune 中自动注册，以便于管理。

> [!NOTE]
> 联接 Azure AD 的 VM 目前仅在 Azure 商业云中受支持。

## <a name="supported-configurations"></a>支持的配置

联接 Azure AD 的 VM 目前支持以下配置：

- 具有本地用户配置文件的个人桌面。
- 用作跳转盒的共用桌面。 在此配置中，用户先访问 Azure 虚拟桌面 VM，然后再连接到网络上的其他电脑。 用户不应在 VM 上保存数据。
- 用户无需在 VM 上保存数据的共用桌面或应用。 例如，联机保存数据或连接到远程数据库的应用程序。

用户帐户可以是同一 Azure AD 租户中的仅云用户或混合用户。

## <a name="known-limitations"></a>已知的限制

以下已知限制可能会影响对本地的或联接 Active Directory 域的资源的访问。在确定联接 Azure AD 的 VM 是否适合你的环境时，应当考虑这些限制。 目前，对于用户只需访问基于云的资源或基于 Azure AD 的身份验证的情况，建议使用联接 Azure AD 的 VM。

- Azure 虚拟桌面（经典）不支持联接 Azure AD 的 VM。
- 联接 Azure AD 的 VM 当前不支持外部用户。
- 联接 Azure AD 的 VM 当前仅支持本地用户配置文件。
- 联接 Azure AD 的 VM 无法访问 FSLogix 或 MSIX 应用附加的 Azure 文件存储文件共享。 需要 Kerberos 身份验证来访问这些功能之一。
- Windows 应用商店客户端当前不支持联接 Azure AD 的 VM。
- Azure 虚拟桌面目前不支持联接 Azure AD 的 VM 的单一登录。

## <a name="deploy-azure-ad-joined-vms"></a>部署联接 Azure AD 的 VM

[创建新主机池](create-host-pools-azure-marketplace.md)或[扩展现存主机池](expand-existing-host-pool.md)时，可以直接从 Azure 门户部署联接 Azure AD 的 VM。 在虚拟机选项卡上，选择将 VM 联接至 Active Directory 或 Azure Active Directory。 选择 Azure Active Directory 后，可以选择自动用 Intune 注册 VM，以便轻松管理 [Windows 10 企业版](/mem/intune/fundamentals/windows-virtual-desktop)和 [Windows 10 企业版多会话](/mem/intune/fundamentals/windows-virtual-desktop-multi-session) VM。 请记住，Azure Active Directory 选项将会把 VM 联接至你所在订阅中的同一 Azure AD 租户。

> [!NOTE]
> - 主机池应该仅包含相同域加入类型的 VM。 例如，联接 AD 的 VM 应该仅与其他 AD VM 一起，反之亦然。
> - 主机池 VM 必须为 Windows 10 单会话或多会话，版本为 2004 或者更高。

### <a name="assign-user-access-to-host-pools"></a>为用户分配对主机池的访问权限

创建主机池后，必须为用户分配访问权限，以便他们访问其资源。 若要授予对资源的访问权限，请将每个用户添加到应用组。 按照[管理应用组](manage-app-groups.md)中的说明，为用户分配应用和桌面的访问权限。 我们建议尽可能使用用户组而不是单个用户。

对于联接 Azure AD 的 VM，需要在基于 Active Directory 或 Azure Active Directory 域服务的部署的要求的基础上执行两项额外操作：  

- 为用户分配“虚拟机用户登录”角色，让他们可以登录到 VM。
- 为需要本地管理权限的管理员分配“虚拟机管理员登录”角色。

若要向用户授予联接 Azure AD 的 VM 的访问权限，则必须[为 VM 配置角色分配](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#configure-role-assignments-for-the-vm)。 可以在 VM、包含 VM 的资源组或订阅上分配虚拟机用户登录名或虚拟机管理员登录角色 。 建议将虚拟机用户登录角色分配给在资源组级别用于应用组的同一用户组，让其应用于主机池中的所有 VM。

## <a name="access-azure-ad-joined-vms"></a>访问联接 Azure AD 的 VM

本节介绍如何从不同的 Azure 虚拟桌面客户端访问联接 Azure AD 的 VM。

### <a name="connect-using-the-windows-desktop-client"></a>使用 Windows 桌面客户端连接

默认配置支持 Windows 10 使用 [Windows 桌面客户端](user-documentation/connect-windows-7-10.md)进行连接。 可以使用凭据、智能卡、[Windows Hello 企业版证书信任](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)或 [Windows Hello 企业版证书密钥信任](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)来登录到会话主机。 但是，若要访问会话主机，本地电脑必须满足以下条件之一：

- 本地电脑 Azure AD 会话主机联接到同一个 Azure AD 租户
- 本地电脑 Azure AD 与会话主机混合联接到同一个 Azure AD 租户
- 本地电脑在 Windows 10（2004 及更高版本）上运行，Azure AD 注册到与会话主机相同的 Azure AD 租户

若要从未联接至 Azure AD 的 Windows 设备进行访问，请将 targetisaadjoined:i:1 作为[自定义 RDP 属性](customize-rdp-properties.md)添加到主机池中。 这些连接仅限在登录会话主机时输入用户名和密码凭据。

### <a name="connect-using-the-other-clients"></a>使用其他客户端连接

若要使用 Web、Android、macOS 和 iOS 客户端访问联接 Azure AD 的 VM，必须将 targetisaadjoined:i:1 作为[自定义 RDP 属性](customize-rdp-properties.md)添加到主机池中。 这些连接仅限在登录会话主机时输入用户名和密码凭据。

### <a name="enabling-mfa-for-azure-ad-joined-vms"></a>为联接 Azure AD 的 VM 启用 MFA

可以通过在 Azure 虚拟桌面应用上设置条件访问策略，为联接 Azure AD 的 VM 启用[多重身份验证](set-up-mfa.md)。 若要成功进行连接，必须[禁用旧版按用户多重身份验证](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required)。 如果不想将登录限制为强身份验证方法（如 Windows Hello for Business），则还需要从条件访问策略中[排除 Azure Windows VM Sign-In 应用](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required)。

## <a name="user-profiles"></a>用户配置文件

Azure 虚拟桌面目前仅支持联接 Azure AD 的 VM 的本地配置文件。

## <a name="next-steps"></a>后续步骤

现在你已经部署了一些联接 Azure AD 的 VM，可以登录到支持的 Azure 虚拟桌面客户端，将其作为用户会话的一部分进行测试。 如需了解如何连接到会话，请参阅以下文章：

- [使用 Windows 桌面客户端进行连接](user-documentation/connect-windows-7-10.md)
- [使用 Web 客户端进行连接](user-documentation/connect-web.md)
- [排查联接 Azure AD 的 VM 的连接问题](troubleshoot-azure-ad-connections.md)
