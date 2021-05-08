---
title: 如何通过 Azure AD 和 Azure Maps REST API 保护输入受限设备
titleSuffix: Azure Maps
description: 如何配置支持登录到 Azure AD 并调用 Azure Maps REST API 的无浏览器应用程序。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895624"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>通过 Azure AD 和 Azure Maps REST API 保护输入受限设备

本指南讨论如何保护不能安全地存储机密或接受浏览器输入的公共应用程序或设备。 这些类型的应用程序属于 IoT 或物联网的类别。 这些应用程序的一些示例包括：智能电视设备或传感器数据发出应用程序。 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中创建应用程序注册

> [!NOTE]
> * “先决条件读取”：[方案：用于调用 Web API 的桌面应用](../active-directory/develop/scenario-desktop-overview.md)
> * 以下方案使用的设备代码流不涉及 web 浏览器来获取令牌。

在 Azure AD 中创建基于设备的应用程序，以启用 Azure AD 登录。 将向此应用程序授予 Azure Maps REST API 的访问权限。

1. 在 Azure 门户的 Azure 服务列表中，依次选择“Azure Active Directory” > “应用注册” > “新注册”。    

    > [!div class="mx-imgBorder"]
    > ![应用注册](./media/how-to-manage-authentication/app-registration.png)

2. 输入“名称”，选择“仅此组织目录中的帐户”作为“支持的帐户类型”。   在“重定向 URI”中，指定“公共客户端/本机（移动和桌面）”，然后将 `https://login.microsoftonline.com/common/oauth2/nativeclient` 添加到值。  有关详细信息，请参阅 Azure AD [用于调用 Web API 的桌面应用：应用注册](../active-directory/develop/scenario-desktop-app-registration.md)。 然后，“注册”应用程序。

    > [!div class="mx-imgBorder"]
    > ![为名称和重定向 uri 添加应用注册详细信息](./media/azure-maps-authentication/devicecode-app-registration.png)

3. 导航到“身份验证”，并启用“将应用程序视为公共客户端”。  这将启用 Azure AD 的设备代码身份验证。
    
    > [!div class="mx-imgBorder"]
    > ![启用作为公共客户端的应用注册](./media/azure-maps-authentication/devicecode-public-client.png)

4.  若要将委托的 API 权限分配到 Azure Maps，请访问应用程序。 然后依次选择“API 权限” > “添加权限”。  在“我的组织使用的 API”下，搜索并选择“Azure Maps” 。

    > [!div class="mx-imgBorder"]
    > ![添加应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

5. 选中“访问 Azure Maps”旁边的复选框，然后选择“添加权限” 。

    > [!div class="mx-imgBorder"]
    > ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

6. 为用户或组配置 Azure 基于角色的访问控制 (Azure RBAC)。 请参阅 [向用户授予 Azure Maps 的基于角色的访问权限](#grant-role-based-access-for-users-to-azure-maps)。

7. 若要添加用于在应用程序中获取令牌流的代码，有关实现详细信息，请参阅[设备代码流](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow)。 获取令牌时，参考范围：`user_impersonation`（前面的步骤中已选定）。

> [!Tip]
> 使用 Microsoft 身份验证库 (MSAL) 获取访问令牌。 请参阅[调用 Web API 的桌面应用：代码配置](../active-directory/develop/scenario-desktop-app-configuration.md)上的建议

8. 使用从 Azure AD 获取的令牌编写 HTTP 请求，并使用有效的 HTTP 客户端发送请求。

### <a name="sample-request"></a>示例请求
以下用于上传简单地理围栏的示例请求正文表示为使用中心点和半径的圆形几何图形。

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 下面的示例请求正文在 GeoJSON 中：
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>示例响应：

标头：
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

正文：
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>后续步骤

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)