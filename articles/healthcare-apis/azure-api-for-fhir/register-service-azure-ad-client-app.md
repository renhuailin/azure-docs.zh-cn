---
title: 在 Azure AD 中注册服务应用 - Azure API for FHIR
description: 了解如何在 Azure Active Directory 中注册服务客户端应用程序。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: cavoeg
ms.openlocfilehash: 84e767c91f2a48940aeed501bcec7e6739fd6772
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397112"
---
# <a name="register-a-service-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>在 Azure Active Directory 中为 Azure API for FHIR 注册服务客户端应用程序

在本文中，你将了解如何在 Azure Active Directory 中注册服务客户端应用程序。 客户端应用程序注册是可用于对令牌进行身份验证和获取令牌的应用程序的 Azure Active Directory 表示形式。 服务客户端旨在供应用程序用来获取访问令牌，无需用户进行交互式身份验证。 它将具有某些应用程序权限，在获取访问令牌时将使用应用程序机密（密码）。

按照下列步骤创建新的服务客户端。

## <a name="app-registrations-in-azure-portal"></a>Azure 门户中的应用注册

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“Azure Active Directory”。

2. 选择“应用注册”。

    ![Azure 门户。 新应用注册](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. 选择“新注册”。

4. 为服务客户端指定一个显示名称。 服务客户端应用程序通常不使用回复 URL。

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure 门户。新服务客户端应用注册。":::

5. 选择“注册”。

## <a name="api-permissions"></a>API 权限

现在，你已注册了应用程序，接下来需要选择此应用程序应当能够代表用户请求哪些 API 权限：

1. 选择“API 权限”。
1. 选择“添加权限”。

    如果你使用 Azure API for FHIR，则需通过在“我的组织使用的 API”下搜索“Azure Healthcare APIs”来添加对 Azure Healthcare APIs 的权限。 

    如果引用其他资源应用程序，请在“我的 API”下选择以前创建的 [FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)。

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="机密客户端。我的组织 API" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. 选择机密应用程序应当能够代表用户请求的作用域（权限）：

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="服务客户端。委托的权限":::

1. 向应用程序授予同意。 如果你没有所需的权限，请联系 Azure Active Directory 管理员：

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="服务客户端。授予同意":::

## <a name="application-secret"></a>应用程序机密

服务客户端需要使用机密（密码）来获取令牌。

1. 选择“证书和机密”。
2. 选择“新建客户端机密”。

    ![Azure 门户。 服务客户端机密](media/add-azure-active-directory/portal-aad-register-new-app-registration-service-client-secret.png)

3. 提供机密的说明和持续时间（1 年、2 年或永不到期）。

4. 生成机密后，它只会在门户中显示一次。 请记下它，并安全地存储它。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何在 Azure Active Directory 中注册服务客户端应用程序。 接下来，请使用 Postman 测试对 FHIR 服务器的访问。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR 服务](../fhir/using-postman.md)
