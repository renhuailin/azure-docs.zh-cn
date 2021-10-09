---
title: 使用主页领域发现策略配置登录自动加速
titleSuffix: Azure AD
description: 了解如何为联合用户配置关于 Azure Active Directory 身份验证的主页领域发现策略，包括自动加速和域提示。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/13/2021
ms.author: davidmu
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.reviewer: hirsin
ms.openlocfilehash: 56a4c06759841e5e011deb5fde50f1e9fe2cbea7
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061820"
---
# <a name="configure-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>使用主领域发现策略为应用程序配置登录行为

本文介绍了如何使用主领域发现 (HRD) 策略为联合用户配置 Azure Active Directory (Azure AD) 身份验证行为。  它涉及使用自动加速来跳过用户名输入屏幕，并自动将用户转发到联合登录终结点。  Microsoft 不再建议配置自动加速，因为它可能会阻止使用更强的身份验证方法（如 First Identity Online (FIDO)），并阻碍协作。

## <a name="prerequisites"></a>先决条件

若要为 Azure AD 中的应用程序配置 HRD 策略，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 最新的 Azure AD PowerShell cmdlet 预览版。

## <a name="set-up-an-hrd-policy-on-an-application"></a>设置应用程序的 HRD 策略

将使用 Azure AD PowerShell cmdlets 浏览几种方案，包括：

- 在包含单个联合域的租户中为应用程序设置 HRD 策略来执行自动加速。

- 针对已针对租户进行验证的多个域之一，为应用程序设置 HRD 策略以执行自动加速。

- 设置 HRD 策略，使旧式应用程序能够在 Azure AD 中对联合用户执行直接的用户名/密码身份验证。

- 列出为其配置了策略的应用程序。

以下示例在 Azure AD 中的应用程序服务主体上创建、更新、链接和删除策略。

1. 在开始之前，请运行 Connect 命令，以使用管理员帐户登录到 Azure AD：

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. 运行以下命令，查看组织中的所有策略：

    ```powershell
    Get-AzureADPolicy
    ```

如果未返回任何内容，则表示租户中未创建任何策略。

此示例创建一个策略。将此策略分配到应用程序后，它会：

- 当租户中包含单个域时，在用户登录到应用程序期间，自动加速将用户转到 AD FS 登录屏幕的过程。
- 自动加速将用户转到 AD FS 登录屏幕的过程，前提是租户中包含多个联合域。
- 使策略分配到的应用程序的联合用户能够使用用户名/密码以非交互方式直接登录到 Azure AD。

## <a name="create-an-hrd-policy"></a>创建 HRD 策略

当租户中包含单个域时，在用户登录到应用程序期间，以下策略自动加速将用户转到 AD FS 登录屏幕的过程。

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

当租户中包含多个联合域时，以下策略自动加速将用户转到 AD FS 登录屏幕的过程。 如果有多个联合域对应用程序的用户进行身份验证，则需要指定要自动加速的域。

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

若要创建一个策略，使特定应用程序的联合用户能够直接在 Azure AD 中进行用户名/密码身份验证，请运行以下命令：

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

若要查看新策略并获取其“ObjectID”，请运行以下命令：

```powershell
Get-AzureADPolicy
```

若要在创建 HRD 策略后启用该策略，可将它分配到多个应用程序服务主体。

## <a name="locate-the-service-principal-to-which-to-assign-the-policy"></a>查找要向其分配策略的服务主体

需要要向其分配策略的服务主体的“ObjectID”。 可通过多种方法查找服务主体的“ObjectID”。

可以使用 [Azure 门户](https://portal.azure.com)，也可以查询 [Microsoft Graph](/graph/api/resources/serviceprincipal)。 还可以转到 [Graph 浏览器工具](https://developer.microsoft.com/graph/graph-explorer)，并登录到 Azure AD 帐户，查看组织的所有服务主体。

由于使用的是 PowerShell，因此可以运行下面的 cmdlet 来列出服务主体及其 ID。

```powershell
Get-AzureADServicePrincipal
```

## <a name="assign-the-policy-to-your-service-principal"></a>向服务主体分配策略

在获取了要配置自动加速的应用程序的服务主体的“ObjectID”后，请运行以下命令。 该命令将步骤 1 中创建的 HRD 策略与步骤 2 中找到的服务主体关联起来。

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

可以为要向其添加策略的每个服务主体重复此命令。

如果已经为应用程序分配了 HomeRealmDiscovery 策略，则无法添加另一个策略。  在这种情况下，请更改分配到应用程序的主领域发现策略的定义，以添加其他参数。

### <a name="check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>检查 HRD 策略分配到的应用程序服务主体

若要检查为其配置了 HRD 策略的应用程序，请使用 **Get-AzureADPolicyAppliedObject** cmdlet。 并向其传递要检查的策略的“ObjectID”。

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>

Try the application to check that the new policy is working.

### List the applications for which HRD policy is configured

1. List all policies that were created in your organization

```powershell
Get-AzureADPolicy
```

记下要列出其分配情况的策略的“ObjectID”。

2. 列出向其分配了策略的服务主体

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="remove-an-hrd-policy-from-an-application"></a>从应用程序中删除 HRD 策略

1. 获取 ObjectID

使用前一个示例获取策略的“ObjectID”，以及希望从中删除策略的应用程序服务主体的“ObjectID”。

2. 从应用程序服务主体中删除策略分配

```powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

3. 通过列出向其分配了策略的服务主体检查删除情况

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解主领域发现在 Azure AD 中的工作原理，请参阅 [Azure AD 中的应用程序的主领域发现](home-realm-discovery-policy.md)。