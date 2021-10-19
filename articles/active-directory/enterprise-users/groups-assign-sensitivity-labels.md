---
title: 将敏感度标签分配给组 - Azure AD | Microsoft Docs
description: 了解如何向组分配敏感度标签。 请参阅故障排除信息并查看其他可用资源。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/28/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cda4fe1af43b0bf7f091f34ae03a021c2ac4d20
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667528"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>向 Azure Active Directory 中的 Microsoft 365 组分配敏感度标签

Azure Active Directory (Azure AD) 支持将 [Microsoft 365 合规中心](https://sip.protection.office.com/homepage)发布的敏感度标签应用于 Microsoft 365 组。 敏感度标签适用于各种服务（例如 Outlook、Microsoft Teams 和 SharePoint）中的组。 有关 Microsoft 365 应用支持的详细信息，请参阅 [Microsoft 365 对敏感度标签的支持](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)。

> [!IMPORTANT]
> 若要配置此功能，Azure AD 组织中必须至少有一个活动的 Azure Active Directory Premium P1 许可证。

## <a name="enable-sensitivity-label-support-in-powershell"></a>在 PowerShell 中启用敏感度标签支持

若要将已发布的标签应用于组，必须先启用此功能。 以下步骤将在 Azure AD 中启用此功能。

1. 在计算机上打开 Windows PowerShell 窗口。 无需提升的权限即可打开该窗口。
1. 运行以下命令，准备运行 cmdlet。

    ```PowerShell
    Install-Module AzureADPreview
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    在“登录到你的帐户”页面上，输入管理员帐户和密码以连接到服务，然后选择“登录” 。
1. 提取 Azure AD 组织的当前组设置。

    ```PowerShell
    $setting = (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ)
    $template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
    $setting = $template.CreateDirectorySetting()
    ```

    > [!NOTE]
    > 如果没有为此 Azure AD 组织创建组设置，你将收到一个错误，内容为“无法将实参绑定到形参‘Id’，因为它为 NULL”。 在这种情况下，必须先创建设置。 按照[用于配置组设置的 Azure Active Directory cmdlet](../enterprise-users/groups-settings-cmdlets.md) 中的步骤为此 Azure AD 组织创建组设置。

1. 下一步，显示当前组设置。

    ```PowerShell
    $Setting.Values
    ```

1. 然后启用此功能：

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. 接着保存更改并应用设置：

    ```PowerShell
    New-AzureADDirectorySetting -DirectorySetting $setting
    ```

还需要将敏感度标签与 Azure AD 同步。 有关说明，请参阅[如何为容器启用敏感度标签以及同步标签](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels)。

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>在 Azure 门户中向新组分配标签

1. 登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 依次选择“组”、“新建组”。 
1. 在“新建组”页面上，选择“Office 365”，填写新组所需的信息，然后从列表中选择一个敏感度标签。

   ![在“新建组”页面中分配敏感度标签](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 保存更改，然后选择“创建”。

组即已创建，并且将自动强制实施与选定标签关联的站点和组设置。

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>向 Azure 门户中的现有组分配标签

1. 使用组管理员帐户或以组所有者身份登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择“组”。
1. 在“所有组”页面中，选择要添加标签的组。
1. 在选定组的页面上，选择“属性”，然后从列表中选择一个敏感度标签。

   ![在组的“概述”页面上分配敏感度标签](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. 选择“保存”以保存更改。

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>从 Azure 门户中的现有组删除标签

1. 使用全局管理员或组管理员帐户或以组所有者身份登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择“组”。
1. 从“所有组”页面中，选择要从中删除标签的组。
1. 在“组”页面上，选择“属性”。
1. 选择“删除” 。
1. 单击“保存”应用所做的更改。

## <a name="using-classic-azure-ad-classifications"></a>使用经典 Azure AD 分类

启用此功能后，组的“经典”分类将仅显示现有组和站点，并且仅当在不支持敏感度标签的应用中创建组时，才应将其用于新组。 稍后管理员可以根据需要将其转换为敏感度标签。 经典分类是指通过在 Azure AD PowerShell 中为 `ClassificationList` 设置定义值来设置的旧分类。 启用此功能后，这些分类将不会应用于组。

## <a name="troubleshooting-issues"></a>对问题进行故障排除

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>敏感度标签不适用于对组的分配

仅当满足以下所有条件时，才会为组显示敏感度标签选项：

1. 标签在此 Azure AD 组织的 Microsoft 365 合规中心发布。
1. 启用此功能后，Azure AD PowerShell 模块中的 EnableMIPLabels 将设置为 True。
1. 在安全与合规 PowerShell 模块中，使用 Execute-AzureAdLabelSync cmdlet 将标签与 Azure AD 同步。
1. 组为 Microsoft 365 组。
1. 组织具有活动的 Azure Active Directory Premium P1 许可证。
1. 当前登录的用户具有足够的权限来分配标签。 用户必须是全局管理员、组管理员或组所有者。

请确保满足所有条件，以便将标签分配给组。

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>列表中没有要分配的标签

如果你需要的标签不在列表中，可能的原因如下：

- 此标签可能未在 Microsoft 365 合规中心发布。 这也适用于不再发布的标签。 有关详细信息，请与管理员联系。
- 标签已发布，但不适用于已登录的用户。 有关如何获取标签访问权限的详细信息，请咨询管理员。

### <a name="how-to-change-the-label-on-a-group"></a>如何更改组的标签

可以使用与向现有组分配标签的相同步骤随时交换标签，如下所示：

1. 使用全局或组管理员帐户或以组所有者身份登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择“组”。
1. 在“所有组”页面中，选择要添加标签的组。
1. 在选定组的页面上，选择“属性”，然后从列表中选择一个新的敏感度标签。
1. 选择“保存”。

### <a name="group-setting-changes-to-published-labels-arent-updated-on-the-groups"></a>对已发布标签的组设置所做的更改不会在组中更新

当你在 [Microsoft 365 合规中心](https://sip.protection.office.com/homepage)更改已发布的标签的组设置时，这些策略更改不会自动应用于带标签的组。 发布敏感度标签并将其应用到组后，Microsoft 建议不要在 Microsoft 365 合规中心更改该标签的组设置。

如果必须进行更改，请使用 [Azure AD PowerShell 脚本](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)将更新手动应用于受影响的组。 此方法可确保所有现有组都强制实施新的设置。

## <a name="next-steps"></a>后续步骤

- [在 Microsoft Teams、Microsoft 365 组和 SharePoint 站点中使用敏感度标签](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [更改标签策略后使用 Azure AD PowerShell 脚本手动更新组](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [编辑组设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [使用 PowerShell 命令管理组](../enterprise-users/groups-settings-v2-cmdlets.md)
