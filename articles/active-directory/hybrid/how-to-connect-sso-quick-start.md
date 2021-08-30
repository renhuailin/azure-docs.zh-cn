---
title: Azure AD Connect：无缝单一登录 - 快速入门 | Microsoft Docs
description: 本文介绍如何开始使用 Azure Active Directory 无缝单一登录
services: active-directory
keywords: 什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a1c9bcec2a9aec2673e29a3f578146cad7de5d6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730760"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Azure Active Directory 无缝单一登录：快速入门

## <a name="deploy-seamless-single-sign-on"></a>部署无缝单一登录

Azure Active Directory (Azure AD) 无缝单一登录（无缝 SSO）可使登录连接到企业网络的企业台式机的用户自动登录。 无缝 SSO 可让用户轻松访问基于云的应用程序，而无需使用其他任何本地组件。

要部署无缝 SSO，需要遵循以下步骤。

## <a name="step-1-check-the-prerequisites"></a>步骤 1：检查先决条件

请确保符合以下先决条件：

* 设置 Azure AD Connect 服务器：如果使用[直通身份验证](how-to-connect-pta.md)作为登录方法，则无需进行其他先决条件检查。 如果使用[密码哈希同步](how-to-connect-password-hash-synchronization.md)作为登录方法，并且 Azure AD Connect 与 Azure AD 之间有防火墙，则请确保：
   - 使用 Azure AD Connect 版本 1.1.644.0 或更高版本。 
   - 如果防火墙或代理允许，则将连接添加到通过端口 443 的 \*.msappproxy.net URL 的允许列表。 如果需要特定的 URL 而不是通配符来配置代理，则可以配置 tenantid.registration.msappproxy.net，其中 tenantid 是要在其中配置该功能的租户的 GUID。 如果在组织中无法使用基于 URL 的代理例外，则可以改为允许访问每周更新的 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 此先决条件仅适用于启用了该功能的情况。 无需用户实际登录。

    >[!NOTE]
    >Azure AD Connect 版本 1.1.557.0、1.1.558.0、1.1.561.0 和 1.1.614.0 具有密码哈希同步相关问题。 如果不打算将密码哈希同步与直通身份验证结合使用，请阅读 [Azure AD Connect 发行说明](./reference-connect-version-history.md)了解详细信息。
    
    >[!NOTE]
    >如果有传出 HTTP 代理，请确保此 URL (autologon.microsoftazuread-sso.com) 被列入允许列表中。 应显式指定此 URL，因为可能不接受通配符。 

* **使用受支持的 Azure AD Connect 拓扑**：请确保使用 Azure AD Connect 支持的拓扑之一，如 [此处](plan-connect-topologies.md)所述。

    >[!NOTE]
    >无缝 SSO 支持多个 AD 林，无论它们之间是否存在 AD 信任。

* 设置域管理员凭据：你需要为每个 Active Directory 林提供域管理员凭据：
    * 通过 Azure AD Connect 同步到 Azure AD。
    * 包含你想要为其启用无缝 SSO 的用户。
    
* **启用新式身份验证**：需要在租户上启用 [新式身份验证](/office365/enterprise/modern-auth-for-office-2013-and-2016)，此功能才有效。

* 使用最新版本的 Microsoft 365 客户端：若要获取 Microsoft 365 客户端的无提示登录体验，你的用户需要使用 16.0.8730.xxxx 或更高版本。

## <a name="step-2-enable-the-feature"></a>步骤 2：启用功能

通过 [Azure AD Connect](whatis-hybrid-identity.md) 启用无缝 SSO。

