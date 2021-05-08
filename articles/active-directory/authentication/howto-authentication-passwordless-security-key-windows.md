---
title: 无密码安全密钥登录 Windows - Azure Active Directory
description: 了解如何使用 FIDO2 安全密钥实现无密码安全密钥登录 Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190e9c857f1ec9d19eb89493dc4b4a9fb68fac87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653501"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory"></a>实现无密码安全密钥登录包含 Azure Active Directory 的 Windows 10 设备 

本文档重点介绍在 Windows 10 设备中实现基于 FIDO2 安全密钥的无密码身份验证。 在本文末尾，你将能够通过 Azure AD 帐户使用 FIDO2 安全密钥登录到已建立 Azure AD 联接和混合 Azure AD 联接的 Windows 10 设备。

## <a name="requirements"></a>要求

| 设备类型 | 已加入 Azure AD | 已加入混合 Azure AD |
| --- | --- | --- |
| [Azure AD 多重身份验证](howto-mfa-getstarted.md) | X | X |
| [合并安全信息注册](concept-registration-mfa-sspr-combined.md) | X | X |
| 兼容的 [FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN 需要 Windows 10 版本 1903 或更高版本 | X | X |
| [已建立 Azure AD 联接的设备](../devices/concept-azure-ad-join.md)需要 Windows 10 版本 1909 或更高版本 | X |   |
| [已建立混合 Azure AD 联接的设备](../devices/concept-azure-ad-join-hybrid.md)需要 Windows 10 版本 2004 或更高版本 |   | X |
| 已完全修补的 Windows Server 2016/2019 域控制器。 |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 1.4.32.0 或更高版本 |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune)（可选） | X | X |
| 预配包（可选） | X | X |
| 组策略（可选） |   | X |

### <a name="unsupported-scenarios"></a>不支持的方案

不支持以下方案：

- Windows Server Active Directory 域服务 (AD DS) 已建立域联接（仅针对本地设备）的部署。
- 使用安全密钥的 RDP、VDI 和 Citrix 方案。
- 使用安全密钥的 S/MIME。
- 使用安全密钥的“运行身份”。
- 使用安全密钥登录到服务器。
- 如果尚未在设备联机时使用安全密钥登录设备，则无法使用它进行脱机登录或解锁。
- 使用包含多个 Azure AD 帐户的安全密钥登录或解锁 Windows 10 设备。 此方案使用添加到安全密钥的最近一个帐户。 WebAuthN 允许用户选择要使用的帐户。
- 解锁运行 Windows 10 版本 1809 的设备。 为获得最佳体验，请使用 Windows 10 版本 1903 或更高版本。

## <a name="prepare-devices"></a>准备设备

已建立 Azure AD 联接的设备必须运行 Windows 10 版本 1909 或更高版本。

已建立混合 Azure AD 联接的设备必须运行 Windows 10 版本 2004 或更新版本。

## <a name="enable-security-keys-for-windows-sign-in"></a>启用安全密钥登录 Windows

组织可能会根据自身要求选择使用以下一种或多种方法来启用安全密钥登录 Windows：

