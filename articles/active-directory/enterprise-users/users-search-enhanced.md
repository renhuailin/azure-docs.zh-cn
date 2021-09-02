---
title: 用户管理增强功能（预览版）- Azure Active Directory | Microsoft Docs
description: 介绍 Azure Active Directory 如何启用用户搜索、筛选，以及有关用户的详细信息。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de315722641248a2a577c97403da400a6de67e6c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867073"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Azure Active Directory 中的用户管理增强功能（预览版）

本文介绍如何在 Azure Active Directory (Azure AD) 门户中使用用户管理增强功能预览版。 “所有用户”和“已删除的用户”页已更新，现在它会提供更多信息，并让你更轻松地查找用户 。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

预览版包括以下更改：

- 更多的可见用户属性，包括对象 ID、目录同步状态、创建类型和标识颁发者
- “立即搜索”现在允许对名称、电子邮件和对象 ID 进行子字符串搜索和合并搜索
- 增强了筛选，现在可按用户类型（成员、来宾、无）、目录同步状态、创建类型、公司名称和域名进行筛选
- 新增了按属性（例如名称和用户主体名称）排序的功能
- 使用搜索或筛选器进行更新的新用户总数

> [!NOTE]
> 此预览版目前不适用于 Azure AD B2C 租户。

## <a name="find-the-preview"></a>查找预览版

该预览版默认已启用，因此你可以立即使用它。 可以在“所有用户”页上选择“预览功能”来查看最新的功能和改进 。 作为此预览版的一部分更新的所有页都将显示一个“预览版”标记。 如果遇到任何问题，可以改回旧体验：

