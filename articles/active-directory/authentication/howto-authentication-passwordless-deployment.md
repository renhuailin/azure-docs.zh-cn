---
title: 在 Azure Active Directory 中规划无密码身份验证部署
description: 部署无密码身份验证的说明
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/28/2021
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ee82c26217ac1b760c59f56f9875b7dcbcaf663
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124759807"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>在 Azure Active Directory 中规划无密码身份验证部署

密码是主要的攻击向量。 不良参与者会使用社交工程、网络钓鱼和喷射攻击来破解密码。 而无密码身份验证策略降低了此类攻击的风险。

Microsoft 提供了以下与 Azure Active Directory (Azure AD) 集成的[三个无密码身份验证选项](concept-authentication-passwordless.md)：

* [Microsoft Authenticator 应用](./concept-authentication-passwordless.md#microsoft-authenticator-app) - 可将任何 iOS 或 Android 手机变成强大的无密码凭据，从而使用户可以登录到任何平台或浏览器。

* [FIDO2 安全密钥](./concept-authentication-passwordless.md#fido2-security-keys) - 在用户登录到共享机器（例如展台）、限制使用手机以及标识具有高特权等情况下，该方法特别有用。 

* [Windows Hello 企业版](./concept-authentication-passwordless.md#windows-hello-for-business) - 最适合拥有专用 Windows 计算机的用户。 

> [!NOTE]
> 若要创建此包含所有链接的计划的脱机版本，请使用浏览器的“打印为 PDF 格式”功能。

## <a name="use-the-passwordless-methods-wizard"></a>使用无密码方法向导

[Azure 门户](https://portal.azure.com/)现可提供无密码方法向导，有助于为每位受众选择适当的方法。 如果尚未确定适当的方法，请参阅 [https://aka.ms/passwordlesswizard](https://aka.ms/passwordlesswizard)，然后返回本文，继续规划所选的方法。 需要管理员权限才能访问此向导。

## <a name="passwordless-authentication-scenarios"></a>无密码身份验证方案

Microsoft 的无密码身份验证方法可实现多个方案。 请考虑贵组织的需求、先决条件以及每种身份验证方法的功能，以选择你的无密码身份验证策略。 

下表按设备类型列出了无密码身份验证方法。 我们的建议以粗体显示。

| 设备类型| 无密码身份验证方法 |
| - | - |
| 专用非 windows 设备| <li> **Microsoft Authenticator 应用** <li> 安全密钥 |
| 专用 Windows 10 计算机（版本 1703 及以上）| <li> **Windows Hello for Business** <li> 安全密钥 |
| 专用 Windows 10 计算机（1703 之前的版本）| <li> **Windows Hello for Business** <li> Microsoft Authenticator 应用 |
| 共享设备：平板电脑和移动设备| <li> **Microsoft Authenticator 应用** <li> 一次性密码登录 |
| 展台（传统）| **Microsoft Authenticator 应用** |
| 展台和共享计算机 (Windows 10)| <li> **安全密钥** <li> Microsoft Authenticator 应用 |


## <a name="prerequisites"></a>先决条件 

在开始无密码部署之前，请确保满足先决条件。

### <a name="required-roles"></a>必需的角色

下面是此部署所需的最低特权角色：
<p>

| Azure AD 角色| 说明 |
| - | -|
| 全局管理员| 实现合并注册体验。 |
| 身份验证管理员| 实现和管理身份验证方法。 |
| 用户| 在设备上配置 Authenticator 应用或者为安全密钥设备注册 Web 登录或 Windows 10 登录。 |

作为此部署计划的一部分，我们建议为所有[特权帐户](../privileged-identity-management/pim-configure.md)启用无密码身份验证。

### <a name="microsoft-authenticator-app-and-security-keys"></a>Microsoft Authenticator 应用和安全密钥

先决条件由所选的无密码身份验证方法决定。

| 先决条件| Microsoft Authenticator 应用| FIDO2 安全密钥|
| - | -|-|
| 已启用 [Azure AD 多重身份验证 (MFA) 和自助式密码重置 (SSPR) 组合式注册](howto-registration-mfa-sspr-combined.md)| √| √|  |
| [用户可以执行 Azure AD MFA](howto-mfa-getstarted.md)| √| √|  |
| [用户已注册 Azure AD MFA 和 SSPR](howto-registration-mfa-sspr-combined.md)| √| √|  |
| [用户已将其移动设备注册到 Azure Active Directory](../devices/overview.md)| √| |  |
| Windows 10 版本 1809 或更高版本，使 Microsoft Edge 或 Mozilla Firefox（版本 67 或更高版本）等受支持的浏览器。 Microsoft 推荐版本 1903 或更高版本以获取本机支持。| | √|  |
| 兼容的安全密钥。 确保使用的是[经过 Microsoft 测试和验证的 FIDO2 安全密钥](concept-authentication-passwordless.md)或其他兼容的 FIDO2 安全密钥。| | √|  |


### <a name="windows-hello-for-business"></a>Windows Hello 企业版

Windows Hello 企业版的先决条件和部署路径很大程度上取决于你是在本地配置、混合配置还是仅云配置中进行部署。 还取决于设备的加入策略。 

选择“Windows Hello 企业版”并[完成向导](https://aka.ms/passwordlesswizard)，以确定适用于贵组织的先决条件和部署方式。

![在向导中选择 Windows Hello 企业版](media/howto-authentication-passwordless-deployment/passwordless-wizard-select.png)


该向导将使用你输入的信息制定分步计划，以供遵循。

## <a name="plan-the-project"></a>计划项目

如果技术项目失败，通常是由于在影响、结果和责任方面不符合预期而导致的。 若要避免这些问题，[请确保吸引适当的利益干系人](../fundamentals/active-directory-deployment-plans.md)并充分了解项目中的利益干系人角色。

### <a name="plan-a-pilot"></a>规划试点

部署无密码身份验证时，应该首先启用一个或多个试点组。 可以专门为此目的创建组。 将参与试点的用户添加到组中。 然后，为所选组启用新的无密码身份验证方法。 请参阅[关于试点的最佳做法](../fundamentals/active-directory-deployment-plans.md)。

### <a name="plan-communications"></a>规划沟通

与最终用户的通信应包含以下信息：

* [Azure AD MFA 和 SSPR 组合注册指南](howto-registration-mfa-sspr-combined.md)

* [下载 Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)

* [在 Microsoft Authenticator 应用中注册](howto-authentication-passwordless-phone.md)

* [使用手机登录](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)

Microsoft 为最终用户提供通信模板。 下载[身份验证推出材料](https://aka.ms/MFAtemplates)，以便草拟通信内容。 推出材料包括自定义海报和电子邮件模板，可用于通知用户贵组织即将推出无密码身份验证选项。

## <a name="plan-user-registration"></a>计划用户注册

用户在 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 将其无密码方法注册为组合安全信息工作流的一部分。 Azure AD 记录安全密钥和 Microsoft Authenticator 的注册情况，以及对身份验证方法做出的其他任何更改。 

对于没有密码的初次使用者，管理员可以提供[临时访问密码](howto-authentication-temporary-access-pass.md)，以在 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo.md) 中注册其安全信息。 这是一个限时密码，可以满足强身份验证要求。 临时访问密码是一个每用户进程。

此方法还可用于在用户丢失或忘记身份验证因素（如安全密钥或 Microsoft Authenticator 应用）但需要登录以注册新的强身份验证方法时轻松恢复。 

>[!NOTE] 
> 如果在某些情况下无法使用安全密钥或 Microsoft Authenticator 应用，可以使用包含用户名和密码以及另一个已注册方法的多重身份验证作为回退选项。

## <a name="plan-for-and-deploy-the-microsoft-authenticator-app"></a>计划和部署 Microsoft Authenticator 应用

[Microsoft Authenticator 应用](concept-authentication-passwordless.md)可将任何 iOS 或 Android 手机变成强、无密码凭据。 可从 Google Play 或 Apple App Store 免费下载该应用。 让用户[下载 Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)并按照说明启用手机登录。

### <a name="technical-considerations"></a>技术注意事项

Active Directory 联合身份验证服务 (AD FS) 集成 - 用户启用 Microsoft Authenticator 无密码凭据后，该用户的身份验证默认为发送审批通知。 除非选择“改为使用密码”，否则混合租户中的用户会被阻止定向到 AD FS 进行登录。 此过程还会绕过任何本地条件访问策略并通过身份验证 (PTA) 流。 但是，如果指定 login_hint，则会将用户定向到 AD FS，并跳过使用无密码凭据的选项。

Azure MFA 服务器 - 通过组织的本地 Azure MFA 服务器启用了多重身份验证的最终用户可以创建并使用单个无密码手机登录凭据。 如果用户尝试使用凭据升级 Microsoft Authenticator 应用的多个安装（5 个或以上），则此更改可能会导致发生错误。

> [!IMPORTANT]
> 从 2019 年 7 月 1 日开始，Microsoft 不再为新部署提供 MFA 服务器。 希望在登录事件期间要求进行多重身份验证 (MFA) 的新客户应使用基于云的 Azure AD 多重身份验证。 在 2019 年 7 月 1 日之前激活了 MFA 服务器的现有客户可以像平时一样下载最新版本、将来的更新以及生成激活凭据。 我们建议从 Azure MFA 服务器迁移至 Azure Active Directory MFA。

设备注册 - 若要使用 Authenticator 应用进行无密码身份验证，设备必须已在 Azure AD 租户中注册，并且不能是共享设备。 设备只能在单个租户中注册。 此限制意味着只有一个工作帐户或学校帐户可以使用 Microsoft Authenticator 应用进行手机登录。

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>部署使用 Microsoft Authenticator 应用的手机登录

按照[启用使用 Microsoft Authenticator 应用的无密码登录](howto-authentication-passwordless-phone.md)一文中的步骤，在组织中启用使用 Microsoft Authenticator 应用的无密码身份验证方法。

### <a name="testing-microsoft-authenticator-app"></a>测试 Microsoft Authenticator 应用

下面是使用 Microsoft Authenticator 应用的无密码身份验证的示例测试用例：

| 场景| 预期结果 |
| - |-|
| 用户可以注册 Microsoft Authenticator 应用| 用户可以从 https://aka.ms/mysecurityinfo 注册应用 |
| 用户可以启用手机登录| 为工作帐户配置了手机登录 |
| 用户可以使用手机登录来访问应用| 用户完成手机登录流并进入应用程序。 |
| 通过关闭 Microsoft Authenticator 无密码登录，测试回退手机登录注册。 请在 Azure AD 门户中的“身份验证方法”屏幕执行该操作| 以前启用的用户无法通过 Microsoft Authenticator 使用无密码登录。 |
| 禁止通过 Microsoft Authenticator 应用进行手机登录| 工作帐户在 Microsoft Authenticator 上不再可用 |


### <a name="troubleshoot-phone-sign-in"></a>手机登录故障排除


| 方案| 解决方案 |
| - |-|
| 用户无法执行组合式注册。| 确保已启用[组合式注册](concept-registration-mfa-sspr-combined.md)。 |
| 用户无法启用手机登录验证器应用。| 确保用户处于部署范围内。 |
| 用户不在无密码身份验证的范围内，但系统为用户提供了无密码登录选项，而用户无法完成无密码登录。| 发生在用户在创建策略之前在应用程序中启用了手机登录时。 若要启用登录，将用户添加到已启用无密码登录的用户组中。 若要阻止登录：让用户从该应用程序中删除其凭据。 |


## <a name="plan-for-and-deploy-fido2-compliant-security-keys"></a>计划和部署符合 FIDO2 的安全密钥

启用兼容的安全密钥。 下面是 [FIDO2 安全密钥提供程序](concept-authentication-passwordless.md)的列表，这些提供程序提供的密钥公认为与无密码体验兼容。

### <a name="plan-security-key-lifecycle"></a>计划安全密钥生命周期

准备并计划密钥生命周期。

密钥分发 - 计划如何为贵组织预配密钥。 你可以实施集中式预配流程，或允许最终用户购买兼容 FIDO 2.0 的密钥。

 密钥激活 - 最终用户必须自行激活安全密钥。 最终用户在 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 注册其安全密钥，并在首次使用时启用第二个因素（PIN 或生物识别）。 初次使用者可以使用 TAP 注册其安全信息。

 禁用密钥 - 如果管理员想要删除与用户帐户关联的 FIDO2 密钥，他们可以从用户的身份验证方法中删除密钥，如下所示。 有关详细信息，请参阅[禁用密钥](howto-authentication-passwordless-security-key.md#disable-a-key)

 

颁发新密钥：用户可通过访问 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 注册新 FIDO2 密钥 

### <a name="technical-considerations"></a>技术注意事项

有三种使用安全密钥的无密码登录部署：

* 受支持的浏览器上的 Azure AD Web 应用

* 加入 Azure AD 的 Windows 10 设备

* 加入混合 Azure AD 的 Windows 10 设备 

  * 提供对基于云的资源和本地资源的访问权限。 有关访问本地资源的详细信息，请参阅[使用 FIDO2 密钥通过 SSO 访问本地资源](howto-authentication-passwordless-security-key-on-premises.md)

对于 Azure AD Web 应用和加入 Azure AD 的 Windows 设备：请使用：

* Windows 10 版本 1809 或更高版本，使 Microsoft Edge 或 Mozilla Firefox（版本 67 或更高版本）等受支持的浏览器。

* Windows 10 版本 1809 支持 FIDO2 登录，并可能要求部署 FIDO2 密钥制造商提供的软件。 建议使用版本 1903 或更高版本。

对于加入 Hybrid Azure AD 域的设备，请使用： 

* Windows 10 版本 2004 或更高版本。 

* 已完全修补并运行 Windows Server 2016 或 Windows Server 2019 的域服务器。 

* Azure AD Connect 的最新版本。

#### <a name="enable-windows-10-support"></a>启用 Windows 10 支持

若要启用使用 FIDO2 安全密钥的 Windows 10 登录，则需要在 Windows 10 中启用凭据提供程序功能。 选择以下选项之一：

* [使用 Intune 启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md)

  * 我们推荐 Intune 部署。

* [使用预配包启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md)

  * 如果无法部署 Intune，则管理员必须在每台计算机上部署包以启用凭据提供程序功能。 可以通过以下某个选项来执行包安装：
    * 组策略或 Configuration Manager
    * Windows 10 计算机上的本地安装

* [使用组策略启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md)

   * 仅支持加入混合 Azure AD 的设备。

#### <a name="enable-on-premises-integration"></a>启用本地集成

遵循[为本地资源启用无密码安全密钥登录（预览版）](howto-authentication-passwordless-security-key-on-premises.md)一文中的步骤。

> [!IMPORTANT] 
> 要使任何加入混合 Azure AD 的设备能够利用 FIDO2 安全密钥登录到 Windows 10，也必须完成这些步骤。


### <a name="key-restrictions-policy"></a>关键限制策略

部署安全密钥时，可以仅将 FIDO2 密钥的使用限制为仅经贵组织批准的特定制造商。 限制密钥需要 Authenticator Attestation GUID (AAGUID)。 [可通过两种方式获取你的 AAGUID](howto-authentication-passwordless-security-key.md#security-key-authenticator-attestation-guid-aaguid)。

![如何强制密钥限制](media/howto-authentication-passwordless-deployment/security-key-enforce-key-restriction.png)


如果安全密钥受到限制，并且用户尝试注册 FIDO2 安全密钥，则会收到以下错误：

![密钥受限时出现安全密钥错误](media/howto-authentication-passwordless-deployment/security-key-restricted-error.png)


如果 AAGUID 在用户注册安全密钥后受到限制，则会看到以下消息：

![AAGUID 受限时的用户视图](media/howto-authentication-passwordless-deployment/security-key-block-user-window.png)


*FIDO2 密钥被密钥限制策略拦截

### <a name="deploy-fido2-security-key-sign-in"></a>部署 FIDO2 安全密钥登录

按照[启用无密码安全密钥登录](howto-authentication-passwordless-security-key.md)一文中的步骤，在贵组织中启用使用 FIDO2 安全密钥的无密码身份验证方法。 

### <a name="testing-security-keys"></a>测试安全密钥

下面是使用安全密钥的无密码身份验证的示例测试用例。

#### <a name="passwordless-fido-sign-in-to-azure-active-directory-joined-windows-10-devices"></a>到加入 Azure Active Directory 的 Windows 10 设备的无密码 FIDO 登录


| 方案（Windows 版本）| 预期结果 |
| - |-|
| 用户可以注册 FIDO2 设备 (1809)| 用户可以在“设置”>“帐户”>“登录选项”>“安全密钥”中注册 FIDO2 设备 |
| 用户可以重置 FIDO2 设备 (1809)| 用户可以使用制造商软件重置 FIDO2 设备 |
| 用户可以通过 FIDO2 设备登录 (1809)| 用户可以从登录窗口中选择“安全密钥”并成功登录。 |
| 用户可以注册 FIDO2 设备 (1903)| 用户可以在“设置”>“帐户”>“登录选项”>“安全密钥”中注册 FIDO2 设备 |
| 用户可以重置 FIDO2 设备 (1903)| 用户可以在“设置”>“帐户”>“登录选项”>“安全密钥”中重置 FIDO2 设备 |
| 用户可以通过 FIDO2 设备登录 (1903)| 用户可以从登录窗口中选择“安全密钥”并成功登录。 |


#### <a name="passwordless-fido-sign-in-to-azure-ad-web-apps"></a>到 Azure AD Web 应用的无密码 FIDO 登录


| 场景| 预期结果 |
| - |-|
| 用户可以使用 Microsoft Edge 在 aka.ms/mysecurityinfo 上注册 FIDO2 设备| 注册应成功 |
| 用户可以使用 Firefox 在 aka.ms/mysecurityinfo 上注册 FIDO2 设备| 注册应成功 |
| 用户可以使用 Microsoft Edge 通过 FIDO2 设备在线登录到 OneDrive| 登录应成功 |
| 用户可以使用 Firefox 通过 FIDO2 设备在线登录到 OneDrive| 登录应成功 |
| 通过在 Azure Active Directory 门户的“身份验证方法”窗口中禁用 FIDO2 安全密钥，测试回滚 FIDO2 设备注册| 用户将： <li> 收到使用其安全密钥登录的提示 <li> 成功登录并看到错误：“贵公司的策略要求你使用其他方式登录”。 <li>能够选择其他方法并成功登录。 关闭窗口并重新登录，以验证用户是否看不到相同的错误消息。 |


### <a name="troubleshoot-security-key-sign-in"></a>安全密钥登录故障排除


| 方案| 解决方案 |
| - | -|
| 用户无法执行组合式注册。| 确保已启用[组合式注册](concept-registration-mfa-sspr-combined.md)。 |
| 用户无法在其[安全设置](https://aka.ms/mysecurityinfo)中添加安全密钥。| 确保已启用[安全密钥](howto-authentication-passwordless-security-key.md)。 |
| 用户无法在 Windows 10 登录选项中添加安全密钥。| [确保安全密钥用于 Windows 登录](concept-authentication-passwordless.md)已启用 |
| 错误消息：已检测到此浏览器或 OS 不支持 FIDO2 安全密钥。| 只能在 Windows 10 版本 1809 或更高版本上使用受支持的浏览器（Microsoft Edge 和 Firefox 版本 67）注册无密码 FIDO2 安全设备。 |
| 错误消息：“你的公司策略要求你使用其他方式登录”。| 确定已在租户中启用安全密钥。 |
| 用户无法在 Windows 10 版本 1809 上管理其安全密钥| 版本 1809 要求使用 FIDO2 密钥供应商提供的安全密钥管理软件。 请与供应商联系以获取支持。 |
| 我认为我的 FIDO2 安全密钥可能有问题，如何对其进行测试。| 导航至 [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)，输入测试帐户的凭据，插入可疑的安全密钥，选择屏幕右上角的 + 按钮，选择“创建”，然后完成创建过程。 如果此方案失败，则设备可能已损坏。 |


## <a name="manage-passwordless-authentication"></a>管理无密码身份验证

若要在 [Azure 门户](https://portal.azure.com/)中管理用户的无密码身份验证方法，请选择用户帐户，然后选择“身份验证方法”。

### <a name="microsoft-graph-apis"></a>Microsoft 图形 API 

还可使用 Microsoft Graph 中的身份验证方法 API 管理无密码身份验证方法。 例如：

* 可以检索用户的 FIDO2 安全密钥的详细信息，并在用户丢失该密钥时将其删除。

* 可以检索用户的 Microsoft Authenticator 注册的详细信息，并在用户丢失手机时将其删除。

* 管理安全密钥和 Microsoft Authenticator 应用的身份验证方法策略。

有关可在 Microsoft Graph 中管理哪些身份验证方法的详细信息，请参阅 [Azure AD 身份验证方法 API 概述](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)。

### <a name="rollback"></a>回退

虽然无密码身份验证是一项轻型功能，并且对最终用户的影响最小，但可能需要回滚。

若要进行回退，管理员需要登录到 Azure 门户，选择所需的强身份验证方法，并将“启用”选项更改为“否”。 此过程将为所有用户禁用无密码功能。

![无密码回退](media/howto-authentication-passwordless-deployment/passwordless-rollback.png)

已注册 FIDO2 安全设备的用户下次登录时，系统会提示他们使用该安全设备，然后显示以下错误：

![密码回退的错误窗口](media/howto-authentication-passwordless-deployment/passswordless-rollback-error-window.png)

### <a name="reporting-and-monitoring"></a>报告和监视

Azure AD 提供一些报告，这些报告可提供技术见解和业务见解。 根据贵组织的要求，让业务负责人和技术应用程序所有者拥有和使用这些报告。

下表提供了典型报告方案的一些示例：

| 管理风险| 提高工作效率| 管理和符合性|
|-|-|-|
| 报表类型| 身份验证方法 - 为用户注册了组合式安全注册| 身份验证方法 - 为用户注册了应用通知| 登录：查看正在访问租户的用户及其访问方式 |
| 可能的操作| 尚未注册目标用户| 推动采用 Microsoft Authenticator 应用或安全密钥| 撤销管理员的访问权限或强制执行其他安全策略 |

 

#### <a name="track-usage-and-insights"></a>跟踪使用情况和见解

在下列情况下，Azure AD 会向审核日志添加条目：

* 管理员在“身份验证方法”部分中进行更改。

* 用户在 Azure AD 中对其凭据进行任何类型的更改。

* 用户在安全密钥上启用或禁用其帐户，或在其 Win 10 计算机上重置安全密钥的第二个因素。 请参阅事件 ID：4670 和 5382。

Azure AD 会将大多数审核数据保留 30 天，并通过 Azure 管理门户或 API 提供数据，供你下载到分析系统。 如果需要更长的保留期，请在 [Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 Sumo Logic 等 SIEM 工具中导出和使用日志。 建议在适用时延长审核、趋势分析和其他业务需求的保留期

“身份验证方法”活动仪表板中有两个选项卡：“注册”和“使用情况”。

[“注册”选项卡](https://portal.azure.com/)显示支持无密码身份验证的用户数量，以及其他身份验证方法。 此选项卡显示两个图形：

* 通过身份验证方法注册的用户。

* 身份验证方法的最近注册情况。

![用于查看身份验证方法的“注册”选项卡](media/howto-authentication-passwordless-deployment/monitoring-registration-tab.png)

[“使用情况”选项卡](https://portal.azure.com/)显示身份验证方法的登录情况。

![用于查看身份验证方法的“使用情况”选项卡](media/howto-authentication-passwordless-deployment/monitoring-usage-tab.png)

有关详细信息，请参阅[跟踪 Azure AD 组织中注册的身份验证方法和使用情况](howto-authentication-methods-activity.md)。

#### <a name="sign-in-activity-reports"></a>登录活动报告

使用[登录活动报告](../reports-monitoring/concept-sign-ins.md)跟踪用于登录多个应用程序的身份验证方法。 

选择用户行，然后选择“身份验证详细信息”选项卡，以查看哪种身份验证方法用于哪种登录活动。

![报告登录活动](media/howto-authentication-passwordless-deployment/reporting-sign-in-activity.png)

## <a name="next-steps"></a>后续步骤

* [了解无密码身份验证的工作原理](concept-authentication-passwordless.md)

* [部署其他身份功能](../fundamentals/active-directory-deployment-plans.md)