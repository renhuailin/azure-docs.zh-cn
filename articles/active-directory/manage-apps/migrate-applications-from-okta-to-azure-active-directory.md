---
title: 有关将应用程序从 Okta 迁移到 Azure Active Directory 的教程
titleSuffix: Active Directory
description: 了解如何将应用程序从 Okta 迁移到 Azure Active Directory
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: c46410a6998998ace9bc1a9e9809262970a131f2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791719"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>教程：将应用程序从 Okta 迁移到 Azure Active Directory

本教程介绍如何将应用程序从 Okta 迁移到 Azure Active Directory (Azure AD)。

## <a name="create-an-inventory-of-current-okta-applications"></a>创建当前 Okta 应用程序的清单

将 Okta 应用程序转换到 Azure AD 时，建议在迁移之前先记录当前环境和应用程序设置。

Okta 提供了一个 API，该 API 可用于从一个集中的位置收集此信息。 若要使用该 API，需要安装 [Postman](https://www.postman.com/) 之类的 API 浏览器工具。

按照以下步骤创建应用程序清单：

1. 安装 Postman 应用。 安装后，从 Okta 管理控制台生成 API 令牌。

2. 导航到“安全性”部分下的 API 仪表板，选择“令牌” > “创建令牌” 

   ![该插图显示令牌创建操作](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

3. 插入令牌名称，然后选择“创建令牌”。

   ![该插图显示已创建令牌](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

4. 选择“创建令牌”后，请记录并保存令牌值，因为在选择“好的，知道了”后不再可以访问该值 。

   ![该插图显示已创建记录](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

5. 记录 API 令牌后，返回 Postman 应用，并选择工作区下的“导入”。

   ![该插图显示有关导入 API 的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

6. 在“导入”页中选择“链接”，并使用以下链接导入 API：<https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment>

   ![该插图显示用于导入的链接](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

>[!NOTE]
>请不要使用你的租户值修改该链接。

7. 选择“导入”转到下一菜单。

   ![该插图显示下一个导入菜单](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

8. 导入后，将“环境”选择更改为“{yourOktaDomain}”

   ![该插图显示有关更改环境的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png)

9. 更改“环境”选择后，依次选择眼睛图标和“编辑”来编辑 Okta 环境。

   ![该插图显示有关编辑环境的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

10. 在“初始值”和“当前值”字段中更新 URL 和 API 密钥的值，并更改名称以反映环境，然后保存这些值 。

    ![该插图显示有关为 API 更新值的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

11. 保存 API 密钥后，[将应用 API 加载到 Postman 中](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17)。

12. 将 API 加载到 Postman 后，选择“应用”下拉菜单，然后依次选择“获取应用列表”、“发送”  。

现在，可以使用 JSON 格式输出 Okta 租户中的所有应用程序。

![该插图显示应用程序列表](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

建议复制此 JSON 列表，并使用 <https://konklone.io/json/> 之类的公用转换器或者在 PowerShell 中使用 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) 和 [ConvertTo-CSV](/powershell/module/microsoft.powershell.utility/convertto-csv) 将其转换为 CSV。

下载 CSV 后，Okta 租户中的应用程序已成功记录以供将来参考。

## <a name="migrate-a-saml-application-to-azure-ad"></a>将 SAML 应用程序迁移到 Azure AD

若要将 SAML 2.0 应用程序迁移到 Azure AD，请先在 Azure AD 租户中配置该应用程序以进行应用程序访问。 在此示例中，我们将转换一个 Salesforce 实例。 请按照[此教程](../saas-apps/salesforce-tutorial.md)加入应用程序。

若要完成迁移过程，请对 Okta 租户中发现的所有应用程序重复配置步骤。

1. 导航到 [Azure AD 门户](https://aad.portal.azure.com)，选择“Azure Active Directory” > “企业应用程序” > “新建应用程序”  。

   ![该插图显示新应用程序的列表](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

2. Azure AD 库中提供了 Salesforce。 搜索“salesforce”，选择该应用程序，然后选择“创建”。

   ![该插图显示 Salesforce 应用程序](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

3. 创建应用程序后，导航到“单一登录”(SSO) 选项卡并选择“SAML” 。

   ![该插图显示 SAML 应用程序](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

4. 选择“SAML”后，下载要导入到 Salesforce 的“联合元数据 XML 和证书(原始)”。

   ![该插图显示有关下载联合元数据的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

5. 捕获 XML 后，导航到 Salesforce 管理控制台，然后选择“标识” > “单一登录设置” > “从元数据文件新建”  

   ![该插图显示 Salesforce 管理控制台](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

6. 上传从 Azure AD 门户下载的 XML 文件，然后选择“创建”。

   ![该插图显示有关上传 XML 文件的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png)

7. 上传从 Azure 下载的证书，然后在下一个菜单中选择“保存”以便在 Salesforce 中创建 SAML 提供程序。

   ![该插图显示有关创建 SAML 提供程序的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

8. 记录“实体 ID”、“登录 URL”和“注销 URL”的值以便在 Azure 中使用，然后选择“下载元数据”选项   。

   ![该插图显示有关记录 Azure 中的值的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

9. 返回到 Azure AD 企业应用程序菜单，选择“上传元数据文件”以将元数据文件上传到 Azure AD 门户中的 SAML SSO 设置。 在保存之前请确认导入的值与记录的值匹配。

   ![该插图显示在 Azure AD 中上传元数据文件的操作](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

10. 保存 SSO 配置后，返回到 Salesforce 管理控制台并选择“公司设置” > “我的域” 。 导航到“身份验证配置”并选择“编辑” 。

    ![该插图显示有关编辑公司设置的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

11. 选择在前面步骤中配置为可用登录选项的新 SAML 提供程序，然后选择“保存”。

    ![该插图显示用于保存 SAML 提供程序的选项](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

12. 返回到 Azure AD 中的企业应用程序，选择“用户和组”，然后添加测试用户 。

    ![该插图显示有关添加测试用户的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

13. 若要进行测试，请以某个测试用户的身份登录，导航到 <https://aka.ms/myapps>，然后选择“Salesforce”磁贴。

    ![该插图显示有关以测试用户的身份登录的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

14. 选择“Salesforce”磁贴后，选择新配置的标识提供者 (IdP) 进行登录。

    ![该插图显示有关选择新标识提供者的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

15. 如果正确配置了所有设置，则用户将会进入 Salesforce 主页。 如果遇到任何问题，请按照[调试指南](../manage-apps/debug-saml-sso-issues.md)进行操作。

16. 测试从 Azure 进行 SSO 连接后，返回到企业应用程序，将具有正确角色的其余用户分配到 Salesforce 应用程序。

>[!NOTE]
>将其余用户添加到 Azure AD 应用程序后，建议让用户测试连接，确保在继续下一步之前不会出现访问问题。

17. 在用户确认没有登录问题后，返回到 Salesforce 管理控制台并选择“公司设置” > “我的域” 。

18. 导航到“身份验证配置”，选择“编辑”，并取消选择 Okta 作为身份验证服务 。

    ![该插图显示有关取消选择 Okta 作为身份验证服务的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

现已成功地在 Azure AD 中配置了 Salesforce 以执行 SSO。 本文档稍后将介绍清理 Okta 门户的步骤。

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>将 OIDC/OAuth 2.0 应用程序迁移到 Azure AD

首先在 Azure AD 租户中配置应用程序以进行应用程序访问。 在此示例中，我们将转换一个自定义 OIDC 应用。

若要完成迁移过程，请对 Okta 租户中发现的所有应用程序重复配置步骤。

1. 导航到 [Azure AD 门户](https://aad.portal.azure.com)，选择“Azure Active Directory” > “企业应用程序” 。 在“所有应用程序”菜单下，选择“新建应用程序” 。

2. 选择“创建自己的应用程序”。 在弹出的边侧菜单中，为 OIDC 应用命名，选择“注册所用的应用程序以将其与 Azure AD 集成”对应的单选按钮，然后选择“创建” 。

   ![该插图显示有关新建 OIDC 应用程序的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png)

3. 在下一页上，你将看到有关应用程序注册租户的选择。 有关详细信息，请参阅[此文](../develop/single-and-multi-tenant-apps.md)。

在此示例中，我们将依次选择“任何组织目录中的帐户(任何 Azure AD 目录 - 多租户)”、“注册”  。

![该插图显示有关 Azure AD 目录多租户设置的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

4. 注册应用程序后，导航到“Azure Active Directory”下的“应用注册”页，然后打开新建的注册 。

   根据[应用程序方案](../develop/authentication-flows-app-scenarios.md)的不同，可能需要执行多种配置操作。 由于大多数方案需要应用客户端机密，我们将提供相关示例。

5. 在“概述”页上，记录“应用程序(客户端) ID”以便稍后在应用程序中使用。

   ![该插图显示应用程序客户端 ID](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

6. 记录“应用程序 ID”后，在左侧菜单中选择“证书和机密”。 选择“新建客户端机密”，为其命名并相应地设置其过期时间。

   ![该插图显示有关新建客户端机密的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

7. 在离开此页之前，请记录机密的值和 ID。

>[!NOTE]
>以后无法记录此信息，如果丢失此信息，必须重新生成机密。

8. 在上述步骤中记录信息后，选择左侧的“API 权限”，并授予应用程序访问 OIDC 堆栈的权限。

9. 依次选择“添加权限”、“Microsoft Graph”和“委托的权限”  。

10. 在“OpenID 权限”部分添加电子邮件、OpenID 和配置文件，然后选择“添加权限” 。

    ![该插图显示有关添加 OpenID 权限的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png)

11. 添加权限后，为了改善用户体验并消除用户同意提示，请选择“为租户域名授予管理员同意”选项并等待“已授予”状态出现 。

    ![该插图显示有关授予管理员同意的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

12. 如果你的应用程序有重定向 URI 或回复 URL，请依次导航到“身份验证”选项卡、“添加平台”和“Web”，输入相应的 URL，选择“访问令牌”并选择底部的 ID 令牌，然后选择“配置”   。

    ![该插图显示有关配置令牌的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png)

    如有必要，请在“身份验证”菜单中的“高级设置”下，将“允许公共客户端流”切换为“是” 。

    ![该插图显示有关允许公共客户端流的用户界面](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

13. 返回到配置了 OIDC 的应用程序，将应用程序 ID 和客户端机密导入到应用程序，然后进行测试。 将应用程序配置为使用上述配置（例如 clientID、机密和范围）。

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>将自定义授权服务器迁移到 Azure AD

Okta 授权服务器一对一地映射到[公开 API](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope) 的应用程序注册。

默认 Okta 授权服务器应映射到 Microsoft Graph 范围/权限。

![该插图显示默认 Okta 授权](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>后续步骤 

- [将 Okta 联合迁移到 Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [将 Okta 同步预配迁移到基于 Azure AD Connect 的同步](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [将 Okta 登录策略迁移到 Azure AD 条件访问](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)