---
title: 在 Azure Active Directory 中注册服务客户端应用程序 - 适用于 DICOM 的 Azure Healthcare APIs
description: 本文介绍如何在 Azure Active Directory 中注册服务客户端应用程序。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: 8408d1e2e670083f1e5bb1c30723a3c4ad83b8f2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778456"
---
# <a name="register-a-service-client-application"></a>注册服务客户端应用程序

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文介绍如何在 Azure Active Directory (Azure AD) 中注册服务客户端应用程序。

## <a name="application-registrations-in-the-azure-portal"></a>Azure 门户中的应用程序注册

客户端应用程序注册是可用于对令牌进行身份验证和获取令牌的应用程序的 Azure AD 表示形式。 服务客户端旨在供应用程序用来获取访问令牌，而无需用户进行交互式身份验证。 它将具有某些应用程序权限，且获取访问令牌时使用应用程序机密（密码）。

请参阅下面的步骤，创建新的服务客户端。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”  。
2. 在“Azure Active Directory”边栏选项卡中，选择“应用注册”。
3. 选择“新注册”。

   [ ![Azure 应用注册。](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. 为 DICOM 服务应用程序提供面向用户的显示名称。 服务客户端应用程序通常不使用回复 URL。

   [ ![Azure 注册应用程序。](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. 选择“注册”  。

## <a name="api-permissions"></a>API 权限

注册 DICOM 服务应用程序后，需要选择此应用程序必须代表用户请求的 API 权限。

1. 选择“API 权限”边栏选项卡。

   [ ![添加 API 权限。](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. 选择“添加权限”。

   如果正在使用 DICOM，则将通过在“我的组织使用的 API”下搜索“Azure Healthcare APIs”来添加对适用于 DICOM 的 Azure API 的权限。 这只有在已注册 `Microsoft.HealthcareAPIs` 资源提供程序时才能找到。

   [ ![我的 API 权限。](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)


   [ ![请求 API 权限。](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

3. 选择机密客户端应用程序将代表用户请求的范围（权限）。

   [ ![选择权限范围。](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

4. 显式授予对应用程序的同意。 如果没有所需的权限，请联系 ADD 管理员。


## <a name="application-secret"></a>应用程序机密

服务客户端需要密码才能获取令牌。

1. 依次选择“证书和机密”、“新建客户端机密”。 

   [ ![证书和机密。](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. 为客户端密码输入“说明”。 选择“过期”下拉菜单，选择过期期限，然后单击“添加”。

   [ ![客户端密码说明。](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. 创建客户端密码字符串后，复制其“值”和“ID”，并将其存储在自己选择的安全位置。

   [ ![客户端密码值 ID。](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

4. 选择“添加”。

> [!NOTE]
> 客户端密码字符串仅在 Azure 门户中时可见。 导航离开“证书和机密”网页，再返回到该网页时，“值”字符串将被屏蔽。 请务必在生成客户端密码字符串后立即复制该字符串。 如果没有备份客户端密码，必须重复上述步骤，再生成该密码。


## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure AD 中注册服务客户端应用程序。 

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)




