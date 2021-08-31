---
title: 查找用于身份验证的标识对象 ID - Azure API for FHIR
description: 本文介绍如何查找为 Azure API for FHIR 配置身份验证所需的标识对象 ID
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: zxue
ms.openlocfilehash: 96dfc3b10a1a4a9b4f12e8d9decb5d01446385f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778759"
---
# <a name="find-identity-object-ids-for-authentication-configuration-for-azure-api-for-fhir"></a>查找为 Azure API for FHIR 配置身份验证所需的标识对象 ID

在本文中，你将了解如何查找将 Azure API for FHIR 配置为[使用外部或辅助 Active Directory 租户](configure-local-rbac.md)作为数据平面时所需的标识对象 ID。

## <a name="find-user-object-id"></a>查找用户对象 ID

如果你有一个用户名为 `myuser@contoso.com` 的用户，则可以使用以下 PowerShell 命令查找用户 `ObjectId`：

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

也可以使用 Azure CLI：

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>查找服务主体对象 ID

假设你已经注册[服务客户端应用](register-service-azure-ad-client-app.md)，并且希望允许此服务客户端访问 Azure API for FHIR，则可以使用以下 PowerShell 命令查找客户端服务主体的对象 ID：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

其中 `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` 是服务客户端应用程序 ID。 或者，可以使用服务客户端的 `DisplayName`：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

如果正在使用 Azure CLI，则可以使用：

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>查找安全组对象 ID

如果要查找安全组的对象 ID，可以使用以下 PowerShell 命令：

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
其中 `mygroup` 是你感兴趣的组的名称。

如果正在使用 Azure CLI，则可以使用：

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何查找将 Azure API for FHIR 配置为使用外部或辅助 Active Directory 租户时所需的标识对象 ID。 接下来，阅读如何使用对象 ID 配置本地 RBAC 设置：
 
>[!div class="nextstepaction"]
>[配置本地 RBAC 设置](configure-local-rbac.md)
