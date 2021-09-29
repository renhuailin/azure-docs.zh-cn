---
title: Azure 用户主体名称 (UPN) 更改的规划和故障排除
description: 了解 UPN 更改的已知问题和缓解措施
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b48fa334aedce144e274b8155edda3cc7b08b9a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124739945"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Azure Active Directory 中用户主体名称更改的规划和故障排除

用户主体名称 (UPN) 是表示用户帐户的 Internet 通信标准的属性。 UPN 由 UPN 前缀（用户帐户名）和 UPN 后缀（DNS 域名）组成。 前缀与后缀以“\@”符号相联接。 例如，someone@example.com。 UPN 必须在目录林中的所有安全主体对象之间保持唯一。 

**本文假设你使用 UPN 作为用户标识符。其中讲解了 UPN 更改的规划，以及如何解决可能由 UPN 更改引起的问题。**

> [!NOTE]
> 对于开发人员，我们建议使用用户对象 ID 作为不可变标识符，而不是使用 UPN 或电子邮件地址，因为后者的值可能会更改。


## <a name="learn-about-upns-and-upn-changes"></a>了解 UPN 和 UPN 更改
如果必需的值实际上是用户的 UPN 时，登录页通常会提示用户输入其电子邮件地址。 因此，应确保在用户的主电子邮件地址发生更改时立即更改用户的 UPN。

用户的主要电子邮件地址可能因多种原因而发生变化：

* 公司品牌重塑

* 转移到不同公司部门的员工 

* 合并和收购

* 员工姓名更改

### <a name="types-of-upn-changes"></a>UPN 更改的类型

可以通过更改前缀和/或后缀来更改 UPN。

* 更改前缀。

   *  例如，如果某人的名称发生了更改，你可以更改其帐户名：  
‎BSimon@contoso.com 至 BJohnson@contoso.com

   * 还可以更改前缀的企业标准：  
‎Bsimon@contoso.com 至 Britta.Simon@contoso.com

* 更改后缀。 <br>

    例如，如果某个人换了部门，则可以更改其域： 

   * Britta.Simon@contoso.com 至 Britta.Simon@contosolabs.com <br>
     或<br>
    * Britta.Simon@corp.contoso.com 至 Britta.Simon@labs.contoso.com 

建议在每次更新用户的主要电子邮件地址时也要更改用户的 UPN。

在从 Active Directory 到 Azure AD 的初始同步过程中，请确保用户的电子邮件与其 UPN 完全相同。

### <a name="upns-in-active-directory"></a>Active Directory 中的 UPN

在 Active Directory 中，默认的 UPN 后缀是在其中创建用户帐户的域的 DNS 名称。 在大多数情况下，这是在 Internet 上注册为企业域的域名。 如果在 contoso.com 域中创建用户帐户，则默认 UPN 是

username@contoso.com

 但是，可以使用 Active Directory 域和信任来[添加更多的 UPN 后缀](../fundamentals/add-custom-domain.md)。 

例如，你可能想要添加 labs.contoso.com，并让用户的 UPN 和电子邮件反映这一情况。 然后，它们将成为

username@labs.contoso.com.

>[!IMPORTANT]
> 如果要[在 Active Directory 中更改后缀](../fundamentals/add-custom-domain.md)，则必须确保已[在 Azure AD 中添加并验证](../fundamentals/add-custom-domain.md)了匹配的自定义域名。 

