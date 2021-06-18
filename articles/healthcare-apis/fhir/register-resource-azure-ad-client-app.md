---
title: 在 Azure AD 中注册资源Azure API for FHIR
description: 在 (中) 资源组或 API Azure Active Directory，以便客户端应用程序可以在进行身份验证时请求对资源的访问权限。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 011067e336355994f4f45d83a38aec24238ac258
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299062"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册资源应用程序

本文将了解如何在 Azure Active Directory 中注册资源 () 或 API Azure Active Directory。 资源应用程序是 FHIR Azure Active Directory API 本身的一种重要表示形式，客户端应用程序可以在进行身份验证时请求访问资源。 资源应用程序也称为 OAuth parlance 中的受众。 

## <a name="azure-api-for-fhir"></a>适用于 FHIR 的 Azure API

如果使用资源Azure API for FHIR，则部署服务时会自动创建资源应用程序。 只要在部署应用程序Azure API for FHIR同一 Azure Active Directory 租户中使用该资源，就可以跳过本操作指南，改为部署Azure API for FHIR以开始使用。

如果使用的是与订阅Azure Active Directory (租户应用程序) ，可以使用 PowerShell 将 Azure API for FHIR 资源应用程序导入租户：

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

或者，可以使用Azure CLI：

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>适用于 Azure 的 FHIR 服务器

如果使用适用于 Azure 的开源 FHIR 服务器，请按照 [GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) 存储库中的步骤注册资源应用程序。 

## <a name="next-steps"></a>后续步骤

本文已了解如何在 Azure Active Directory 中注册资源应用程序。 接下来，注册机密客户端应用程序。
 
>[!div class="nextstepaction"]
>[注册机密客户端应用程序](register-confidential-azure-ad-client-app.md)