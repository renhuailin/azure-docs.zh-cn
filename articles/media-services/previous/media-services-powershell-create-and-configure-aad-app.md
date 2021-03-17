---
title: 使用 PowerShell 创建 Azure AD 应用程序以访问 Azure 媒体服务 API | Microsoft Docs
description: 了解如何使用 PowerShell 创建 Azure Active Directory (Azure AD) 应用，并安装该应用以访问 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d16c2bbb16a19e5cb22b2b2b0378880ec9aa48b5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009651"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>使用 PowerShell 创建要与 Azure 媒体服务 API 配合使用的 Azure AD 应用

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 <br/>查看最新版本：[媒体服务 v3](../latest/index.yml)。 另请参阅[从 v2 到 v3 的迁移指南](../latest/migrate-v-2-v-3-migration-introduction.md)

了解如何使用 PowerShell 脚本创建 Azure Active Directory (Azure AD) 应用程序和服务主体，以访问 Azure 媒体服务资源。  

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有帐户，请从 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)入手。 
- 一个媒体服务帐户。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。

- Azure PowerShell。 有关详细信息，请参阅[如何使用 Azure PowerShell](/powershell/azure/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>使用 PowerShell 创建 Azure AD 应用  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

有关详细信息，请参阅以下文章：

- [使用 Azure PowerShell 创建服务主体来访问资源](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [使用 Azure PowerShell 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-powershell.md)
- [如何使用证书手动配置守护程序应用](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>后续步骤

开始[将文件上传到帐户](media-services-portal-upload-files.md)。
