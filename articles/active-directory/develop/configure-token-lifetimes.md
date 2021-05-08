---
title: 设置令牌的生存期
titleSuffix: Microsoft identity platform
description: 了解如何设置由 Microsoft 标识平台颁发的令牌的生存期。 了解如何管理组织的默认策略、为 Web 登录创建策略、为调用 Web API 的本机应用创建策略，以及如何管理高级策略。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 3ec94543a53e3e5b7709801de8f4cf1dde3fc3d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99428109"
---
# <a name="configure-token-lifetime-policies-preview"></a>配置令牌生存期策略（预览版）
你可以指定由 Microsoft 标识平台颁发的访问、SAML 或 ID 令牌的生存期。 可以针对组织中的所有应用、多租户（多组织）应用程序或者组织中的特定服务主体设置生存期。 有关详细信息，请阅读[可配置的令牌生存期](active-directory-configurable-token-lifetimes.md)。

本部分逐步讲解常见的策略场景，帮助你针对令牌生存期实施新规则。 本示例介绍如何创建一个要求用户更频繁地在 Web 应用中进行身份验证的策略。

## <a name="get-started"></a>入门
若要开始，请执行以下步骤：

1. 下载最新的 [Azure AD PowerShell 模块公共预览版](https://www.powershellgallery.com/packages/AzureADPreview)。
1. 运行 `Connect` 命令登录到 Azure AD 管理员帐户。 每次启动新会话都需要运行此命令。

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. 若要查看组织中创建的所有策略，请运行 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet。  任何已定义的属性值不同于上面列出的默认值的结果都在停用范围内。

    ```powershell
    Get-AzureADPolicy -All $true
    ```

1. 若要查看哪些应用和服务主体链接到你确定的特定策略，请使用任何策略 ID 替换 1a37dad8-5da7-4cc8-87c7-efbc0326cf20，以运行以下 [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) cmdlet。 然后，你可以决定是配置条件访问登录频率，还是保留 Azure AD 默认值。

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

如果你的租户具有定义刷新和会话令牌配置属性的自定义值的策略，Microsoft 建议你将这些策略更新为反映上述默认值的值。 如果未进行更改，Azure AD 将自动接受默认值。

## <a name="create-a-policy-for-web-sign-in"></a>为 Web 登录创建策略

本示例创建一个要求用户更频繁地在 Web 应用中进行身份验证的策略。 此策略会针对 Web 应用的服务主体设置访问/ID 令牌的生存期以及多因素会话令牌的最大期限。

1. 创建令牌生存期策略。

    这个用于 Web 登录的策略将访问/ID 令牌生存期和单因素会话令牌最大期限设置为 2 小时。

    1. 若要创建该策略，请运行 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 若要查看新策略并获取策略 ObjectId，请运行 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。

    1. 请使用 [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet 来查看组织的所有服务主体或某一个服务主体。
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. 如果你有服务主体，请运行 [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>为刷新和会话令牌创建令牌生存期策略
> [!IMPORTANT]
> 从 2021 年 1 月 30 日起，无法再配置刷新和会话令牌生存期。 Azure Active Directory 不再支持现有策略中的刷新和会话令牌配置。  现有令牌过期后颁发的新令牌现已设置为[默认配置](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement)。 在刷新和会话令牌配置停用后，你仍可配置访问权限、SAML 和 ID 令牌生存期。
>
> 现有令牌的生存期不会更改。 在它们过期后，会根据默认值颁发新令牌。
>
> 如果需要继续定义要求用户再次登录之前的时间段，请配置条件访问中的登录频率。 若要详细了解条件访问，请参阅[使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

### <a name="manage-an-organizations-default-policy"></a>管理组织的默认策略
本示例将创建一个策略，使用户以更低的频率在整个组织中登录。 为此，可以为单因素刷新令牌创建一个令牌生存期策略，该策略应用于整个组织。 此策略将应用到组织中的每个应用程序，以及尚未设置策略的每个服务主体。

1. 创建令牌生存期策略。

    1. 将单因素刷新令牌设置为“until-revoked”。 在吊销访问权限之前该令牌不会过期。 创建以下策略定义：

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. 若要创建该策略，请运行 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 若要删除任何空格，请运行 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. 若要查看新策略并获取其 **ObjectId**，请运行以下命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 更新策略。

    假设在本示例中创建的第一个策略不像服务要求的那样严格。 若要将单因素刷新令牌设置为在两天后过期，请运行以下命令：

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>为调用 Web API 的本机应用创建策略
本示例创建一个不要求用户太频繁进行身份验证的策略。 该策略还可延长用户可保持非活动状态、不必再次身份验证的时间。 该策略将应用到 Web API。 当本机应用以资源形式请求 Web API 时，将应用此策略。

1. 创建令牌生存期策略。

    1. 若要为 Web API 创建严格策略，请运行 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 若要查看新策略，请运行以下命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 将策略分配到 Web API。 还需要获取应用程序的 **ObjectId**。 请使用 [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) cmdlet 来查找应用的 ObjectId，或使用 [Azure 门户](https://portal.azure.com/)。

    获取应用的 ObjectId 并分配该策略：

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>管理高级策略
本示例创建几个策略来演示优先级系统的工作原理。 此外，你还可以了解如何管理应用于多个对象的多个策略。

1. 创建令牌生存期策略。

    1. 若要创建一个将单因素刷新令牌生存期设置为 30 天的组织默认策略，请运行 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 若要查看新策略，请运行 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 将策略分配到服务主体。

    现已创建一个要应用到整个组织的策略。 可能想要为特定的服务主体保留这个 30 天策略，但要将组织默认策略更改为上限“直到吊销”。

    1. 若要查看组织的所有服务主体，请使用 [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet。

    1. 如果你有服务主体，请运行 [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. 将 `IsOrganizationDefault` 标志设置为 false：

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. 创建新的组织默认策略：

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    现在，已将原始策略链接到服务主体，已将新策略设置为组织默认策略。 请务必记住，应用到服务主体的策略优先级高于组织默认策略。

## <a name="next-steps"></a>后续步骤
了解 Azure AD 条件访问中的[身份验证会话管理功能](../conditional-access/howto-conditional-access-session-lifetime.md)。
