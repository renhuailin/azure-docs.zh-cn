---
title: 与已加入 Azure AD 的 VM 的连接 - Azure 虚拟桌面 - Azure
description: 如何解决在 Azure 虚拟桌面中连接到已加入 Azure AD 的 VM 时遇到的问题。
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.author: helohr
ms.openlocfilehash: 97c81eadf7091f04b77b094a4622c1731d19c537
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228882"
---
# <a name="connections-to-azure-ad-joined-vms"></a>与已加入 Azure AD 的 VM 的连接

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。

使用本文解决在 Azure 虚拟桌面中连接到已加入 Azure AD 的 VM 时遇到的问题。

## <a name="provide-feedback"></a>提供反馈

请访问 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum)，与产品团队和活跃的社区成员共同探讨 Azure 虚拟桌面服务。

## <a name="all-clients"></a>所有客户端

### <a name="your-account-is-configured-to-prevent-you-from-using-this-device"></a>你的帐户配置为阻止你使用此设备

如果遇到一个错误指出“你的帐户已配置为阻止你使用此设备。有关详细信息，请与系统管理员联系”，请确保为用户帐户提供了 VM 上的[虚拟机用户登录角色](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#azure-role-not-assigned)。 

## <a name="windows-desktop-client"></a>Windows 桌面客户端

### <a name="the-logon-attempt-failed"></a>登录尝试失败

如果在 Windows 安全凭据提示处遇到一个错误指出“登录尝试失败”，请验证以下内容：

- 你位于已加入 Azure AD 的设备或已建立 Azure AD 混合联接的设备上，并且该设备与会话主机位于同一 Azure AD 租户中，或者
- 你位于运行 Windows 10 2004 或更高版本的设备上，并且该设备已在 Azure AD 中注册并与会话主机位于同一 Azure AD 租户中
- 本地电脑和会话主机上均[启用了 PKU2U 协议](/windows/security/threat-protection/security-policy-settings/network-security-allow-pku2u-authentication-requests-to-this-computer-to-use-online-identities)

### <a name="the-sign-in-method-youre-trying-to-use-isnt-allowed"></a>你尝试使用的登录方法不受允许

如果遇到一个错误指出“你尝试使用的登录方法不受允许。请尝试其他登录方法或与系统管理员联系”，则表明你的条件访问策略限制了访问。 按照[启用多重身份验证](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms)中的说明，为已加入 Azure AD 的 VM 启用多重身份验证。

## <a name="web-client"></a>Web 客户端

### <a name="sign-in-failed-please-check-your-username-and-password-and-try-again"></a>登录失败。 请检查用户名和密码，然后重试

如果在使用 Web 客户端时，遇到一个错误指出“糟糕，我们无法连接到 NAME。登录失败。请检查用户名和密码，然后重试。”， 请确保[已启用来自其他客户端的连接](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients)。

### <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>由于安全错误，无法连接到远程电脑

如果遇到一个错误指出“糟糕，我们无法连接到 NAME。由于安全错误，无法连接到远程电脑。如果这种情况持续发生，请向管理员或技术支持人员寻求帮助。”，则表明你的条件访问策略限制了访问。 按照[启用多重身份验证](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms)中的说明，为已加入 Azure AD 的 VM 启用多重身份验证。

## <a name="android-client"></a>Android 客户端

### <a name="error-code-2607---we-couldnt-connect-to-the-remote-pc-because-your-credentials-did-not-work"></a>错误代码 2607 - 无法连接到远程电脑，因为你的凭据无效

如果在使用 Android 客户端时，遇到一个错误代码为 2607 的错误，指出“无法连接到远程电脑，因为你的凭据无效。远程计算机已加入 AADJ。”， 请确保[已启用来自其他客户端的连接](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients)。

## <a name="next-steps"></a>后续步骤

- 如需大致了解如何排查 Azure 虚拟桌面问题和跟踪升级，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要排查创建 Azure 虚拟桌面环境和在 Azure 虚拟桌面环境中主机池时遇到的问题，请参阅[环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要排查在 Azure 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要排查与 Azure 虚拟桌面代理或会话连接性相关的问题，请参阅[排查常见的 Azure 虚拟桌面代理问题](troubleshoot-agent.md)。
- 若要排查将 PowerShell 与 Azure 虚拟桌面结合使用时遇到的问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
