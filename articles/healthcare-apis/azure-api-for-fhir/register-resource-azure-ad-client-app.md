---
title: 在 Azure AD 中注册资源应用 - Azure API for FHIR
description: 在 Azure Active Directory 中注册资源（或 API）应用，以便客户端应用程序可以在进行身份验证时请求访问该资源。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: e8453a2b82051dc7e63d1193947747d839478427
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778666"
---
# <a name="register-a-resource-application-in-azure-active-directory-for-azure-api-for-fhir"></a>在 Azure Active Directory 中为 Azure API for FHIR 注册资源应用程序

本文介绍如何在 Azure Active Directory 中注册资源（或 API）应用程序。 资源应用程序是 FHIR 服务器 API 本身的 Azure Active Directory 表示形式，客户端应用程序可以在进行身份验证时请求访问资源。 在 OAuth 术语中，资源应用程序也称为“受众”。

## <a name="azure-api-for-fhir"></a>适用于 FHIR 的 Azure API

如果使用的是 Azure API for FHIR，则系统会在部署服务时自动创建资源应用程序。 只要在部署应用程序时使用同一 Azure Active Directory 租户中的 Azure API for FHIR，便可跳过本操作方法指南部署 Azure API for FHIR 以开始使用。

如果你使用不同的 Azure Active Directory 租户（与订阅无关联），则可以使用 PowerShell 将 Azure API for FHIR 资源应用程序导入租户：

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

也可以使用 Azure CLI：

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR Server for Azure

如果你正在使用开源 FHIR Server for Azure，请按照 [GitHub 存储库](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md)中的步骤注册资源应用程序。 

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Active Directory 中注册资源应用程序。 接下来，注册机密客户端应用程序。
 
>[!div class="nextstepaction"]
>[注册机密客户端应用程序](register-confidential-azure-ad-client-app.md)