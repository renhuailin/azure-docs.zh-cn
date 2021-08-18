---
title: 了解 Azure Active Directory 中应用的基于 SAML 的单一登录 (SSO)
description: 了解 Azure Active Directory 中应用的基于 SAML 的单一登录 (SSO)
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: ac68db14ec080372acfae3e9f1e5d3dd3f6a47c9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738795"
---
# <a name="understand-saml-based-single-sign-on"></a>了解基于 SAML 的单一登录

在应用程序管理的[快速入门系列](view-applications-portal.md)中，你已了解如何使用 Azure AD 作为应用程序的标识提供者 (IdP)。 本文将更详细地介绍基于 SAML 的单一登录选项。

## <a name="before-you-begin"></a>在开始之前

使用 Azure AD 作为标识提供者 (IdP) 和配置单一登录 (SSO) 时，操作可能简单也可能复杂，具体取决于所使用的应用程序。 配置某些应用程序只需几个操作就能搞定。 而其他则需要深度配置。 要快速提升知识水平，请浏览有关应用程序管理的[快速入门系列](view-applications-portal.md)。 如果要添加的应用程序很简单，则可能不需要阅读本文。 如果要添加的应用程序需要基于 SAML 的 SSO 的自定义配置，则适合查看本文。

在[快速入门系列](add-application-portal-setup-sso.md)中，有一篇文章介绍了如何配置单一登录。 在此文章中，你将了解如何访问应用的 SAML 配置页。 SAML 配置页由 5 个部分构成。 本文将对其进行详细讨论。

> [!IMPORTANT]
> 在某些情况下，“企业应用程序”中的应用程序的导航中不会出现“单一登录”选项 。
>
> 如果使用“应用注册”注册了应用程序，则默认情况下，单一登录功能将配置为使用 OIDC OAuth。 在这种情况下，“企业应用程序”下的导航中将不会显示“单一登录”选项 。 使用“应用注册”添加自定义应用时，将在清单文件中配置选项。 要详细了解清单文件，请参阅 [Azure Active Directory 应用部件清单 (manifest)](../develop/reference-app-manifest.md)。 要了解有关 SSO 标准的详细信息，请参阅[使用 Microsoft 标识平台进行身份验证和授权](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)。
>
> 导航中将缺少“单一登录”的另一种场景是：应用程序托管在另一个租户中，或者如果你的帐户没有所需权限（全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者）。 权限还可能导致出现你可打开“单一登录”，但无法保存它的情况。 要详细了解 Azure AD 管理角色，请参阅 https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 。

## <a name="basic-saml-configuration"></a>基本 SAML 配置

应从应用程序供应商处获取这些值。 可以手动输入值，或上传元数据文件以提取字段值。

> [!TIP]
> 许多应用都已预先配置为使用 Azure AD。 在将应用添加到 Azure AD 租户时，可浏览的应用库中列出了这些应用。 此[快速入门系列](add-application-portal-setup-sso.md)将演示此过程。 对于库中的应用，你将看到详细的分步说明。 要查看这些步骤，可单击快速入门系列中所述应用的 SAML 配置页链接，也可在 [SaaS 应用配置教程](../saas-apps/tutorial-list.md)中查看所有应用配置教程的列表。

