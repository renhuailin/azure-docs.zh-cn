---
title: 快速入门：配置应用程序的属性
titleSuffix: Azure AD
description: 本快速入门使用 Azure 门户配置已注册到 Azure Active Directory (Azure AD) 租户的应用程序。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/23/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: b043867dad8e9571421fe67a83844f8d13a04212
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669414"
---
# <a name="quickstart-configure-properties-for-an-application"></a>快速入门：配置应用程序的属性

在前面的快速入门中，你已将一个应用程序添加到 Azure Active Directory (Azure AD) 租户。 添加应用程序时，需让你的 Azure AD 租户知道它是该应用的标识提供者。 现在，你将配置应用程序的一些属性。

## <a name="prerequisites"></a>先决条件

要在 Azure AD 租户中配置应用程序的属性，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 可选：完成[查看应用](view-applications-portal.md)。
- 可选：完成[添加应用](add-application-portal.md)。

>[!IMPORTANT]
>使用非生产环境测试本快速入门中的步骤。

## <a name="configure-app-properties"></a>配置应用属性

将应用程序添加到 Azure AD 租户后，“概述”页便会显示。 如果要配置已添加的应用程序，请查看第一个快速入门。 它将引导你查看已添加到租户的应用程序。

编辑应用程序属性：

1. 在 Azure AD 门户中，选择“企业应用程序”。 然后找到并选择要配置的应用程序。
2. 在“管理”部分中，选择“属性”以打开“属性”窗格进行编辑  。
3. 请花点时间了解可用选项。 可用选项将取决于应用与 Azure AD 集成的方式。 例如，使用基于 SAML 的单一登录的应用将具有“用户访问 URL”等字段，而使用基于 OIDC 的单一登录的应用则没有这些字段。 另请注意，默认情况下，通过“Azure Active Directory”>“应用注册”添加的应用是基于 OIDC 的应用。 而通过“Azure Active Directory”>“企业应用程序”添加的应用可能会使用多种单一登录标准中的任何一种。 所有应用都将具有用于配置何时显示和使用应用的字段。 这些字段是：
    - “是否启用以供用户登录?”决定分配到应用程序的用户能否登录。
    - “是否需要进行用户分配?”决定了未分配到应用程序的用户能否登录。
    - “是否对用户可见?”决定分配到应用的用户能否在[我的应用](https://myapps.microsoft.com)和 Microsoft 365 应用启动器中看到该应用。 （请参阅 Microsoft 365 网站左上角的华夫饼菜单。）

    > [!TIP]
    > 在导航的“用户和组”部分分配用户。

    这三个选项可以彼此独立地切换，并且产生的行为并不总是明显的。 下面是一个表，可在以下方面提供帮助：

    | 启用以供用户登录? | 需要进行用户分配? | 对用户可见? | 已分配或未分配到应用的用户的行为。 |
    |---|---|---|---|
    | 是 | 是 | 是 | 已分配的用户可以看到应用并登录。<br>未分配的用户看不到应用且无法登录。 |
    | 是 | 是 | 否  | 已分配的用户看不到应用，但可以登录。<br>未分配的用户看不到应用且无法登录。 |
    | 是 | 否  | 是 | 已分配的用户可以看到应用并登录。<br>未分配的用户看不到应用，但可以登录。 |
    | 是 | 否  | 否  | 已分配的用户看不到应用，但可以登录。<br>未分配的用户看不到应用，但可以登录。 |
    | 否  | 是 | 是 | 已分配的用户看不到应用且无法登录。<br>未分配的用户看不到应用且无法登录。 |
    | 否  | 是 | 否  | 已分配的用户看不到应用且无法登录。<br>未分配的用户看不到应用且无法登录。 |
    | 否  | 否  | 是 | 已分配的用户看不到应用且无法登录。<br>未分配的用户看不到应用且无法登录。 |
    | 否  | 否  | 否  | 已分配的用户看不到应用且无法登录。<br>未分配的用户看不到应用且无法登录。 |

4. 完成后，选择“保存”。

## <a name="use-a-custom-logo"></a>使用自定义徽标

若要使用自定义徽标，请执行以下操作：

1. 创建一个 215 x 215 像素的徽标，将其保存为 .png 格式。
2. 在 Azure AD 门户中，选择“企业应用程序”。 然后找到并选择要配置的应用程序。
3. 在“管理”部分中，选择“属性”以打开“属性”窗格进行编辑  。
4. 选择图标以上传徽标。
5. 完成后，选择“保存”。

    :::image type="content" source="media/add-application-portal/change-logo.png" alt-text="显示如何更改徽标的“属性”屏幕的屏幕截图。":::

   > [!NOTE]
   > 此“属性”窗格中显示的缩略图不会立即更新。 可以关闭并重新打开“属性”窗格以查看更新的图标。

> [!TIP]
> 可使用 Graph API 自动管理应用，具体请参阅[使用 Microsoft Graph API 自动管理应用](/graph/application-saml-sso-configure-api)。

## <a name="add-notes"></a>添加注释

可使用“注释”字段来添加与 Azure AD 中的应用程序管理相关的任何信息。 注释是免费文本字段，最大大小为 1024 个字符。

1. 在 Azure AD 门户中，选择“企业应用程序”。 然后找到并选择要配置的应用程序。
2. 在“管理”部分中，选择“属性”以打开“属性”窗格进行编辑  。
3. 更新“注释”字段，然后选择“保存”。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续学习快速入门系列，请考虑删除应用以清理测试租户。 本系列中的最后一个快速入门介绍如何删除应用，请参阅[删除应用](delete-application-portal.md)。

## <a name="next-steps"></a>后续步骤

继续学习下一篇文章，了解如何向应用分配用户。
> [!div class="nextstepaction"]
> [向应用分配用户](add-application-portal-assign-users.md)
