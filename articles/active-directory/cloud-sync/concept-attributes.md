---
title: 了解 Azure AD 架构和自定义表达式
description: 本文介绍 Azure AD 架构、预配代理传递的特性，以及自定义表达式。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 750bc2f4f535238dbb757afc8b615fa71063d74d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613200"
---
# <a name="understand-the-azure-ad-schema"></a>了解 Azure AD 架构
与所有目录类似，Azure Active Directory (Azure AD) 中的对象是一种程序化的高级数据构造，可表示用户、组和联系人等。 在 Azure AD 中新建用户或联系人的同时，你正在创建该对象的新实例。 可以根据实例的属性区分实例。

Azure AD 中的属性是负责存储 Azure AD 中对象实例的相关信息的元素。

Azure AD 架构定义规则，规定了可在条目中使用的属性、这些属性可能具有的值类型，以及用户如何与这些值进行交互。 

Azure AD 具有两种类型的属性：
- **内置属性**：由 Azure AD 架构预定义的属性。 这些属性具有各种用途，可能可以访问，也可能不可以访问。
- **目录扩展**：提供的属性，确保可以自定义 Azure AD 以供自己使用。 例如，如果你为本地 Active Directory 扩展了特定特性并且希望传递该特性，则可以使用提供的自定义属性之一。 

## <a name="attributes-and-expressions"></a>特性和表达式
将用户等对象预配到 Azure AD 的同时，将创建用户对象的新实例。 该创建包括该对象的属性，又称为特性。 最初，新创建的对象的特性设为同步规则确定的值。 然后，这些特性通过云预配代理保持为最新。

![对象预配](media/concept-attributes/attribute-1.png)

例如，用户可能是市场营销部门的成员。 其 Azure AD 部门特性最初是在对其进行预配时创建的，且该值设为“营销”。 六个月后，如果用户调动到销售部门，则其本地 Active Directory 部门特性将更改为“销售”。 此更改将同步到 Azure AD，并在其 Azure AD 用户对象中反映。

特性同步可能很直接，即 Azure AD 中的值直接设为本地特性的值。 或者，程序化表达式可以处理同步。 需要一些逻辑或必须决定填充值时，需使用程序化表达式。

例如，如果电子邮件特性为“john.smith@contoso.com”，且需删除“@contoso.com”部分并仅传递值“john.smith”，则你应使用以下命令：

`Replace([mail], "@contoso.com", , ,"", ,)`  

**示例输入/输出：** <br>

* **输入** (mail)：“john.smith@contoso.com”
* **输出**：“john.smith”

若要详细了解如何编写自定义表达式和语法，请参阅[在 Azure Active Directory 中编写特性映射的表达式](../app-provisioning/functions-for-customizing-application-data.md)。

下表列出了常见的特性，以及如何将它们同步到 Azure AD。


|本地 Active Directory|映射类型|Azure AD|
|-----|-----|-----|
|cn|直接|CommonName
|countryCode|直接|countryCode|
|displayName|直接|displayName|
|givenName|Expression|givenName|
|objectGUID|直接|sourceAnchorBinary|  
|userprincipalName|直接|userPrincipalName|
|ProxyAdress|直接|ProxyAddress|

## <a name="view-the-schema"></a>查看架构
> [!WARNING]
> 云同步配置会创建服务主体。 服务主体在 Azure 门户中可见。 你不应按照 Azure 门户中的服务主体体验来修改特性映射。  此操作不受支持。

若要查看并验证架构，请按照以下步骤操作。

1.  转到[图形资源管理器](https://developer.microsoft.com/graph/graph-explorer)。
1.  使用全局管理员帐户登录。
1.  选择左侧的“修改权限”，并确保 Directory.ReadWrite.All 为“已同意” 。
1.  运行查询 `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`。 该查询返回筛选的服务主体列表。
1.  找到 `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"`，并记下 `"id"` 的值。
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. 将 `{Service Principal id}` 替换为你的值，然后运行查询 `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`。
1. 找到 `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"`，并记下 `"id"` 的值。
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. 运行查询 `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`。
 
    示例： https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   将 `{Service Principal Id}` 和 `{AD2ADD Provisioning Id}` 替换为自定义值。

1. 此查询将返回架构。

   ![返回的架构](media/concept-attributes/schema-1.png)
 
## <a name="next-steps"></a>后续步骤

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)