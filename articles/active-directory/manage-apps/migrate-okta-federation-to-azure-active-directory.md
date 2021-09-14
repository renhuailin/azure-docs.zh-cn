---
title: 教程：将 Okta 联合身份验证迁移到 Azure AD 托管身份验证
titleSuffix: Active Directory
description: 了解如何将 Okta 联合应用程序迁移到 Azure AD 托管的身份验证
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: da42e6ea14167b3214be9162197e2cbc3afabcd5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439718"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>教程：将 Okta 联合迁移到 Azure Active Directory 托管的身份验证

在本教程中，了解如何将现有的 Office 365 租户与 Okta 进行联合以实现单一登录 (SSO) 功能。

将联合迁移到 Azure Active Directory (AD) 的过程可以分阶段完成，以确保用户获得所需的身份验证体验。 此外，测试对剩余 Okta SSO 应用程序的反向联合访问。

## <a name="prerequisites"></a>必备条件

- 联合到 Okta 以实现 SSO 的 Office 365 租户
- 配置 Azure AD Connect 服务器或 Azure AD Connect 云预配代理以将用户预配到 Azure AD。

## <a name="step-1---configure-azure-ad-connect-for-authentication"></a>步骤 1 - 配置 Azure AD Connect 进行身份验证

对于已将其 Office 365 域与 Okta 联合的客户，当前可能没有在 Azure AD 中配置有效的身份验证方法。 迁移到托管身份验证之前，应验证 Azure AD Connect，并为其配置下列选项之一以允许用户登录。

使用以下方法来确定哪种方法最适合你的环境：

-  - 密码哈希同步 [密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs)是对由 Azure AD Connect 服务器或云预配代理实现的目录同步功能的一种扩展。 可以使用此功能登录到 Azure AD 服务（例如 Microsoft 365）。 登录到该服务时使用的密码与登录到本地 Active Directory 实例时使用的密码相同。

- 直通身份验 - Azure AD [直通身份验证](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta)允许用户使用相同的密码登录本地应用程序和基于云的应用程序。 如果用户使用 Azure AD 进行登录，此功能可通过直通身份验证大力，直接针对本地 Active Directory 验证用户的密码。

- 无缝 SSO - 当用户登录到连接企业网络的企业台式机之后，[Azure AD 无缝 SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) 可使用户自动登录。 无缝 SSO 可让用户轻松访问基于云的应用程序，而无需使用其他任何本地组件。

无缝 SSO 还可以部署到密码哈希同步或直通身份验证，为 Azure AD 中的用户创造无缝的身份验证体验。

请确保按照[部署指南](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-1-check-the-prerequisites)，将无缝 SSO 必备的所有必备软件部署到最终用户。

对于我们的示例，我们将配置密码哈希同步和无缝 SSO。

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>配置用于密码哈希同步和无缝 SSO 的 Azure AD Connect

按照以下步骤配置 Azure AD Connect 以用于密码哈希同步：