| 基本 SAML 配置设置 | SP 启动 | idP 启动 | 说明 |
|:--|:--|:--|:--|
| **标识符(实体 ID)** | 某些应用所需 | 某些应用所需 | 唯一标识应用程序。 Azure AD 将该标识符作为 SAML 令牌的 Audience 参数发送回应用程序。 应用程序应当对其进行验证。 该值也在应用程序提供的任何 SAML 元数据中显示为实体 ID。 输入使用以下模式的 URL：“https://<subdomain>.contoso.com”，可以在应用程序发送的 AuthnRequest（SAML 请求）中找到此值（Issuer 元素）**。 |
| 回复 URL | 必选 | 必选 | 指定应用程序应在何处接收 SAML 令牌。 回复 URL 也称断言使用者服务 (ACS) URL。 可以使用其他回复 URL 字段来指定多个回复 URL。 例如，你可能需要多个子域的其他回复 URL。 或者，出于测试目的，可以一次指定多个回复 URL（本地主机和公共 URL）。 |
| **登录 URL** | 必选 | 不指定 | 当用户打开此 URL 时，服务提供程序会将用户重定向到 Azure AD 进行身份验证和登录。 Azure AD 使用此 URL 从 Microsoft 365 或 Azure AD 的“我的应用”启动应用程序。 如果为空，则 Azure AD 在用户从 Microsoft 365、Azure AD 的“我的应用”或 Azure AD SSO URL 启动应用程序时进行 IdP 发起的登录。|
| **中继状态** | 可选 | 可选 | 指定应用程序在完成身份验证以后将用户重定向到何处。 通常，该值是应用程序的有效 URL。 但是，某些应用程序以不同的方式使用此字段。 有关详细信息，请询问应用程序供应商。
| **注销 URL** | 可选 | 可选 | 用于将 SAML 注销响应发回到应用程序。

## <a name="user-attributes-and-claims"></a>用户特性和声明

当用户对应用程序进行身份验证时，Azure AD 会向应用程序颁发 SAML 令牌，其中包含用于唯一标识用户的用户相关信息（或声明）。 默认情况下，这些信息包括用户的用户名、电子邮件地址、名字和姓氏。 例如，如果应用程序需要特定的声明值或用户名以外的名称格式，则可能需要自定义这些声明。

> [!IMPORTANT]
> 很多应用已经过预先配置且包含在应用库中，你无需担心设置用户和组声明。 [快速入门系列](add-application-portal.md)将演示如何添加和配置应用。

唯一用户标识符（名称 ID）标识符值是必需的声明，而且非常重要。 默认值为 user.userprincipalname。 用户标识符用于唯一标识应用程序中的每个用户。 例如，如果电子邮件地址既是用户名，也是唯一标识符，请将此值设置为 *user.mail*。

若要了解如何自定义 SAML 声明，请查看[如何：为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。

可添加新声明，请查看[添加特定于应用程序的声明](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)了解详细信息；若要添加组声明，请查看[配置组声明](../hybrid/how-to-connect-fed-group-claims.md)。

> [!NOTE]
> 有关在应用程序中自定义 Azure AD 提供的 SAML 令牌的其他方式，请参阅以下资源。
>- 若要通过 Azure 门户创建自定义角色，请参阅[配置角色声明](../develop/active-directory-enterprise-app-role-management.md)。
>- 若要通过 PowerShell 自定义声明，请参阅[自定义声明 - PowerShell](../develop/active-directory-claims-mapping.md)。
>- 若要修改应用程序清单以配置应用程序的可选声明，请参阅[配置可选声明](../develop/active-directory-optional-claims.md)。
>- 若要为刷新令牌、访问令牌、会话令牌和 ID 令牌设置令牌生存期策略，请参阅[配置令牌生存期](../develop/active-directory-configurable-token-lifetimes.md)。 或者，若要通过 Azure AD 条件访问限制身份验证会话，请参阅[身份验证会话管理功能](../conditional-access/howto-conditional-access-session-lifetime.md)。

## <a name="saml-signing-certificate"></a>SAML 签名证书

Azure AD 使用证书对它发送到应用程序的 SAML 令牌进行签名。 需要使用此证书在 Azure AD 与应用程序之间配置信任。 有关证书格式的详细信息，请参阅应用程序的 SAML 文档。 有关详细信息，请参阅[管理用于联合单一登录的证书](manage-certificates-for-federated-single-sign-on.md)和 [SAML 令牌中的高级证书签名选项](certificate-signing-options.md)。

> [!IMPORTANT]
> 很多应用已经过预先配置且包含在应用库中，你无需深入了解证书。 [快速入门系列](add-application-portal.md)将演示如何添加和配置应用。

从 Azure AD 中，可以直接从“使用 SAML 设置单一登录”页直接下载 Base64 或原始格式的活动证书。 此外，可下载应用程序元数据 XML 文件或使用应用联合元数据 URL 来获取有效证书。 若要查看、创建或下载证书（活动或非活动），请执行以下步骤。

要检查来验证证书的一些常见操作包括：

   - 到期时间是否正确。 最长可将到期日期配置为未来 3 年。
   - 适当证书的状态是否为“有效”。 如果状态为“非活动”，请将状态更改为“活动” 。 若要更改状态，请右键单击证书所在行，然后选择“激活证书”。
   - *正确的签名选项和算法。*
   - *正确的通知电子邮件地址。* 当活动的证书即将过期时，Azure AD 会向此字段中配置的电子邮件地址发送通知。

有时可能需要下载证书。 不过，请小心保存它！ 若要下载证书，请选择 Base64 格式、原始格式或联合元数据 XML 的选项之一。 Azure AD 还提供“应用联合元数据 URL”，从中可以访问特定于应用程序的、采用 `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` 格式的元数据。

> [!NOTE]
> 应用程序应能够处理在使用 https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} 时呈现的 XML 中显示的字节顺序标记。 字节顺序标记显示为不可打印的 ASCII 字符 »¿。在十六进制中，查看 XML 数据时，它显示为 EF BB BF。

