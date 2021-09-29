---
title: 在 Azure AD 中管理应用同意策略
description: 了解如何管理内置的和自定义的应用同意策略，以控制何时可以授予同意。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: davidmu
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: bc457ad3a610f8c5942ba5b00a953f0cc5e05481
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124835809"
---
# <a name="manage-app-consent-policies"></a>管理应用同意策略

利用 Azure AD PowerShell，你可以查看和管理应用同意策略。

应用同意策略由一个或一个以上的“包括”条件集和零个或零个以上的“排除”条件集构成。 对于要在应用同意策略中考虑的事件，它必须与至少一个“包括”条件集匹配，并且不得与任何“排除”条件集匹配。

每个条件集包含多个条件。 若要让事件与条件集匹配，条件集中的所有条件都必须得到满足。

ID 以“microsoft-”开头的应用同意策略是内置策略。 其中的某些内置策略用于现有的内置目录角色。 例如，`microsoft-application-admin` 应用同意策略描述了允许“应用程序管理员”和“云应用程序管理员”角色授予租户级管理员同意的条件。 内置策略可用在自定义目录角色中，可用来配置用户同意设置，但你不能编辑或删除它们。

## <a name="pre-requisites"></a>先决条件

1. 请确保使用 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 模块。 如果同时安装了 [AzureAD](/powershell/module/azuread/) 模块和 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 模块，则此步骤非常重要。

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. 连接到 Azure AD PowerShell。

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>列出现有的应用同意策略

最好先熟悉组织中现有的应用同意策略：

1. 列出所有应用同意策略：

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. 查看策略的“包括”条件集：

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. 查看“排除”条件集：

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>创建自定义应用同意策略

按照以下步骤创建自定义应用同意策略：

1. 创建新的空的应用同意策略。

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. 添加“包括”条件集。

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   重复此步骤来添加其他“包括”条件集。

1. （可选）添加“排除”条件集。

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   重复此步骤来添加其他“排除”条件集。

创建应用同意策略后，你可以根据此策略[允许用户同意](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy)。

## <a name="delete-a-custom-app-consent-policy"></a>删除自定义应用同意策略

1. 下面展示了如何删除自定义应用同意策略。 **此操作不可撤消。**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> 无法还原已删除的应用同意策略。 如果意外删除了自定义应用同意策略，则需重新创建该策略。

---

### <a name="supported-conditions"></a>支持的条件

下表提供了应用同意策略支持的条件的列表。

| 条件 | 说明|
|:---------------|:----------|
| PermissionClassification | 要授予的权限的[权限分类](configure-permission-classifications.md)，或“all”（匹配任何权限分类，包括未分类的权限）。 默认值为“all”。 |
| PermissionType | 要授予的权限的权限类型。 使用“application”表示应用程序权限（例如应用角色），或使用“delegated”表示委托的权限。 <br><br>**注意**：值“delegatedUserConsentable”表示尚未经 API 发布者配置为需要管理员同意的委托的权限 - 此值可以在内置的权限授予策略中使用，但不能在自定义权限授予策略中使用。 必需。 |
| ResourceApplication | 要为其授予权限的资源应用程序（例如 API）的 **AppId**，或“any”（匹配任何资源应用程序或 API）。 默认值为“any”。 |
| 权限 | 要与之匹配的特定权限的权限 ID 的列表；或其中包含单个值“all”的列表，这匹配任何权限。 默认值为单个值“all”。 <ul><li>可以在 API 的 ServicePrincipal 对象的 **OAuth2Permissions** 属性中找到委托的权限 ID。</li><li>可以在 API 的 ServicePrincipal 对象的 **AppRoles** 属性中找到应用程序权限 ID。</li></ol> |
| ClientApplicationIds | 要与之匹配的客户端应用程序的 **AppId** 值的列表；或其中包含单个值“all”的列表，这匹配任何客户端应用程序。 默认值为单个值“all”。 |
| ClientApplicationTenantIds | 在其中注册了客户端应用程序的 Azure Active Directory 租户 ID 的列表；或其中包含单个值“all”的列表，这将匹配在任何租户中注册的客户端应用。 默认值为单个值“all”。 |
| ClientApplicationPublisherIds | 客户端应用程序的[已验证发布者](../develop/publisher-verification-overview.md)的 Microsoft 合作伙伴网络 (MPN) ID 的列表；或其中包含单个值“all”的列表，这将匹配来自任何发布者的客户端应用。 默认值为单个值“all”。 |
| ClientApplicationsFromVerifiedPublisherOnly | 设置为 `$true` 将仅匹配具有[已验证发布者](../develop/publisher-verification-overview.md)的客户端应用程序。 设置为 `$false` 将匹配任何客户端应用，即使它没有已验证的发布者。 默认值为 `$false`。 |

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请访问以下链接：

* [配置用户同意设置](configure-user-consent.md)
* [配置管理员同意工作流](configure-admin-consent-workflow.md)
* [了解如何管理对应用程序的同意并评估同意请求](manage-consent-requests.md)
* [向应用程序授予租户范围的管理许可](grant-admin-consent.md)
* [Microsoft 标识平台中的权限和许可](../develop/v2-permissions-and-consent.md)

获取帮助或查找问题的答案：

* [Microsoft Q&A 上的 Azure AD](/answers/products/)
