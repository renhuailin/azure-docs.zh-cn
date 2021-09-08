---
title: 创建或编辑动态组并获取状态 - Azure AD | Microsoft Docs
description: 如何在 Azure 门户中创建或更新组成员资格规则并检查其处理状态。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25019d00fc38cb58d928bcbc52209bd5ec54f0e9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438617"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>在 Azure Active Directory 中创建或更新动态组

在 Azure Active Directory (Azure AD) 中，可以使用规则根据用户或设备属性确定组成员资格。 本文介绍如何为 Azure 门户中的动态组设置一项规则。
支持为安全组或 Microsoft 365 组启用动态成员身份。 应用组成员身份规则时，将会对用户和设备属性进行评估，确定其是否与成员身份规则匹配。 当用户或设备的任何属性发生更改时，将处理组织中的所有动态组规则以进行成员身份更改。 如果用户和设备符合组的条件，则会对其执行添加或删除操作。 安全组可以用于设备或用户，但 Microsoft 365 组只能是用户组。 需要 Azure AD 高级 P1 许可证才能使用动态组。 有关更多详细信息，请参阅[组的动态成员身份规则](./groups-dynamic-membership.md)。 

## <a name="rule-builder-in-the-azure-portal"></a>Azure 门户中的规则生成器

Azure AD 提供了一个规则生成器，用于更快地创建和更新重要规则。 规则生成器支持最多包含五个表达式的构造。 通过规则生成器可以更轻松地使用几个简单表达式来组成规则，但是，它无法用于重现每个规则。 如果规则生成器不支持要创建的规则，则可以使用文本框。

下面是建议使用文本框构造的高级规则或语法的一些示例：

- 具有五个以上表达式的规则
- 直接下属规则
- 设置[运算符优先级](groups-dynamic-membership.md#operator-precedence)
- [具有复杂表达式的规则](groups-dynamic-membership.md#rules-with-complex-expressions)；例如 `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> 规则生成器可能无法显示在文本框中构造的某些规则。 当规则生成器无法显示规则时，可能会看到一条消息。 规则生成器不会以任何方式更改动态组规则的支持语法、验证或处理。

![屏幕截图显示了“动态成员身份规则”页，其中选择了“配置规则”选项卡上的“添加表达式”操作。](./media/groups-create-rule/update-dynamic-group-rule.png)

如需成员身份规则的语法、支持的属性、运算符和值的示例，请参阅 [Azure Active Directory 中的组的动态成员资格规则](groups-dynamic-membership.md)。

## <a name="to-create-a-group-membership-rule"></a>要创建组成员资格规则，请执行以下操作：

1. 使用在 Azure AD 组织中具有全局管理员、Intune 管理员或用户管理员角色的帐户登录 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 搜索并选择“组”。
1. 选择“所有组”，然后选择“新组”。

   ![选择用于添加新组的命令](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. 在“组”页面上，输入新组的名称和说明。 为用户或设备选择“成员身份类型”，然后选择“添加动态查询”。 规则生成器支持最多五个表达式。 若要添加五个以上的表达式，必须使用文本框。

   ![屏幕截图显示了“所有组”页，其中选择了“新建组”操作。](./media/groups-create-rule/add-dynamic-group-rule.png)

1. 查看适用于成员身份查询的自定义扩展属性：
   1. 选择“获取自定义扩展属性”
   1. 输入应用程序 ID，然后选择“刷新属性”。
1. 创建规则之后，选择“保存”。
1. 在“新建组”页中，选择“创建”以创建该组。  

如果输入的规则无效，则会在门户的 Azure 通知中显示一个说明，指出为何系统无法处理规则。 请仔细阅读，了解如何修复规则。

## <a name="to-update-an-existing-rule"></a>更新现有规则

1. 使用在 Azure AD 组织中具有全局管理员、组管理员、Intune 管理员或用户管理员角色的帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择“组” > “所有组” 。
1. 选择组以打开其配置文件。
1. 在组的配置文件页上，选择“动态成员身份规则”。 规则生成器支持最多五个表达式。 若要添加五个以上的表达式，必须使用文本框。

   ![为动态组添加成员身份规则](./media/groups-create-rule/update-dynamic-group-rule.png)

1. 查看适用于成员身份规则的自定义扩展属性：
   1. 选择“获取自定义扩展属性”
   1. 输入应用程序 ID，然后选择“刷新属性”。
1. 更新规则后，选择“保存”。

## <a name="turn-on-or-off-welcome-email"></a>打开或关闭欢迎电子邮件

创建新的 Microsoft 365 组时，会向添加到该组的用户发送欢迎电子邮件通知。 以后，如果用户或设备的任何属性发生更改时，将处理组织中的所有动态组规则以进行成员身份更改。 添加的用户也会收到欢迎通知。 可以在 [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup) 中关闭此行为。

## <a name="check-processing-status-for-a-rule"></a>检查规则的处理状态

可在组的“概述”页上查看成员资格处理状态和上次更新日期。
  
  ![显示动态组状态](./media/groups-create-rule/group-status.png)

“成员资格处理”状态会显示以下几种状态消息：

- **正在评估**：已收到组更改，正在评估更新。
- **正在处理**：正在进行更新。
- **更新完成**：处理已完成，且已完成所有适用更新。
- **处理错误**：无法完成处理是因为评估成员身份规则时遇到错误。
- **更新已暂停**：管理员暂停了动态成员资格规则更新。 MembershipRuleProcessingState 设置为“已暂停”。

“上次更新的成员资格”状态会显示以下几种状态消息：

- &lt;**日期和时间**&gt;：上次更新成员资格的时间。
- **正在进行**：目前正在进行更新。
- **未知**：无法检索上次更新时间。 该组可能是新的。

如果在处理特定组的成员资格规则时出错误，则该组的“概述”页顶部会显示警报。 如果无法在 24 小时之后处理组织中所有组的待处理动态成员资格更新，则会在所有组的顶部显示警报。

![正在处理错误消息警报](./media/groups-create-rule/processing-error.png)

以下文章提供了有关 Azure Active Directory 中的组的更多信息。

- [查看现有组](../fundamentals/active-directory-groups-view-azure-portal.md)
- [创建新组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)
- [管理组的设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [管理组的成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理组中用户的动态规则](groups-dynamic-membership.md)