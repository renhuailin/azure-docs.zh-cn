---
title: PowerShell 示例 - 替代 Azure Active Directory 应用程序代理应用中的证书
description: PowerShell 示例，它会批量替换 Azure Active Directory (Azure AD) 应用程序代理应用程序中的证书。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 7897cc324095c38e6b626c2057bd2bbca921b397
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293620"
---
# <a name="get-all-azure-active-directory-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>获取使用相同证书发布的所有 Azure Active Directory 应用程序代理应用程序，并替换该证书

通过该 PowerShell 脚本示例，可批量替换已使用相同证书发布的所有 Azure Active Directory (Azure AD) 应用程序代理应用程序的证书。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2) (AzureAD) 或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>脚本说明

| Command | 说明 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 获取服务主体。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | 获取 Azure AD 应用程序。 |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | 检索为 Azure AD 中的应用程序代理配置的应用程序。 |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate) | 将证书分配给为 Azure AD 中的应用程序代理配置的应用程序。 此命令会上传证书，应用程序还能通过它来使用自定义域。 |

## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](/powershell/azure/active-directory/overview)。

有关应用程序代理的其他 PowerShell 示例，请参阅 [Azure AD 应用程序代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。
