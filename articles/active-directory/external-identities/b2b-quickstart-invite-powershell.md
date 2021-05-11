---
title: 快速入门：使用 PowerShell 添加来宾用户 - Azure AD
description: 本快速入门介绍如何使用 PowerShell 向外部 Azure AD B2B 协作用户发送邀请。
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/28/2018
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-api
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3541698e4a02f82f54d84eabf1bf7bb285cda10
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987571"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>快速入门：使用 PowerShell 添加来宾用户

有很多种方法可用于邀请外部合作伙伴通过 Azure Active Directory B2B 协作访问你的应用和服务。 在上个快速入门中，你已了解如何在 Azure Active Directory 管理门户中直接添加来宾用户。 此外还可以使用 PowerShell 添加来宾用户，可以选择一次添加一个，也可以选择批量添加。 在本快速入门中，你将使用 New-AzureADMSInvitation 命令将一个来宾用户添加到 Azure 租户。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>必备条件

### <a name="powershell-module"></a>PowerShell 模块
安装[适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2) (AzureAD) 或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview)。

### <a name="get-a-test-email-account"></a>获取测试电子邮件帐户

需要可向其发送邀请的测试电子邮件帐户。 这些帐户必须来自组织外部。 可使用任意类型的帐户，包括 gmail.com 或 outlook.com 地址等社交帐户。

## <a name="sign-in-to-your-tenant"></a>登录租户

运行以下命令以连接到租户域：

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
例如，`Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`。

在系统提示时输入凭据。

## <a name="send-an-invitation"></a>发送邀请

1. 要向测试电子邮件帐户发送邀请，请运行以下 PowerShell 命令（将“Sanda”和“sanda\@fabrikam.com”替换为你的测试电子邮件帐户名和电子邮件地址） ： 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. 该命令向指定的电子邮件地址发送邀请。 查看输出，输出应如下所示：

   ![显示“等待用户接受”的 PowerShell 输出](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>验证用户是否存在于目录中

1. 要验证邀请的用户是否已添加到 Azure AD，请运行以下命令：
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. 查看输出，确保已列出受邀用户，其中用户主体名称 (UPN) 采用 emailaddress#EXT#\@domain 的格式 。 例如，sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com，其中 contoso.onmicrosoft.com 是你从其发送邀请的组织。

   ![显示已添加的来宾用户的 PowerShell 输出](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>清理资源

目录中不再需要测试用户帐户时，请将其删除。 运行以下命令来删除用户帐户：

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
例如： `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>后续步骤
在本快速入门中，你已邀请一个来宾用户并使用 PowerShell 将其添加到目录。 接下来，了解如何使用 PowerShell 批量邀请来宾用户。

> [!div class="nextstepaction"]
> [教程：批量邀请 Azure AD B2B 协作用户](tutorial-bulk-invite.md)
