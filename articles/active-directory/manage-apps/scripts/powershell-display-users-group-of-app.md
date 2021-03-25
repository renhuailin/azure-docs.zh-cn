---
title: PowerShell 示例 - 列出应用程序代理应用的用户和组
description: PowerShell 示例，其中列出了分配给特定 Azure Active Directory (Azure AD) 应用程序代理应用程序的所有用户和组。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 540df7cbe2d35cd705e9d2b88f66a82604951957
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548529"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>显示分配到应用程序代理应用程序的用户和组

在该 PowerShell 脚本示例中，可找到分配给特定 Azure Active Directory (Azure AD) 应用程序代理应用程序的用户和组。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2) (AzureAD) 或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>脚本说明

| Command | 说明 |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| 获取用户。 |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| 获取组。 |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 获取服务主体。 |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | 获取用户应用程序角色分配。 |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | 获取组应用程序角色分配。 |

## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](/powershell/azure/active-directory/overview)。

有关应用程序代理的其他 PowerShell 示例，请参阅 [Azure AD 应用程序代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。
