---
title: 登录到 LUIS 门户
description: 如果你是登录到 LUIS 门户的新用户，登录体验将基于你当前的用户帐户而略有不同。
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: aeb84fca47dbf2922f17a3e8931e3158f9ee2cca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706769"
---
# <a name="sign-in-to-luis-portal"></a>登录到 LUIS 门户

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

借助本文开始使用 LUIS 门户和创建创作资源。 完成本文中的步骤后，你将能够创建和发布 LUIS 应用。

## <a name="access-the-portal"></a>访问门户


1. 若要开始使用 LUIS，请转到 [LUIS 门户](https://www.luis.ai)。 如果你还没有订阅，系统将提示你创建一个 [免费帐户](https://azure.microsoft.com//free/cognitive-services/) 并返回到门户。
2. 刷新页面，使其更新为新创建的订阅
3. 从下拉列表中选择你的订阅

    > [!div class="mx-imgBorder"]
    > ![选择订阅](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. 如果你的订阅在另一个租户下，则无法从现有窗口切换租户。 可以通过以下方式切换租户：关闭此窗口，并在顶部栏中选择最右侧包含你的姓名首字母的头像。 单击顶部的“选择其他创作资源”以重新打开窗口。

    > [!div class="mx-imgBorder"]
    > ![切换目录](./media/migrate-authoring-key/switch-directories.png)

5. 如有与订阅关联的现有 LUIS 创作资源，请从下拉列表中选择它。 可以查看在此创作资源下创建的所有应用程序。
6. 如果没有，请单击此模式底部的“新建创作资源”。
7.  创建新的创作资源时，请提供以下信息：

    > [!div class="mx-imgBorder"]
    > ![新建资源](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **租户名称** - 与 Azure 订阅关联的租户。 无法从现有窗口切换租户。 可通过以下方式切换租户：关闭此窗口，并选择屏幕右上角包含你的姓名首字母的头像。 选择顶部的“选择其他创作资源”以重新打开窗口。
    * **Azure 资源组名称** - 你在订阅中选择的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。 如果订阅中目前没有资源组，则不允许在 LUIS 门户中创建资源组。 请转到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup)来创建一个资源组，然后转到 LUIS 以继续完成登录过程。
    * **Azure 资源名称** - 你选择的自定义名称，用作创作事务的 URL 的一部分。 资源名称只能包含字母数字字符、`-`，且不能以 `-` 开头或结尾。 如果名称中包含任何其他符号，则资源创建会失败。
    * **位置** -选择在 LUIS 当前支持的 [三个创作位置](./luis-reference-regions.md) 之一中创建应用程序，包括：美国西部、西欧和澳大利亚东部
    * **定价层** - 默认选择 F0 创作定价层，这是推荐选项。 若需要额外的安全层，可从 Azure 门户创建[客户托管密钥](./encrypt-data-at-rest.md#customer-managed-keys-for-language-understanding)。
8. 现在，你已成功登录到 LUIS。 现在可以开始创建应用程序。

## <a name="troubleshooting"></a>疑难解答

* 如果要新建资源，请确保资源名称只包含字母数字字符，'-'，且不能以'-'开头或结尾。 否则，操作将失败。
* 请确保具有[可创建 Azure 资源的适当订阅权限](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。 如果没有适当权限，请与订阅管理员联系，以获得足够的权限。

## <a name="next-steps"></a>后续步骤

* 了解如何[启动新应用](luis-how-to-start-new-app.md)