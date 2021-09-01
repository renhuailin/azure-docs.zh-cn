---
title: 在 Azure AD 中注册机密客户端应用 - Azure API for FHIR
description: 在 Azure Active Directory 中注册机密客户端应用程序，该应用程序代表用户进行身份验证，并请求访问资源应用程序。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: cavoeg
ms.openlocfilehash: 5f49b73e034e4a38ba6022270a68f3d6c2018a40
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396836"
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

注册应用程序后，必须选择此应用程序应代表用户请求哪些 API 权限。

1. 选择“API 权限”。

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client-api-permissions.png" alt-text="机密客户端。API 权限。":::

1. 选择“添加权限”。

    若使用 Azure API for FHIR，则将通过在“我组织使用的 API”下搜索 Azure Healthcare API 来添加对 Azure Healthcare APIs 的权限。 Azure Healthcare API 的搜索结果仅在[已部署 Azure API for FHIR ](fhir-paas-powershell-quickstart.md)时返回。

    若要引用不同的资源应用程序，请选择之前在“我的 API”下创建的 [FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)。


    :::image type="content" source="media/confidential-client-application/confidential-client-org-api.png" alt-text="机密客户端。我的组织 API" lightbox="media/confidential-client-application/confidential-app-org-api-expanded.png":::
    

1. 选择机密客户端应用程序将代表用户请求的作用域（权限）。 依次选择“user_impersonation”、“添加权限”。 

    :::image type="content" source="media/confidential-client-application/confidential-client-add-permission.png" alt-text="机密客户端。委托的权限":::


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
>[使用 Postman 访问 FHIR 服务](../fhir/using-postman.md)
