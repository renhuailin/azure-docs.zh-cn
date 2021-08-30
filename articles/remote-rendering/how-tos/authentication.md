---
title: 身份验证
description: 说明身份验证的工作原理
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 01b2dbaa8ed318f08fd68660078ae6fb9923221d
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112969723"
---
# <a name="configure-authentication"></a>配置身份验证

Azure 远程渲染使用与 [Azure 空间定位点 (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp) 相同的身份验证机制。 客户端需要设置以下项之一才能成功调用 REST API：

* AccountKey：可以在 Azure 门户上远程渲染帐户的“密钥”选项卡中获得。 建议仅将帐户密钥用于开发/原型制作。
    ![帐户 ID](./media/azure-account-primary-key.png)

* AccountDomain：可以在 Azure 门户上远程渲染帐户的“概述”选项卡中获得。
    ![帐户域](./media/azure-account-domain.png)

* AuthenticationToken：Azure AD 令牌，可使用 [MSAL 库](../../active-directory/develop/msal-overview.md)获取。 可以通过多个不同的流来接受用户凭据并使用这些凭据获取访问令牌。

* MRAccessToken：MR 令牌，可从 Azure 混合现实安全令牌服务 (STS) 中获取。 使用类似以下的 REST 调用从 `https://sts.<accountDomain>` 终结点检索：

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    其中的 Authorization 标头的格式如下所示：`Bearer <Azure_AD_token>` 或 `Bearer <accoundId>:<accountKey>`。 从安全角度来看，前者更可取。 从此 REST 调用返回的令牌为 MR 访问令牌。

## <a name="authentication-for-deployed-applications"></a>已部署应用程序的身份验证

建议仅在开发期间使用帐户密钥进行快速原型制作。 建议不要使用应用程序中的嵌入式帐户密钥将应用程序交付到生产环境。 建议的方法是使用基于用户或基于服务的 Azure AD 身份验证方法。

### <a name="azure-ad-user-authentication"></a>Azure AD 用户身份验证

[Azure 空间定位点文档](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication)中介绍了 Azure AD 身份验证。

按照以下步骤在 Azure 门户内配置 Azure Active Directory 用户身份验证。

1. 在 Azure Active Directory 中注册应用程序。 在注册过程中，需要确定应用程序是否应为多租户应用程序。 还需要在“身份验证”边栏选项卡中提供应用程序允许的重定向 URL。
:::image type="content" source="./media/azure-active-directory-app-setup.png" alt-text="身份验证设置":::

1. 在“API 权限”选项卡中，在“mixedreality”下请求“mixedreality.signin”范围的“委托权限”  。
:::image type="content" source="./media/azure-active-directory-app-api-permissions.png" alt-text="API 权限":::

1. 在“安全性”>“权限”选项卡中授予管理员同意。:::image type="content" source="./media/azure-active-directory-grant-admin-consent.png" alt-text="管理员同意":::

1. 然后，导航到 Azure 远程渲染资源。 在“访问控制”面板中，为应用程序和用户授予所需的[角色](#azure-role-based-access-control)，代表他们使用对 Azure 远程渲染资源的委托访问权限。
:::image type="content" source="./media/azure-remote-rendering-add-role-assignment.png" alt-text="添加权限":::
:::image type="content" source="./media/azure-remote-rendering-role-assignments.png" alt-text="角色分配":::

有关在应用程序代码中使用 Azure AD 用户身份验证的信息，请参阅[教程：保护 Azure 远程渲染和模型存储 - Azure Active Directory 身份验证](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

请在授予基于角色的访问权限时使用以下角色，这有助于控制授予服务的访问级别：

* **远程渲染管理员**：为用户提供 Azure 远程渲染的转换、管理会话、渲染和诊断功能。
* **远程渲染客户端**：为用户提供 Azure 远程渲染的管理会话、渲染和诊断功能。

## <a name="next-steps"></a>后续步骤

* [创建帐户](create-an-account.md)
* [使用 Azure 前端 API 进行身份验证](frontend-apis.md)
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
