---
title: PowerShell 示例 - 列出应用程序代理应用的扩展信息
description: PowerShell 示例，其中列出了所有 Azure Active Directory (Azure AD) 应用程序代理应用程序，还列出了应用程序 ID (AppId)、名称 (DisplayName)、外部 URL (ExternalUrl)、内部 URL (InternalUrl) 和身份验证类型 (ExternalAuthenticationType)。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 1b3234a13a0e3fac760a899ce66e7faa7e7b642c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377321"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>获取所有应用程序代理应用并列出扩展信息

在该 PowerShell 脚本示例中，可找到所有 Azure Active Directory (Azure AD) 应用程序代理应用程序的相关信息，包括应用程序 ID (AppId)、名称 (DisplayName)、外部 URL (ExternalUrl)、内部 URL (InternalUrl)、身份验证类型 (ExternalAuthenticationType)、SSO 模式以及更多的设置。

通过更改 $ssoMode 变量的值，可按 SSO 模式筛选输出。 脚本中提供了更多详细信息。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2) (AzureAD)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>脚本说明

| Command | 说明 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 获取服务主体。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | 获取 Azure AD 应用程序。 |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | 检索为 Azure AD 中的应用程序代理配置的应用程序。 |

## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](/powershell/azure/active-directory/overview)。

有关应用程序代理的其他 PowerShell 示例，请参阅 [Azure AD 应用程序代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。
