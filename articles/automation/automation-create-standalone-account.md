---
title: 创建独立的 Azure 自动化帐户
description: 本文介绍如何创建独立的 Azure 自动化帐户和经典运行方式帐户。
services: automation
ms.subservice: process-automation
ms.date: 07/24/2021
ms.topic: conceptual
ms.openlocfilehash: f60c6d08f3ab09a3900203cbebc49477d5c84f0a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812621"
---
# <a name="create-a-standalone-azure-automation-account"></a>创建独立的 Azure 自动化帐户

本文介绍如何使用 Azure 门户创建 Azure 自动化帐户。 可使用自动化帐户来评估和了解自动化，无需使用其他管理功能，也无需与 Azure Monitor 日志集成。 可在以后随时添加管理功能或者与 Azure Monitor 日志集成，对 Runbook 作业进行高级监视。

使用自动化帐户，可以对在 Azure 资源管理器部署或经典部署中管理资源的 Runbook 进行身份验证。 一个自动化帐户可以跨所有区域和订阅管理给定租户的资源。

在 Azure 门户中创建自动化帐户时，会自动创建运行方式帐户。 此帐户执行以下任务：

* 在 Azure Active Directory (Azure AD) 中创建服务主体。
* 创建证书。
* 分配“参与者”角色，以便使用 Runbook 管理 Azure 资源管理器资源。

使用系统创建的此帐户，可以快速开始构建和部署 Runbook 来支持自动化需求。

## <a name="permissions-required-to-create-an-automation-account"></a>创建自动化帐户所需的权限

若要创建或更新自动化帐户，并完成本文所述的任务，必须具有以下特权和权限：

* 若要创建自动化帐户，必须将 Azure AD 用户帐户添加到一个角色，该角色的权限相当于 `Microsoft.Automation` 资源的所有者角色。 有关详细信息，请参阅 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)。
* 在 Azure 门户的“Azure Active Directory” > “管理” > “用户设置”下，如果“应用注册”设置为“是”，则 Azure AD 租户中的非管理员用户可以[注册 Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions)。 如果“应用注册”设置为“否”，则执行此操作的用户必须至少是 Azure AD 中的“应用开发人员”角色的成员 。

如果你在被添加到订阅的全局管理员/共同管理员角色之前不是订阅的 Active Directory 实例成员，则你将作为来宾添加到 Active Directory。 在这种情况下，“添加自动化帐户”页中会显示此消息：`You do not have permissions to create.`

如果用户已添加到全局管理员/共同管理员角色，则你可先从订阅的 Active Directory 实例中删除该用户。 然后将其重新添加到 Active Directory 中的用户角色。 若要验证用户角色，请执行以下操作：

1. 在 Azure 门户中，转到 Azure Active Directory 页面。
1. 选择“用户和组”  。
1. 选择“所有用户”。
1. 选择特定的用户后，选择“配置文件”。 用户配置文件下的“用户类型”属性值不应为“来宾”。

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>在 Azure 门户中创建新的自动化帐户

若要在 Azure 门户中创建 Azure 自动化帐户，请完成以下步骤：

