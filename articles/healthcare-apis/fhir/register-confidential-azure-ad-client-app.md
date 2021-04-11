---
title: 在 Azure AD 中注册机密客户端应用-Azure API for FHIR
description: 在 Azure Active Directory 中注册机密客户端应用程序，该应用程序代表用户进行身份验证，并请求对资源应用程序的访问权限。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284421"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册机密客户端应用程序

在本教程中，你将了解如何在 Azure Active Directory (Azure AD) 中注册机密客户端应用程序。  

客户端应用程序注册是应用程序的 Azure AD 表示形式，可用于代表用户进行身份验证并请求对 [资源应用程序](register-resource-azure-ad-client-app.md)的访问权限。 机密客户端应用程序是一种可信任的应用程序，用于在请求访问令牌时保存机密并提供机密。 机密应用程序的示例包括服务器端应用程序。 

若要注册新的机密客户端应用程序，请参阅以下步骤。 

## <a name="register-a-new-application"></a>注册新应用程序

1. 在[Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”。

1. 选择“应用注册” 。 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure 门户。新应用注册。":::

1. 选择“新注册”。

1. 为应用程序指定面向用户的显示名称。

1. 对于 " **支持的帐户类型**"，请选择可使用应用程序的用户或访问 API。

1.  (可选) 提供 **重定向 URI**。 稍后可以更改这些详细信息，但如果你知道应用程序的回复 URL，请立即输入。

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="新的机密客户端应用注册。":::

1. 选择“注册”。

## <a name="api-permissions"></a>API 权限

现在，你已注册应用程序，你必须选择此应用程序应代表用户请求哪些 API 权限。

1. 选择“API 权限”。

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="机密客户端。API 权限。":::

1. 选择“添加权限”。

    如果你使用的是用于 FHIR 的 Azure API，则需要通过在 **我的组织使用的 api** 下搜索 **azure 医疗保健 Api** 来添加 azure 医疗保健 api 的权限。 仅当已 [部署用于 FHIR 的 AZURE API](fhir-paas-powershell-quickstart.md)时，AZURE 医疗保健 API 的搜索结果才会返回。

    如果引用的是其他资源应用程序，请选择之前在 **"我的 api**" 下创建的 [FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)。


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="机密客户端。我的组织 Api" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. 选择 "作用域" ("机密" 客户端应用程序将代表用户请求) 权限。 依次选择“user_impersonation”、“添加权限”。 

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="机密客户端。委托的权限":::


## <a name="application-secret"></a>应用程序密码

1. 依次选择“证书和机密”、“新建客户端机密”。  

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="机密客户端。应用程序机密。":::

1. 为客户端密码输入“说明”。 选择 " **过期** " 下拉菜单选择过期时间框架，然后单击 " **添加**"。

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="添加客户端密码。":::

1. 创建客户端密码字符串后，复制其 **值** 和 **ID**，并将其存储在所选的安全位置。

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="客户端密码字符串。"::: 

> [!NOTE]
>在 Azure 门户中，客户端密码字符串只会出现一次。 当你导航到 "证书 & 机密" 网页并返回到它时，值字符串将变为 "已屏蔽"。 必须在生成客户端机密字符串后立即将其复制，这一点很重要。 如果你没有客户端密钥的备份副本，则必须重复上述步骤以重新生成它。
 
## <a name="next-steps"></a>后续步骤

本文将指导你完成如何在 Azure AD 中注册机密客户端应用程序的步骤。 还将指导你完成如何向 Azure 医疗保健 API 添加 API 权限的步骤。 最后，演示了如何创建应用程序机密。 此外，还可以了解如何使用 Postman 访问 FHIR 服务器。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 Azure API for FHIR](access-fhir-postman-tutorial.md)
