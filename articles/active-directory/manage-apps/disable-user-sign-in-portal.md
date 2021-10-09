---
title: 禁用用户登录方式
titleSuffix: Azure AD
description: 如何禁用企业应用程序，防止用户在 Azure Active Directory 中登录该程序
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c07d650ee3b04099a4e4e41cdd7ee7db200bff
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061801"
---
# <a name="disable-how-a-user-signs-in-for-an-application-in-azure-active-directory"></a>禁用用户登录 Azure Active Directory 中应用程序的方式

在本文中，你将禁用用户登录 Azure Active Directory 中应用程序的方式。

## <a name="prerequisites"></a>先决条件

若要禁用用户登录方式，需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。

## <a name="disable-how-a-user-signs-in"></a>禁用用户登录方式

1. 以目录的全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”。
1. 选择“企业应用程序”。
1. 搜索你要禁止用户登录的应用程序，然后选择该应用程序。
1. 选择“属性”。
1. 对于“是否允许用户登录?”，选择“否”。
1. 选择“保存”。

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>使用 Azure AD PowerShell 禁用未列出的应用

确保已安装 AzureAD 模块（使用命令 Install-Module -Name AzureAD）。 出现安装 NuGet 模块或新的 Azure Active Directory V2 PowerShell 模块的提示时，请键入 Y，然后按 ENTER。

如果你知道未出现在企业应用列表中的应用的 AppId（例如，因为你删除了该应用，或者由于该应用正在由 Microsoft 预授权而尚未创建服务主体），则可以手动创建该应用的服务主体，然后使用 [AzureAD PowerShell cmdlet](/powershell/module/azuread/New-AzureADServicePrincipal) 将其禁用。

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>后续步骤

- [删除企业应用的用户或组分配](./assign-user-or-group-access-portal.md)
