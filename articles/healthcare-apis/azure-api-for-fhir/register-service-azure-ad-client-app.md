---
title: 在 Azure AD 中注册服务应用 - Azure API for FHIR
description: 了解如何在 Azure Active Directory 中注册服务客户端应用程序。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 3eb942160e45c589377ee3ecd283ea2939a23934
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273393"
---
# <a name="register-a-service-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>在 Azure Active Directory 中为 Azure API for FHIR 注册服务客户端应用程序

在本文中，你将了解如何在 Azure Active Directory 中注册服务客户端应用程序。 客户端应用程序注册是可用于对令牌进行身份验证和获取令牌的应用程序的 Azure Active Directory 表示形式。 服务客户端旨在供应用程序用来获取访问令牌，而无需用户进行交互式身份验证。 它将具有某些应用程序权限，在获取访问令牌时将使用应用程序机密（密码）。

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

用户的权限Azure API for FHIR RBAC 进行管理。 有关更多详细信息，请访问 [配置 Azure RBAC for FHIR](configure-azure-rbac.md)。

>[!NOTE]
>尝试使用 grant_type client_credentials 等工具为用户Azure API for FHIR访问令牌时，请使用安全令牌。 有关更多详细信息，请访问[在上测试 FHIR API Azure API for FHIR。](tutorial-web-app-test-postman.md)

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
>[使用 Postman 访问 FHIR 服务](./../use-postman.md)
