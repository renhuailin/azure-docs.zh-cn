---
title: PowerShell 示例 - 导出 Azure Active Directory 租户中企业应用的机密和证书。
description: PowerShell 示例，该示例导出了 Azure Active Directory 租户中指定企业应用的所有机密和证书。
services: active-directory
author: mtillman
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: mtillman
ms.reviewer: mifarca
ms.openlocfilehash: 08fc5558cfe7b3459189f168e465ee2fa88d992a
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112076791"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>导出企业应用的机密和证书
此 PowerShell 脚本示例会将指定企业应用的所有机密、证书和所有者从目录导出到 CSV 文件。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2) (AzureAD) 或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>脚本说明

“Add-Member”命令负责在 CSV 文件中创建列。
如果希望导出为非交互式，则可以在 PowerShell 中使用 CSV 文件路径直接修改“$Path”变量。

| 命令 | 说明 |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | 从目录中检索企业应用程序。 |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | 从目录中检索企业应用程序的所有者。 |


## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](/powershell/azure/active-directory/overview)。

有关应用程序管理的其他 PowerShell 示例，请参阅[应用程序管理的 Azure AD PowerShell 示例](../app-management-powershell-samples.md)。
