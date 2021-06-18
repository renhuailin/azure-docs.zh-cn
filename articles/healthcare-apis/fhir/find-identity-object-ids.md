---
title: 查找用于身份验证的标识对象AZURE API FOR FHIR
description: 本文介绍如何查找配置身份验证所需的标识对象AZURE API FOR FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: zxue
ms.openlocfilehash: d65aa4494c7ca5827added3d1159bc6838b2ac7b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288696"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>查找身份验证配置的标识对象标识

本文介绍在将标识对象配置为将数据平面Azure API for FHIR外部或辅助 [Active Directory](configure-local-rbac.md) 租户时，如何查找所需的标识对象 ID。

## <a name="find-user-object-id"></a>查找用户对象 ID

如果你有一个用户名为 的用户， `myuser@contoso.com` 可以使用以下 `ObjectId` PowerShell 命令查找用户：

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

或者，可以使用以下Azure CLI：

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>查找服务主体对象 ID

假设已 [注册服务客户端](register-service-azure-ad-client-app.md) 应用，并且希望允许此服务客户端访问 Azure API for FHIR，可以使用以下 PowerShell 命令查找客户端服务主体的对象 ID：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

其中 `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` 是服务客户端应用程序 ID。 或者，可以使用服务 `DisplayName` 客户端的 ：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

如果使用以下Azure CLI，可以使用：

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>查找安全组对象 ID

若要查找安全组的对象 ID，可以使用以下 PowerShell 命令：

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
`mygroup`其中 是感兴趣的组的名称。

如果使用以下Azure CLI，可以使用：

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何查找将标识对象配置为使用外部或辅助Azure API for FHIR租户所需的标识Azure Active Directory ID。 接下来，请阅读如何使用对象 ID 配置本地 RBAC 设置：
 
>[!div class="nextstepaction"]
>[配置本地 RBAC 设置](configure-local-rbac.md)
