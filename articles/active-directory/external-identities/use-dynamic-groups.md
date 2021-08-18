---
title: 动态组和 B2B 协作 - Azure Active Directory | Microsoft Docs
description: 说明如何将 Azure AD 动态组与 Azure Active Directory B2B 协作一起使用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6638e06a8bd7020db993db024d76a566b878ca23
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730399"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>动态组和 Azure Active Directory B2B 协作

## <a name="what-are-dynamic-groups"></a>什么是动态组？
Azure Active Directory (Azure AD) 的安全组成员身份动态配置在 [Azure 门户](https://portal.azure.com)中提供。 管理员可以设置规则以填充在 Azure AD 中基于用户属性（如 userType、部门或国家/地区）创建的组。 可基于成员属性自动在安全组中添加或删除成员。 这些组可以为成员提供对应用程序或云资源（SharePoint 站点、文档）的访问权限并分配许可证。 在 [Azure Active Directory 中的专用组](../fundamentals/active-directory-groups-create-azure-portal.md)中阅读有关动态组的详细信息。

创建和使用动态组需要相应的 [Azure AD Premium P1 或 P2 授权](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。 有关详细信息，请参阅[在 Azure Active Directory 中为动态组成员身份创建基于属性的规则](../enterprise-users/groups-dynamic-membership.md)一文。

## <a name="creating-an-all-users-dynamic-group"></a>创建“所有用户”动态组
可使用成员资格规则创建包含租户中所有用户的组。 以后向租户添加用户或从中删除用户时，将自动调整该组的成员资格。

1. 使用在租户中分配有全局管理员或用户管理员角色的帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“Azure Active Directory”  。
2. 在“管理”下选择“组”，然后选择“新建组”    。
1. 在“新建组”  页的“组类型”  下选择“安全性”  。 为新组输入“组名称”  和“组说明”  。 
2. 在“成员身份类型”  下，选择“动态用户”  ，然后选择“添加动态查询”  。 
4. 在“规则语法”  文本框上方，选择“编辑”  。 在“编辑规则语法”  页上的文本框中键入以下表达式：

   ```
   user.objectId -ne null
   ```
1. 选择“确定”  。 规则会出现在“规则语法”框中：

   ![“所有用户”动态组的规则语法](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  选择“保存”  。 新的动态组现在将包含 B2B 来宾用户和成员用户。


1. 在“新建组”页中，选择“创建”以创建该组。  

## <a name="creating-a-group-of-members-only"></a>仅创建成员组

如果希望组排除来宾用户，只包含租户的成员，请按照上述要求创建动态组，但在“规则语法”  框中，请输入以下表达式：

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

下图显示了已修改为仅包括成员并排除来宾的动态组的规则语法。

![显示了用户类型为“成员”的规则](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>仅创建来宾组

你可能还会发现创建仅包含来宾用户的新动态组很有用，这样就可以向来宾用户应用策略（例如 Azure AD 条件访问策略）。 根据上述要求创建动态组，但在“规则语法”  框中，请输入以下表达式：

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

下图显示了已修改为仅包括来宾并排除成员用户的动态组的规则语法。

![显示用户类型等于“来宾”的规则](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>后续步骤

- [B2B 协作用户属性](user-properties.md)
- [将 B2B 协作用户添加到角色](./add-users-administrator.md)
- [B2B 协作用户的条件访问](conditional-access.md)