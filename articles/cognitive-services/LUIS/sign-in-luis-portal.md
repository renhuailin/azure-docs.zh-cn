---
title: 登录到 LUIS 门户
description: 如果你是登录到 LUIS 门户的新用户，则根据你的当前用户帐户，登录体验将略有不同。
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: b8382b76496976054ebb452e39866765d986ccbb
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368166"
---
# <a name="sign-in-to-luis-portal"></a>登录到 LUIS 门户

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

使用本文开始使用 LUIS 门户，并创建创作资源。 完成本文中的步骤后，你将能够创建并发布 LUIS 应用。

## <a name="access-the-portal"></a>访问门户


1. 若要开始 LUIS，请转到 [LUIS 门户](https://www.luis.ai)。 如果你还没有订阅，系统将提示你创建一个 [免费帐户](https://azure.microsoft.com//free/cognitive-services/) 并返回到门户。
2. 刷新页面以将其更新为新创建的订阅
3. 从下拉列表中选择你的订阅

    > [!div class="mx-imgBorder"]
    > ![选择订阅](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. 如果你的订阅在另一个租户下，你将无法从现有窗口切换租户。 可以通过关闭此窗口并在顶部栏中选择包含首字母缩写的最右侧头像来切换租户。 单击顶部的 " **选择其他创作资源** " 重新打开该窗口。

    > [!div class="mx-imgBorder"]
    > ![切换目录](./media/migrate-authoring-key/switch-directories.png)

5. 如果有与订阅关联的现有 LUIS 创作资源，请从下拉列表中选择它。 可以查看在此创作资源下创建的所有应用程序。
6. 如果不是，则单击此模式底部的 " **创建新的创作资源** "。
7.  创建新的创作资源时，请提供以下信息：

    > [!div class="mx-imgBorder"]
    > ![新建资源](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **租户名称** -Azure 订阅与之关联的租户。 你将不能从现有窗口切换租户。 可以通过关闭此窗口并选择屏幕右上角的头像，其中包含姓名，来切换租户。 选择顶部的 " **选择其他创作资源** "，重新打开该窗口。
    * **Azure 资源组名称** -在订阅中选择的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。 如果订阅中目前没有资源组，则不允许在 LUIS 门户中创建资源组。 转到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) ，然后转到 LUIS 继续登录过程。
    * **Azure 资源名称** -所选的自定义名称，用作创作事务的 URL 的一部分。 资源名称只能包含字母数字字符， `-` 且不能以开头或结尾 `-` 。 如果名称中包含任何其他符号，则创建资源会失败。
    * **位置** -选择在 LUIS 当前支持的 [三个创作位置](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions) 之一中创建应用程序，包括：美国西部、西欧和澳大利亚东部
    * **定价层** -默认情况下，将选择 F0 创作定价层，因为这是推荐的设置。 如果要寻找额外的安全层，请从 Azure 门户创建 [客户管理的密钥](https://docs.microsoft.com/azure/cognitive-services/luis/luis-encryption-of-data-at-rest#customer-managed-keys-for-language-understanding) 。
8. 现在，你已成功登录到 LUIS。 你现在可以开始创建应用程序。

## <a name="troubleshooting"></a>疑难解答

* 创建新资源时，请确保资源名称只包含字母数字字符 "-"，并且不能以 "-" 开头或结尾。 否则，操作将失败。
* 请确保对 [订阅拥有适当的权限来创建 Azure 资源](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。 如果你没有适当的权限，请与你的订阅管理员联系，以获得足够的权限。

## <a name="next-steps"></a>后续步骤

* 了解如何 [启动新应用](luis-how-to-start-new-app.md)
