---
title: 与他人协作 - LUIS
titleSuffix: Azure Cognitive Services
description: 应用所有者可以向创作资源添加参与者。 这些参与者可以修改模型，训练并发布应用。
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2021
ms.openlocfilehash: 5a320d6368d4fdecaf4001fc9255c4c0ff3b276c
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285589"
---
# <a name="add-contributors-to-your-app"></a>向应用添加参与者

应用所有者可以向应用添加参与者。 这些参与者可以修改模型，训练并发布应用。 [迁移](luis-migration-authoring.md)帐户以后，可以在 Azure 门户中通过“访问控制(IAM)”页管理该创作资源的参与者。 使用协作者的电子邮件地址和“参与者”角色添加用户。

## <a name="add-contributor-to-azure-authoring-resource"></a>向 Azure 创作资源添加参与者

如果你的 LUIS 创作体验已绑定到 LUIS 门户中“管理 -> Azure 资源”页上的某个创作资源，则表明你已完成迁移。

在 Azure 门户中，找到语言理解 (LUIS) 创作资源。 它的类型为 `LUIS.Authoring`。 在资源的“访问控制 (IAM)”页中，为希望参与的用户添加“参与者”角色 。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

## <a name="view-the-app-as-a-contributor"></a>以参与者身份查看应用

被添加为参与者后，请[登录到 LUIS 门户](sign-in-luis-portal.md)。

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>有多个电子邮件的用户

如果将参与者添加到 LUIS 应用，则需指定确切的电子邮件地址。 虽然 Azure Active Directory (Azure AD) 允许单个用户交替使用多个电子邮件帐户，但 LUIS 要求用户使用在添加参与者时指定的电子邮件地址登录。

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory 资源

如果你在组织中使用了 [Azure Active Directory](../../active-directory/index.yml) (Azure AD)，则在用户希望使用语言理解 (LUIS) 时，LUIS 需要有权访问用户的访问权限相关信息。 LUIS 需要的资源是最少的。

尝试使用已获得管理员同意或不需要管理员同意的帐户进行登录时，你将看到详细说明，例如管理员同意：

* 允许你使用组织帐户登录到应用并让应用读取你的配置文件。 它还允许应用读取基本的公司信息。 这将授权 LUIS 读取基本的配置文件数据，例如用户 ID、电子邮件、姓名
* 允许应用查看和更新你的数据，即使你当前未使用应用。 此权限是刷新用户的访问令牌时所必需的。


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租户用户

LUIS 使用标准的 Azure Active Directory (Azure AD) 许可流程。

租户管理员直接处理需要访问权限才能在 Azure AD 中使用 LUIS 的用户。

* 用户首先要登录 LUIS，此时看到需要管理员批准的弹出对话框。 继续操作之前，用户需联系租户管理员。
* 然后，租户管理员登录 LUIS，并看到一个“同意流”弹出对话框。 管理员需在此对话框中向用户授予权限。 管理员接受权限后，用户才能够继续使用 LUIS。 如果租户管理员不登录 LUIS，还可访问 LUIS 的[同意](https://account.activedirectory.windowsazure.com/r#/applications)部分。 在此页上，可以将列表筛选到包含名称 `LUIS` 的项。

如果租户管理员只希望某些用户使用 LUIS，则有几种可能的解决方案：
* 给予“管理员同意”（同意 Azure AD 的所有用户），但随后在“企业应用程序属性”下将“需要进行用户分配”设置为“是”，最后仅将所需用户分配/添加到应用程序。 使用此方法，管理员仍然向应用提供“管理员同意”，但是，可以控制可以访问应用的用户。
* 另一种解决方法是使用 [Microsoft Graph 中的 Azure AD 标识和访问管理 API](/graph/azuread-identity-access-management-concept-overview) 向每个特定用户提供许可。

详细了解 Azure Active Directory 用户和同意：
* [将应用限制为](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)供一组用户使用

## <a name="next-steps"></a>后续步骤

* 了解[如何使用版本](luis-how-to-manage-versions.md)来控制应用生命周期。
* 了解如何[创作资源](luis-how-to-azure-subscription.md)以及如何在该资源中[添加参与者](luis-how-to-collaborate.md)。
* 了解[如何创建](luis-how-to-azure-subscription.md)创作和运行时资源
* 迁移到新的[创作资源](luis-migration-authoring.md)