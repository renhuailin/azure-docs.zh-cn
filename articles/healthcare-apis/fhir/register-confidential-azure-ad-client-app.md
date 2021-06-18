---
title: 在 Azure AD 中注册机密客户端应用 - Azure API for FHIR
description: 在应用程序中注册Azure Active Directory应用程序，该应用程序代表用户进行身份验证并请求访问资源应用程序。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: cavoeg
ms.openlocfilehash: 20d2e86786c3586cd4f45e98d670d4a937a77992
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299188"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册机密客户端应用程序

本教程介绍如何在应用程序中注册机密客户端Azure Active Directory (Azure AD) 。  

客户端应用程序注册Azure AD应用程序的一种主要表示形式，可用于代表用户进行身份验证并请求 [访问资源应用程序](register-resource-azure-ad-client-app.md)。 机密客户端应用程序是一个应用程序，可以信任该应用程序来保存机密，在请求访问令牌时提供该机密。 机密应用程序的示例包括服务器端应用程序。 

若要注册新的机密客户端应用程序，请参阅以下步骤。 

## <a name="register-a-new-application"></a>注册新应用程序

1. 在“Azure 门户”中，选择“Azure Active Directory”。

1. 选择“应用注册” 。 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure 门户。新建应用注册。":::

1. 选择“新注册”。

1. 为应用程序指定面向用户的显示名称。

1. 对于 **"支持的帐户类型**"，选择可以使用应用程序或访问 API 的用户。

1.  (可选) 提供 **重定向 URI。** 稍后可以更改这些详细信息，但如果知道应用程序的回复 URL，请现在输入它。

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="新的机密客户端应用注册。":::

1. 选择“注册”。

## <a name="api-permissions"></a>API 权限

注册应用程序后，必须选择此应用程序应代表用户请求的 API 权限。

1. 选择“API 权限”。

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="机密客户端。API 权限。":::

1. 选择“添加权限”。

    如果使用的是 azure 医疗保健 API，Azure API for FHIR组织使用的 API 下搜索 **Azure 医疗保健 API，** 以向 Azure 医疗保健 API **添加权限**。 Azure 医疗保健 API 的搜索结果仅在已部署 azure 医疗保健 API 时[Azure API for FHIR。](fhir-paas-powershell-quickstart.md)

    如果要引用不同的资源应用程序，请选择之前在"我的 API"下创建的 [FHIR API](register-resource-azure-ad-client-app.md) **资源应用程序注册**。


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="机密客户端。我的组织 API" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. 选择 (范围) 机密客户端应用程序将代表用户请求的权限。 依次选择“user_impersonation”、“添加权限”。 

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="机密客户端。委托的权限":::


## <a name="application-secret"></a>应用程序密码

1. 依次选择“证书和机密”、“新建客户端机密”。  

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="机密客户端。应用程序机密。":::

1. 为客户端密码输入“说明”。 选择"**过期"** 下拉菜单以选择过期时间范围，然后单击"添加 **"。**

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="添加客户端机密。":::

1. 创建客户端机密字符串后，复制其 **"值** "和 **"ID"，** 将其存储在你选择的安全位置。

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="客户端机密字符串。"::: 

> [!NOTE]
>客户端机密字符串仅在客户端密钥Azure 门户。 当你离开"证书"&机密网页，然后返回到该网页时，"值"字符串将屏蔽。 请务必在生成客户端机密字符串后立即复制该字符串。 如果没有客户端机密的备份副本，则必须重复上述步骤以重新生成它。
 
## <a name="next-steps"></a>后续步骤

本文指导你完成如何在应用程序中注册机密客户端应用程序Azure AD。 还指导你完成如何将 API 权限添加到 Azure 医疗保健 API 的步骤。 最后，你已了解如何创建应用程序机密。 此外，还可以了解如何使用 Postman 访问 FHIR 服务器。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 Azure API for FHIR](access-fhir-postman-tutorial.md)
