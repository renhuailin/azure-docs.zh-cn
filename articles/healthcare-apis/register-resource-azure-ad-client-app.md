---
title: 在 Azure AD 中注册资源应用 - Azure API for FHIR
description: 在 Azure Active Directory 中注册资源（或 API）应用，使客户端应用程序能够在身份验证时请求对资源的访问权限。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024476"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册资源应用程序

本文介绍如何在 Azure Active Directory 中注册资源（或 API）应用程序。 资源应用程序是 FHIR 服务器 API 本身的 Azure Active Directory 表示形式，客户端应用程序在身份验证时可以请求对资源的访问权限。 在 OAuth 行话中，资源应用程序也称为“受众”。 

## <a name="azure-api-for-fhir"></a>适用于 FHIR 的 Azure API

如果使用 Azure API for FHIR，在部署服务时，系统会自动创建资源应用程序。 只要在应用程序所部署到的同一个 Azure Active Directory 租户中使用 Azure API for FHIR，就可以跳过本操作指南，并改为部署 Azure API for FHIR 开始操作。

如果使用不同的 Azure Active Directory 租户（与订阅不关联），可以使用 PowerShell 将 Azure API for FHIR 资源应用程序导入到租户中：

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

或者，可以使用 Azure CLI：

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>适用于 Azure 的 FHIR 服务器

如果正在使用适用于 Azure 的开源 FHIR 服务器，请按照 [GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) 存储库上的步骤注册资源应用程序。 

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Active Directory 中注册资源应用程序。 接下来，注册你的机密客户端应用程序。
 
>[!div class="nextstepaction"]
>[注册机密客户端应用程序](register-confidential-azure-ad-client-app.md)