---
title: Azure Synapse Analytics 中的条件访问
description: 这些文章介绍了 Azure Synapse Analytics 中的条件访问
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/07/2021
ms.sub-service: security
ms.custom: template-concept
ms.openlocfilehash: 4461ac0874eef735b7d01a3fc9c2c4158ddcb62d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594346"
---
# <a name="conditional-access-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的条件访问

现在，你可以配置 Azure Synapse Analytics 工作区的条件访问策略。 条件访问是 Azure Active Directory 提供的一种工具，可将设备类型和设备 IP 位置等多种信号结合在一起，以做出授予访问权限、阻止访问权限或对资源实施多重身份验证的决策。 条件访问策略是在 Azure Active Directory 中配置的。 详细了解[条件访问](../../active-directory/conditional-access/overview.md)。


## <a name="configure-conditional-access"></a>配置条件访问
以下步骤显示了如何为 Azure Synapse 工作区配置条件访问策略。

1. 使用具有全局管理员权限的帐户登录 Azure 门户，选择“Azure Active Directory”，从菜单中选择“安全”。  
2. 选择“条件访问”，然后选择“+ 新建策略”，并提供策略的名称。 
3. 在“分配”下，选择“用户和组”，勾选“选择用户和组”选项，然后选择用于条件性访问的 Azure AD 用户或组。   依次单击“选择”、“完成”。
4. 选择“云应用”，单击“选择应用”。 选择“Microsoft Azure Synapse Gateway”。 然后依次单击“选择”和“完成”   。
5. 在“访问控制”下面，选择“授予”，然后勾选要应用的策略，然后选择“完成”。 
6. 将“启用策略”开关设置为“开”，然后选择“创建”。 


## <a name="next-steps"></a>后续步骤
详细了解条件访问策略及其组件。
- [常见的条件访问策略](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)
- [生成条件访问策略](../../active-directory/conditional-access/concept-conditional-access-policies.md)