- [通过 Intune 启用](#enable-with-intune)
- [目标 Intune 部署](#targeted-intune-deployment)
- [通过预配包启用](#enable-with-a-provisioning-package)
- [通过组策略启用（仅针对已建立混合 Azure AD 联接的设备）](#enable-with-group-policy)

> [!IMPORTANT]
> 具有已建立混合 Azure AD 联接的设备的组织还必须完成本文中的[启用对本地资源的 FIDO2 身份验证](howto-authentication-passwordless-security-key-on-premises.md)步骤，Windows 10 FIDO2 安全密钥身份验证才能进行。
>
> 具有已建立 Azure AD 联接的设备的组织必须完成此步骤，其设备才能使用 FIDO2 安全密钥对本地资源进行身份验证。

### <a name="enable-with-intune"></a>通过 Intune 启用

若要通过 Intune 启用安全密钥，请完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Microsoft Intune” > “设备注册” > “Windows 注册” > “Windows Hello 企业版” > “属性”。
1. 在“设置”下，将“使用安全密钥登录”设置为“启用”。

安全密钥登录配置与 Windows Hello 企业版配置无关。

### <a name="targeted-intune-deployment"></a>目标 Intune 部署

若要以特定设备组为目标启用凭据提供程序，请通过 Intune 使用以下自定义设置：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Microsoft Intune” > “设备配置” > “配置文件” > “创建配置文件”。  
1. 使用以下设置配置新的配置文件：
   - 名称：用于登录 Windows 的安全密钥
   - 说明：启用登录 Windows 期间要使用的 FIDO 安全密钥
   - 平台：Windows 10 及更高版本
   - 配置文件类型：自定义
   - 自定义 OMA-URI 设置：
      - 名称：开启 FIDO 安全密钥登录 Windows
      - OMA-URI：./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - 数据类型：整数
      - 值：1
1. 可将此策略分配给特定的用户、设备或组。 有关详细信息，请参阅[在 Microsoft Intune 中分配用户和设备配置文件](/intune/device-profile-assign)。

![创建 Intune 自定义设备配置策略](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>通过预配包启用

对于不受 Intune 管理的设备，可以安装预配包以启用此功能。 可以从 [Microsoft 应用商店](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)安装 Windows 配置设计器应用。 完成以下步骤以创建预配包：

1. 启动 Windows 配置设计器。
1. 选择“文件” > “新建项目” 。
1. 为项目指定名称并记下在其中创建项目的路径，然后选择“下一步”。
1. 保持“预配包”选中“选定的项目工作流”，然后选择“下一步”。
1. 在“选择要查看和配置的设置”下选择“所有 Windows 桌面版本”，然后选择“下一步”。
1. 选择“完成”  。
1. 在新创建的项目中，浏览到“运行时设置” > “WindowsHelloForBusiness” > “SecurityKeys” > “UseSecurityKeyForSignIn”。
1. 将“UseSecurityKeyForSignIn”设置为“启用”。
1. 选择“导出” > “预配包”
1. 在“生成”窗口中，保留“描述预配包”下的默认值，然后选择“下一步”。  
1. 在“生成”窗口中，保留“选择预配包的安全性详细信息”下的默认值，然后选择“下一步”。  
1. 记下或更改“生成”窗口的“选择保存预配包的位置”下的路径，然后选择“下一步”。  
1. 在“生成预配包”页中选择“生成”。
1. 将创建的两个文件（ppkg 和 cat）保存到稍后可以在其中将它们应用到计算机的位置。
1. 若要应用创建的预配包，请参阅[应用预配包](/windows/configuration/provisioning-packages/provisioning-apply-package)。

> [!NOTE]
> 运行 Windows 10 版本 1903 的设备还必须启用共享 PC 模式 (EnableSharedPCMode)。 有关启用此功能的详细信息，请参阅[为 Windows 10 设置共享或来宾 PC](/windows/configuration/set-up-shared-or-guest-pc)。

### <a name="enable-with-group-policy"></a>通过组策略启用

对于 **已建立混合 Azure AD 联接的设备**，组织可以配置以下组策略设置，以启用 FIDO 安全密钥登录。 在“计算机配置” > “管理模板” > “系统” > “登录” > “开启安全密钥登录”下可以找到此设置：

- 将此策略设置为“启用”，将允许用户使用安全密钥登录。
- 将此策略设置为“禁用”或“未配置”，将阻止用户使用安全密钥登录。

“组策略”设置需要更新版本的 `CredentialProviders.admx` 组策略模板。 下一版本的 Windows Server 和 Windows 10 20H1 中提供此新模板。 可以使用运行这些 Windows 更新版本之一的设备管理此设置，也可以遵循支持主题[如何为 Windows 中的组策略管理模板创建和管理中央存储](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)中的指南进行集中管理。

## <a name="sign-in-with-fido2-security-key"></a>使用 FIDO2 安全密钥登录

在以下示例中，名为 Bala Sandhu 的用户已使用上一篇文章[启用无密码安全密钥登录](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)中的步骤预配其 FIDO2 安全密钥。 对于已建立混合 Azure AD 联接的设备，请确保还[启用了无密码安全密钥登录本地资源](howto-authentication-passwordless-security-key-on-premises.md)。 Bala 可以从 Windows 10 锁屏界面中选择安全密钥凭据提供程序，并插入安全密钥登录 Windows。

![Windows 10 锁屏界面中的安全密钥登录](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全密钥生物识别、PIN 或重置安全密钥

* Windows 10 版本 1903 或更高版本
   * 用户可以通过设备 >“帐户” > “安全密钥”打开“Windows 设置”。  
   * 用户可以更改 PIN，更新生物识别或重置安全密钥

## <a name="troubleshooting-and-feedback"></a>故障排除和反馈

如果你想要共享有关此功能的反馈或遇到的问题，请使用以下步骤，通过 Windows 反馈中心应用来进行共享：

1. 启动“反馈中心”并确保你已登录。
1. 按照以下分类提交反馈：
   - 类别：安全和隐私
   - 子类别：FIDO
1. 若要捕获日志，请使用选项“重新创建问题”。

## <a name="next-steps"></a>后续步骤

[为已建立 Azure AD 联接和混合 Azure AD 联接的设备启用对本地资源的访问权限](howto-authentication-passwordless-security-key-on-premises.md)

[详细了解注册设备](../devices/overview.md)

[详细了解 Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md)