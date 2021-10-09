---
title: 在权利管理中创建和管理资源目录 - Azure AD
description: 了解如何在 Azure Active Directory 权利管理中创建资源和访问包的新容器。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: ''
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 8/31/2021
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9540c207388dfc5b37e2dd03939b14753c3d8ec7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791776"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中创建和管理资源目录

本文介绍如何在 Azure Active Directory (Azure AD) 权利管理中创建与管理资源和访问包的目录。

## <a name="create-a-catalog"></a>创建目录

目录是资源和访问包的容器。 需要将相关的资源和访问包分组时，可以创建目录。 创建目录的任何人将成为第一个目录所有者。 目录所有者可以添加其他目录所有者。

必备角色：全局管理员、Identity Governance 管理员、用户管理员或目录创建者

> [!NOTE]
> 用户如果已分配有“用户管理员”角色，则将无法再在其未拥有的目录中创建目录或管理访问包。 如果组织中的用户已分配有“用户管理员”角色，以配置目录、访问包或权利管理中的策略，则应为这些用户分配“Identity Governance 管理员”角色。

若要创建目录，请执行以下操作：

1. 在 Azure 门户中，选择“Azure Active Directory” > “Identity Governance” 。

1. 在左侧菜单中选择“目录”。

    ![显示 Azure 门户中的权利管理目录的屏幕截图。](./media/entitlement-management-catalog-create/catalogs.png)

1. 选择“新建目录”。

1. 输入目录的唯一名称，并提供说明。

    用户将在访问包的详细信息中看到此信息。

1. 如果你希望此目录中的访问包在创建后立即可供用户请求，请将“启用”设置为“是” 。

1. 如果希望允许选定外部目录中的用户请求此目录中的访问包，请将“为外部用户启用”设置为“是”。

    ![显示“新建目录”窗格的屏幕截图。](./media/entitlement-management-shared/new-catalog.png)

1. 选择“创建”以创建该目录。

## <a name="create-a-catalog-programmatically"></a>以编程方式创建目录

可通过两种编程方式创建目录。

### <a name="create-a-catalog-with-microsoft-graph"></a>使用 Microsoft Graph 创建目录

可以使用 Microsoft Graph 创建目录。 对于具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，或具有该应用程序权限的应用程序，如果用户在其中具有相应角色，则可调用 API 来[创建 accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true)。

### <a name="create-a-catalog-with-powershell"></a>使用 PowerShell 创建目录

