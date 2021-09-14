---
title: 使用 Azure AD 配置组所有者对应用访问组数据的同意
description: 了解如何管理组和团队所有者是否同意应用程序访问组或团队的数据。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/31/2021
ms.author: davidmu
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: babc9da622fdca1365d5b963191775881ad992e8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431921"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>配置组所有者对应用访问组数据的同意

组和团队所有者可授权应用程序（如第三方供应商发布的应用程序）访问你的组织中与组关联的数据。 例如，Microsoft Teams 中的团队所有者可允许应用读取团队中的所有 Teams 消息，或允许列出组成员的基本个人资料。 若要了解详细信息，请参阅 [Microsoft Teams 中的资源特定同意](/microsoftteams/resource-specific-consent)。

## <a name="prerequisites"></a>先决条件

要完成本指南中的任务，需具备以下各项：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 全局管理员角色。
- 安装 Azure AD PowerShell。 请参阅 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/)

## <a name="manage-group-owner-consent-to-apps"></a>管理组所有者对应用的同意

可配置允许哪些用户同意应用访问其组或团队的数据，也可以对所有用户禁用此功能。

# <a name="portal"></a>[Portal](#tab/azure-portal)

请按照以下步骤操作，管理组所有者对访问组数据的应用的同意：

1. 以[全局管理员](../roles/permissions-reference.md#global-administrator)的身份登录 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “企业应用程序” > “同意和权限” > “用户同意设置”   。
3. 在“针对应用访问数据的组所有者同意”下，选择要启用的选项。
4. 选择“保存”以保存设置。

在下例中，所有组所有者都可同意应用访问其组数据：

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="组所有者同意设置":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

可使用 Azure AD PowerShell 预览版模块 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 允许或禁止组所有者同意应用程序访问你组织的数据来查找他们拥有的组。

1. 请确保使用 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 模块。 如果同时安装了 [AzureAD](/powershell/module/azuread/) 模块和 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 模块，则此步骤非常重要。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. 连接到 Azure AD PowerShell。

   ```powershell
   Connect-AzureAD
   ```

1. 检索租户中“同意策略设置”目录设置的当前值。 这需要检查是否已创建此功能的目录设置；如果没有，则使用相应目录设置模板中的值。

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. 了解设置值。 有两个设置值可用于定义哪些用户能够允许应用访问其组的数据：

    | 设置       | 类型         | 说明  |
    | ------------- | ------------ | ------------ |
    | EnableGroupSpecificConsent   | Boolean | 指示是否允许组所有者授予特定于组的权限的标志。 |
    | ConstrainGroupSpecificConsentToMembersOfGroupId | Guid | 如果 EnableGroupSpecificConsent 设置为“True”，并且此值设置为组的对象 ID，则将授权已标识组的成员向其拥有的组授予特定于组的权限。 |

1. 更新所需配置的设置值：

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. 保存设置。

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

---

> [!NOTE]
> “用户可以自行许可访问公司数据的应用”设置关闭后不会禁用“用户可以同意应用访问其拥有的组的公司数据”选项

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请访问以下链接：

* [配置用户同意设置](configure-user-consent.md)
* [配置管理员同意工作流](configure-admin-consent-workflow.md)
* [了解如何管理对应用程序的同意并评估同意请求](manage-consent-requests.md)
* [向应用程序授予租户范围的管理许可](grant-admin-consent.md)
* [Microsoft 标识平台中的权限和许可](../develop/v2-permissions-and-consent.md)

获取帮助或查找问题的答案：

* [Microsoft Q&A 上的 Azure AD](/answers/topics/azure-active-directory.html)
