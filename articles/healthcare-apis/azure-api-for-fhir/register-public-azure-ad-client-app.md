---
title: 在 Azure AD 中注册公共客户端应用 - Azure API for FHIR
description: 本文介绍如何在 Azure Active Directory 中注册公共客户端应用程序，以准备好在 Azure 中部署 FHIR API。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: cavoeg
ms.openlocfilehash: 5b089e1c1c6d5a5b621fb50fda9e975e2320ea7e
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396735"
---
# <a name="register-a-public-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>在 Azure Active Directory 中为 Azure API for FHIR 注册公共客户端应用程序

本文介绍如何在 Azure Active Directory 中注册公共应用程序。  

客户端应用程序注册是可以代表用户完成身份验证和请求 API 权限的应用程序的 Azure Active Directory 表示形式。 公共客户端是无法将机密保密的应用程序，例如移动应用程序和单页 JavaScript 应用程序。 此过程类似于[注册机密客户端](register-confidential-azure-ad-client-app.md)，但由于不信任公共客户端能够保守应用程序的机密，因此无需添加此类客户端。

本快速入门提供有关如何[使用 Microsoft 标识平台注册应用程序](../../active-directory/develop/quickstart-register-app.md)的一般信息。

## <a name="app-registrations-in-azure-portal"></a>Azure 门户中的应用注册

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，单击“Azure Active Directory”。

2. 在“Azure Active Directory”边栏选项卡中，单击“应用注册” ：

    ![Azure 门户。 新建应用注册。](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. 单击“新建注册”。

## <a name="application-registration-overview"></a>应用程序注册概述

1. 指定应用程序的显示名称。

2. 提供回复 URL。 将在回复 URL 中向客户端应用程序返回身份验证代码。 以后可以添加更多的回复 URL 以及编辑现有的回复 URL。

    ![Azure 门户。 新建公共应用注册。](media/add-azure-active-directory/portal-aad-register-new-app-registration-pub-client-name.png)


若要将[桌面](../../active-directory/develop/scenario-desktop-app-registration.md)、[移动](../../active-directory/develop/scenario-mobile-app-registration.md)或[单页](../../active-directory/develop/scenario-spa-app-registration.md)应用程序配置为公共应用程序，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)上的“应用注册”中选择你的应用，然后选择“身份验证” 。

2. 选择“高级设置” > “默认客户端类型” 。 对于“将应用程序视为公共客户端”，请选择“是” 。

3. 对于单页应用程序，请选择“访问令牌”和“ID 令牌”以启用隐式流 。

   - 如果你的应用程序登录了用户，请选择“ID 令牌”。
   - 如果你的应用程序也需调用受保护的 Web API，请选择“访问令牌”。

## <a name="api-permissions"></a>API 权限

与[机密客户端应用程序](register-confidential-azure-ad-client-app.md)一样，需要选择此应用程序应该能够代表用户请求哪些 API 权限：

1. 打开“API 权限”。

    如果使用 Azure API for FHIR，可以通过在“我的组织使用的 API”下搜索“Azure Healthcare APIs”来添加对 Azure Healthcare APIs 的权限。 仅当已[部署 Azure API for FHIR](fhir-paas-powershell-quickstart.md) 时，才能找到此项。

    
    如果引用其他资源应用程序，请在“我的 API”下选择以前创建的 [FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)：

    ![Azure 门户。 新建公共 API 权限 - Azure API for FHIR 默认值](media/public-client-application/api-permissions.png)


2. 选择希望应用程序能够请求的权限：![Azure 门户。 应用权限](media/public-client-application/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>验证 FHIR 服务器颁发机构
如果在本文中注册的应用程序和你的 FHIR 服务器位于同一 Azure AD 租户中，则你可以继续执行后续步骤。

如果在不包含你的 FHIR 服务器的其他 Azure AD 租户中配置客户端应用程序，则需要更新颁发机构。 在 Azure API for FHIR 中的“设置”->“身份验证”下设置颁发机构。 将颁发机构设置为“https://login.microsoftonline.com/\<TENANT-ID>”。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何在 Azure Active Directory 中注册公共客户端应用程序。 接下来，请使用 Postman 测试对 FHIR 服务器的访问。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR 服务](../fhir/using-postman.md)