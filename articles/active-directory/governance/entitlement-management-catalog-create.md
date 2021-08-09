---
title: 在权利管理中创建和管理资源目录 - Azure AD
description: 了解如何在 Azure Active Directory 权利管理中创建资源和访问包的新容器。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 394565c857320c8fd94d72a0ca15358c83b0d09d
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109714379"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中创建和管理资源目录

## <a name="create-a-catalog"></a>创建目录

目录是资源和访问包的容器。 需要将相关的资源和访问包分组时，可以创建目录。 创建目录的任何人将成为第一个目录所有者。 目录所有者可以添加其他目录所有者。

**必备角色**：全局管理员、标识治理管理员、用户管理员或目录创建者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击“目录”。

    ![Azure 门户中的权利管理目录](./media/entitlement-management-catalog-create/catalogs.png)

1. 单击“新建目录”。

1. 输入目录的唯一名称，并提供说明。

    用户将在访问包的详细信息中看到此信息。

1. 如果你希望在创建此目录中的访问包后，用户可立即请求这些访问包，请将“启用”设置为“是”。

1. 如果希望允许选定外部目录中的用户请求此目录中的访问包，请将“为外部用户启用”设置为“是”。

    ![“新建目录”窗格](./media/entitlement-management-shared/new-catalog.png)

1. 单击“创建”以创建该目录。

### <a name="creating-a-catalog-programmatically"></a>以编程方式创建目录

还可以使用 Microsoft Graph 创建目录。  通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，相应角色中的用户可以调用 API 来[创建 accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true)。

## <a name="add-resources-to-a-catalog"></a>将资源添加到目录

若要在访问包中包含资源，这些资源必须存在于目录中。 可添加的资源类型包括组、应用程序和 SharePoint Online 站点。 组可以是云创建的 Microsoft 365 组，或者云创建的 Azure AD 安全组。 应用程序可以是 Azure AD 企业应用程序，包括 SaaS 应用程序，以及你自己的已联合到 Azure AD 的应用程序。 站点可以是 SharePoint Online 站点或 SharePoint Online 站点集合。

**必备角色：** 请参阅 [将资源添加到目录所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“目录”，然后打开要将资源添加到的目录。

1. 在左侧菜单中单击“资源”。

1. 单击“添加资源”。

1. 单击某个资源类型：“组和团队”、“应用程序”或“SharePoint 站点”。  

    如果未看到所要添加的资源或无法添加资源，请确保具有所需的 Azure AD 目录角色和权利管理角色。 可能需要求助某个具有所需角色的人员将资源添加到目录。 有关详细信息，请参阅[将资源添加到目录所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

1. 选择一个或多个要添加到目录中的所需类型的资源。

    ![将资源添加到目录](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 完成后，单击“添加”。

    现在，可将这些资源包含在目录中的访问包内。

### <a name="add-a-multi-geo-sharepoint-site-preview"></a>添加多地理位置 SharePoint 站点（预览版）

1. 若已为 SharePoint 启用[多地域](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)，请选择想从中选择站点的环境。
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="访问包 - 添加资源角色 - 选择 SharePoint 多地域站点":::

1. 然后选择要添加到目录中的站点。 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>以编程方式将资源添加到目录

还可以使用 Microsoft Graph 将资源添加到目录。  通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，相应角色中的用户或目录和资源所有者可以调用 API 来[创建 accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true)。

## <a name="remove-resources-from-a-catalog"></a>从目录中删除资源

可以从目录中删除资源。 如果资源未在目录的任何访问包中使用时，才能从该目录中删除该资源。

**必备角色：** 请参阅 [将资源添加到目录所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“目录”，然后打开要从中删除资源的目录。

1. 在左侧菜单中单击“资源”。

1. 选择要删除的资源。

1. 单击“删除”（或单击省略号 (**...**)，然后单击“删除资源”）。


## <a name="add-additional-catalog-owners"></a>添加其他目录所有者

创建了目录的用户将成为第一个目录所有者。 若要委托目录的管理，请将用户添加到目录所有者角色。 这有助于共享目录管理责任。 

遵循以下步骤将用户分配到目录所有者角色：

**必备角色**：全局管理员、标识治理管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“目录”，然后打开要将管理员添加到的目录。

1. 在左侧菜单中，单击“角色和管理员”。

    ![目录角色和管理员](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 单击“添加所有者者”，以选择这些角色的成员。

1. 单击“选择”以添加这些成员。

## <a name="edit-a-catalog"></a>编辑目录

可以编辑目录的名称和说明。 用户将在访问包的详细信息中看到此信息。

**必备角色**：全局管理员、标识治理管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“目录”，然后打开要编辑的目录。

1. 在该目录的“概述”页上单击“编辑”。

1. 编辑目录的名称、说明或启用的设置。

    ![编辑目录设置](./media/entitlement-management-shared/catalog-edit.png)

1. 单击“保存”。

## <a name="delete-a-catalog"></a>删除目录

可以删除目录，但前提是它不包含任何访问包。

**必备角色**：全局管理员、标识治理管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“目录”，然后打开要删除的目录。

1. 在该目录的“概述”中单击“删除”。

1. 在出现的消息框中单击“是”。

### <a name="deleting-a-catalog-programmatically"></a>以编程方式删除目录

还可以使用 Microsoft Graph 删除目录。  通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，相应角色中的用户可以调用 API 来[删除 accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true)。

## <a name="next-steps"></a>后续步骤

- [将访问权限管理委托给访问包管理者](entitlement-management-delegate-managers.md)
