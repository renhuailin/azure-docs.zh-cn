---
title: 有关将应用程序从 Okta 迁移到 Azure Active Directory 的教程
titleSuffix: Active Directory
description: 了解如何将应用程序从 Okta 迁移到 Azure Active Directory。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 76acf97366407a34faa2f6a6583d5871ba4c5b4a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355938"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>教程：将应用程序从 Okta 迁移到 Azure Active Directory

本教程介绍如何将应用程序从 Okta 迁移到 Azure Active Directory (Azure AD)。

## <a name="create-an-inventory-of-current-okta-applications"></a>创建当前 Okta 应用程序的清单

在开始迁移之前，应记录当前环境和应用程序设置。 可以使用 Okta API 从一个集中位置收集此信息。 若要使用该 API，需要安装 [Postman](https://www.postman.com/) 之类的 API 浏览器工具。

按照以下步骤创建应用程序清单：

1. 安装 Postman 应用。 然后从 Okta 管理控制台生成 API 令牌。

2. 在 API 仪表板上的“安全性”下，选择“令牌” > “创建令牌”  。

   ![显示用于创建令牌的按钮的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

3. 插入令牌名称，然后选择“创建令牌”。

   ![显示令牌命名位置的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

4. 记录并保存令牌值。 在选择“好的，知道了”之后不再可以访问该值。

   ![显示令牌值的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

5. 在 Postman 应用的工作区中，选择“导入”。

   ![显示导入 API 的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

6. 在“导入”页上选择“链接” 。 然后插入以下链接以导入 API：`https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment`

   ![显示用于导入的链接的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

   >[!NOTE]
   >请不要使用你的租户值修改该链接。

7. 选择“导入”继续操作。

   ![显示下一个“导入”页的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

8. 导入 API 后，将“环境”选择更改为“{yourOktaDomain}” 。

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png" alt-text="显示如何更改环境的屏幕截图。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png":::

9. 选择眼睛图标编辑 Okta 环境。 然后选择“编辑”。

   ![显示如何编辑 Okta 环境的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

10. 在“初始值”和“当前值”字段中更新 URL 和 API 密钥的值 。 更改名称以反映你的环境。 然后保存值。

    ![显示如何更新 API 值的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

11. [将 API 加载到 Postman 中](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17)。

12. 选择“应用” > “获取应用列表” > “发送”  。

现在，可以输出 Okta 租户中的所有应用程序。 列表采用 JSON 格式：

![显示 Okta 租户中的应用程序列表的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

建议复制此 JSON 列表并将其转换为 CSV 格式。 可以使用 [Konklone](https://konklone.io/json/) 之类的公用转换器。 或者，可以在 PowerShell 中使用 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) 和 [ConvertTo-CSV](/powershell/module/microsoft.powershell.utility/convertto-csv)。

下载 CSV，以保留 Okta 租户中应用程序的记录供将来参考。

## <a name="migrate-a-saml-application-to-azure-ad"></a>将 SAML 应用程序迁移到 Azure AD

若要将 SAML 2.0 应用程序迁移到 Azure AD，请先在 Azure AD 租户中配置该应用程序以进行应用程序访问。 在此示例中，我们将转换一个 Salesforce 实例。 请按照[此教程](../saas-apps/salesforce-tutorial.md)配置应用程序。

若要完成迁移，请对 Okta 租户中发现的所有应用程序重复配置步骤。

1. 在 [Azure AD 门户](https://aad.portal.azure.com)中，选择“Azure Active Directory” > “企业应用程序” > “新建应用程序”  。

   ![显示新应用程序列表的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

2. 在“Azure AD 库”中，搜索“Salesforce”，选择该应用程序，然后选择“创建”  。

   ![显示 Azure AD 库中的 Salesforce 应用程序的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

3. 创建应用程序后，在“单一登录”(SSO) 选项卡选择“SAML” 。

   ![显示 SAML 应用程序的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

4. 下载要导入到 Salesforce 的“联合元数据 XML 和证书(原始)”。

   ![显示联合元数据下载位置的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

5. 在 Salesforce 管理控制台上，选择“标识” > “单一登录设置” > “从元数据文件新建”  。

   ![显示 Salesforce 管理控制台的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

6. 上传从 Azure AD 门户下载的 XML 文件。 然后选择“创建”。

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png" alt-text="显示 XML 文件上传位置的屏幕截图。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png":::

7. 上传从 Azure 下载的证书。 然后选择“保存”以在 Salesforce 中创建 SAML 提供程序。

   ![显示如何在 Salesforce 中创建 SAML 提供程序的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

8. 记录以下字段中的值。 稍后将在 Azure 中使用这些值。 
   * **实体 ID**
   * **登录 URL** 
   * **注销 URL** 

   然后选择“下载元数据”。

   ![显示应记录的、以便在 Azure 中使用的值的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

9. 在 Azure AD“企业应用程序”页上的“SAML SSO 设置”中，选择“上传元数据文件”以将该文件上传到 Azure AD 门户 。 在保存之前，请确保导入的值与记录的值匹配。

   ![显示如何在 Azure AD 中上传元数据文件的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

10. 在 Salesforce 管理控制台中，选择“公司设置” > “我的域” 。 转到“身份验证配置”，然后选择“编辑” 。

    ![显示如何编辑公司设置的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

11. 对于登录选项，请选择前面配置的新 SAML 提供程序。 再选择“保存”。

    ![显示 SAML 提供程序选项保存位置的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

12. 在 Azure AD 中的“企业应用程序”页上，选择“用户和组” 。 然后添加测试用户。

    ![显示已添加的测试用户的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

13. 若要测试配置，请以某个测试用户的身份登录。 转到你的 Microsoft [应用库](https://aka.ms/myapps)，然后选择“Salesforce”。

    ![显示如何从应用库打开 Salesforce 的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

14. 选择新配置的标识提供者 (IdP) 以登录。

    ![显示登录位置的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

    如果正确配置了所有设置，则测试用户将会进入 Salesforce 主页。 如需故障排除帮助，请参阅[调试指南](../manage-apps/debug-saml-sso-issues.md)。

16. 在“企业应用程序”页上，将具有正确角色的其余用户分配到 Salesforce 应用程序。

    >[!NOTE]
    >将其余用户添加到 Azure AD 应用程序后，这些用户应测试连接，以确保能够正常访问。 在继续下一步之前测试连接。

17. 在 Salesforce 管理控制台上，选择“公司设置” > “我的域” 。

18. 在“身份验证配置”下，选择“编辑” 。 清除用作身份验证服务的“Okta”对应的选择。

    ![显示在何处清除用作身份验证服务的“Okta”对应的选择的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

现已成功地在 Azure AD 中配置 Salesforce 用于 SSO。

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>将 OIDC/OAuth 2.0 应用程序迁移到 Azure AD

若要将 OpenID Connect (OIDC) 或 OAuth 2.0 应用程序迁移到 Azure AD，请先在 Azure AD 租户中配置应用程序以进行访问。 在此示例中，我们将转换一个自定义 OIDC 应用。

若要完成迁移，请对 Okta 租户中发现的所有应用程序重复以下配置步骤。

1. 在 [Azure AD 门户](https://aad.portal.azure.com)中，选择“Azure Active Directory” > “企业应用程序” 。 在“所有应用程序”下，选择“新建应用程序” 。

2. 选择“创建自己的应用程序”。 在显示的菜单中，为该 OIDC 应用命名，然后选择“注册所用的应用程序以便与 Azure AD 集成”。 然后选择“创建”。

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png" alt-text="显示如何创建 OIDC 应用程序的屏幕截图。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png":::

3. 在下一页上，设置应用程序注册的租户。 有关详细信息，请参阅 [Azure Active Directory 中的租户](../develop/single-and-multi-tenant-apps.md)。

   在此示例中，我们将选择“任何组织目录中的帐户(任何 Azure AD 目录 - 多租户)” > “注册” 。

   ![显示如何选择 Azure AD 目录多租户的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

4. 在“应用注册”页上的“Azure Active Directory”下，打开新建的注册 。

   根据[应用程序方案](../develop/authentication-flows-app-scenarios.md)的不同，可能需要执行多种配置操作。 大多数方案需要应用客户端机密，因此我们将包括这些方案。

5. 在“概述”页上，记录“应用程序(客户端) ID” 。 稍后将在应用程序中使用此 ID。

   ![显示应用程序客户端 ID 的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

6. 在左侧选择“证书和机密”。 然后选择“新建客户端机密”。 为客户端机密命名并设置其过期时间。

   ![显示“新建客户端机密”的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

7. 请记下该机密的值和 ID。

   >[!NOTE]
   >如果丢失客户端机密，将无法检索它， 而是需要重新生成机密。

8. 在左侧选择“API 权限”。 然后向应用程序授予对 OIDC 堆栈的访问权限。

9. 选择“添加权限” > “Microsoft Graph” > “委托的权限”  。

10. 在“OpenId 权限”部分，添加“电子邮件”、“openid”和“配置文件”   。 然后选择“添加权限”。

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png" alt-text="显示在何处添加 OpenID 权限的屏幕截图。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png":::

11. 为了改善用户体验并消除用户同意提示，请选择“为租户域名授予管理员同意”。 然后等待“已授予”状态出现。

    ![显示授予管理员同意的位置的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

12. 如果你的应用程序具有重定向 URI，请输入相应的 URI。 如果回复 URL 依次以“身份验证”选项卡、“添加平台”和“Web”为目标，请输入相应的 URL  。 选择“访问令牌”和“ID 令牌” 。 然后选择“配置”。

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png" alt-text="显示如何配置令牌的屏幕截图。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png":::
    
    如果需要，请在“身份验证”菜单中的“高级设置”和“允许公共客户端流”下，选择“是”   。

    ![显示如何允许公共客户端流的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

13. 在配置了 OIDC 的应用程序中，导入应用程序 ID 和客户端机密，然后进行测试。 按照前面的步骤，使用客户端 ID、机密和范围等设置来配置应用程序。

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>将自定义授权服务器迁移到 Azure AD

Okta 授权服务器一对一地映射到[公开 API](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope) 的应用程序注册。

默认 Okta 授权服务器应映射到 Microsoft Graph 范围或权限。

![显示默认 Okta 授权的屏幕截图。](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>后续步骤 

- [将 Okta 联合迁移到 Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [将 Okta 同步预配迁移到基于 Azure AD Connect 的同步](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [将 Okta 登录策略迁移到 Azure AD 条件访问](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
