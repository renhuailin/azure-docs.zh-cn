---
title: LUIS 应用持续集成和持续交付工作流
description: 如何为“语言理解 (LUIS)”实现 DevOps 的 CI/CD 工作流。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/01/2021
ms.author: aahi
author: aahill
ms.manager: nitinme
ms.openlocfilehash: 7079c1ee309db9563142c54eea88ccd4ba6f6e28
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463632"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>LUIS DevOps 持续集成和持续交付工作流

正在开发语言理解 (LUIS) 应用的软件工程师可以对[源代码管理](luis-concept-devops-sourcecontrol.md)、[自动生成](luis-concept-devops-automation.md)、[测试](luis-concept-devops-testing.md)和[发布管理](luis-concept-devops-automation.md#release-management)应用 DevOps 实践。 本文介绍有关实现 LUIS 的自动化生成的概念。

## <a name="build-automation-workflows-for-luis"></a>生成 LUIS 的自动化工作流

![CI 工作流](./media/luis-concept-devops-automation/luis-automation.png)

在源代码管理 (SCM) 系统中，将自动化生成管道配置为在发生以下事件时运行：

1. 当有[拉取请求](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) 发起时，触发“PR 工作流”。 在将更新合并到主分支之前，此工作流要验证 PR 的内容。
1. 在将更新推送到主分支时（例如当合并来自 PR 的更改时），触发“CI/CD 工作流”。 此工作流可确保主分支的所有更新的质量。

“CI/CD 工作流”合并两个补充开发进程：

* [持续集成](/devops/develop/what-is-continuous-integration) (CI) 是经常在共享存储库中提交代码并对其执行自动生成的工程实践。 在结合了自动化[测试](luis-concept-devops-testing.md)方法后，持续集成使我们能够验证：对于每个更新，LUDown 源仍有效并且可以导入到 LUIS 应用中，同时还会通过一组测试来验证定型应用是否可识别解决方案所需的意图和实体。

* [持续交付](/devops/deliver/what-is-continuous-delivery) (CD) 在持续集成概念的基础上更进了一步，可将应用程序自动部署到可执行更深入测试的环境中。 CD 使我们能够及早了解我们所做的更改所带来的任何无法预料的问题，还可以了解我们的测试覆盖率与目标的差距。

持续集成和持续交付的目标是确保“主分支始终可提供服务”。 对于 LUIS 应用程序，这意味着，如果需要，我们可以从主分支 LUIS 应用中获取任何版本并将其交付生产。

### <a name="tools-for-building-automation-workflows-for-luis"></a>LUIS 自动化工作流生成工具

> [!TIP]
> 可以在 [LUIS DevOps 模板存储库](#apply-devops-to-luis-app-development-using-github-actions)中找到用于实现 DevOps 的完整解决方案。

有多种不同的生成自动化技术可用于创建生成自动化工作流。 所有这些方法都要求使用命令行接口 (CLI) 或 REST 调用来编写步骤的脚本，以便可以在生成服务器上执行。

使用以下 LUIS 自动化工作流生成工具：

* 使用 [Bot Framework 工具 LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) 在 LUIS 服务中处理 LUIS 应用及其各种版本，并对其进行定型、测试和发布。

* 使用 [Azure CLI](/cli/azure/) 查询 Azure 订阅、提取 LUIS 创作和预测密钥，并创建 Azure [服务主体](/cli/azure/ad/sp)用于自动化身份验证。

* 使用用于[测试 LUIS 应用](luis-concept-devops-testing.md)的 [NLU.DevOps](https://github.com/microsoft/NLU.DevOps)工具来分析测试结果。

### <a name="the-pr-workflow"></a>PR 工作流

如上所述，将此工作流配置为在以下操作发生时运行：开发人员发起 PR 并请求实施要从功能分支合并到主分支的更改。 该配置的目的是在 PR 中的更改合并到主分支之前验证它们的质量。

此工作流应：

* 通过在 PR 中导入 `.lu` 源来创建临时 LUIS 应用。
* 定型和发布 LUIS 应用版本。
* 对该应用运行所有[单元测试](luis-concept-devops-testing.md)。
* 如果所有测试均通过，则顺利通过工作流，否则不通过。
* 清理并删除临时应用。

如果 SCM 支持，请配置分支保护规则，使操作者必须成功完成此工作流才能完成 PR。

### <a name="the-main-branch-cicd-workflow"></a>主分支 CI/CD 工作流

将此工作流配置为在 PR 中的更新合并到主分支后运行。 其目的是通过测试更新来提升主分支的质量。 如果更新满足质量要求，则此工作流将新的 LUIS 应用版本部署到可执行更深入测试的环境。

此工作流应：

* 使用已更新的源代码在主 LUIS 应用（为主分支维护的应用）中生成新版本。

* 定型和发布 LUIS 应用版本。

  > [!NOTE]
  > 如[在自动化生成工作流中运行测试](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow)中所述，你发布的 LUIS 应用版本必须经过测试，这样 NLU.DevOps 等工具才可以访问它。 LUIS 仅支持两个命名的发布槽：“过渡”和“生产”，用于 LUIS 应用，但也可以[直接发布版本](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)和[按版本查询](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name) 。 在自动化工作流中使用直接版本发布，以避免仅限使用命名发布槽。

* 运行所有[单元测试](luis-concept-devops-testing.md)。

* （可选）运行[批测试](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing)以测量 LUIS 应用版本的质量和准确性，并将其与某个基线进行比较。

* 如果测试成功完成：
  * 在存储库中标记源。
  * 运行持续交付 (CD) 作业，将 LUIS 应用版本部署到环境中，以便进行进一步的测试。

### <a name="continuous-delivery-cd"></a>持续交付 (CD)

CI/CD 工作流中的 CD 作业会根据设置的条件在生成和自动化单元测试成功后运行。 其工作是自动将 LUIS 应用程序部署到可以执行更多测试的环境。

不存在所谓的最佳 LUIS 应用部署解决方案，必须根据具体项目的需要来实现此部署过程。 [LUIS DevOps 模板](https://github.com/Azure-Samples/LUIS-DevOps-Template)存储库为此过程实现了一个简单的解决方案：[将新的 LUIS 应用版本发布](./luis-how-to-publish-app.md)到“生产”发布槽。 这适用于简单的设置。 但是，如果需要同时支持多个不同的生产环境（例如，“开发”、“暂存”和“UAT”），则为每个应用配置两个命名的发布槽就不够了  。

用于部署某个应用版本的其他选项包括：

* 仍然将该应用版本发布到直接版本终结点，并实现一个进程，用于根据需要使用直接版本终结点配置下游生产环境。
* 为每个生产环境维护不同的 LUIS 应用，并编写自动化步骤，以将 `.lu` 导入到用于目标生产环境的 LUIS 应用中的新版本中，以便对其进行定型和发布。
* 将测试过的 LUIS 应用版本导出到 [LUIS Docker 容器](./luis-container-howto.md?tabs=v3)中，并将 LUIS 容器部署到 Azure [容器实例](../../container-instances/index.yml)。

## <a name="release-management"></a>发布管理

通常，我们建议你仅对非生产环境（例如开发和暂存）进行持续交付。 大多数团队需要一个手动审查和批准过程才能实现对生产环境的部署。 对于生产部署，你可能希望确保它在开发团队中的关键人员可提供支持时或在低流量时期执行。


## <a name="apply-devops-to-luis-app-development-using-github-actions"></a>使用 GitHub Actions 将 DevOps 应用于 LUIS 应用开发

请转到 [LUIS DevOps 模板存储库](https://github.com/Azure-Samples/LUIS-DevOps-Template)，以获取针对 LUIS 实现 DevOps 和软件工程最佳做法的完整解决方案。 可以使用此模板存储库创建自己的存储库，其中提供对 CI/CD 工作流和做法的内置支持，这些做法通过 LUIS 为项目实现[源代码管理](luis-concept-devops-sourcecontrol.md)、自动生成、[测试](luis-concept-devops-testing.md)和发布管理。

[LUIS DevOps 模板存储库](https://github.com/Azure-Samples/LUIS-DevOps-Template)演练如何执行以下操作：

* 克隆模板存储库 - 将模板复制到自己的 GitHub 存储库。
* 配置 LUIS 资源 - [在 Azure 中创建 LUIS 创作和预测资源](./luis-how-to-azure-subscription.md)，这些资源将由持续集成工作流使用。
* 配置 CI/CD 工作流 - 配置 CI/CD 工作流的参数并将它们存储在 [GitHub 机密](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)中。
* 演练[“开发内部循环”](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow) - 开发人员在开发分支中工作时对示例 LUIS 应用进行更新，测试更新，然后引发拉取请求来提议更改并寻求审批。
* 执行 CI/CD 工作流 - 使用 GitHub Actions 执行[持续集成工作流以生成和测试 LUIS 应用](#build-automation-workflows-for-luis)。
* 执行自动测试 - 执行 [LUIS 应用的自动批处理测试](luis-concept-devops-testing.md)以评估应用的质量。
* 部署 LUIS 应用 - 执行[持续交付 (CD) 作业](#continuous-delivery-cd)以发布 LUIS 应用。
* 将存储库用于自己的项目 - 说明如何将存储库用于自己的 LUIS 应用程序。

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 NLU.DevOps 编写 GitHub Actions 工作流](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)

* 使用 [LUIS DevOps 模板存储库](https://github.com/Azure-Samples/LUIS-DevOps-Template)将 DevOps 应用于自己的项目。
* [LUIS 的源代码管理和分支策略](luis-concept-devops-sourcecontrol.md)
* [LUIS DevOps 测试](luis-concept-devops-testing.md)
