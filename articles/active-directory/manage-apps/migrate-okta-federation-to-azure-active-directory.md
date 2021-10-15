---
title: 有关将 Okta 联合迁移到 Azure Active Directory 托管的身份验证的教程
titleSuffix: Active Directory
description: 了解如何将 Okta 联合应用程序迁移到 Azure AD 托管的身份验证。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 28759e1f53199aca866a6b073c9e05ffd31f3d1e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355924"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>教程：将 Okta 联合迁移到 Azure Active Directory 托管的身份验证

本教程介绍如何将现有的 Office 365 租户与 Okta 进行联合以实现单一登录 (SSO) 功能。

可以分阶段将联合迁移到 Azure Active Directory (Azure AD)，以确保用户获得良好的身份验证体验。 在分阶段迁移中，还可以测试对任何剩余 Okta SSO 应用程序的反向联合访问。

## <a name="prerequisites"></a>先决条件

- 一个已联合到 Okta 以实现 SSO 的 Office 365 租户
- 已配置一个 Azure AD Connect 服务器或 Azure AD Connect 云预配代理，以将用户预配到 Azure AD

## <a name="1-configure-azure-ad-connect-for-authentication"></a>1. 配置 Azure AD Connect 进行身份验证

对于已将其 Office 365 域与 Okta 联合的客户，当前可能没有在 Azure AD 中配置有效的身份验证方法。 在迁移到托管身份验证之前，请验证 Azure AD Connect，并将其配置为允许用户登录。

设置最适合你环境的登录方法：

- 密码哈希同步：[密码哈希同步](../hybrid/whatis-phs.md)是对 Azure AD Connect 服务器或云预配代理实现的目录同步功能的一种扩展。 可以使用此功能登录到 Azure AD 服务（例如 Microsoft 365）。 登录到该服务时使用的密码与登录到本地 Active Directory 实例时使用的密码相同。

- 直通身份验：通过 Azure AD [直通身份验证](../hybrid/how-to-connect-pta.md)，用户可以使用相同的密码登录到本地应用程序和基于云的应用程序。 当用户通过 Azure AD 登录时，直通身份验证代理将直接针对本地 Active Directory 验证密码。

- 无缝 SSO：当用户位于已连接到企业网络的企业桌面时，[Azure AD 无缝 SSO](../hybrid/how-to-connect-sso.md) 会自动将用户登录。 用户可以通过无缝 SSO 轻松访问基于云的应用程序，而无需使用其他任何本地组件。

无缝 SSO 可以部署到密码哈希同步或直通身份验证，为 Azure AD 中的用户创建无缝的身份验证体验。

遵循[部署指南](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)，确保为最终用户部署无缝 SSO 必备的所有必备组件。

在本示例中，我们将配置密码哈希同步和无缝 SSO。

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>配置用于密码哈希同步和无缝 SSO 的 Azure AD Connect

按照以下步骤为密码哈希同步配置 Azure AD Connect：

