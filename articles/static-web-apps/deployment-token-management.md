---
title: 在 Azure Static Web Apps 中重置部署令牌
description: 在 Azure Static Web Apps 站点中重置令牌
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: 8d516a448240e32f58318689d985bc0aad906b9f
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814003"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps"></a>在 Azure Static Web Apps 中重置部署令牌

创建新的 Azure Static Web Apps 站点时，Azure 会生成用于在部署过程中标识应用程序的令牌。 在预配期间，此令牌作为机密存储在 GitHub 存储库中。 本文介绍如何使用和管理此令牌。

通常情况下，无需担心部署令牌，但出于以下一些原因，可能需要检索或重置令牌。

* **令牌泄漏**：如果令牌公开给了外部方，请重置令牌。
* **从单独的 GitHub 存储库进行部署**：如果要从单独的 GitHub 存储库中手动部署，则需要在新存储库中设置部署令牌。

## <a name="prerequisites"></a>先决条件

- 使用 Azure 静态 Web 应用配置的现有 GitHub 存储库。
- 如果没有，请参阅[生成第一个静态应用](getting-started.md)。

## <a name="reset-a-deployment-token"></a>重置部署令牌

1. 在 Azure Static Web Apps 站点的“概述”页面上，单击“管理部署令牌”链接。

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="正在管理部署令牌":::

1. 单击“重置令牌”按钮。

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="正在重置部署令牌":::

1. 在“部署令牌”字段中显示新令牌后，单击“复制到剪贴板”图标来复制该令牌。


## <a name="update-a-secret-in-the-github-repository"></a>更新 GitHub 存储库中的机密

要使自动部署保持运行，重置令牌后，需要在相应的 GitHub 存储库中设置新值。

1. 导航到 GitHub 上的项目存储库，并单击“设置”选项卡。
1. 单击“机密”菜单项。 将在“存储库机密”部分中看到 Static Web App 预配期间生成的名为 AZURE_STATIC_WEB_APPS_API_TOKEN...的机密 。

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="正在列出存储库机密":::

    > [!NOTE]
    > 如果为此存储库的多个分支创建了 Azure Static Web Apps 站点，则会在该列表中看到多个 AZURE_STATIC_WEB_APPS_API_TOKEN...机密。 通过匹配 Static Web Apps 站点的“概述”选项卡上的“编辑工作流”字段中列出的文件名，选择正确的机密。

1. 单击“更新”按钮以打开编辑器。
1. 将部署令牌的值粘贴到“值”字段。
1. 单击“更新机密”。

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="正在更新存储库机密":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从静态站点生成器发布](publish-gatsby.md)
