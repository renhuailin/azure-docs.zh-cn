---
title: 列出应用的 Azure AD 应用程序代理连接器组
description: PowerShell 示例，其中列出了分配有应用程序的所有 Azure Active Directory (Azure AD) 应用程序代理连接器组。
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
ms.openlocfilehash: 008959c3e22f026314ec28b42b649e6c2dabefee
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861610"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>获取所有应用程序代理应用并按连接器组列出

在该 PowerShell 脚本示例中，可找到分配有应用程序的所有 Azure Active Directory (Azure AD) 应用程序代理连接器组的相关信息。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2) (AzureAD) 或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>脚本说明

| Command | 说明 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 获取服务主体。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | 获取 Azure AD 应用程序。 |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | 检索为 Azure AD 中的应用程序代理配置的应用程序。 |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | 检索包含所有连接器组的列表，或检索指定的连接器组的详细信息（若指定）。 |


## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](/powershell/azure/active-directory/overview)。

有关应用程序代理的其他 PowerShell 示例，请参阅 [Azure AD 应用程序代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。
