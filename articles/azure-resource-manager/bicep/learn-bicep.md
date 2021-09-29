---
title: 发现 Microsoft Learn 中的 Bicep
description: 概述 Microsoft Learn 中可用的 Bicep 单元。
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 71af6012dcdd9cd5b951a0edbe8f244fb3a8be26
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793505"
---
# <a name="bicep-on-microsoft-learn"></a>Microsoft Learn 上的 Bicep

准备了解 Bicep 如何帮助简化和加速部署到 Azure？ 查看 Microsoft Learn 上的多个实践课程。

## <a name="get-started"></a>入门

这两个学习路径将帮助你入门：

:::row:::
:::column:::
<img src="media/learn-bicep/bicep-deploy-manage.svg" width="101" height="120" alt="The trophy for the Deploy and manage resources in Azure by using Bicep learning path." role="presentation"></img>

[第 1 部分：使用 Bicep 在 Azure 中部署和管理资源](/learn/paths/bicep-deploy/)

:::column-end:::
:::column:::
<img src="media/learn-bicep/bicep-collaborate.svg" width="101" height="120" alt="The trophy for the Build Azure infrastructure in a team environment by using Bicep learning path." role="presentation"></img>

[第 2 部分：使用 Bicep 在团队环境中构建 Azure 基础结构](/learn/paths/bicep-collaborate/)

:::column-end:::
:::row-end:::

## <a name="azure-pipelines-and-github-actions-modules"></a>Azure Pipelines 和 GitHub Actions 模块

除了前面的学习路径，以下模块还包含与 Azure Pipelines 和 GitHub Actions 相关的 Bicep 内容。

| Learn 模块 | 说明 |
| ------------ | ----------- |
| [使用 Azure Pipelines 构建你的第一个 Bicep 部署管道](/learn/modules/build-first-bicep-deployment-pipeline-using-azure-pipelines/) | 为 Bicep 代码构建基本的部署管道。 使用服务连接将管道安全标识到 Azure。 使用触发器配置管道运行的时间。 |
| [使用 GitHub Actions 构建第一个 Bicep 部署工作流](/learn/modules/build-first-bicep-deployment-pipeline-using-github-actions/) | 为 Bicep 代码构建基本的部署工作流。 使用机密将 GitHub Actions 工作流安全地标识到 Azure，然后使用触发器和计划设置工作流的运行时间。 |
| [使用服务主体对 Azure 部署管道进行身份验证](/learn/modules/authenticate-azure-deployment-pipeline-service-principals/) | 通过服务主体，部署管道能够安全地向 Azure 进行身份验证。 在本模块中，你将了解服务主体的本质、其工作原理及创建方式。 你还将学习如何向它们授予对 Azure 资源的权限，以便管道可部署 Bicep 文件。 |
| [使用 Azure Pipelines 测试 Bicep 代码](/learn/modules/test-bicep-code-using-azure-pipelines/) | 在部署管道中验证和测试 Bicep 代码。 在部署之前，使用 Lint 分析、预检验证和 What-if 操作来验证 Azure 更改，并在每次部署后测试资源。 |

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 的简短介绍，请参阅 [Bicep 快速入门](quickstart-create-bicep-use-visual-studio-code.md)。
* 若要了解如何改进 Bicep 文件，请参阅 [Bicep 最佳做法](best-practices.md)。
