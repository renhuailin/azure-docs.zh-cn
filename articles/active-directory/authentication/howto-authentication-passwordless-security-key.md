---
title: 无密码安全密钥登录 - Azure Active Directory
description: 使用 FIDO2 安全密钥实现无密码安全密钥登录 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 436a972693aafd220d277d7411c0da12636e9cc6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829793"
---
# <a name="enable-passwordless-security-key-sign-in"></a>启用无密码安全密钥登录 

对于目前使用密码并且具有共享的 PC 环境的企业，安全密钥为工作者提供了无需输入用户名或密码即可进行身份验证的无缝方式。 安全密钥可提高工作者的工作效率，并且安全性更高。

本文档重点介绍如何启用基于安全密钥的无密码身份验证。 在本文末尾，你将能够使用 FIDO2 安全密钥通过 Azure AD 帐户登录到基于 Web 的应用程序。

## <a name="requirements"></a>要求

- [Azure AD 多重身份验证](howto-mfa-getstarted.md)
- 启用[合并安全信息注册](concept-registration-mfa-sspr-combined.md)
- 兼容的 [FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN 需要 Windows 10 版本 1903 或更高版本**

若要使用安全密钥来登录 Web 应用和服务，你必须具有支持 WebAuthN 协议的浏览器。 这种浏览器包括 Microsoft Edge、Chrome、Firefox 和 Safari。


## <a name="prepare-devices"></a>准备设备

对于已加入 Azure AD 的设备，最佳体验是在 Windows 10 版本 1903 或更高版本上。

已加入混合 Azure AD 的设备必须运行 Windows 10 版本 2004 或更高版本。

## <a name="enable-passwordless-authentication-method"></a>启用无密码身份验证方法

### <a name="enable-the-combined-registration-experience"></a>启用合并注册体验

适用于无密码身份验证方法的注册功能依赖于合并注册功能。 请按照[启用合并安全信息注册](howto-registration-mfa-sspr-combined.md)一文中的步骤来启用合并注册。

### <a name="enable-fido2-security-key-method"></a>启用 FIDO2 安全密钥方法

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory” > “安全” > “身份验证方法” > “身份验证方法策略”   。
1. 在方法“FIDO2 安全密钥”下，选择以下选项：
   1. “启用”-“是”或“否”
   1. “目标”-“所有用户”或“选择用户”
1. 保存配置。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 安全密钥的用户注册和管理

1. 浏览到 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)。
1. 如果尚未登录，请登录。
1. 单击“安全信息”。
   1. 如果用户已注册至少一种 Azure AD 多重身份验证方法，则他们可以立即注册 FIDO2 安全密钥。
   1. 如果他们尚未注册任何 Azure AD 多重身份验证方法，则必须添加一种。
1. 通过单击“添加方法”并选择“安全密钥”来添加 FIDO2 安全密钥 。
1. 选择“USB 设备”或“NFC 设备”。
1. 准备好密钥，然后选择“下一步”。
1. 屏幕上将会出现一个框，要求用户为你的安全密钥创建/输入 PIN，然后为该密钥执行所需的笔势（生物识别或触摸）。
1. 用户将返回到合并注册体验，并需要为密钥提供有意义的名称，以便识别出特定密钥（如果他们有多个密钥）。 单击 **“下一步”** 。
1. 单击“完成”以完成此过程。

## <a name="sign-in-with-passwordless-credential"></a>通过无密码凭据登录

在下面的示例中，用户已预配 FIDO2 安全密钥。 在 Windows 10 版本 1903 或更高版本中，用户可以选择在受支持的浏览器内使用 FIDO2 安全密钥登录 Web。

![安全密钥登录 Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>故障排除和反馈

如果你想要共享有关此功能的反馈或遇到的问题，请使用以下步骤，通过 Windows 反馈中心应用来进行共享：

1. 启动反馈中心并确保你已登录。
1. 按照以下分类提交反馈：
   - 类别：安全和隐私
   - 子类别：FIDO
1. 若要捕获日志，请使用选项“重新创建问题”。

## <a name="known-issues"></a>已知问题

### <a name="security-key-provisioning"></a>安全密钥预配

管理员预配和取消预配安全密钥不可用。

### <a name="cached-logon-on-hybrid-azure-ad-joined-devices"></a>混合 Azure AD 联接设备上缓存的登录

在运行 Windows 10 版本 20H2 的混合 Azure AD 联接设备上，使用 FIDO2 密钥的缓存登录失败。 因此，无法访问本地域控制器时，用户无法登录。 此问题目前正在调查中。

### <a name="upn-changes"></a>UPN 更改

我们正在努力支持一项功能，该功能允许在已加入混合 Azure AD 的和已加入 Azure AD 的设备上进行 UPN 更改。 如果用户的 UPN 发生更改，你将无法再修改 FIDO2 安全密钥来消除该更改。 解决方法是重置设备，并且用户必须重新注册。

## <a name="next-steps"></a>后续步骤

[FIDO2 安全密钥 Windows 10 登录](howto-authentication-passwordless-security-key-windows.md)

[对本地资源启用 FIDO2 身份验证](howto-authentication-passwordless-security-key-on-premises.md)

[详细了解如何注册设备](../devices/overview.md)

[详细了解 Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md)