若要更改证书，请选择“编辑”按钮。 可在“SAML 签名证书”页面上执行几项操作：

- 创建新证书：依次选择“新建证书”、“到期日期”和“保存”  。 若要激活证书，请选择上下文菜单 ( **...** )，然后选择“激活证书”。
- 上传包含私钥和 pfx 凭据的证书：选择“导入证书”并浏览到该证书。 输入 **PFX 密码**，然后选择“添加”。  
- 配置高级证书签名。 有关这些选项的详细信息，请查看[高级证书签名选项](certificate-signing-options.md)。
- 在有效证书即将过期时通知其他人：在“通知电子邮件地址”字段中输入电子邮件地址。

## <a name="set-up-the-application-to-use-azure-ad"></a>将应用程序设置为使用 Azure AD

“设置 \<applicationName>”部分列出了需要在应用程序中配置哪些值，以使应用程序将 Azure AD 用作 SAML 标识提供者。 在应用程序网站的配置页面上设置这些值。 例如，如果要配置 GitHub，需转到 github.com 网站并设置相应值。 如果应用程序已经过预配置且包含在 Azure AD 库中，你将找到“查看分步说明”的链接。 否则，需要查找要配置的应用程序的相应文档。 

“登录 URL”和“注销 URL”值都解析为同一终结点，即 Azure AD 租户的 SAML 请求处理终结点 。 

“Azure AD 标识符”是颁发给应用程序的 SAML 令牌中的 **Issuer** 值。

## <a name="test-single-sign-on"></a>测试单一登录

将应用程序配置为将 Azure AD 用作基于 SAML 的标识提供程序之后，就可以测试设置以查看单一登录是否适用于帐户。 

选择“测试”，然后选择以当前登录的用户的身份或作为其他人进行测试。 

如果登录成功，则你可以将用户和组分配到 SAML 应用程序。 祝贺你！

如果出现了错误消息，请完成以下步骤：

1. 将具体内容复制并粘贴到“错误如何呈现?”框中。

    ![获取解决方案指南](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. 选择“获取解决方法指导”。 此时会显示根本原因和解决方案指南。  在本示例中，用户未分配到应用程序。

3. 阅读解决方法指导，然后尝试解决问题。

4. 再次运行测试，直至成功完成。

有关详细信息，请参阅[在 Azure Active Directory 中调试对应用程序进行的基于 SAML 的单一登录](./debug-saml-sso-issues.md)。


## <a name="next-steps"></a>后续步骤

- [应用程序管理的快速入门系列](view-applications-portal.md)
- [将用户或组分配到应用程序](./assign-user-or-group-access-portal.md)
- [配置用户帐户自动预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [单一登录 SAML 协议](../develop/single-sign-on-saml-protocol.md)

