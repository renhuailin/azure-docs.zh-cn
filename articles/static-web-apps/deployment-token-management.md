---
title: " (预览中重置 Azure 静态 Web 应用中的部署令牌) "
description: 重置 Azure 静态 Web 应用站点中的令牌
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744433"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a> (预览中重置 Azure 静态 Web 应用中的部署令牌) 

当你创建新的 Azure 静态 Web 应用网站时，Azure 会生成用于在部署过程中标识应用程序的令牌。 在预配期间，此令牌在 GitHub 存储库中存储为机密。 本文介绍如何使用和管理此令牌。

通常情况下，你无需担心部署令牌，但以下原因可能需要检索或重置令牌。

* **令牌泄露**：如果令牌公开给外部方，请重置令牌。
* **从单独的 github 存储库进行部署**：如果要从单独的 github 存储库中手动部署，则需要在新存储库中设置部署令牌。

## <a name="prerequisites"></a>先决条件

- 使用 Azure 静态 Web 应用配置的现有 GitHub 存储库。
- 如果没有，请参阅[生成第一个静态应用](getting-started.md)。

## <a name="reset-a-deployment-token"></a>重置部署令牌

1. 在 Azure 静态 Web Apps 网站的 "_概述_" 页上，单击 "**管理部署令牌**" 链接。

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="管理部署令牌":::

1. 单击 " **重置令牌** " 按钮。

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="正在重置部署令牌":::

1. 在 " _部署令牌_ " 字段中显示新令牌后，通过单击 " **复制到剪贴板** " 图标来复制该令牌。


## <a name="update-a-secret-in-the-github-repository"></a>更新 GitHub 存储库中的机密

若要保持自动部署的运行状态，请在重置令牌后，在相应的 GitHub 存储库中设置新值。

1. 导航到 GitHub 上的项目存储库，并单击 " **设置** " 选项卡。
1. 单击 " **机密** " 菜单项。 在静态 Web 应用预配期间，你会发现一个机密 _AZURE_STATIC_WEB_APPS_API_TOKEN_.。。 _存储库机密_ 部分。

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="列出存储库机密":::

    > [!NOTE]
    > 如果为此存储库的多个分支创建了 Azure 静态 Web 应用站点，则会看到多个 _AZURE_STATIC_WEB_APPS_API_TOKEN_.。。此列表中的机密。 选择正确的文件，方法是在静态 Web 应用站点的 "_概述_" 选项卡上的 "_编辑工作流_" 字段中列出的文件名进行匹配。

1. 单击 " **更新** " 按钮以打开编辑器。
1. 将部署令牌的 **值粘贴** 到 "_值_" 字段。
1. 单击 " **更新密钥**"。

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="正在更新存储库机密":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从静态站点生成器发布](publish-gatsby.md)
