---
title: 教程：使用 Azure DevOps 发布 Azure Static Web Apps
description: 了解如何使用 Azure DevOps 发布 Azure Static Web Apps。
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 17a41bd64f1bba4a5ae4d6d9d497c03afae037e7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444219"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>教程：使用 Azure DevOps 发布 Azure Static Web Apps

本文演示如何使用 [Azure DevOps](https://dev.azure.com/) 部署到 [Azure Static Web Apps](./overview.md)。

在本教程中，学习：

- 设置 Azure Static Web Apps 站点
- 创建 Azure 管道以生成并发布静态 Web 应用

## <a name="prerequisites"></a>先决条件

- **有效的 Azure 帐户：** 如果你没有帐户，可以 [免费创建一个帐户](https://azure.microsoft.com/free/)。
- Azure DevOps 项目：如果你没有此项目，可以[免费创建一个项目](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/)。
  - Azure DevOps 包括 Azure Pipelines。 如需 Azure Pipelines 入门方面的帮助，请参阅[创建第一个管道](/azure/devops/pipelines/create-first-pipeline?preserve-view=true&view=azure-devops)。
  - 静态 Web 应用管道任务目前只适用于 Linux 计算机。 在运行下面提到的管道时，请确保它在 Linux VM 上运行。

## <a name="create-a-static-web-app-in-an-azure-devops"></a>在 Azure DevOps 中创建静态 Web 应用

  > [!NOTE]
  > 如果你的存储库中已有一个应用，则可以跳到下一部分。

1. 导航到 Azure Repos 中的存储库。

1. 选择“导入”开始导入示例应用程序。
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps 存储库":::

1. 在“克隆 URL”中输入 `https://github.com/staticwebdev/vanilla-api.git`。

1. 选择“导入”  。

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

1. 导航到 [Azure 门户](https://portal.azure.com)。

1. 选择“创建资源”。

1. 搜索“静态 Web 应用”。

1. 选择“静态 Web 应用”。

1. 选择“创建”  。

1. 在“部署详细信息”下，确保选择“其他”。 这样你就可以使用 Azure Repos 中的代码。

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="部署详细信息 - 其他":::

1. 部署成功后，导航到新的 Static Web Apps 资源。

1. 选择“管理部署令牌”。

1. 复制 **部署令牌** 并将其粘贴到文本编辑器中，以便在另一屏幕中使用。

    > [!NOTE]
    > 暂时不要理会此值，因为在后面的步骤中你还要复制并粘贴其他值。

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="部署令牌":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>在 Azure DevOps 中创建管道任务

1. 导航到前面创建的 Azure Repos 中的存储库。

1. 选择“设置生成”。

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="生成管道":::

1. 在“配置管道”屏幕中，选择“初学者管道”。

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="配置管道":::

1. 将以下 YAML 复制并粘贴到你的管道中。

    ```yaml
    trigger:
      - main

    pool:
      vmImage: ubuntu-latest

    steps:
      - checkout: self
        submodules: true
      - task: AzureStaticWebApp@0
        inputs:
          app_location: '/'
          api_location: 'api'
          output_location: ''
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > 如果使用的不是示例应用，则需要更改 `app_location`、`api_location` 和 `output_location` 的值，使其与应用程序中的值相匹配。

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token` 值是自我管理的，并且是手动配置的。

2. 选择“变量”。

3. 创建新变量。

4. 将该变量命名为 **deployment_token**（与工作流中的名称匹配）。

5. 复制前面已粘贴到文本编辑器中的部署令牌。

6. 将该部署令牌粘贴到“值”框中。

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="变量令牌":::

7. 选择“将此值保密”。

8. 选择“确定”。

9. 选择“保存”，返回到管道 YAML。

10. 选择“保存并运行”打开“保存并运行”对话框。

    管道

11. 选择“保存并运行”以运行管道。

12. 部署成功后，导航到 Azure Static Web Apps 的“概述”，其中包含了部署配置的链接。 请注意“源”链接现在如何指向 Azure DevOps 存储库的分支和位置。

13. 选择“URL”查看新部署的网站。

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="部署位置":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置 Azure Static Web Apps](./configuration.md)