还可使用[适用于 Identity Governance 的 Microsoft Graph PowerShell cmdlet](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 模块 1.6.0 或更高版本中的 `New-MgEntitlementManagementAccessPackageCatalog` cmdlet 在 PowerShell 中创建目录。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>将资源添加到目录

若要在访问包中包含资源，这些资源必须存在于目录中。 可添加的资源类型包括组、应用程序和 SharePoint Online 站点。 例如：

* 组可以是云创建的 Microsoft 365 组，或者云创建的 Azure AD 安全组。 无法将源自于本地 Active Directory 的组分配为资源，因为无法在 Azure AD 中更改其所有者或成员属性。 在 Azure AD 中也无法修改作为通讯组源自于 Exchange Online 的组。
* 应用程序可以是 Azure AD 企业应用程序，包括服务型软件 (SaaS) 应用程序，以及你自己的与 Azure AD 集成的应用程序。 有关如何为具有多个角色的应用程序选择适当资源的详细信息，请参阅[添加资源角色](entitlement-management-access-package-resources.md#add-resource-roles)。
* 站点可以是 SharePoint Online 站点或 SharePoint Online 站点集合。

必备角色：请参阅[将资源添加到目录所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

若要将资源添加到目录，请执行以下操作：

1. 在 Azure 门户中，选择“Azure Active Directory” > “Identity Governance” 。

1. 在左侧菜单中选择“目录”，然后打开要将资源添加到的目录。

1. 在左侧菜单中选择“资源”。

1. 选择“添加资源”。

1. 选择资源类型“组和团队”、“应用程序”或“SharePoint 站点”  。

    如果看不到想要添加的资源或无法添加资源，请确保具有所需的 Azure AD 目录角色和权利管理角色。 可能需要求助某个具有所需角色的人员将资源添加到目录。 有关详细信息，请参阅[将资源添加到目录所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

1. 选择一个或多个要添加到目录中的所需类型的资源。

    ![显示“将资源添加到目录”窗格的屏幕截图。](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 完成后，选择“添加”。

    现在，可将这些资源包含在目录中的访问包内。

### <a name="add-resource-attributes-preview-in-the-catalog"></a>在目录中添加资源属性（预览版）

属性是请求者在提交访问请求之前需要回答的必填字段。 他们为这些属性提供的回答将显示给审批者，并且会标记在 Azure AD 中的用户对象上。 

> [!NOTE]
>在资源上设置的所有属性都需要一个答案，然后对包含该资源的访问包的请求才能提交。 如果请求者不提供答案，将不会处理其请求。

若要要求为访问请求提供属性，请执行以下操作：

1. 在左侧菜单中选择“资源”，此时将显示目录中的资源列表。 

1. 选择要添加属性的资源旁边的省略号，然后选择“需要属性(预览版)”。 

    ![显示选择“需要属性(预览版)”的屏幕截图。](./media/entitlement-management-catalog-create/resources-require-attributes.png)
 
1.  选择属性类型：

    1. “内置”包括 Azure AD 用户配置文件属性。
    1. “目录架构扩展”提供一种在 Azure AD 中存储关于用户对象和其他目录对象的其他数据的方法。 这包括组、租户详细信息和服务主体。 仅用户对象上的扩展属性可用于向应用程序发出声明。
1. 如果选择“内置”，请从下拉列表中选择一个属性。 如果选择“目录架构扩展”，请在文本框中输入属性名称。

    > [!NOTE]
    > 只能为非管理员用户更新 User.mobilePhone 属性。 在[此网站](/graph/permissions-reference#remarks-5)上了解详细信息。

1.  选择你希望请求者为其答案使用的答案格式。 答案格式包括“短文本”、“多选”和“长文本”  。

1.  如果选择“多选”，请选择“编辑和本地化”以配置答案选项。 
    1. 在显示的“查看/编辑问题”窗格中，在“答案值”框中输入你希望在请求者回答问题时向其提供的响应选项 。
    1. 选择响应选项的语言。 如果选择其他语言，则可将响应选项本地化。
    1. 输入所需数量的响应，然后选择“保存”。

1. 如果希望在直接分配和自助服务请求期间可编辑属性值，请选择“是”。

    > [!NOTE]
    > ![显示使属性可编辑的屏幕截图。](./media/entitlement-management-catalog-create/attributes-are-editable.png)
    > - 如果在“属性值可编辑”框中选择“否”，并且属性值为空，则用户可以输入该属性的值 。 该值在保存后无法编辑。 
    > - 如果在“属性值可编辑”框中选择“否”，并且属性值不为空，则用户将无法在直接分配和自助式请求期间编辑预先存在的值 。
 
    ![显示添加本地化内容的屏幕截图。](./media/entitlement-management-catalog-create/add-attributes-questions.png)

1.  如果你要添加本地化内容，请选择“添加本地化内容”。

    1. 在“为问题添加本地化内容”窗格中，为要本地化与选定属性相关的问题的语言选择语言代码。
    1. 使用配置的语言，在“本地化文本”框中输入问题。
    1. 添加所有所需的本地化内容后，选择“保存”。

       ![显示保存本地化内容的屏幕截图。](./media/entitlement-management-catalog-create/attributes-add-localization.png)

1.  在“需要属性(预览版)”页上填写所有属性信息后，选择“保存” 。

### <a name="add-a-multi-geo-sharepoint-site"></a>添加多地域 SharePoint 站点

1. 如果已为 SharePoint 启用[多地域](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)，请选择要从中选择站点的环境。
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="显示“选择 SharePoint Online 站点”窗格的屏幕截图。":::

1. 然后选择要添加到目录中的站点。

### <a name="add-a-resource-to-a-catalog-programmatically"></a>以编程方式将资源添加到目录

还可以使用 Microsoft Graph 将资源添加到目录。 通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，相应角色中的用户或目录和资源所有者可以调用 API 来[创建 accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true)。 但是，具有应用程序权限的应用程序尚无法在请求时没有用户上下文的情况下以编程方式添加资源。

## <a name="remove-resources-from-a-catalog"></a>从目录中删除资源

可以从目录中删除资源。 仅当资源未在目录的任何访问包中使用时，才能从此目录中删除该资源。

必备角色：请参阅[将资源添加到目录所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

若要从目录中删除资源，请执行以下操作：

1. 在 Azure 门户中，选择“Azure Active Directory” > “Identity Governance” 。

1. 在左侧菜单中选择“目录”，然后打开要从中删除资源的目录。

1. 在左侧菜单中选择“资源”。

1. 选择要删除的资源。

1. 选择“删除” 。 或者，可以选择省略号 (...)，然后选择“删除资源” 。

## <a name="add-more-catalog-owners"></a>添加更多目录所有者

创建了目录的用户将成为第一个目录所有者。 若要委托目录的管理，请将用户添加到目录所有者角色。 添加更多目录所有者有助于分担目录管理责任。

必备角色：全局管理员、Identity Governance 管理员、用户管理员或目录所有者

若要将用户分配到目录所有者角色，请执行以下操作：

1. 在 Azure 门户中，选择“Azure Active Directory” > “Identity Governance” 。

1. 在左侧菜单中选择“目录”，然后打开要将管理员添加到的目录。

1. 在左侧菜单中，选择“角色和管理员”。

    ![显示目录角色和管理员的屏幕截图。](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 选择“添加所有者”，以选择这些角色的成员。

1. 单击“选择”以添加这些成员。

## <a name="edit-a-catalog"></a>编辑目录

可以编辑目录的名称和说明。 用户将在访问包的详细信息中看到此信息。

必备角色：全局管理员、Identity Governance 管理员、用户管理员或目录所有者

若要编辑目录，请执行以下操作：

1. 在 Azure 门户中，选择“Azure Active Directory” > “Identity Governance” 。

1. 在左侧菜单中选择“目录”，然后打开要编辑的目录。

1. 在该目录的“概述”页上选择“编辑” 。

1. 编辑目录的名称、说明或启用的设置。

    ![显示编辑目录设置的屏幕截图。](./media/entitlement-management-shared/catalog-edit.png)

1. 选择“保存”。

## <a name="delete-a-catalog"></a>删除目录

可以删除目录，但前提是它不包含任何访问包。

必备角色：全局管理员、Identity Governance 管理员、用户管理员或目录所有者

若要删除目录，请执行以下操作：

1. 在 Azure 门户中，选择“Azure Active Directory” > “Identity Governance” 。

1. 在左侧菜单中选择“目录”，然后打开要删除的目录。

1. 在该目录的“概述”页上选择“删除” 。

1. 在出现的消息框中选择“是”。

### <a name="delete-a-catalog-programmatically"></a>以编程方式删除目录

还可以使用 Microsoft Graph 删除目录。 通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，相应角色中的用户可以调用 API 来[删除 accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true)。

## <a name="next-steps"></a>后续步骤

[将访问权限管理委托给访问包管理者](entitlement-management-delegate-managers.md)