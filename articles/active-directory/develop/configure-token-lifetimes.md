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
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: ac1fa8db8d153365f990c4d3eff120a254ed8096
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060624"
---
# <a name="configure-token-lifetime-policies-preview"></a>配置令牌生存期策略（预览版）
你可以指定由 Microsoft 标识平台颁发的访问、SAML 或 ID 令牌的生存期。 可以针对组织中的所有应用、多租户（多组织）应用程序或者组织中的特定服务主体设置生存期。 有关详细信息，请阅读[可配置的令牌生存期](active-directory-configurable-token-lifetimes.md)。

本部分逐步讲解常见的策略场景，帮助你针对令牌生存期实施新规则。 本示例介绍如何创建一个要求用户更频繁地在 Web 应用中进行身份验证的策略。

## <a name="get-started"></a>入门

若要开始使用，请下载最新的 [Azure AD PowerShell 模块公共预览版](https://www.powershellgallery.com/packages/AzureADPreview)。

接下来，运行 `Connect` 命令登录到 Azure AD 管理员帐户。 每次启动新会话都需要运行此命令。

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>为 Web 登录创建策略

本示例创建一个要求用户更频繁地在 Web 应用中进行身份验证的策略。 此策略会针对 Web 应用的服务主体设置访问/ID 令牌的生存期。

1. 创建令牌生存期策略。

    这个用于 Web 登录的策略将访问/ID 令牌生存期设置为两小时。

    若要创建该策略，请运行 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    若要查看新策略并获取策略 ObjectId，请运行 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。

    请使用 [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet 来查看组织的所有服务主体或某一个服务主体。

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    如果你有服务主体，请运行 [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>查看租户中的现有策略

若要查看组织中创建的所有策略，请运行 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet。  任何已定义的属性值不同于上面列出的默认值的结果都在停用范围内。

```powershell
Get-AzureADPolicy -All $true
```

若要查看哪些应用和服务主体链接到你确定的特定策略，请使用任何策略 ID 替换 1a37dad8-5da7-4cc8-87c7-efbc0326cf20，以运行以下 [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) cmdlet。 然后，你可以决定是配置条件访问登录频率，还是保留 Azure AD 默认值。

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

如果你的租户具有定义刷新和会话令牌配置属性的自定义值的策略，Microsoft 建议你将这些策略更新为反映上述默认值的值。 如果未进行更改，Azure AD 将自动接受默认值。

### <a name="troubleshooting"></a>疑难解答
一些用户在运行 `Get-AzureADPolicy` cmdlet 之后报告了 `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` 错误。 作为一种解决方法，请运行以下步骤卸载/重新安装 AzureAD 模块，然后安装 AzureADPreview 模块：

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber
Note: You cannot install both the preview and the GA version on the same computer at the same time. 

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>后续步骤
了解 Azure AD 条件访问中的[身份验证会话管理功能](../conditional-access/howto-conditional-access-session-lifetime.md)。
