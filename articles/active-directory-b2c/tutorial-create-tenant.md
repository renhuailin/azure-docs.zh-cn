---
title: 教程 - 创建 Azure Active Directory B2C 租户
description: 按照本教程进行操作，了解如何通过使用 Azure 门户创建 Azure Active Directory B2C 租户来准备注册应用程序。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/25/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b2d3cb2eadc86ec04cca6561777a44c4700403eb
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081465"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教程：创建 Azure Active Directory B2C 租户

必须先在你管理的租户中注册应用程序，然后这些应用程序才能与 Azure Active Directory B2C (Azure AD B2C) 交互。 

> [!NOTE]
> 每个订阅最多可创建 20 个租户。 此限制有助于防范对资源的威胁（例如拒绝服务攻击），并在 Azure 门户和基础租户创建 API 中强制实施。 如果需要创建超过 20 个租户，请联系 [Microsoft 支持部门](support-options.md)。
> 
> 如果要重用以前尝试删除的租户名称，但在输入域名时看到错误“已被另一个目录使用”，则需要[按照以下步骤先完全删除租户](./faq.yml?tabs=app-reg-ga#how-do-i-delete-my-azure-ad-b2c-tenant-)。 至少需要订阅管理员的角色。 删除租户后，你可能还需要先注销并重新登录，然后才能重用域名。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅
> * 切换到包含 Azure AD B2C 租户的目录
> * 在 Azure 门户中将 Azure AD B2C 资源添加为“收藏夹”中的项

在下一个教程中，学习如何注册应用程序。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 需要使用至少在订阅中或在订阅的某资源组中分配有[参与者](../role-based-access-control/built-in-roles.md)角色的 Azure 帐户。 

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

1. 登录 [Azure 门户](https://portal.azure.com/)。 

1. 选择包含你的订阅的目录：
    1. 在 Azure 门户工具栏中，选择“目录 + 订阅”筛选器图标。 
    
    ![“目录 + 订阅”筛选器图标](media/tutorial-create-tenant/directories-subscription-filter-icon.png)

    1. 找到包含你的订阅的目录，然后选择其旁边的“切换”按钮。 切换目录会重新加载门户。

    ![带有“切换”按钮的“目录 + 订阅”](media/tutorial-create-tenant/switch-directory.png)

1. 将 Microsoft.AzureActiveDirectory 添加为所使用 Azure 订阅的资源提供程序（[了解详细信息](../azure-resource-manager/management/resource-providers-and-types.md?WT.mc_id=Portal-Microsoft_Azure_Support#register-resource-provider-1)）：

    1. 在 Azure 门户菜单或主页上，选择“订阅” 。
    2. 选择你的订阅，然后在左侧菜单中选择“资源提供程序”。
    3. 确保 Microsoft.AzureActiveDirectory 行显示“已注册”状态 。 如果不是，请选择该行，然后选择“注册”。

1. 在 Azure 门户菜单或“主页”页上，选择“创建资源” 。

   ![选择“创建资源”按钮](media/tutorial-create-tenant/create-a-resource.png)

1. 搜索“Azure Active Directory B2C”，然后选择“创建” 。
2. 选择“创建新的 Azure AD B2C 租户”。

    ![创建在 Azure 门户中选择的新 Azure AD B2C 租户](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. 在“创建目录”页面上，输入以下内容：

   - **组织名称** - 输入 Azure AD B2C 租户的名称。
   - **初始域名** - 输入 Azure AD B2C 租户的域名。
   - **国家或地区**：从列表中选择你的国家或地区。 此选择以后无法更改。
   - **订阅** - 从列表中选择你的订阅。
   - **资源组** - 选择或搜索将包含租户的资源组。

    ![在 Azure 门户中创建包含示例值的租户窗体](media/tutorial-create-tenant/review-and-create-tenant.png)

1. 选择“查看 + 创建”。
1. 查看目录设置。 然后选择“创建”。 详细了解如何[排查部署错误](../azure-resource-manager/templates/common-deployment-errors.md)。

可以将多个 Azure AD B2C 租户链接到单个 Azure 订阅以进行计费。 若要链接租户，你必须是 Azure AD B2C 租户中的管理员，并且在 Azure 订阅中分配有至少一个参与者角色。 请参阅[将 Azure AD B2C 租户链接到订阅](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)。

## <a name="select-your-b2c-tenant-directory"></a>选择 B2C 租户目录

若要开始使用新的 Azure AD B2C 租户，需要切换到包含该租户的目录：
1. 在 Azure 门户工具栏中，选择“目录 + 订阅”筛选器图标。
1. 在“所有目录”选项卡上，找到包含 Azure AD B2C 租户的目录，然后选择其旁边的"切换"按钮 。

如果一开始未在列表中看到新的 Azure B2C 租户，请刷新浏览器窗口，或者注销后再重新登录。 然后在 Azure 门户工具栏中，再次选择“目录 + 订阅”筛选器。


## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>将 Azure AD B2C 添加为收藏项（可选）

此可选步骤使你更容易在以下及所有后续教程中选择 Azure AD B2C 租户。

不用在每次想使用租户时在所有服务中搜索 Azure AD B2C，而是可以将该资源纳入收藏夹。 然后，可以从门户菜单的“收藏夹”部分中选择它，快速浏览到 Azure AD B2C 租户。

只需执行此操作一次。 在执行这些步骤之前，确保已切换到包含 Azure AD B2C 租户的目录，如前一节[选择 B2C 租户目录](#select-your-b2c-tenant-directory)中所述。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户菜单中，选择“所有服务”。
1. 在“所有服务”搜索框中，搜索“Azure AD B2C”，将鼠标悬停在搜索结果上，然后选择工具提示中的星形图标 。 Azure AD B2C 现在显示在 Azure 门户的“收藏夹”下 。
1. 如果要更改新收藏内容的位置，转到 Azure 门户菜单，选择 Azure AD B2C，然后将其上下拖动到所需位置。

    ![Azure AD B2C、收藏夹菜单、Microsoft Azure 门户](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅
> * 切换到包含 Azure AD B2C 租户的目录
> * 在 Azure 门户中将 Azure AD B2C 资源添加为“收藏夹”中的项

接下来，了解如何在新租户中注册 web 应用程序。

> [!div class="nextstepaction"]
> [注册应用程序 >](tutorial-register-applications.md)