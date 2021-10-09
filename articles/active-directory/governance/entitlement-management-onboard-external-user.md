---
title: 教程 - 通过审批流程将外部用户加入 Azure AD - Azure Active Directory
description: 分步教程，其中介绍了如何为需要在 Azure Active Directory 权利管理中进行审批的外部用户创建访问包。
services: active-directory
documentationCenter: ''
author: sama
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 08/18/2021
ms.author: sama
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e0a0d9bf53192249b50f03df508e86cc88b91e
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155960"
---
# <a name="tutorial---onboard-external-users-to-azure-ad-through-an-approval-process"></a>教程 - 通过审批流程将外部用户加入 Azure AD

可以将权利管理作为加入外部用户的方式。 此功能允许外部用户请求对一组资源的访问权限，并且你可以在用户对目录获得访问权限之前设置审批。 对于通过权利管理加入的外部用户，可以使用访问包来管理他们的生命周期。 外部用户的最后一个访问包过期时，将从目录中删除这些用户。

本教程假设你在 WoodGrove Bank 担任 IT 管理员。 公司要求你创建访问包，以便加入业务组与之合作的外部组织中的合作伙伴。 他们需要访问名为“外部协作”的 Teams 组。 协作组织的内部发起人需要进行审批。 此外，你获知合作伙伴的访问权限需要在 60 天后过期。
若要使用 Azure AD 权利管理，必须具有以下某个许可证：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5 许可证

有关详细信息，请参阅[许可证要求](entitlement-management-overview.md#license-requirements)。

## <a name="step-1-configure-basics"></a>步骤 1：配置基础信息

**必备角色：** 全局管理员、标识治理管理员、用户管理员、目录所有者或访问包管理员

1. 在 Azure 门户的左侧导航栏中，单击“Azure Active Directory”。 

2. 在左侧菜单中，单击“标识监管”。

3. 在左侧菜单中，单击“访问包”。  如果看到“访问被拒绝”，请确认目录中是否存在 Azure AD Premium P2 许可证  。

4. 单击“新建访问包”。 

5. 在“基本信息”选项卡上，输入名称“外部用户包”和描述“外部用户的访问权限待审批”  。

6. 可以将“目录”下拉列表的设置保留为“常规” 。

## <a name="step-2-configure-resources"></a>步骤 2：配置资源

1. 单击“下一步”打开“资源角色”选项卡。  
 
   在此选项卡上，选择要包含在访问包中的资源和资源角色。

2. 单击“组和团队”，然后搜索组“外部协作” 。

## <a name="step-3-configure-requests"></a>步骤 3：配置请求

1. 单击“下一步”，打开“请求”选项卡   。

   在此选项卡上，你将创建请求策略。 策略定义有关访问某个访问包的规则或准则。  请创建一个使资源目录中的特定用户能够请求此访问包的策略。

2. 在“可以请求访问权限的用户”部分中，单击“对于不在目录中的用户”，然后单击“所有用户(所有连接的组织 + 任何新的外部用户)”  。

3. 确保将“需要审批”设置为“是” 。

4. 以下设置允许配置如何对外部用户进行审批：

5. 对于“需要请求者理由”，请保留为“是” 。

6. 对于“多少个阶段”，请将其保留为“1” 。

7. 对于“审批者”场景，请选择“内部发起人” 。 此选项来自配置的 [已连接组织](entitlement-management-organization.md)，可以在其中赞助与之合作的特定组织。 这样，便可以将组织内已连接组织中指定的某人设置为审批者。 

8. 对于“必须在多少天内做出决定?”，请将其保留为“14” 。

9. 对于“需要审批者理由”，请保留为“是” 。

10. 将“启用新请求和分配”设置为“是”，从而使访问包在创建后立即可以向其发出请求 。

## <a name="step-4-configure-requestor-information"></a>步骤 4：配置请求者信息

1. 单击“下一步”，打开“请求者信息”选项卡 

2. 在此屏幕上，可以提出更多问题，以从请求者处收集更多信息。 这些问题显示在请求表单上，可以设置为“必需”或“可选”。 目前可以暂时留空。

## <a name="step-5-configure-lifecycle"></a>步骤 5：配置生命周期

1. 单击“下一步”，打开“生命周期”选项卡 

2. 在“过期时间”部分，将“访问包分配过期时间”设置为“天数”  。

3. 请将“分配过期时间”设置为 60 天 。 此字段用于确定来宾用户必须续订其访问权限的时间。

4. 还可以配置“访问评审”，从而定期检查来宾是否仍然需要访问访问包。 评审可以是自我评审，你也可以为此任务设置特定的评审。 有关详细信息，请参阅[访问评审](entitlement-management-access-reviews-create.md)。

## <a name="step-6-review-and-create-your-access-package"></a>步骤6：查看和创建访问包

1. 单击“下一步”打开“查看 + 创建”选项卡。  

2. 在此屏幕上，可以在创建访问包之前查看其配置。 如果有任何问题，可以使用选项卡导航到创建体验中的特定点进行编辑。

3. 确认选择后，单击“创建”。 几分钟后，应会看到一条通知指出已成功创建访问包。

4. 创建后，将转至访问包的“概述”页。 可在此处找到并复制“我的访问门户”链接。 与外部用户共享此链接，他们可以请求此包以便开始协作。

## <a name="step-7-clean-up-resources"></a>步骤 7：清理资源

在此步骤中，你可以删除“外部用户包”访问包。 

必备角色：全局管理员、标识治理管理员或访问包管理员

1. 在 Azure 门户的左侧导航栏中，单击“Azure Active Directory” 。

2. 在左侧菜单中，单击“标识监管”。

3. 在左侧菜单中，单击“访问包”。 

4. 打开“外部用户包”访问包。 

5. 单击“资源角色”。

6. 选择已添加到此访问包的外部协作组，然后在“详细信息”窗格中选择“删除资源角色”  。 在显示的消息中，选择“是”。

7. 打开访问包列表。

8. 对于“外部用户包”，选择省略号 (...)，然后选择“删除” 。 在显示的消息中，选择“是”。

## <a name="next-steps"></a>后续步骤

了解如何创建访问包以管理对其他类型的资源（例如应用程序和站点）的访问。 [Tutorial:管理对 Azure AD 权利管理中的资源的访问](/active-directory/governance/entitlement-management-access-package-first.md)