1. 登录到 [Azure AD 管理中心](https://aad.portal.azure.com)并选择“用户”。
1. 在“用户 – 所有用户”页中，选择页面顶部的横幅。
1. 在“预览功能”窗格中，禁用“增强的用户管理” 。

   ![如何以及在何处启用和禁用“增强的用户管理”](./media/users-search-enhanced/enable-preview.png)

欢迎向我们提供反馈，让我们能够改进体验。

## <a name="more-user-properties"></a>更多用户属性

我们对“所有用户”和“已删除的用户”页上提供的列做了一些更改 。 除了我们为管理用户列表而提供的现有列之外，我们还添加了其他几个列。

### <a name="all-users-page"></a>“所有用户”页

下面是“所有用户”页上显示的用户属性：

- 名称：用户的显示名称。
- 用户主体名称：用户的用户主体名称 (UPN)。
- 用户类型：成员、来宾、无。
- 创建时间：创建用户的日期和时间。
- 职务：用户的职务。
- 部门：用户所在的部门。
- 已同步目录：指示用户是否已从本地目录同步。
- 标识颁发者：用于登录到用户帐户的标识的颁发者。
- 对象 ID：用户的对象 ID。
- 创建类型：指示用户帐户是如何创建的。
- 公司名称：与用户关联的公司名称。
- 邀请状态：来宾用户的邀请状态。
- 邮件：用户的电子邮件。

![“所有用户”和“已删除的用户”页上显示的新用户属性](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>“已删除的用户”页

“已删除的用户”页包括“所有用户”页上提供的所有列，以及其他几个列，即 ：

- 删除日期：首次将用户从组织中删除的日期（用户可还原）。
- 永久删除日期：在该日期后，会自动开始执行将用户从组织中永久删除的过程。
- 原始用户主体名称：在将用户的对象 ID 作为前缀添加到其已删除的 UPN 之前，该用户的原始 UPN。

> [!NOTE]
> 删除日期以协调世界时 (UTC) 显示。

默认会显示某些列。 若要添加其他列，请在页面上选择“列”，选择要添加的列名称，然后选择“确定”保存首选项 。

### <a name="identity-issuers"></a>标识颁发者

在任何用户的“标识颁发者”列中选择某个条目可以查看有关颁发者的其他详细信息，包括登录类型和颁发者分配的 ID。 “标识颁发者”列中的条目可为多值类型。 如果用户的标识存在多个颁发者，则“所有用户”和“已删除的用户”页上的“标识颁发者”列中会显示“多个”一词，并且详细信息窗格会列出所有颁发者  。

> [!NOTE]
> “源”列已替换为多个列（包括“创建类型”、“已同步目录”和“标识颁发者”），以提供更精细的筛选   。

## <a name="user-list-search"></a>用户列表搜索

当你输入搜索字符串时，搜索功能现在会使用“开头为”和子字符串搜索，通过一次搜索来匹配名称、电子邮件或对象 ID。 你可以在搜索框中输入上述任一特性，搜索将自动查找所有这些属性，以返回任何匹配结果。 只会针对完整单词执行子字符串搜索。 可以在“所有用户”和“已删除的用户”页上执行相同的搜索 。

## <a name="user-list-filtering"></a>用户列表筛选

筛选功能已得到增强，为“所有用户”和“已删除的用户”页提供更多筛选选项 。 现在，可以同时按多个属性进行筛选，并可以按更多属性进行筛选。

### <a name="filtering-all-users-list"></a>筛选“所有用户”列表

下面是“所有用户”页上的可筛选属性：

- 用户类型：成员、来宾、无
- “已同步目录”状态：是、否
- 创建类型：邀请、已验证电子邮件、本地帐户
- 创建时间：过去 7 天、14 天、30 天、90 天、360 天或超过 360 天之前
- 职务：输入职务
- 部门：输入部门名称
- 组：搜索组
- 邀请状态：等待接受、已接受
- 域名：输入域名
- 公司名称：输入公司名称
- 管理单元：选择此选项可将要查看的用户范围限制为单个管理单元。 有关详细信息，请参阅[管理单元管理预览版](../roles/administrative-units.md)。

### <a name="filtering-deleted-users-list"></a>筛选“已删除的用户”列表

“已删除的用户”页包含“所有用户”页中所不包含的其他筛选器 。 下面是“已删除的用户”页上的可筛选属性：

- 用户类型：成员、来宾、无
- “已同步目录”状态：是、否
- 创建类型：邀请、已验证电子邮件、本地帐户
- 创建时间：过去 7 天、14 天、30 天、90 天、360 天或超过 360 天之前
- 职务：输入职务
- 部门：输入部门名称
- 邀请状态：等待接受、已接受
- 删除日期：过去 7 天、14 天或 30 天
- 域名：输入域名
- 公司名称：输入公司名称
- 永久删除日期：过去 7 天、14 天或 30 天

## <a name="user-list-sorting"></a>用户列表排序

现在，可以在“所有用户”和“已删除的用户”页中按名称和用户主体名称进行排序 。 还可以在“已删除的用户”列表中按删除日期进行排序。

## <a name="user-list-counts"></a>用户列表计数

可以在“所有用户”和“已删除的用户”页中查看用户总数 。 搜索或筛选列表时，计数将会更新，以反映找到的用户总数。

![“所有用户”页上的用户列表计数插图](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

问题 | Answer
-------- | ------
为何永久删除日期过去之后还会显示已删除的用户？ | 永久删除日期是以 UTC 时区显示的，可能与你的当前时区不相符。 此外，此日期是过后即从组织中永久删除该用户的最早日期，因此删除处理可能仍在进行。 永久删除的用户将自动从列表中删除。
针对用户和来宾的批量功能有什么变化？ | 仍可对用户和来宾执行所有批量操作，包括批量创建、批量邀请、批量删除和下载用户。 我们只是将这些操作合并到了一个名为“批量操作”的菜单中。 可以在“所有用户”页的顶部找到“批量操作”选项 。
“源”列有什么变化？ | “源”列已替换为其他列，这些列提供类似的信息，同时还允许你单独对这些值进行筛选。 示例包括“创建类型”、“已同步目录”和“标识颁发者”  。
“用户名”列有什么变化？ | “用户名”列仍然存在，但已重命名为“用户主体名称” 。 这更好地反映了该列中包含的信息。 你还会注意到，现在会显示 B2B 来宾的完整用户主体名称。 这与你在 MS Graph 中看到的信息相符。  

## <a name="next-steps"></a>后续步骤

用户操作

- [添加或更改个人资料信息](../fundamentals/active-directory-users-profile-azure-portal.md)
- [添加或删除用户](../fundamentals/add-users-azure-active-directory.md)

批量操作

- [下载用户列表](users-bulk-download.md)
- [批量添加用户](users-bulk-add.md)
- [批量删除用户](users-bulk-delete.md)
- [批量还原用户](users-bulk-restore.md)
