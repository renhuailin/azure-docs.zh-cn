---
title: 如何使用用户登录保护单页应用程序
titleSuffix: Azure Maps
description: 如何配置使用 Azure Maps Web SDK 支持 Azure AD 单一登录的单页应用程序。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 60d8dc45fb26ea210b1827a6938716474faa0304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895607"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>使用用户登录保护单页应用程序

以下指南适用于内容服务器上托管的应用程序或对 Web 服务器依赖性最小的应用程序。 应用程序提供仅对 Azure AD 用户而言安全的受保护资源。 此方案的目标是使 Web 应用能够对 Azure AD 进行身份验证，并代表用户调用 Azure Maps REST API。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中创建应用程序注册

在 Azure AD 中创建 Web 应用，以便用户登录。 Web 应用程序将用户访问权限委托给 Azure Maps REST API。

1. 在 Azure 门户的 Azure 服务列表中，依次选择“Azure Active Directory” > “应用注册” > “新注册”  。  

    > [!div class="mx-imgBorder"]
    > ![应用注册](./media/how-to-manage-authentication/app-registration.png)

2. 输入“名称”，选择“支持帐户类型”，提供重定向 URI，它表示 Azure AD 将颁发令牌的 url，是用于托管地图控件的 url 。 有关详细示例，请参阅 [Azure Maps Azure AD 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)。 然后选择“注册”。  

3. 若要将委托的 API 权限分配给 Azure Maps，请访问应用程序。 然后在“应用注册”下，选择“API 权限” > “添加权限”  。 在“我的组织使用的 API”下，搜索并选择“Azure Maps” 。

    > [!div class="mx-imgBorder"]
    > ![添加应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

4. 选中“访问 Azure Maps”旁边的复选框，然后选择“添加权限” 。

    > [!div class="mx-imgBorder"]
    > ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

5. 启用 `oauth2AllowImplicitFlow`。 若要启用它，请在应用注册的“清单”部分，将 `oauth2AllowImplicitFlow` 设为 `true`。

6. 复制应用注册中的 Azure AD 应用程序 ID 和 Azure AD 租户 ID，以在 Web SDK 中使用。 将 Azure AD 应用注册详细信息和 `x-ms-client-id` 从 Azure Map 帐户添加到 Web SDK。

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. 为用户或组配置 Azure 基于角色的访问控制 (Azure RBAC)。 请参阅[以下部分，了解如何启用 Azure RBAC](#grant-role-based-access-for-users-to-azure-maps)。
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>后续步骤

进一步了解单页应用程序场景：
> [!div class="nextstepaction"]
> [单页应用程序](../active-directory/develop/scenario-spa-overview.md)

查找 Azure Maps 帐户的 API 使用指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示如何将 Azure AD 与 Azure Maps 集成的示例：
> [!div class="nextstepaction"]
> [Azure Maps 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)