1. 在 Azure AD Connect 服务器上，从开始菜单或桌面图标启动 Azure AD Connect 应用程序，然后选择“配置”。 

   ![显示 Azure AD 图标和“配置”按钮的图像](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

2. 选择“更改用户登录” > “下一步。 

   ![显示“更改用户登录”屏幕的图像](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

3. 在下一页中输入全局管理员凭据。

   ![显示如何输入全局管理员凭据的图像](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

4. 当前，服务器配置为与 Okta 联合。 将所选内容更新为密码哈希同步。 选中“启用单点登录”复选框

5. 更新选择后，选择“下一步”。

若要启用无缝 SSO，请执行下列步骤：

1. 输入本地管理员的域管理员凭据，然后选择“下一步”。

   ![显示如何输入域管理员凭据的图像](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

2. 在最后一页上，选择“配置”以更新 Azure AD Connect 服务器。

   ![显示更新 azure ad connect 服务器的图像](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

3. 暂时忽略混合 Azure AD 加入的警告，但是在从 Okta 禁用联合后需要重新配置设备选项。

   ![显示重新配置设备选项的图像](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="step-2---configure-staged-rollout-features"></a>步骤 2 - 配置分阶段推出功能

[分阶段推出云身份验证](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout)是 Azure AD 的一项功能，可用于在取消整个域的联合之前测试取消联合的用户。 在部署之前，请先查看[必备组件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout#prerequisites)。

在 Azure AD Connect 服务器上启用密码哈希同步和无缝 SSO 后，请按照以下步骤配置分阶段推出。

1. 转到 [Azure 门户](https://portal.azure.com/#home)，然后选择“查看”或“管理 Azure Active Directory”。 

   ![显示 azure 门户的图像](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

2. 在 Azure 活动目录菜单中，选择 **Azure AD Connect** 并确认密码哈希同步在租户中显示为已启用。

3. 确认后，选择“为托管用户登录启用分阶段推出”

   ![显示分阶段推出的图像](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

4. 配置服务器后，密码哈希同步设置可能已转换为“开启”，如果没有，请立即启用，你会注意到无缝 SSO 设置为“关闭”。  如果尝试在菜单中启用此功能，则会收到一个错误，指出已为租户中的用户启用了此功能。

5. 启用密码哈希同步之后，选择“管理组”。

   ![显示启用密码哈希同步的图像](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

按照有关将组添加到密码哈希同步推出的说明进行操作。 在以下示例中，使用一个安全组，开始时有 10 个成员。

![显示安全组示例的图像](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

添加组后，请等待大约 30 分钟，让该功能在你的租户中生效。 该功能生效后，你的用户在尝试访问 Office 365 服务时将不再被重定向到 Okta。

分阶段推出功能在某些情况下不受支持，如下所示：  

- 不支持 POP3 和 SMTP 等旧式身份验证。

- 如果已将混合 Azure AD 加入配置为与 Okta 一起使用，则所有混合 Azure AD 加入流仍将进入 Okta，直到域被解除联合。 登录策略应保留在 Okta 中，允许对混合 Azure AD 加入 Windows 客户端进行旧式身份验证。

## <a name="step-3---create-okta-app-in-azure-ad"></a>步骤 3 - 在 Azure AD 中创建 Okta 应用

转换为托管身份验证的用户可能仍在 Okta 中具有需要访问的应用程序，从而使用户可以轻松访问这些应用程序。 了解如何为用户配置链接到 Okta 主页的 Azure AD 应用程序注册。

1. 若要为 Okta 配置企业应用程序注册，请转到 [Azure 门户](https://portal.azure.com/#home)。 在“管理 Azure Active Directory”中选择“查看”。 

2. 接下来，从“管理”部分下的菜单中选择“企业应用程序”。

   ![显示企业应用程序的图像](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

3. 在“所有应用程序”菜单中，选择“新建应用程序”。 

   ![此图像显示新应用程序](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

4. 选择“创建自己的应用程序”，并在弹出的侧菜单上，为 Okta 应用提供名称，并选择“注册所用的应用程序以将其与 Azure AD 集成”单选按钮，然后选择“创建”。  

   ![显示注册应用程序的图像](media/migrate-okta-federation-to-azure-active-directory/register-application.png)

5. 注册应用程序后，将应用程序更改为在任何组织目录中的帐户（例如任何 Azure AD 目录 - 多租户），然后选择“注册”。

   ![显示注册应用程序并更改应用程序帐户的图像](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

6. 添加注册后，回到 Azure AD 菜单，选择“应用注册”，然后打开新创建的注册。

   ![显示应用注册的图像](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

7. 打开应用程序后，记录你的租户 ID 和应用程序 ID。

   >[!Note]
   >稍后需要租户 ID 和应用程序 ID 才能在 Okta 中配置标识提供程序。

   ![显示记录租户 ID 和应用程序 ID 的图像](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

8. 在左侧菜单中选择“证书和密码”。 选择“新建客户端机密”，为其指定一个通用名称并设置其过期时间。

9. 在离开此页之前，记录机密的值和 ID。

   >[!NOTE]
   >稍后将无法记录此信息，如果丢失，则必须重新生成机密。

   ![显示记录机密的图像](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

10. 在左侧菜单中选择“API 权限”，并授予应用程序访问 OpenID Connect (OIDC) 堆栈的权限。

11. 选择“添加权限” > “Microsoft Graph” > “委派权限”。  

    ![显示委托权限的图像](media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png)

12. 从 OpenID 权限部分，添加“电子邮件”、“OpenID”和“配置文件”，然后选择“添加权限”。   

    ![显示添加权限的图像](media/migrate-okta-federation-to-azure-active-directory/add-permissions.png)

13. 选择“为租户域名授予管理员许可”选项并等待授予状态出现。

    ![显示授予许可的图像](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

14. 同意许可后，将在你的用户的应用程序主页的“品牌”下添加主页 URL。

    ![显示添加品牌的图像](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

15. 配置应用程序后，过渡到 Okta 管理门户并将 Microsoft 配置为标识提供者。 选择“安全性” > “标识提供者”并添加一个新的标识提供者，并添加默认选项“Microsoft”。  

    ![该插图显示如何配置 idp](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

16. 在“标识提供者”页上，将你的应用程序 ID 复制到客户端 ID 字段，并将客户端机密复制到客户端机密字段。

17. 选择“显示高级设置”。 默认情况下，这将绑定 Okta 和 Azure AD 中的用户主体名称 (UPN) 以进行反向联合访问。

    >[!IMPORTANT]
    >如果你的 UPN 在 Okta 和 Azure AD 中不匹配，请选择用户之间的通用属性进行匹配。

18. 完成自动预配的选择。 默认情况下，如果用户与 Okta 不匹配，将会尝试在 Azure AD 中预配用户。 如果已将预配从 Okta 迁移，请选择“重定向到 Okta 登录页面”选项。

    ![显示重定向 okta 登录的图像](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    创建 IDP 后，需要额外配置才能将用户发送到正确的 IDP。

19. 从“标识提供者”菜单中选择路由规则，然后使用 Okta 配置文件中的可用属性之一选择“添加路由规则”。 

20. 如图所示配置策略以将来自所有备和 IP 的登录定向到 Azure AD。

    在示例中，所有 Okta 配置文件中都没有使用我们的属性 Division，该属性可以让候选对象轻松用于 IDP 路由。

    ![显示 IDP 路由划分的图像](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

21. 添加路由规则后，记录 Redirect URI，并将其添加到“应用程序注册”中。

    ![显示了应用程序注册的图像](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

22. 导航回应用程序注册并选择“身份验证”选项卡，然后选择“添加平台”和“Web”。 

    ![显示添加平台的图像](media/migrate-okta-federation-to-azure-active-directory/add-platform.png)

23. 从 Okta 中的 IDP 添加重定向 URI，然后选择“访问和 ID 令牌”。

    ![显示 okta 访问和 ID 令牌的图像](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

24. 从管理员控制台中选择“目录” > “人员”。 选择第一个测试用户及其配置文件。

25. 在编辑配置文件时，添加 ToAzureAD 以匹配示例并选择“保存”。 

    ![显示配置文件编辑的图像](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

26. 保存用户属性后，尝试以修改后的用户身份登录 [Microsoft 356 门户](https://portal.office.com)。 如果用户不是托管身份验证试点的一部分，您会注意到会循环显示。 若要让用户退出循环，请将其添加到托管身份验证体验。

## <a name="step-4---test-okta-app-access-on-pilot-members"></a>步骤 4 - 在试点成员上测试 Okta 应用访问权限

在 Azure AD 中配置 Okta 应用并在 Okta 门户中配置标识提供程序后，必须将应用程序分配给用户。

1. 导航到 Azure 门户，选择“Azure Active Directory” > “企业应用程序”。

2. 选择之前创建的应用注册，导航到“用户和组”。 添加与托管身份验证试点相关的组。

>[!NOTE]
>只能从企业应用程序选项中添加用户和组，不能在“应用注册”菜单下添加用户。

   ![显示添加组的图像](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

3. 大约 15 分钟后，以某个托管身份验证试点用户的身份登录并访问 [Myapplications](https://myapplications.microsoft.com)。

   ![显示访问 myapplications 的图像](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

4. 在通过身份验证后，将出现一个“Okta 应用程序访问”磁贴，可将用户链接回 Okta 主页。

## <a name="step-5---test-managed-authentication-on-pilot-members"></a>步骤 5 - 对试点成员执行托管身份验证测试

配置 Okta 反向联合应用程序后，让你的用户对托管身份验证体验进行全面测试。 建议设置公司品牌，以帮助用户区分他们正在登录的正确租户。 获得有关设置公司品牌的[指南](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)。

>[!IMPORTANT]
>在从 Okta 整体解除域联合之前，确定可能需要的任何其他条件访问策略。 请参阅 **Okta 登录策略到 Azure AD 条件访问的迁移**，了解在完全关闭之前需要采取哪些步骤来保护你的环境。

## <a name="step-6---remove-federation-for-office-365-domains"></a>步骤 6 - 删除 Office 365 域的联合

一旦你的组织对托管身份验证体验感到满意，就可以解除你的域与 Okta 的联合。 要完成此操作，请使用以下命令连接到 MSOnline PowerShell - 如果还没有 MSOnline PowerShell 模块，可以先通过执行安装模块 MSOnline 来下载。

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

将域设置为托管身份验证后，便已成功解除你的 Office 365 租户与 Okta 的联合，同时保持用户对 Okta 主页的访问权限。 

## <a name="next-steps"></a>后续步骤

- [将 Okta 同步预配迁移到基于 Azure AD Connect 的同步](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [将 Okta 登录策略迁移到 Azure AD 条件访问](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)

- [将应用程序从 Okta 迁移到 Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
