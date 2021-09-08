---
title: 使用 PowerShell 和 Graph 更改子域身份验证类型 - Azure Active Directory | Microsoft Docs
description: 更改从 Azure Active Directory 中的根域设置继承的默认子域身份验证设置。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36347a8367cd5e2f99f6da2cb97023b3647f8afa
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438671"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>在 Azure Active Directory 中更改子域身份验证类型

将根域添加到 Azure Active Directory (Azure AD) 后，Azure AD 组织中添加到该根的所有后续子域将自动从根域继承身份验证设置。 但是，如果要独立于根域设置来管理域身份验证设置，现在可以使用 Microsoft Graph API 来实现。 例如，如果你有一个联合根域（如 contoso.com），则本文可以帮助你将子域（如 child.contoso.com）验证为托管子域而非联合子域。

在 Azure AD 门户中，当父域联合并且管理员尝试在“自定义域名”页上验证托管子域时，你将收到“无法添加域”错误，原因为“一个或多个属性包含无效值”。 如果尝试从 Microsoft 365 管理中心添加此子域，将收到类似的错误。 有关错误的详细信息，请参阅[子域不继承 Office 365、Azure 或 Intune 中的父域更改](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)。

## <a name="how-to-verify-a-custom-subdomain"></a>如何验证自定义子域

因为默认情况下子域继承根域的身份验证类型，因此必须使用 Microsoft Graph 将子域升级为 Azure AD 中的根域，才能将身份验证类型设置为所需的类型。

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>添加子域并查看其身份验证类型

1. 使用 PowerShell 添加新子域，该子域具有其根域的默认身份验证类型。 Azure AD 和 Microsoft 365 管理中心尚不支持此操作。

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. 使用 [Azure AD Graph 资源管理器](https://graphexplorer.azurewebsites.net)获取该域。 因为该域不是根域，所以它继承了根域身份验证类型。 使用你自己的租户 ID，命令和结果可能如下所示：

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

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>使用 Azure AD Graph 资源管理器 API 将其设为根域

使用以下命令升级子域：

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>更改子域身份验证类型

1. 使用以下命令更改子域身份验证类型：

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. 在 Azure AD Graph 资源管理器中通过 GET 验证子域身份验证类型现是托管：

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