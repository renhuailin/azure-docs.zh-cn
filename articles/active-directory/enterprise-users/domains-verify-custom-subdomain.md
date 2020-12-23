---
title: 使用 PowerShell 和图形 Azure Active Directory 更改子域身份验证类型 |Microsoft Docs
description: 更改 Azure Active Directory 中从根域设置继承的默认子域身份验证设置。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734e6824f13e62ad080500eff18c4892e1f76807
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503668"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>在 Azure Active Directory 中更改子域身份验证类型

将根域添加到 Azure Active Directory (Azure AD) 后，在 Azure AD 组织中添加到该根的所有后续子域会自动从根域继承身份验证设置。 但是，如果你想要独立于根域设置管理域身份验证设置，现在可以将 Microsoft Graph API。 例如，如果你有一个联合根域（如 contoso.com），则本文可以帮助你将子域（例如 child.contoso.com）验证为托管，而不是联合。

在 Azure AD 门户中，当父域联合并且管理员尝试验证 " **自定义域名** " 页上的托管子域时，你将收到 "无法添加域" 错误，原因是 "一个或多个属性包含无效值"。 如果尝试从 Microsoft 365 管理中心添加此子域，将收到类似的错误。 有关错误的详细信息，请参阅 [在 Office 365、Azure 或 Intune 中子域不会继承父域的更改](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)。

## <a name="how-to-verify-a-custom-subdomain"></a>如何验证自定义子域

由于子域默认继承根域的身份验证类型，因此必须使用 Microsoft Graph 将子域升级到 Azure AD 中的根域，以便将身份验证类型设置为所需的类型。

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>添加子域并查看其身份验证类型

1. 使用 PowerShell 添加新子域，该子域具有其根域的默认身份验证类型。 Azure AD 和 Microsoft 365 管理中心尚不支持此操作。

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. 使用 [Azure AD Graph 资源管理器](https://graphexplorer.azurewebsites.net) 获取该域。 由于域不是根域，因此它继承根域身份验证类型。 使用自己的租户 ID，你的命令和结果可能如下所示：

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>使用 Azure AD Graph 资源管理器 API 使其成为根域

使用以下命令来升级子域：

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>更改子域身份验证类型

1. 使用以下命令更改子域身份验证类型：

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. 通过获取 Azure AD Graph 资源管理器验证子域身份验证类型现在已托管：

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>后续步骤

- [添加自定义域名](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [管理域名](domains-manage.md)
- [使用 Microsoft 图形 API 强制删除自定义域名](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)