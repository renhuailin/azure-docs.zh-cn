---
title: 在 Azure Active Directory 中注册公共客户端应用程序 - 适用于 DICOM 的 Azure Healthcare APIs
description: 本文介绍如何在 Azure Active Directory 中注册公共应用程序。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f8a720508221d1a9a3308d64649863d11c6b57c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778464"
---
# <a name="register-a-public-client-application"></a>注册公共客户端应用程序

> [!IMPORTANT]
> Azure Healthcare APIs 目前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文介绍如何在 Azure Active Directory (Azure AD) 中注册公共应用程序。

客户端应用程序注册是可代表用户进行身份验证和请求 API 权限的应用程序的 Azure AD 表示形式。 公共客户端是移动应用程序和单页 JavaScript 应用程序等无法保密机密的应用程序。 此过程类似于[注册机密客户端应用程序](dicom-register-confidential-client-application.md)，但由于不能信任公共客户端保存应用程序机密，因此无需添加。

本快速入门指南提供有关如何[使用 Microsoft 标识平台注册应用程序](.././../active-directory/develop/quickstart-register-app.md)的常规信息。

## <a name="application-registrations-in-the-azure-portal"></a>Azure 门户中的应用程序注册

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”  。
2. 在“Azure Active Directory”边栏选项卡中，选择“应用注册”。
3. 选择“新注册”。

   [ ![Azure 门户中的 DICOM 应用程序注册。](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

## <a name="application-registration-overview"></a>应用程序注册概述

1. 为 DICOM 服务应用程序提供面向用户的显示名称。

   [ ![Azure 注册应用程序。](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

2. 对于“支持的帐户类型”，选择哪些用户可以使用该应用程序或访问 API。
3. （“可选”）提供“重定向 URI” 。 稍后可更改这些详细信息，但若知道应用程序的回复 URL，请输入。
4. 选择“注册”  。

## <a name="configuring-an-application-as-a-public-application"></a>将应用程序配置为公共应用程序

若要将[桌面](.././../active-directory/develop/scenario-desktop-app-registration.md)、[移动](.././../active-directory/develop/scenario-mobile-app-registration.md)或[单页](.././../active-directory/develop/scenario-mobile-app-registration.md)应用程序配置为公共应用程序，请参阅以下步骤。

1. 在 [Azure 门户](https://portal.azure.com)中，依次选择“应用注册”边栏选项卡、“身份验证”边栏选项卡 。

   [ ![Azure 公共应用程序注册。](media/dicom-authentication.png) ](media/dicom-authentication.png#lightbox)

2. 在“高级设置”中，选择“是”。

   对于“单页应用程序”，选择“访问令牌”和“ID 令牌”，以启用隐式流 。
   * 如果你的应用程序登录了用户，请选择“ID 令牌”。
   * 如果你的应用程序也需调用受保护的 Web API，请选择“访问令牌”。

3. 选择“保存”。

## <a name="api-permissions"></a>API 权限

与[机密客户端应用程序](dicom-register-confidential-client-application.md)类似，需要选择此应用程序必须代表用户请求哪些 API 权限。

如果正在使用 DICOM 服务，则将通过在我的组织使用的 API 下搜索 Azure Healthcare APIs 来添加对适用于 DICOM 的 Azure API 的权限。 这只有在已注册 Microsoft.HealthcareAPIs 资源提供程序时，才能找到。

[![我的 API 权限。](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)

选择希望 DICOM 服务应用程序请求的权限。

[ ![请求 API 权限。](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

## <a name="next-steps"></a>后续步骤

本文介绍如何在 Azure Active Directory 中注册公共客户端应用程序。  

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)