1. 使用帐户登录到 Azure 门户，该帐户应为订阅管理员角色的成员，并且是订阅的共同管理员。
1. 选择“+ 创建资源”。
1. 搜索“自动化”。 在搜索结果中，选择“自动化”。

   ![在 Azure 市场中搜索并选择“自动化和控制”](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. 在下一个屏幕上，选择“新建”。

   ![添加自动化帐户](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > 如果“添加自动化帐户”页中显示以下消息，则表示你的帐户不是订阅管理员角色和订阅的共同管理员的成员。
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-without-perms.png" alt-text="提示“你无权在 Azure Active Directory 中创建运行方式帐户”的屏幕截图。":::

1. 在“添加自动化帐户”页的“名称”字段中，输入新的自动化帐户的名称 。 选择后，将无法更改此名称。 

    > [!NOTE]
    > 自动化帐户名称在每个区域和资源组中是唯一的。 已删除的自动化帐户的名称可能无法立即可用。

1. 如果有多个订阅，请使用“订阅”字段指定用于新帐户的订阅。
1. 对于“资源组”，请输入或选择新的或现有的资源组。
1. 对于“位置”，请选择一个 Azure 数据中心位置。
1. 对于“创建 Azure 运行方式帐户”选项，请确保选择“是”，然后单击“创建”  。

   > [!NOTE]
   > 如果对“创建 Azure 运行方式帐户”选择“否”以选择不创建运行方式帐户，则“添加自动化帐户”页中会显示一条消息  。 尽管该帐户是在 Azure 门户中创建的，但它在经典部署模型订阅或 Azure 资源管理器订阅目录服务中没有对应的身份验证标识。 因此，自动化帐户无法访问订阅中的资源。 这会导致引用此帐户的任何 Runbook 无法进行身份验证，也无法针对这些部署模型中的资源执行任务。
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-decline-create-runas-msg.png" alt-text="带有“你已选择不创建运行方式帐户”消息的提示的屏幕截图。":::
   >
   > 未创建服务主体时不会分配参与者角色。
   >

1. 若要跟踪自动化帐户的创建进度，请在菜单中选择“通知”。

成功创建自动化帐户后，系统会自动创建几个资源。 创建后，如果不想保留这些 runbook，可以放心地将其删除。 运行方式帐户可用于对 runbook 中的帐户进行身份验证，除非创建其他运行方式帐户或不需要它们，否则应将其保留。 下表汇总了运行方式帐户的资源。

| 资源 | 说明 |
| --- | --- |
| AzureAutomationTutorial Runbook |一个示例图形 Runbook，演示如何使用运行方式帐户进行身份验证。 该 Runbook 获取所有资源管理器资源。 |
| AzureAutomationTutorialScript Runbook |一个示例 PowerShell Runbook，演示如何使用运行方式帐户进行身份验证。 该 Runbook 获取所有资源管理器资源。 |
| AzureAutomationTutorialPython2 Runbook |一个示例 Python Runbook，演示如何使用运行方式帐户进行身份验证。 该 Runbook 列出订阅中的所有资源组。 |
| AzureRunAsCertificate |在创建自动化帐户时自动创建的，或针对现有帐户使用 PowerShell 脚本创建的证书资产。 使用该证书可向 Azure 进行身份验证，以便通过 Runbook 管理 Azure 资源管理器资源。 此证书有一年的有效期。 |
| AzureRunAsConnection |在创建自动化帐户时自动创建的，或针对现有帐户使用 PowerShell 脚本创建的连接资产。 |

## <a name="create-a-classic-run-as-account"></a>创建经典运行方式帐户

默认情况下，在创建 Azure 自动化帐户时不会创建经典运行方式帐户。 若需使用经典运行方式帐户管理 Azure 经典资源，请执行以下步骤：

1. 在“自动化帐户”的“帐户设置”下，选择“运行方式帐户” 。
2. 选择“Azure 经典运行方式帐户”。
3. 单击“创建”，继续创建经典运行方式帐户。

## <a name="next-steps"></a>后续步骤

* 若要详细了解图形创作，请参阅[在 Azure 自动化中创作图形 Runbook](automation-graphical-authoring-intro.md)。
* 若要开始使用 PowerShell Runbook，请参阅[教程：创建 PowerShell Runbook](./learn/powershell-runbook-managed-identity.md)。
* 若要开始使用 PowerShell 工作流 Runbook，请参阅[教程：创建 PowerShell 工作流 Runbook](learn/automation-tutorial-runbook-textual.md)。
* 若要开始使用 Python 3 Runbook，请参阅[教程：创建 Python 3 Runbook](learn/automation-tutorial-runbook-textual-python-3.md)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation)。