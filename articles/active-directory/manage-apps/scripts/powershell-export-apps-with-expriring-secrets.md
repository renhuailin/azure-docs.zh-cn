---
title: PowerShell 示例 - 导出 Azure Active Directory 租户中具有即将过期的机密和证书的应用。
description: PowerShell 示例，该示例导出了 Azure Active Directory 租户中指定应用的所有具有即将过期的机密和证书的应用。
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 7f129e06904497b43eff8a3f0221fb57565ac112
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375400"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>导出具有即将过期的机密和证书的应用

此 PowerShell 脚本示例从目录中将包含指定应用的即将过期机密、证书及其所有者的所有应用注册信息导出到一个 CSV 文件中。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2) (AzureAD) 或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>脚本说明

此脚本无需修改即可直接使用。 系统会询问管理员到期日期，以及他们是否希望看到已过期的机密或证书。

“Add-Member”命令负责在 CSV 文件中创建列。
“New-Object”命令创建一个对象，用于 CSV 文件导出内容中的列。
如果希望导出为非交互式，则可以在 PowerShell 中使用 CSV 文件路径直接修改“$Path”变量。

| 命令 | 说明 |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | 从目录中检索应用程序。 |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | 从目录中检索应用程序的所有者。 |

## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](/powershell/azure/active-directory/overview)。

有关应用程序管理的其他 PowerShell 示例，请参阅[应用程序管理的 Azure AD PowerShell 示例](../app-management-powershell-samples.md)。
