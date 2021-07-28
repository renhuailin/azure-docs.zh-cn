---
title: 使用 SCIM、Microsoft Graph 和 Azure Active Directory 来预配用户并利用数据扩充应用
description: 在 Azure Active Directory 中协同使用 SCIM 和 Microsoft Graph 来预配用户并使用应用程序需要的数据来扩充应用程序。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 3e3b8670aebb52c1b1bf36d9ce933113b17b7714
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109784892"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>协同使用 SCIM 和 Microsoft Graph 来预配用户并使用应用程序需要的数据来扩充应用程序

目标受众：本文面向那些构建与 Azure Active Directory (Azure AD) 集成的应用程序的开发人员。 如果希望使用已与 Azure AD 集成的应用程序（例如 Zoom、ServiceNow 和 DropBox），则可跳过本文，并查看应用程序特定的[教程](../saas-apps/tutorial-list.md)，或查看[预配服务工作原理](./how-provisioning-works.md)。

**常见方案**

Azure AD 提供了现成的服务来进行预配，并且提供了可扩展的平台用于构建应用程序。 决策树概述了开发人员如何使用 [SCIM](https://aka.ms/scimoverview) 和 [Microsoft Graph](/graph/overview) 来自动完成预配。 

> [!div class="checklist"]
> * 在我的应用程序中自动创建用户
> * 在用户不再需要访问权限时，自动从我的应用程序中删除这些用户
> * 将我的应用程序与多个标识提供者集成以进行预配
> * 利用来自 Microsoft 服务（如 Teams、Outlook 和 Office）的数据来扩充我的应用程序。
> * 在 Azure AD 和 Active Directory 中自动创建、更新和删除用户和组

![SCIM Graph 决策树](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>方案 1：在我的应用中自动创建用户
目前，IT 管理员通过手动创建用户帐户或定期将 CSV 文件上传到我的应用程序来预配用户。 对于客户来说，此过程非常耗时，并会降低采用我的应用程序的速度。 我创建用户时只需要基本的用户信息，例如，姓名、电子邮件和 userPrincipalName。 

**建议**： 
* 如果你的客户使用各种 IdP，而你不想维护同步引擎来与每个 IdP 都集成，则可支持一个符合 SCIM 的 [/Users](https://aka.ms/scimreferencecode) 终结点。 你的客户可以轻松使用此终结点来与 Azure AD 预配服务集成，并且可以在用户需要访问权限时自动创建用户帐户。 可以构建一次终结点，该终结点将与所有 IdP 兼容。 请查看以下示例请求，以了解如何使用 SCIM 来创建用户。
* 如果需要在 Azure AD 中的用户对象上找到的用户数据，并且需要 Microsoft 提供的其他数据，请考虑构建 SCIM 终结点，以进行用户预配和调用 Microsoft Graph 来获取其余数据。 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>方案 2：自动从我的应用中删除用户
使用我的应用程序的客户非常关注安全性，并且会有在员工不再需要帐户时删除帐户的治理需求。 如何自动从我的应用程序取消预配？

建议：支持一个符合 SCIM 的 /Users 终结点。 Azure AD 预配服务将会在用户不应再具有访问权限时发送请求来禁用和删除这些用户。 建议支持禁用和删除用户这两种操作。 有关禁用和删除请求的内容，请参阅以下示例。 

禁用用户
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
删除用户
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>方案 3：自动管理我的应用中的组成员身份
我的应用程序依赖于组来访问各种资源，并且客户希望重复使用他们在 Azure AD 中拥有的组。 如何从 Azure AD 导入组并在成员身份更改时使这些组保持更新？  

建议：支持一个符合 SCIM 的 /Groups [终结点](https://aka.ms/scimreferencecode)。 Azure AD 预配服务将会负责在你的应用程序中创建组并管理成员身份更新。 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>方案 4：利用来自 Microsoft 服务（如 Teams、Outlook 和 OneDrive）的数据来扩充我的应用
我的应用程序内置于 Microsoft Teams 中，并且依赖于消息数据。 此外，我们还在 OneDrive 中存储用户的文件。 如何使用来自这些服务的以及 Microsoft 的数据来扩充我的应用程序？

建议：[Microsoft Graph](/graph/) 是访问 Microsoft 数据的入口点。 每个工作负载都会向 API 公开你需要的数据。 Microsoft Graph 可与 [SCIM 预配](./use-scim-to-provision-users-and-groups.md)一起用于上述方案。 可以使用 SCIM 将基本用户属性预配到应用程序中，同时调用 Graph 来获取任何其他需要的数据。 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>方案 5：跟踪 Microsoft 服务（如 Teams、Outlook 和 Azure AD）中的更改
我需要能够实时跟踪 Teams 和 Outlook 消息的更改并应对这些更改。 如何将这些更改推送到我的应用程序？

建议：Microsoft Graph 提供各种资源的[更改通知](/graph/webhooks)和[更改跟踪](/graph/delta-query-overview)。 请注意更改通知的以下限制：
- 如果事件接收者确认了某个事件，但出于任何原因而未能采取措施，则可能会丢失该事件。
- 不保证接收更改的顺序是按时间顺序发生的。
- 更改通知并不总是包含[资源数据](/graph/webhooks-with-resource-data)。出于上述原因，开发人员通常会将更改通知和更改跟踪协同用于同步方案。 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>方案 6：在 Azure AD 中预配用户和组
我的应用程序创建客户在 Azure AD 中需要的用户相关信息。 这可能是管理招聘的 HR 应用程序、为用户创建电话号码的通信应用，或者某种其他的在 Azure AD 中生成有价值数据的应用。 如何使用这些数据在 Azure AD 中填充用户记录？ 

建议：现在，Microsoft Graph 公开了 /Users 和 /Groups 终结点，可将它们集成，以便将用户预配到 Azure AD 中。 请注意，Azure Active Directory 不支持将这些用户写回到 Active Directory 中。 

> [!NOTE]
> Microsoft 提供了从 HR 应用程序（例如 Workday 和 SuccessFactors）拉取数据的预配服务。 这些集成由 Microsoft 构建和管理。 若要将新 HR 应用程序加入到我们的服务，可在 [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests) 上提出请求。 

## <a name="related-articles"></a>相关文章

- [查看同步 Microsoft Graph 文档](/graph/api/resources/synchronization-overview)
- [将自定义 SCIM 应用与 Azure AD 集成](use-scim-to-provision-users-and-groups.md)