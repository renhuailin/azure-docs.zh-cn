---
title: 在 Azure AD 中注册服务应用 - Azure API for FHIR
description: 了解如何在 Azure Active Directory 中注册服务客户端应用程序。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 12df23908d1d5aab18598fcefc574422e233bf19
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299026"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册服务客户端Azure Active Directory

本文将了解如何在 Azure Active Directory 中注册服务客户端应用程序。 客户端应用程序注册Azure Active Directory应用程序的一种表示形式，可用于进行身份验证和获取令牌。 服务客户端旨在由应用程序用来获取访问令牌，而无需用户进行交互式身份验证。 它将具有特定的应用程序权限，并使用应用程序机密 (密码) 获取访问令牌。

按照以下步骤创建新的服务客户端。

## <a name="app-registrations-in-azure-portal"></a>应用注册 Azure 门户

1. 在 [Azure 门户](https://portal.azure.com)中， **导航到** Azure Active Directory 。

2. 选择“应用注册” 。

    ![Azure 门户。 新建应用注册。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 选择“新注册”。

4. 为服务客户端指定显示名称。 服务客户端应用程序通常不使用回复 URL。

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure 门户。新建服务客户端应用注册。":::

5. 选择“注册”。

## <a name="api-permissions"></a>API 权限

注册应用程序后，需要选择此应用程序应能够代表用户请求的 API 权限：

1. 选择“API 权限”。
1. 选择“添加权限”。

    如果使用的是 Azure API for FHIR，则通过搜索我的组织使用的 API 下的 **Azure 医疗保健** API，向 Azure 医疗保健 **API 添加权限**。 

    如果要引用不同的资源应用程序，请选择之前在"我的 [API"](register-resource-azure-ad-client-app.md) 下创建的 FHIR API **资源应用程序注册**。

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="机密客户端。我的组织 API" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. 选择 (权限) 机密应用程序应能够代表用户请求的权限：

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="服务客户端。委托的权限":::

1. 向应用程序授予许可。 如果没有所需的权限，请与管理员联系Azure Active Directory管理员：

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="服务客户端。授予同意":::

## <a name="application-secret"></a>应用程序密码

服务客户端需要机密 (密码) 才能获取令牌。

1. 选择“证书和机密”。
2. 选择“新建客户端机密”。

    ![Azure 门户。 服务客户端机密](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. 提供机密说明和 (1 年、2 年或永不) 。

4. 生成机密后，它只会在门户中显示一次。 记下它，并安全地存储在 中。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Active Directory 中注册服务客户端应用程序。 接下来，使用 Postman 测试对 FHIR 服务器的访问。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 Azure API for FHIR](access-fhir-postman-tutorial.md)
