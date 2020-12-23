---
title: PowerShell 示例 - 将应用程序代理应用移动到其他组
description: Azure Active Directory (Azure AD) 应用程序代理 PowerShell 示例，它用于将当前分配给某个连接器组的所有应用程序移动到其他连接器组。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: a2215b3de6c6ad1286570dbbf168d93a7ed62b94
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861560"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>将分配到某连接器组的所有应用移动到另一个连接器组

该 PowerShell 脚本示例会将当前分配给某个连接器组的所有 Azure Active Directory (Azure AD) 应用程序代理应用程序移动到其他连接器组。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2) (AzureAD) 或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>脚本说明

| Command | 说明 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 获取服务主体。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | 获取 Azure AD 应用程序。 |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | 检索包含所有连接器组的列表，或检索指定的连接器组的详细信息（若指定）。 |
| [Set-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup) | 将给定的连接器组分配给指定的应用程序。|

## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](/powershell/azure/active-directory/overview)。

有关应用程序代理的其他 PowerShell 示例，请参阅 [Azure AD 应用程序代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。
