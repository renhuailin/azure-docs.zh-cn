---
title: 隐藏企业应用程序
titleSuffix: Azure AD
description: 如何在 Azure Active Directory 访问门户或 Microsoft 365 启动器中隐藏企业应用程序以避免用户使用。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c76498e27d7cba32e7129b1fcca57e20ab11cfb
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061478"
---
# <a name="hide-an-enterprise-application-in-azure-active-directory"></a>在 Azure Active Directory 中隐藏企业应用程序

了解如何在 Azure Active Directory 中隐藏企业应用程序。 当应用程序隐藏后，用户仍然拥有对应用程序的权限。

## <a name="prerequisites"></a>先决条件

- 在“我的应用”门户和 Microsoft 365 启动器中隐藏应用程序需要应用程序管理员权限。

- 隐藏所有 Microsoft 365 应用程序需要全局管理员权限。

## <a name="hide-an-application-from-the-end-user"></a>向最终用户隐藏应用程序

按照以下步骤在“我的应用”门户和 Microsoft 365 应用程序启动器中隐藏应用程序。

1. 以目录的全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“Azure Active Directory” 。
1. 选择“企业应用程序”。
1. 在“应用程序类型”下，选择“企业应用程序”（如果尚未选择）。
1. 搜索要隐藏的应用程序，然后选择该应用程序。
1. 对于“对用户可见?”问题，选择“否”。
1. 选择“保存”。

> [!NOTE]
> 这些说明仅适用于企业应用程序。

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>使用 Azure AD PowerShell 隐藏应用程序

若要从“我的应用”门户中隐藏应用程序，可以将 HideApp 标记手动添加到应用程序的服务主体。 运行下面的 [AzureAD PowerShell](/powershell/module/azuread/#service_principals) 命令，将应用程序的“是否对用户可见?”属性设置为“否”。

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-my-apps-portal"></a>在“我的应用”门户中隐藏 Microsoft 365 应用程序

按照以下步骤在“我的应用”门户中隐藏所有 Microsoft 365 应用程序。 应用程序在 Office 365 门户中仍然可见。

1. 以目录的全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“Azure Active Directory” 。
1. 选择“用户”。
1. 选择“用户设置”。
1. 在“企业应用程序”下，选择“管理最终用户启动和查看其应用程序的方式”。
1. 对于“用户只能在 Office 365 门户中查看 Office 365 应用”，选择“是”。 
1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

- [删除企业应用的用户或组分配](./assign-user-or-group-access-portal.md)