>[!NOTE]
> 此外，如果 Azure AD Connect 不能满足你的要求，还可以[使用 PowerShell 启用无缝 SSO](tshoot-connect-sso.md#manual-reset-of-the-feature)。 如果每个 Active Directory 林具有多个域，并且你想要更具针对性地指向要启用无缝 SSO 的域，请使用此选项。

如果你要全新安装 Azure AD Connect，请选择[自定义安装路径](how-to-connect-install-custom.md)。 在“用户登录”页上，请选择“启用单一登录”选项。

>[!NOTE]
> 仅当登录方式为“密码哈希同步”或“传递身份验证”时，该选项才适用。

![Azure AD Connect：用户登录](./media/how-to-connect-sso-quick-start/sso8.png)

如果你已安装了 Azure AD Connect，请在 Azure AD Connect 上选择“更改用户登录”页，然后选择“下一步”。 如果使用 Azure AD Connect 1.1.880.0 版本或更高版本，则默认情况下将选则“启用单一登录”选项。 如果使用是较旧版本的 Azure AD Connect，请选择“启用单一登录”选项。

![Azure AD Connect：更改用户登录](./media/how-to-connect-sso-quick-start/changeusersignin.png)

继续按向导操作，直到出现“启用单一登录”页。 为每个 Active Directory 林提供域管理员凭据，以便：

* 通过 Azure AD Connect 同步到 Azure AD。
* 包含你想要为其启用无缝 SSO 的用户。

完成向导后，租户即启用了无缝 SSO。

>[!NOTE]
> 域管理员凭据不存储在 Azure AD Connect 或 Azure AD 中。 它们仅用于启用该功能。

按照这些说明验证是否已正确启用无缝 SSO：

1. 使用租户的全局管理员凭据登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 在左窗格中选择“Azure Active Directory”。
3. 选择“Azure AD Connect”。
4. 验证无缝单一登录功能是否显示为“已启用”。

![Azure 门户：Azure AD Connect 窗格](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> 无缝 SSO 在每个 AD 林的本地 Active Directory (AD) 中创建一个名为 `AZUREADSSOACC` 的计算机帐户。 出于安全原因，需要对 `AZUREADSSOACC` 计算机帐户进行严格保护。 只有域管理员才能管理计算机帐户。 请确保计算机帐户上的 Kerberos 委派处于禁用状态，并且 Active Directory 中的其他帐户没有对 `AZUREADSSOACC` 计算机帐户的委派权限。 将计算机帐户存储在组织单位 (OU) 中，在这里，它们不会被意外删除，并且只有域管理员具有访问权限。

>[!NOTE]
> 如果在本地环境中使用哈希传递和凭据盗用缓解体系结构，请进行适当更改，确保 `AZUREADSSOACC` 计算机帐户最终不会出现在“隔离”容器中。 

## <a name="step-3-roll-out-the-feature"></a>步骤 3：扩展此功能

可使用以下说明向用户逐步推出无缝 SSO。 首先，使用 Active Directory 中的组策略将以下 Azure AD URL 添加到所有或所选用户的 Intranet 区域设置：

- `https://autologon.microsoftazuread-sso.com`

此外，还需要通过“组策略”启用称为“允许通过脚本更新状态栏”的 Intranet 区域策略设置。 

>[!NOTE]
> 以下说明仅适用于 Windows 上的 Internet Explorer、Microsoft Edge 和 Google Chrome（如果它与 Internet Explorer 共享一组相同的受信任站点 URL）。 请阅读下一节，了解在 macOS 上设置 Mozilla Firefox 和 Google Chrome 的说明。

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>为什么需要修改用户的 Intranet 区域设置？

默认情况下，浏览器将自动从特定 URL 计算正确的区域（Internet 或 Intranet）。 例如，`http://contoso/` 映射到 Intranet 区域，而 `http://intranet.contoso.com/` 映射到 Internet 区域（因为此 URL 包含句点）。 浏览器不会将 Kerberos 票证发送到云终结点（例如 Azure AD URL），除非将此 URL 显式添加到浏览器的 Intranet 区域。

有两种方法来修改用户的 Intranet 区域设置：

| 选项 | 管理员注意事项 | 用户体验 |
| --- | --- | --- |
| 组策略 | 管理员锁定 Intranet 区域设置的编辑 | 用户无法修改自己的设置 |
| 组策略首选项 |  管理员允许编辑 Intranet 区域设置 | 用户可以修改自己的设置 |

### <a name="group-policy-option---detailed-steps"></a>“组策略”选项 - 详细步骤

1. 打开“组策略管理编辑器”工具。
2. 编辑适用于部分或全部用户的组策略。 此示例使用 **默认域策略**。
3. 浏览到“用户配置” > “策略” > “管理模板” > “Windows 组件” > “Internet Explorer” > “Internet 控制面板” > “安全页”。       然后选择“站点到区域分配列表”。
    ![显示“安全页”的屏幕截图，其中已选择“站点到区域分配列表”。](./media/how-to-connect-sso-quick-start/sso6.png)
4. 启用策略，然后在对话框中输入以下值：
   - 值名称：要将 Kerberos 票证转发到的 Azure AD URL。
   - 值（数据）：1 指示 Intranet 区域。

     结果类似以下形式：

     值名称：`https://autologon.microsoftazuread-sso.com`
  
     值（数据）：1

   >[!NOTE]
   > 如果你想禁止某些用户使用无缝 SSO（例如，如果这些用户在共享展台上登录），请将前面的值设置为 4。 此操作将 Azure AD URL 添加到受限区域，并且始终无法使用无缝 SSO。
   >

5. 选择“确定”，然后再选择“确定”。 

    ![显示“显示内容”窗口的屏幕截图，其中已选择区域分配。](./media/how-to-connect-sso-quick-start/sso7.png)

6. 浏览到“用户配置” > “策略” > “管理模板” > “Windows 组件” > “Internet Explorer” > “Internet 控制面板” > “安全页” > “Intranet 区域”。       。 然后选择“允许通过脚本更新状态栏”。

    ![显示“Intranet 区域”页的屏幕截图，其中已选择“允许通过脚本更新状态栏”。](./media/how-to-connect-sso-quick-start/sso11.png)

7. 启用策略设置，然后选择“确定”。

    ![显示“允许通过脚本更新状态栏”窗口的屏幕截图，其中已启用策略设置。](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>“组策略首选项”选项 - 详细步骤

1. 打开“组策略管理编辑器”工具。
2. 编辑适用于部分或全部用户的组策略。 此示例使用 **默认域策略**。
3. 浏览到“用户配置” > “首选项” > “Windows 设置” > “注册” > “新建” > “注册项”     。

    ![显示已选择“注册表”和“注册表项”的屏幕截图。](./media/how-to-connect-sso-quick-start/sso15.png)

4. 在相应字段中输入以下值，然后单击“确定”。
   - 密钥路径：Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon 
   - 值名称：https 
   - 值类型：REG_DWORD 
   - 值数据：00000001 
 
     ![显示“新建注册表属性”窗口的屏幕截图。](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![单一登录](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>浏览器注意事项

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox（所有平台）

如果要在环境中使用[身份验证](https://github.com/mozilla/policy-templates/blob/master/README.md#authentication)策略设置，请确保将 Azure AD URL (`https://autologon.microsoftazuread-sso.com`) 添加到 SPNEGO 部分。 还可以将 PrivateBrowsing 选项设置为 true，以允许在隐私浏览模式下使用无缝 SSO。

#### <a name="safari-macos"></a>Safari (macOS)

确保运行 macOS 的计算机已加入 AD。 有关 AD 加入 macOS 设备的说明超出了本文的范围。

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>基于 Chromium 的 Microsoft Edge（所有平台）

如果已替代环境中的 [AuthNegotiateDelegateAllowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) 或 [AuthServerAllowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) 策略设置，请确保也向其添加 Azure AD 的 URL (`https://autologon.microsoftazuread-sso.com`)。

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>基于 Chromium 的 Microsoft Edge（macOS 和其他非 Windows 平台）

有关 macOS 和其他非 Windows 平台上基于 Chromium 的 Microsoft Edge，请参阅基于 [Chromium 的 Microsoft Edge 策略列表](/DeployEdge/microsoft-edge-policies#authserverallowlist)，了解如何将用于集成身份验证的 Azure AD URL 添加到允许列表。

#### <a name="google-chrome-all-platforms"></a>Google Chrome（所有平台）

如果已替代环境中的 [AuthNegotiateDelegateAllowlist](https://chromeenterprise.google/policies/#AuthNegotiateDelegateAllowlist) 或 [AuthServerAllowlist](https://chromeenterprise.google/policies/#AuthServerAllowlist) 策略设置，请确保也向其添加 Azure AD 的 URL (`https://autologon.microsoftazuread-sso.com`)。

#### <a name="macos"></a>macOS

使用第三方 Active Directory 组策略扩展将 Azure AD URL 扩展到面向 macOS 用户的 Firefox 和 Google Chrome，此内容不在本文讨论范围之内。

#### <a name="known-browser-limitations"></a>已知的浏览器限制

无缝 SSO 在以增强保护模式下运行的 Internet Explorer 中不起作用。 无缝 SSO 支持基于 Chromium 的下一版本的 Microsoft Edge，它根据设计在 InPrivate 和来宾模式下工作。 不再支持 Microsoft Edge（旧版）。 

 可能需要根据相应的文档，为 InPrivate 和/或来宾用户配置 `AmbientAuthenticationInPrivateModesEnabled`：
 
   - [Microsoft Edge Chromium](/DeployEdge/microsoft-edge-policies#ambientauthenticationinprivatemodesenabled)
   - [Google Chrome](https://chromeenterprise.google/policies/?policy=AmbientAuthenticationInPrivateModesEnabled)

## <a name="step-4-test-the-feature"></a>步骤 4：测试功能

要测试特定用户的功能，请确保符合以下所有条件：
  - 用户登录到某个企业设备。
  - 该设备已加入 Active Directory 域。 设备不需要[加入 Azure AD](../devices/overview.md)。
  - 该设备已通过远程访问连接（例如 VPN 连接）与企业有线或无线网络中的域控制器 (DC) 建立了直接连接。
  - 你可以通过组策略[将此功能扩展到用户](#step-3-roll-out-the-feature)。

要测试用户仅输入用户名而不是密码的场景：
   - 登录 https://myapps.microsoft.com/ 。 请确保清除浏览器缓存，或在专用模式下将新的专用浏览器会话与任何受支持的浏览器一起使用。

要测试用户并非必须输入用户名或密码的场景，请使用以下步骤之一： 
   - 登录到 `https://myapps.microsoft.com/contoso.onmicrosoft.com` 请确保清除浏览器缓存，或在专用模式下将新的专用浏览器会话与任何受支持的浏览器一起使用。 将“contoso”替换为租户的名称。
   - 在新的专用浏览器会话中登录到 `https://myapps.microsoft.com/contoso.com`。 将“contoso.com”替换为租户中的已验证域（而不是联盟域）。

## <a name="step-5-roll-over-keys"></a>步骤 5：滚动更新密钥

在步骤 2 中，Azure AD Connect 在已启用无缝 SSO 的所有 Active Directory 林中创建计算机帐户（表示 Azure AD）。 若要了解详细信息，请参阅 [Azure Active Directory 无缝单一登录：深入技术探究](how-to-connect-sso-how-it-works.md)。

>[!IMPORTANT]
>如果泄露，可以使用计算机帐户上的 Kerberos 解密密钥为 AD 林中的任意用户生成 Kerberos 票证。 然后，恶意执行组件可以为遭到入侵的用户模拟 Azure AD 登录。 强烈建议定期滚动更新这些 Kerberos 解密密钥，至少每 30 天一次。

有关如何滚动更新密钥的说明，请参阅 [Azure Active Directory 无缝单一登录：常见问题](how-to-connect-sso-faq.yml)。

>[!IMPORTANT]
>启用该功能后无需 _立即_ 执行此步骤。 至少每隔 30 天滚动更新一次 Kerberos 解密密钥。

## <a name="next-steps"></a>后续步骤

- [深入技术探究](how-to-connect-sso-how-it-works.md)：了解无缝单一登录功能的工作原理。
- [常见问题](how-to-connect-sso-faq.yml)：获取无缝单一登录常见问题的解答。
- [疑难解答](tshoot-connect-sso.md)：了解如何解决无缝单一登录功能的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 论坛来提交新的功能请求。
