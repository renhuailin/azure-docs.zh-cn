---
title: 发现 Microsoft Learn 中的 Bicep
description: 概述 Microsoft Learn 中可用的 Bicep 单元。
ms.topic: conceptual
ms.date: 08/08/2021
ms.openlocfilehash: a0459e44c4abd69810bcc70974ea2e160adf5a5e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739735"
---
# <a name="bicep-on-microsoft-learn"></a>Microsoft Learn 上的 Bicep

有关使用 Bicep 将基础结构部署到 Azure 的分步指导，Microsoft Learn 提供了多个学习模块。

## <a name="introductory-path"></a>介绍性路径

[在 Azure 中使用 Bicep 部署和管理资源](/learn/paths/bicep-deploy/)学习路径是最佳的入门课程。 该路径向你介绍了基础结构作为代码的概念。 该路径将向你逐步介绍成编译越来越复杂的 Bicep 文件的步骤。

该路径包含以下模块。

| Learn 模块 | 说明 |
| ------------ | ----------- |
| [使用 Bicep 的基础结构即代码简介](/learn/modules/introduction-to-infrastructure-as-code-using-bicep/) | 此模块介绍了使用基础结构即代码、Azure 资源管理器和 Bicep 快速且自信地缩放云部署的好处。 帮助你选择部署类型，让 Bicep 成为一个很好的部署工具。 |
| [编译你的第一个 Bicep 模板](/learn/modules/build-first-bicep-template/) | 在此模块中，你将在 Bicep 模板中定义 Azure 资源。 你将提高部署的一致性和可靠性，减少所需的手动工作量，并跨环境缩放部署。 通过使用参数、变量、表达式和模块，你的模板将非常灵活，并且可以重复使用。 |
| [使用参数编译可重新使用的 Bicep 模板](/learn/modules/build-reusable-bicep-templates-parameters/) | 此模块介绍如何在每个部署过程中使用 Bicep 参数为模板提供信息。 你将了解参数修饰器，让参数易于理解和使用。 你还将了解在处理安全信息时，提供并保护参数值的不同方式。 |
| [使用条件和循环编译灵活的 Bicep 模板](/learn/modules/build-flexible-bicep-templates-conditions-loops/) | 了解如何仅在存在特定约束的情况下使用条件部署资源。 此外，还要了解如何使用循环来部署具有类似属性的多个资源。 |
| [使用 Bicep 部署子资源和扩展资源](/learn/modules/child-extension-bicep-templates/) | 此模块演示了如何在 Bicep 代码中部署各种 Azure 资源。 了解子资源和扩展资源，以及如何在 Bicep 中定义和使用这些资源。 使用 Bicep 处理在 Bicep 模板或模块外部创建的资源。 |
| [使用 Bicep 将资源部署至订阅、管理组和租户](/learn/modules/deploy-resources-scopes-bicep/) | 在订阅、管理组和租户范围内部署 Azure 资源。 了解这些资源是什么、为何使用它们，以及如何创建 Bicep 代码来部署它们。 此外，还要了解如何创建一组 Bicep 文件，让你可以在一个操作中进行跨多个范围的部署。 |
| [使用部署脚本扩展模板](/learn/modules/extend-resource-manager-template-deployment-scripts/) | 了解如何使用部署脚本将自定义步骤添加到你的 Bicep 文件或 Azure 资源管理器模板（ARM 模板）中。 |

## <a name="other-modules"></a>其他模块

除上述路径之外，以下模块也包含 Bicep 内容。

| Learn 模块 | 说明 |
| ------------ | ----------- |
| [使用 Git 管理对 Bicep 代码的更改](/learn/modules/manage-changes-bicep-code-git/) | 了解如何使用 Git，通过跟踪工作时所做的更改来支持 Bicep 开发工作流。 用户将了解如何提交文件、如何查看所更改文件的历史记录，以及如何使用分支同时开发多个版本的代码。 你还将了解如何使用 GitHub 或 Azure Repos 发布存储库，以便与团队成员协作。 |
| [使用模板规格发布可重用的基础结构代码库](/learn/modules/arm-template-specs/) | 使用模板规格，可以在组织中重用和共享 ARM 模板。 了解如何创建和发布模板规格，以及如何部署它们。 你还将了解如何管理模板规格，包括如何控制访问以及如何使用版本安全地进行更新。 |
| [使用 What-if 预览 Azure 部署更改](/learn/modules/arm-template-whatif/) | 此模块向你介绍了如何通过 What-if 操作预览更改。 通过使用 what-if，你可以确保 Bicep 文件仅进行你指定的更改。 |
| [构建用于协作的 Bicep 代码](/learn/modules/structure-bicep-code-collaboration/) | 生成支持协作开发的 Bicep 文件并遵循最佳做法。 规划参数，使模板易于部署。 使用一致的样式、清晰的结构和注释来使 Bicep 代码易于理解、使用和修改。 |
| [使用服务主体对 Azure 部署管道进行身份验证](/learn/modules/authenticate-azure-deployment-pipeline-service-principals/) | 通过服务主体，部署管道能够安全地向 Azure 进行身份验证。 在本模块中，你将了解服务主体的本质、其工作原理及创建方式。 你还将学习如何向它们授予对 Azure 资源的权限，以便管道可部署 Bicep 文件。 |
| [使用 Azure Pipelines 构建你的第一个 Bicep 部署管道](/learn/modules/build-first-bicep-deployment-pipeline-using-azure-pipelines/) | 为 Bicep 代码构建基本的部署管道。 使用服务连接将管道安全标识到 Azure。 使用触发器配置管道运行的时间。 |

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 的简短介绍，请参阅 [Bicep 快速入门](quickstart-create-bicep-use-visual-studio-code.md)。
* 若要了解如何改进 Bicep 文件，请参阅 [Bicep 最佳做法](best-practices.md)。
