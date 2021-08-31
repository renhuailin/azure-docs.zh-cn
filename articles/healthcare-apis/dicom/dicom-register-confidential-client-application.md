---
title: 在 Azure Active Directory 中注册机密客户端应用程序 - 适用于 DICOM 的 Azure Healthcare APIs
description: 本文介绍如何在 Azure Active Directory 中注册机密客户端应用程序。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f5dd5e5136ac2cd47b637d7b95cb7ba95c3ddaaa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778467"
---
# <a name="register-a-confidential-client-application"></a>注册机密客户端应用程序

> [!IMPORTANT]
> Azure Healthcare APIs 目前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本教程介绍如何在 Azure Active Directory (Azure AD) 中注册机密客户端应用程序。

## <a name="register-a-new-application"></a>注册新应用程序

客户端应用程序注册是应用程序的 Azure AD 表示形式，可用于代表用户进行身份验证和请求对资源应用程序的访问权限。 机密客户端应用程序是一种可信任的应用程序，可保存机密，并在请求访问令牌时提供该机密。 机密应用程序的示例包括服务器端应用程序。

若要注册新的机密客户端应用程序，请参阅以下步骤。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”  。
2. 选择“应用注册”边栏选项卡。
3. 选择“新注册”。

   [ ![Azure 应用注册。](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. 输入应用程序的面向用户的显示名称。

   [ ![Azure 注册应用程序。](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. 对于“支持的帐户类型”，选择哪些用户可以使用该应用程序或访问 API。
6. （“可选”）提供“重定向 URI” 。 稍后可更改这些详细信息，但若知道应用程序的回复 URL，请输入。
7. 选择“注册”  。

## <a name="api-permissions"></a>API 权限

注册应用程序后，必须选择此应用程序必须代表用户请求的 API 权限。

1. 选择“API 权限”边栏选项卡。

   [ ![添加 API 权限](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. 选择“添加权限”。

   如果正在使用 Azure Healthcare APIs，则将通过在“我的组织使用的 API”下搜索“适用于 DICOM 的 Azure API”来添加对 DICOM 服务的权限。  

   [ ![搜索 API 权限](media/dicom-search-apis-permissions.png) ](media/dicom-search-apis-permissions.png#lightbox)

   只有在工作区中部署了 DICOM 服务后，适用于 DICOM 的 Azure API 的搜索结果才会返回。

   如果要引用不同的资源应用程序，请选择之前在“我的组织使用的 API”下创建的 DICOM API 资源应用程序注册。

3. 选择机密客户端应用程序将代表用户请求的范围（权限）。 依次选择“user_impersonation”、“添加权限”。 

   [ ![选择权限范围。](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

## <a name="application-secret"></a>应用程序机密

1. 依次选择“证书和机密”、“新建客户端机密”。 

   [ ![证书和机密。](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. 为客户端密码输入“说明”。 选择“过期”下拉菜单，选择过期期限，然后单击“添加”。

   [ ![客户端机密说明。](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. 创建客户端机密字符串后，复制其“值”和“ID”，并将其存储在自己选择的安全位置。

   [ ![复制客户端机密值 ID。](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

   > [!NOTE]
   > 客户端机密字符串仅在 Azure 门户中时可见。 导航离开证书和机密网页，再返回到该网页时，“值”字符串将被过滤。 请务必在生成客户端机密字符串后立即复制该字符串。 如果没有备份客户端机密，必须重复上述步骤以再生成。

## <a name="next-steps"></a>后续步骤

本文指导你按步骤在 Azure AD 中注册机密客户端应用程序。 还指导你按步骤将 API 权限添加到适用于 DICOM 的 Azure API。 最后，介绍了如何创建应用程序机密。 

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)