1. 在 Azure AD Connect 服务器上打开“Azure AD Connect”应用，然后选择“配置” 。

   ![显示 Azure AD 图标和“配置”按钮的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

2. 选择“更改用户登录” > “下一步。 

   ![显示用于更改用户登录的页面的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

3. 输入你的全局管理员凭据。

   ![显示全局管理员凭据输入位置的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

4. 当前，服务器配置为与 Okta 联合。 将选项更改为“密码哈希同步”。 然后选择“启用单一登录”。

5. 选择“下一步”  。

按照以下步骤启用无缝 SSO：

1. 输入本地系统的域管理员凭据。 然后，选择“下一步”  。

   ![显示用户登录设置的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

2. 在最后一页上，选择“配置”以更新 Azure AD Connect 服务器。

   ![显示配置页的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

3. 暂时忽略有关混合 Azure AD 加入的警告。 禁用从 Okta 的联合后，你将重新配置设备选项。

   ![显示用于配置设备选项的链接的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="2-configure-staged-rollout-features"></a>2. 配置分阶段推出功能

在 Azure AD 中，可以先使用[分阶段推出云身份验证](../hybrid/how-to-connect-staged-rollout.md)来测试用户取消联合，然后再测试整个域的取消联合。 在部署之前，请先查看[先决条件](../hybrid/how-to-connect-staged-rollout.md#prerequisites)。

在 Azure AD Connect 服务器上启用密码哈希同步和无缝 SSO 后，请按照以下步骤配置分阶段推出：

1. 在 [Azure 门户](https://portal.azure.com/#home)中，选择“视图”或“管理 Azure Active Directory” 。

   ![显示 Azure 门户的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

2. 在“Azure Active Directory”菜单中选择“Azure AD Connect” 。 然后确认已在租户中启用“密码哈希同步”。

3. 选择“为托管用户登录启用分阶段推出”。

   ![显示用于启用分阶段推出的选项的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

4. 配置服务器后，“密码哈希同步”设置可能已更改为“打开” 。 如果未启用该设置，现在请启用。 

   请注意“无缝 SSO”已设置为“关闭” 。 如果你尝试启用无缝 SSO，则会收到错误，因为已经为租户中的用户启用了此功能。

5. 选择“管理组”。

   ![显示用于管理组的按钮的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

按照说明将一个组添加到密码哈希同步推出。 在以下示例中，安全组最初有 10 个成员。

![显示安全组示例的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

添加该组后，等待大约 30 分钟，让该功能在租户中生效。 该功能生效后，你的用户在尝试访问 Office 365 服务时将不再被重定向到 Okta。

分阶段推出功能不支持某些方案：  

- 不支持 POP3 和 SMTP 等旧式身份验证。

- 如果已将混合 Azure AD 加入配置为与 Okta 一起使用，则所有混合 Azure AD 加入流将进入 Okta，直到域被取消联合。 应在 Okta 中保留一个登录策略，以允许混合 Azure AD 加入 Windows 客户端进行旧式身份验证。

## <a name="3-create-an-okta-app-in-azure-ad"></a>3. 在 Azure AD 中创建 Okta 应用

已转换为托管身份验证的用户可能仍然需要访问 Okta 中的应用程序。 要使用户能够轻松访问这些应用程序，可以注册一个链接到 Okta 主页的 Azure AD 应用程序。

若要为 Okta 配置企业应用程序注册，请执行以下操作： 
1. 在 [Azure 门户](https://portal.azure.com/#home)中的“管理 Azure Active Directory”下，选择“视图” 。

2. 在左侧菜单中的“管理”下，选择“企业应用程序” 。

   ![显示“企业应用程序”选项的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

3. 在“所有应用程序”菜单中，选择“新建应用程序” 。

   ![显示“新建应用程序”选项的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

4. 选择“创建自己的应用程序”。 在打开的菜单中，为该 Okta 应用命名，然后选择“注册所用的应用程序以便与 Azure AD 集成”。 然后选择“创建”。

   :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/register-application.png" alt-text="显示如何注册应用程序的屏幕截图。" lightbox="media/migrate-okta-federation-to-azure-active-directory/register-application.png":::

5. 选择“任何组织目录中的帐户(任何 Azure AD 目录 - 多租户)” > “注册” 。

   ![显示如何注册应用程序和更改应用程序帐户的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

6. 在 Azure AD 菜单中，选择“应用注册”。 然后打开新建的注册。

   ![显示新应用注册的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

7. 请记下你的租户 ID 和应用程序 ID。

   >[!Note]
   >稍后需要使用该租户 ID 和应用程序 ID 在 Okta 中配置标识提供者。

   ![显示租户 ID 和应用程序 ID 的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

8. 在左侧菜单中，选择“证书和机密”。 然后选择“新建客户端机密”。 为机密指定一个通用名称，并设置其到期日期。

9. 请记下该机密的值和 ID。

   >[!NOTE]
   >以后不再会显示该值和 ID。 如果现在不记下此信息，以后必须重新生成机密。

   ![显示在何处记下机密值和 ID 的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

10. 在左侧菜单中，选择“API 权限”。 向应用程序授予对 OpenID Connect (OIDC) 堆栈的访问权限。

11. 选择“添加权限” > “Microsoft Graph” > “委托的权限”  。

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png" alt-text="显示“委托的权限”的屏幕截图。" lightbox="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png":::

12. 从“OpenID 权限”部分，添加“电子邮件”、“openid”和“配置文件”  。 然后选择“添加权限”。

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png" alt-text="显示如何添加权限的屏幕截图。" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png":::

13. 选择“为 \<tenant domain name> 授予管理员同意”，并等待“已授予”状态出现 。

    ![显示已授予同意的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

14. 在左侧菜单中，选择“品牌”。 对于“主页 URL”，请添加用户的应用程序主页。

    ![显示如何添加品牌的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

15. 在 Okta 管理门户中，选择“安全性” > “标识提供者”以添加新的标识提供者 。 选择“添加 Microsoft”。

    ![显示如何添加标识提供者的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

16. 在“标识提供者”页上，将你的应用程序 ID 复制到“客户端 ID”字段 。 将客户端机密复制到“客户端机密”字段。

17. 选择“显示高级设置”。 默认情况下，此配置会将 Okta 中的用户主体名称 (UPN) 关联到 Azure AD 中的 UPN，以实现反向联合访问。

    >[!IMPORTANT]
    >如果 Okta 和 Azure AD 中的 UPN 不匹配，请选择在用户之间通用的属性。

18. 完成自动预配的选择。 默认情况下，如果某个用户在 Okta 中没有匹配的用户，则系统会尝试在 Azure AD 中预配相应的用户。 如果已将预配从 Okta 迁移出来，请选择“重定向到 Okta 登录页”。

    ![显示用于重定向到 Okta 登录页的选项的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    创建标识提供者 (IDP) 后，需要将用户发送到正确的 IDP。

19. 在“标识提供者”菜单中，选择“路由规则” > “添加路由规则”  。 使用 Okta 配置文件中的可用属性之一。

20. 若要将来自所有设备和 IP 的登录定向到 Azure AD，请按下图所示设置策略。

    在此示例中，所有 Okta 配置文件上都没有使用 Division 属性，因此该属性非常适合用于 IDP 路由。

    ![显示用于 IDP 路由的 division 属性的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

21. 添加路由规则后，请记下重定向 URI，以便可以将它添加到应用程序注册。

    ![显示重定向 URI 位置的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

22. 在应用程序注册上的左侧菜单中，选择“身份验证”。 然后选择“添加平台” > “Web” 。

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-platform.png" alt-text="显示如何添加 Web 平台的屏幕截图。" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-platform.png":::

23. 在 Okta 中添加你在 IDP 中记下的重定向 URI。 然后选择“访问令牌”和“ID 令牌” 。

    ![显示 Okta 访问令牌和 ID 令牌的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

24. 在管理控制台中，选择“目录” > “人员” 。 选择第一个测试用户以编辑配置文件。

25. 在配置文件中添加“ToAzureAD”，如下图所示。 再选择“保存”。

    ![显示如何编辑配置文件的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

26. 尝试以修改后的用户身份登录到 [Microsoft 356 门户](https://portal.office.com)。 如果该用户不是托管身份验证试点的一部分，则你会发现你的操作循环往复。 若要退出这种循环，请将该用户添加到托管身份验证体验。

## <a name="4-test-okta-app-access-on-pilot-members"></a>4. 测试试点成员的 Okta 应用访问权限

在 Azure AD 中配置 Okta 应用并在 Okta 门户中配置 IDP 后，必须将应用程序分配到用户。

1. 在 Azure 门户中，选择“Azure Active Directory” > “企业应用程序”。

2. 选择前面创建的应用注册，并转到“用户和组”。 添加与托管身份验证试点关联的组。

   >[!NOTE]
   >只能从“企业应用程序”页添加用户和组。 不能从“应用注册”菜单添加用户。

   ![显示如何添加组的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

3. 大约 15 分钟后，以某个托管身份验证试点用户的身份登录并转到“[我的应用](https://myapplications.microsoft.com)”。

   ![显示“我的应用”库的屏幕截图。](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

4. 选择“Okta 应用程序访问”磁贴，将用户返回到 Okta 主页。

## <a name="5-test-managed-authentication-on-pilot-members"></a>5. 测试试点成员的托管身份验证

配置 Okta 反向联合应用后，让用户在托管身份验证体验上展开全面测试。 建议设置公司品牌，以帮助用户识别他们要登录到的租户。 有关详细信息，请参阅[将品牌添加到组织的 Azure AD 登录页](../fundamentals/customize-branding.md)。

>[!IMPORTANT]
>确定在从 Okta 完全取消联合域之前可能需要的任何其他条件访问策略。 若要在完全切断联合之前保护环境，请参阅 [Okta 登录策略到 Azure AD 条件访问的迁移](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)。

## <a name="6-defederate-office-365-domains"></a>6. 取消联合 Office 365 域

如果你的组织对托管身份验证体验感到满意，则可以从 Okta 取消联合你的域。 若要开始，请使用以下命令连接到 MSOnline PowerShell。 如果尚未安装 MSOnline PowerShell 模块，可以输入 `install-module MSOnline` 下载该模块。

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

将域设置为托管身份验证后，便已成功地从 Okta 取消联合了你的 Office 365 租户，同时还保留了用户对 Okta 主页的访问权限。 

## <a name="next-steps"></a>后续步骤

- [将 Okta 同步预配迁移到基于 Azure AD Connect 的同步](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [将 Okta 登录策略迁移到 Azure AD 条件访问](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)

- [将应用程序从 Okta 迁移到 Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
