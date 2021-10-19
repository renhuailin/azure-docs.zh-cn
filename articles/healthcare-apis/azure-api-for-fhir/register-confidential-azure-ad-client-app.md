---
title: 在 Azure AD 中注册机密客户端应用 - Azure API for FHIR
description: 在 Azure Active Directory 中注册机密客户端应用程序，该应用程序代表用户进行身份验证，并请求访问资源应用程序。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 2851f230a19bfbafb514e9fb22fd3f37de1f610b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272540"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>在 Azure Active Directory 中为 Azure API for FHIR 注册机密客户端应用程序

本教程介绍如何在 Azure Active Directory (Azure AD) 中注册机密客户端应用程序。  

客户端应用程序注册是应用程序的一种 Azure AD 表示形式，可用于代表用户进行身份验证并请求访问[资源应用程序](register-resource-azure-ad-client-app.md)。 机密客户端应用程序是一种可信任的应用程序，可保存机密，并在请求访问令牌时提供该机密。 机密应用程序的示例包括服务器端应用程序。 

若要注册新的机密客户端应用程序，请参阅以下步骤。 

## <a name="register-a-new-application"></a>注册新应用程序

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”  。

1. 选择“应用注册”。 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-new-app-registration.png" alt-text="Azure 门户。新应用注册。":::

1. 选择“新注册”。

1. 为应用程序指定面向用户的显示名称。

1. 对于支持的帐户类型，选择哪些用户可以使用该应用程序或访问 API。

1. （可选）提供“重定向 URI”。 这些详细信息可在稍后更改，但若知道应用程序的回复 URL，请现在输入。

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client.png" alt-text="新机密客户端应用注册。":::

1. 选择“注册”。

## <a name="api-permissions"></a>API 权限

用户的权限Azure API for FHIR RBAC 进行管理。 有关更多详细信息，请访问 [配置 Azure RBAC for FHIR](configure-azure-rbac.md)。

>[!NOTE]
>尝试使用 grant_type client_credentials 等工具为用户Azure API for FHIR访问令牌时，请使用安全令牌。 有关更多详细信息，请访问[在上测试 FHIR API Azure API for FHIR。](tutorial-web-app-test-postman.md)


## <a name="application-secret"></a>应用程序机密

1. 依次选择“证书和机密”、“新建客户端机密”。  

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client-secret.png" alt-text="机密客户端。应用程序机密。":::

1. 为客户端密码输入“说明”。 选定“过期”下拉菜单，选择过期期限，然后单击“添加”。

   :::image type="content" source="media/add-azure-active-directory/add-a-client-secret.png" alt-text="添加客户端机密。":::

1. 创建客户端机密字符串后，复制其值和 ID 并存储在所选择的安全位置。

   :::image type="content" source="media/add-azure-active-directory/client-secret-string-password.png" alt-text="客户端机密字符串。"::: 

> [!NOTE]
>客户端机密字符串在 Azure 门户中仅显示一次。 导航离开“证书和机密”网页后再返回时，“值”字符串将屏蔽。 请务必在生成客户端机密字符串后立即复制该字符串。 若没有客户端机密的备份副本，必须重复上述步骤以重新生成。
 
## <a name="next-steps"></a>后续步骤

本文向你说明在 Azure AD 中注册机密客户端应用程序的步骤， 以及将 API 权限添加到 Azure Healthcare API 的步骤。 最后，向你展示如何创建应用程序机密。 此外，还能了解如何使用 Postman 访问 FHIR 服务器。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR 服务](./../use-postman.md)
