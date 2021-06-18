---
title: 在 Azure AD 中注册公共客户端应用 - Azure API for FHIR
description: 本文介绍如何在 Azure Active Directory 注册公共客户端应用程序，以准备在 Azure 中部署 FHIR API。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 559803a45fc42129d55739ec1d1feba20de41536
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299134"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册公共客户端应用程序

本文将了解如何在 Azure Active Directory 中注册公共应用程序。  

客户端应用程序注册Azure Active Directory应用程序的一种表示形式，可以代表用户进行身份验证和请求 API 权限。 公共客户端是移动应用程序和单页 JavaScript 应用程序等无法保密机密的应用程序。 此过程 [类似于注册机密](register-confidential-azure-ad-client-app.md)客户端，但由于公共客户端不能信任来保存应用程序机密，因此无需添加机密。

本快速入门提供有关如何将应用程序注册到 [Microsoft 标识平台 的常规信息](../../active-directory/develop/quickstart-register-app.md)。

## <a name="app-registrations-in-azure-portal"></a>应用注册 Azure 门户

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，单击“Azure Active Directory”。

2. 在"Azure Active Directory"边栏选项卡 **中，单击** 应用注册： 

    ![Azure 门户。 新建应用注册。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 单击"**新建注册"。**

## <a name="application-registration-overview"></a>应用程序注册概述

1. 为应用程序指定显示名称。

2. 提供回复 URL。 回复 URL 是身份验证代码将返回到客户端应用程序的地方。 可以添加更多回复 URL，并稍后编辑现有 URL。

    ![Azure 门户。 新的公共应用注册。](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)


将桌面[、移动](../../active-directory/develop/scenario-desktop-app-registration.md)[或](../../active-directory/develop/scenario-mobile-app-registration.md)[单页应用程序](../../active-directory/develop/scenario-spa-app-registration.md)配置为公共应用程序：

1. 在Azure 门户 [中](https://portal.azure.com)，应用注册，选择应用，然后选择"身份验证 **"。**

2. 选择 **"高级设置**  >  **""默认客户端类型"。** 对于 **"将应用程序视为公共客户端"，** 请选择"**是"。**

3. 对于单页应用程序，请选择"**访问令牌****和 ID 令牌**"以启用隐式流。

   - 如果你的应用程序登录了用户，请选择“ID 令牌”。
   - 如果你的应用程序也需调用受保护的 Web API，请选择“访问令牌”。

## <a name="api-permissions"></a>API 权限

与 [机密客户端应用程序](register-confidential-azure-ad-client-app.md)类似，你需要选择此应用程序应代表用户请求的 API 权限：

1. 打开 **API 权限**。

    如果使用的是 Azure API for FHIR，则通过搜索我的组织使用的 API 下的 Azure 医疗保健 API，向 Azure 医疗保健 **API 添加权限**。 只有在已部署 Azure API for FHIR 时[才能找到Azure API for FHIR。](fhir-paas-powershell-quickstart.md)

    
    如果要引用不同的资源应用程序，请选择之前在"我的 [API"](register-resource-azure-ad-client-app.md) 下创建的 FHIR API **资源应用程序注册**：

    ![Azure 门户。 新的公共 API 权限 - Azure API for FHIR默认值](media/public-client-app/api-permissions.png)


2. 选择希望应用程序能够请求的权限 ![ ：Azure 门户。 应用权限](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>验证 FHIR 服务器颁发机构
如果本文中注册的应用程序和 FHIR 服务器在同一Azure AD租户中，则你可以继续执行以下步骤。

如果在 FHIR 服务器Azure AD租户中配置客户端应用程序，则需要更新 **颁发机构**。 在Azure API for FHIR中，在"设置"--"身份验证"下>颁发机构。 将"颁发机构"设置为 **https://login.microsoftonline.com/\<TENANT-ID>** 。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Active Directory 中注册公共客户端应用程序。 接下来，使用 Postman 测试对 FHIR 服务器的访问。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 Azure API for FHIR](access-fhir-postman-tutorial.md)