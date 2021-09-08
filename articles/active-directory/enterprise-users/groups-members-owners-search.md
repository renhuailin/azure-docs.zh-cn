---
title: 搜索和筛选组成员和所有者（预览）- Azure Active Directory | Microsoft Docs
description: 在 Azure 门户中搜索和筛选组成员和所有者。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e45ccf6a2d6a11e0acc8bf1c7c3a00a4867e0d2a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433010"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>在 Azure Active Directory 中搜索组和成员（预览）

本文介绍了如何搜索组成员和所有者，以及如何使用搜索筛选器作为 Azure Active Directory (Azure AD) 门户中组体验改进（预览）的一部分。 组体验有许多改进，可以帮助你快速、轻松地管理组（包括成员和所有者）。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此预览中的变更包括：

- 新增了组搜索功能，如组名中的子字符串搜索
- 在成员和所有者列表上新增了筛选和排序选项
- 针对成员和所有者列表新增了搜索功能
- 提高了大型组的组计数准确度

## <a name="enabling-and-managing-the-preview"></a>启用和管理预览

我们让加入预览变得很容易：

  1. 登录 [Azure AD 门户](https://portal.azure.com)，然后选择“组”。
  2. 依次转到“组”和“所有组”页，然后选择页面顶部的横幅来加入预览。

还可以通过选择“所有组”页上的“预览信息”来查看最新功能和改进。 加入预览后，可以在有改进并且是此预览的一部分的所有组页面上看到预览标记。 并不是每个组页面都已更新为此预览的一部分。

如果有任何问题，可以通过选择“所有组”页顶部的横幅来切换回旧体验。 欢迎向我们提供你的反馈，让我们能够改进体验。

## <a name="group-search-and-sorting"></a>组搜索和排序

组列表搜索得到了增强，当你可以输入搜索字符串时，搜索会自动对组名列表执行 `startswith` 和子字符串搜索。 子字符串搜索只对整个单词执行，且不包括特殊字符。 子字符串搜索区分大小写。

![“所有组”页上新增的子字符串搜索](./media/groups-members-owners-search/groups-search-preview.png)

例如，搜索“policy”现在会同时返回“MDM policy – West”和“Policy group”， 但不会返回名为“New_policy”的组。

- 也可以对组成员资格列表执行相同的搜索。
- 现在可以使用“名称”列标题右侧的箭头按名称对组列表进行排序，以按升序或降序对列表进行排序。

## <a name="group-member-search-and-filtering"></a>组成员搜索和筛选

### <a name="search-group-member-and-owner-lists"></a>搜索组成员和所有者列表

现在可以按名称搜索特定组的成员，并对组的所有者列表执行相同的搜索。 在新体验中，如果在搜索框中输入字符串，则会自动执行 startswith 搜索。 例如，搜索“Scott”会返回“Scott Wilkinson”。

![组成员和所有者列表上新增的子字符串搜索](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>筛选成员和所有者列表

除了搜索之外，现在还可以按用户类型筛选成员和所有者列表。 此信息是在列表的“用户类型”列中发现的。 因此，可以按成员和来宾筛选列表，以确定组中是否有任何来宾。

### <a name="view-and-manage-membership"></a>查看和管理成员资格

除了查看特定组的直接成员之外，现在还可以在“成员”页中查看组的所有成员的列表。 成员列表包括组的所有唯一成员，包括任何可传递成员。

还可以单独搜索和筛选直接成员列表和所有成员列表。 筛选所有成员列表不会影响应用于直接成员列表的筛选器。

## <a name="improved-group-member-counts"></a>改进了组成员计数

我们改进了组“概述”页，为所有大小的组提供组成员计数。 甚至可以看到成员超过 1,000 的组的成员计数。 现在，可以在“概述”页上查看组的直接成员总数和成员资格总计数（组的所有唯一成员，包括可传递成员）。

![提高了组成员资格计数的准确度](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>后续步骤

下面这些文章提供了关于如何在 Azure AD 中使用组的额外信息。

- [查看组和成员](../fundamentals/active-directory-groups-view-azure-portal.md)
- [管理组成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理组中用户的动态规则](groups-create-rule.md)
- [编辑组设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [使用组管理对资源的访问权限](../fundamentals/active-directory-manage-groups.md)
- [使用组管理对 SaaS 应用程序的访问权限](groups-saasapps.md)
- [使用 PowerShell 命令管理组](../enterprise-users/groups-settings-v2-cmdlets.md)
- [将 Azure 订阅添加到 Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
