---
title: PowerShell 示例 - 导出其机密和证书在 Azure Active Directory 租户中超出所需日期的应用。
description: PowerShell 示例，该示例导出了 Azure Active Directory 租户中指定应用的所有机密和证书超出所需日期的应用。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: daeea48758a9f08e7eedbfcaddcde3815f5c1e16
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729093"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>导出其机密和证书超出所需日期的应用

此 PowerShell 脚本示例以非交互方式将目录中指定应用所需的期限后过期的所有应用注册机密和证书导出到 CSV 文件。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>脚本说明

此脚本以非交互方式工作。 使用此脚本的管理员需要使用自己的应用 ID、应用程序机密、租户名称、应用凭据到期期限以及用于导出 CSV 的路径来更改“#PARAMETERS TO CHANGE”节中的值。
此脚本使用 [Client_Credential Oauth 流](../../develop/v2-oauth2-client-creds-grant-flow.md)。函数“RefreshToken”将基于管理员修改的参数值来生成访问令牌。

“Add-Member”命令负责在 CSV 文件中创建列。

| 命令 | 说明 |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | 将 HTTP 和 HTTPS 请求发送到网页或 Web 服务。 它将分析响应并返回链接、图像和其他重要 HTML 元素的集合。 |

## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](/powershell/azure/active-directory/overview)。

有关应用程序管理的其他 PowerShell 示例，请参阅[应用程序管理的 Azure AD PowerShell 示例](../app-management-powershell-samples.md)。
