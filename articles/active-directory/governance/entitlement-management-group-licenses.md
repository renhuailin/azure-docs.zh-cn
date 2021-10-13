---
title: 在 Azure AD 中管理基于组的许可证的生命周期
description: 本分步教程介绍如何创建访问包，以在 Azure Active Directory 权利管理中管理基于组的许可证。
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
ms.openlocfilehash: 05633ab0a46f3aa88ab3e520b493d40527717872
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809318"
---
# <a name="tutorial-manage-the-lifecycle-of-your-group-based-licenses-in-azure-ad"></a>教程：在 Azure AD 中管理基于组的许可证的生命周期
 
借助 Azure Active Directory (Azure AD)，可以使用组来管理[应用程序的许可证](../enterprise-users/licensing-groups-assign.md)。 使用权利管理可以更轻松地管理这些组： 

* 配置定期访问评审，确保只有需要许可证的员工在组中。 
* 允许其他员工请求组的成员身份。

在本教程中，你将担当 Woodgrove Bank 的 IT 管理员角色。 该组织要求你创建一个访问包，使其员工能够轻松获得 Office 许可证访问权限。 （你应该已有一个用于管理 [Office 许可证](../enterprise-users/licensing-groups-assign.md)的组。）你希望能够每年评审这些组成员。 你还想要允许新员工请求 Office 许可证（待经理批准）。
 
若要使用 Azure AD 权利管理，必须具有以下某个许可证：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5

有关详细信息，请参阅[许可证要求](entitlement-management-overview.md#license-requirements)。
## <a name="step-1-configure-basics-for-your-access-package"></a>步骤 1：配置访问包的基本信息

必备角色：全局管理员、Identity Governance 管理员、用户管理员、目录所有者或访问包管理员

1. 在 Azure 门户的左侧窗格中，选择“Azure Active Directory”。

2. 在“管理”下，选择“Identity Governance” 。

3. 在“权利管理”下，选择“访问包” 。 

4. 选择“新建访问包”。

5. 在“基本信息”选项卡上的“名称”框中，输入“Office 许可证”  。 在“说明”框中，输入“访问 Office 应用程序的许可证” 。

6. 可以在“目录”列表中保留“常规” 。

## <a name="step-2-configure-the-resources-for-your-access-package"></a>步骤 2：为访问包配置资源

1. 选择“下一步: 资源角色”转到“资源角色”选项卡 。

2. 在此选项卡上，选择要包含在访问包中的资源和资源角色。 在此场景中，选择“组和团队”并搜索分配了 [Office 许可证](/azure/active-directory/enterprise-users/licensing-groups-assign)的组。

3. 在“角色”列表中，选择“成员” 。

## <a name="step-3-configure-requests-for-your-access-package"></a>步骤 3：为访问包配置请求

1. 选择“下一步: 请求”转到“请求”选项卡 。

   在此选项卡上，你将创建请求策略。 策略针对访问包的访问定义规则。 你将创建一个允许资源目录中的员工请求访问包的策略。

3. 在“可以请求访问权限的用户”部分，选择“针对目录中的用户”，然后选择“所有成员(不包括来宾)”  。 指定这些设置后，只有你目录的成员才能请求 Office 许可证。

4. 确保将“需要审批”设置为“是” 。

5. 将“需要请求者理由”保留设置为“是” 。

6. 将“阶段数”设置为“1” 。

7. 在“审批者”下，选择“经理兼审批者” 。 此选项使得请求者的经理能够批准请求。 如果系统找不到经理，你可以选择另一个人作为回退审批者。

8. 将“必须在几天内做出决定?”保留设置为“14” 。

9. 将“需要审批者理由”保留设置为“是” 。

10. 在“启用新请求和分配”下选择“是”，以便在创建访问包后，员工能够立即请求该访问包 。

## <a name="step-4-configure-requestor-information-for-your-access-package"></a>步骤 4：为访问包配置请求者信息

1. 选择“下一步”转到“请求者信息”选项卡 。

2. 在此选项卡上，可以提问以从请求者那里收集详细信息。 这些问题显示在请求表单上，可以设置为必需或可选问题。 在此场景中，系统不要求你包含访问包的请求者信息，因此可将这些框留空。

## <a name="step-5-configure-the-lifecycle-for-your-access-package"></a>步骤 5：为访问包配置生命周期

1. 选择“下一步: 生命周期”转到“生命周期”选项卡 。

2. 在“过期时间”部分，为“访问包分配到期”选择“天数”  。
    
3. 在“分配在此时间后过期”中，输入“365” 。 此框指定有权访问该访问包的成员何时需要续订其访问权限。 

4. 还可以配置访问评审，以便定期检查员工是否仍然需要访问该访问包。 评审可以是员工执行的自我评审。 或者，你可以将员工的经理或另一个人设置为评审者。 有关详细信息，请参阅[访问评审](entitlement-management-access-reviews-create.md)。 
 
    在此场景中，你希望所有员工每年都评审他们是否仍然需要 Office 许可证。

    1.  在“需要访问评审”下，选择“是” 。
    2.  可以将“开始时间”保留设置为当前日期。 此日期是访问评审的开始日期。 创建访问评审后，无法更新其开始日期。
    3.  在“评审频率”下选择“每年”，因为评审每年进行一次 。 可在“评审频率”框中确定访问评审的运行频率。
    4.  请指定“持续时间(天)”。  可在“持续时间”框中指明每次访问评审系列要运行的天数。
    5.  在“评审者”下，选择“经理” 。

## <a name="step-6-review-and-create-your-access-package"></a>步骤6：查看和创建访问包

1. 选择“下一步: 查看 + 创建”转到“查看 + 创建”选项卡 。

   在此选项卡上，可以先检查访问包的配置，然后再创建该访问包。 如果有任何问题，可以使用该选项卡转到配置过程的特定位置进行编辑。

3. 如果你对配置感到满意，请选择“创建”。 片刻后，应会看到一条通知，指出已创建访问包。

4. 创建访问包后，你将看到该包的“概述”页。 在此处可以找到“我的访问权限”门户链接。 复制该链接并将其与团队共享，使团队成员可以请求访问包以获得 Office 许可证。

## <a name="step-7-clean-up-resources"></a>步骤 7：清理资源

在此步骤中，可以删除“Office 许可证”访问包。 

必备角色：全局管理员、Identity Governance 管理员或访问包管理员

1. 在 Azure 门户的左侧窗格中，选择“Azure Active Directory”。

2. 在“管理”下，选择“Identity Governance” 。

3. 在“权利管理”下，选择“访问包” 。 

4. 打开“Office 许可证”访问包。 

5. 选择“资源角色”。

6. 选择已添加到访问包的组。 在详细信息窗格中，选择“删除资源角色”。 在出现的消息框中选择“是”。

7. 打开访问包列表。

8. 对于“Office 许可证”，请选择省略号按钮 (…)，然后选择“删除” 。 在出现的消息框中选择“是”。

## <a name="next-steps"></a>后续步骤

了解如何创建访问包以管理对其他类型的资源（例如应用程序和站点）的访问： 

[在 Azure AD 权利管理中管理对资源的访问](/azure/active-directory/governance/entitlement-management-access-package-first)