![经验证的域的屏幕截图](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure Active Directory 中的 UPN

用户以其 userPrincipalName 属性中的值登录到 Azure AD。 

将 Azure AD 与本地 Active Directory 结合使用时，将使用 Azure AD Connect 服务来同步用户帐户。 默认情况下，Azure AD Connect 向导使用本地 Active Directory 中的 userPrincipalName 属性作为 Azure AD 中的 UPN。 在自定义安装中，可以将其更改为其他属性。

更新单个用户或整个组织的用户主体名称 (UPN) 时，必须具有定义的进程，这一点很重要。 

请参阅本文档中的“已知问题和解决方法”。

将用户帐户从 Active Directory 同步到 Azure AD 时，请确保 Active Directory 中的 Upn 映射到 Azure AD 中的已验证域。

![显示映射到已验证 Azure AD 域的 UPN 示例的屏幕截图。](./media/howto-troubleshoot-upn-changes/verified-domains.png)

如果 userPrincipalName 属性的值不对应于 Azure AD 中已验证的域，则同步过程会将后缀替换为默认值 .onmicrosoft.com。


### <a name="roll-out-bulk-upn-changes"></a>推出批量 UPN 更改

遵照有关批量 UPN 更改的[试点的最佳实践](../fundamentals/active-directory-deployment-plans.md)。 如果发现无法快速解决的问题，还可以使用已测试的回退计划来还原 UPN。 试点计算机运行后，可以开始将包含各种组织角色的用户小集合及其特定应用或设备的集合作为目标。

浏览用户的第一个子集，你可以很好地了解用户在更改过程中应期待的内容。 将此信息包含在用户通信中。

创建一个已定义的过程，用于在正常操作过程中更改各个用户的 UPN。 我们建议事先预备好一个已测试的过程，其中包含有关已知问题和解决方法的文档。

以下各节详细介绍了在更改 UPN 时可能存在的已知问题和解决方法。

## <a name="apps-known-issues-and-workarounds"></a>应用方面的已知问题和解决方法

[服务型软件 (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) 和业务线 (LoB) 应用程序通常依赖于 UPN 来查找用户并存储用户配置文件信息，包括角色。 如果应用程序是使用[即时预配](../app-provisioning/user-provisioning.md)在用户首次登录应用时创建了用户配置文件，则 UPN 更改可能会对此类应用程序造成影响。

**已知问题**<br>
更改用户的 UPN 可能会破坏 Azure AD 用户与在应用程序上创建的用户配置文件之间的关系。 如果应用程序使用[即时预配](../app-provisioning/user-provisioning.md)，则可能会创建全新的用户配置文件。 这将要求应用程序管理员进行手动更改来修复此关系。

**解决方法**<br>
利用[Azure AD 自动化用户预配](../app-provisioning/user-provisioning.md)，你可以在受支持的云应用程序中自动创建、维护和删除用户标识。 在应用程序上配置自动化用户预配会自动更新应用程序上的 UPN。 请在渐进式推出过程中测试应用程序，以验证它们不会受到 UPN 更改的影响。
如果你是开发人员，请考虑[将 SCIM 支持添加到应用程序](../app-provisioning/use-scim-to-provision-users-and-groups.md)，以通过 Azure Active Directory 启用自动用户预配。 

## <a name="managed-devices-known-issues-and-workarounds"></a>托管设备方面的已知问题和解决方法

借助[将设备引入 Azure AD](../devices/overview.md)，可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。

### <a name="azure-ad-joined-devices"></a>已加入 Azure AD 的设备

[Azure AD 联接](../devices/concept-azure-ad-join.md)的设备会直接加入到 Azure AD，并允许用户使用其组织的标识登录到设备。

**已知问题** <br>
如果应用程序依赖于 Azure AD 进行身份验证，用户在这些应用程序中可能会遇到单一登录问题。

**分辨率** <br>
此部分中提到的问题已在 Windows 10 2020 年 5 月的更新 (2004) 中得到解决。

**解决方法** <br>
留出足够时间让 UPN 更改同步到 Azure AD。 在确认了 Azure AD 门户中已经反映出新 UPN 之后，让用户选择“其他用户”磁贴，以通过其新 UPN 进行登录。 还可以通过 [PowerShell](/powershell/module/azuread/get-azureaduser) 进行验证。 用户使用新的 UPN 登录后，Windows 设置“访问工作或学校”中可能仍然显示对旧 UPN 的引用。

![已验证域的屏幕截图](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>已加入混合 Azure AD 的设备

[混合 Azure AD 联接](../devices/concept-azure-ad-join-hybrid.md)设备将加入到 Active Directory 和 Azure AD。 如果你的环境部署了本地 Active Directory，并且还希望享受到 Azure AD 提供的功能，则可以实施混合 Azure AD 连接。

**已知问题** 

Windows 10 混合 Azure AD 联接的设备可能会遇到意外的重新启动和访问问题。

如果用户是在新 UPN 同步到 Azure AD 之前登录 Windows，或者继续使用现有的 Windows 会话，则当条件访问已配置为强制使用混合加入设备访问资源时，对于使用 Azure AD 进行身份验证的应用程序，用户可能会遇到单一登录问题。 

此外，将显示以下消息，并强制一分钟后重启。 

“你的电脑将在一分钟内自动重启。 Windows 遇到问题，需要重启。 应该立即关闭此消息，并保存工作”。

**分辨率** <br>
此部分中提到的问题已在 Windows 10 2020 年 5 月的更新 (2004) 中得到解决。

**解决方法** 

设备必须与 Azure AD 脱离并重启。 重启后，设备将再次自动加入 Azure AD，而且用户必须通过选择“其他用户”磁贴，使用新的 UPN 登录。 要将设备与 Azure AD 脱离，请在命令提示符下运行以下命令：

dsregcmd /leave

如果使用 Windows Hello 企业版，用户将需要[重新注册](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision)。 UPN 更改后产生的这个问题对于 Windows 7 和8.1 设备没有影响。


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator 已知问题和解决方法

你的组织可能需要使用 [Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)来登录和访问组织的应用程序和数据。 尽管用户名可能会在应用中显示，但在用户完成注册过程前，该帐户不会设置为用作验证方法。

[Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)有四个主要功能：

* 通过推送通知或验证码进行多重身份验证

* 作为 iOS 和 Android 设备上的身份验证代理，以便为使用[中介身份验证](../develop/msal-android-single-sign-on.md)的应用程序提供单一登录

* 向 Azure AD 注册设备（也称为 Workplace Join），这是某些需要进行 MFA 和设备注册的其他功能的要求。如 Intune 应用保护和设备注册/管理、

* 手机登录。

### <a name="multi-factor-authentication-with-android-devices"></a>Android 设备的多重身份验证

Microsoft Authenticator 应用提供一个带外验证选项。 [多重身份验证](../authentication/concept-mfa-howitworks.md)会将通知推送到用户智能手机或平板电脑上的 Microsoft Authenticator 应用中，而不是在用户登录期间对用户进行自动电话呼叫或向其发送短信。 用户只需在该应用中点击“批准”（或输入 PIN 码生物特征或并点击“身份验证”）即可完成登录。

**已知问题** 

更改用户的 UPN 时，旧的 UPN 仍将显示在用户帐户上，并且可能无法接收通知。 [验证码](https://support.microsoft.com/account-billing/common-problems-with-the-microsoft-authenticator-app-12d283d1-bcef-4875-9ae5-ac360e2945dd)将继续有效。

**解决方法**

如果收到通知，请指示用户关闭通知，打开 Authenticator 应用，点击“检查通知”选项，并批准 MFA 提示。 此后，将更新在该帐户上显示的 UPN。 请注意，更新后的 UPN 可能显示为新帐户，这是因为正在使用 Authenticator 的其他功能。 有关详细信息，请参阅本文中的其他已知问题。

### <a name="brokered-authentication"></a>中介身份验证

在 Android 和 iOS 中介（例如Microsoft Authenticator）上启用下列功能：

* 单一登录 (SSO) - 用户无需登录到每个应用程序。

* 设备标识 - 中介会访问设备证书，该证书是在设备加入工作区时在设备上创建的。

* 应用程序标识验证 - 应用程序在调用中介时会传递其重定向 URL，而中介会验证该 URL。

此外，它还允许应用程序参与更高级的功能，如[条件访问](../conditional-access/index.yml)，并支持 [Microsoft Intune 方案](../develop/msal-net-use-brokers-with-xamarin-apps.md)。

**已知问题**<br>
由于应用程序传递的 login_hint 与代理上存储的 UPN 不匹配，导致在使用中介辅助登录的新应用程序上，会向用户显示更多的交互式身份验证提示。

**解决方法** <br> 用户需要从 Microsoft Authenticator 中手动删除帐户，然后从中介辅助的应用程序开始新的登录。 初始身份验证后，将自动添加该帐户。

### <a name="device-registration"></a>设备注册

Microsoft Authenticator 应用负责向 Azure AD 注册设备。 设备注册允许设备向 Azure AD 进行身份验证，并且在以下情况下是必需的：

* Intune 应用保护

* Intune 设备注册

* 手机登录

**已知问题**<br>
更改 UPN 后，具有新 UPN 的新帐户将列示在 Microsoft Authenticator 应用上，并且仍会列出具有旧 UPN 的帐户。 此外，旧 UPN 将显示在应用设置的“设备注册”部分中。 设备注册的正常功能或依赖方案没有任何变化。

**解决方法** <br> 要在 Microsoft Authenticator 应用中删除对旧 UPN 的所有引用，请指示用户手动从 Microsoft Authenticator 中同时删除旧帐户和新帐户，重新注册 MFA 并重新加入设备。

### <a name="phone-sign-in"></a>手机登录

使用手机登录，用户无需密码即可登录 Azure AD。 要启用手机登录，用户需要使用 Authenticator 应用注册 MFA，然后在 Authenticator 上直接启用手机登录。 在配置过程中，设备将注册到 Azure AD。

**已知问题** <br>
用户由于没有收到任何通知而无法使用手机登录。 如果用户点击“检查通知”，会遇到错误。

**解决方法**<br>
用户需要在启用了手机登录的帐户中选择下拉菜单，然后选择“禁用手机登录”。 如果需要，可以重新启用手机登录。

## <a name="security-key-fido2-known-issues-and-workarounds"></a>安全密钥 (FIDO2) 方面的已知问题和解决方法

**已知问题** <br>
如果用同一密钥注册多个用户，则登录屏幕会显示一个帐户选择页，其中显示旧的 UPN。 使用安全密钥的登录不受 UPN 更改的影响。  

**解决方法**<br>
要删除对旧 UPN 的引用，用户必须[重置安全密钥并重新注册](../authentication/howto-authentication-passwordless-security-key.md#known-issues)。

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive 的已知问题和解决方法

已确认 OneDrive 用户在 UPN 更改后会遇到问题。 有关详细信息，请参阅 [UPN 更改对于 ONEDRIVE URL 和 OneDrive 功能的影响](/onedrive/upn-changes)。

## <a name="next-steps"></a>后续步骤

请参阅以下资源：
* [Azure AD Connect：设计概念](./plan-connect-design-concepts.md)

* [Azure AD UserPrincipalName 填充](./plan-connect-userprincipalname.md)

* [Microsoft 标识平台 ID 令牌](../develop/id-tokens.md)