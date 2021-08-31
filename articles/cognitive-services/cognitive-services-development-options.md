---
title: Azure 认知服务开发选项
description: 了解如何将 Azure 认知服务用于各种开发和部署选项，例如客户端库、REST API、逻辑应用、Power Automate、Azure Functions、Azure 应用服务、Azure Databricks，等等。
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 8c40af42248e41346d10ec0028c1f13ff7ef22d6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114457940"
---
# <a name="cognitive-services-development-options"></a>认知服务开发选项

本文档概述了有助于你完成 Azure 认知服务入门的开发和部署选项。

Azure 认知服务是基于云的 AI 服务，使开发人员无需深入了解机器学习即可将智能构建到其应用程序和产品中。 使用认知服务，你可以访问由 Microsoft 构建、训练和更新的可以在你的应用程序中使用的 AI 功能或模型。 在许多情况下，你还可以根据业务需求自定义这些模型。 

认知服务分为四个类别：决策、语言、语音和视觉。 通常，你可以通过 Microsoft 提供的 REST API、客户端库和自定义工具（例如命令行界面）访问这些服务。 但是，这只是通向成功的一个路径。 通过 Azure，你还可以访问若干开发选项，例如：

* 自动化和集成工具，例如逻辑应用和 Power Automate。
* 部署选项，例如 Azure Functions 和应用服务。 
* 用于安全访问的认知服务 Docker 容器。
* Apache Spark、Azure Databricks、Azure Synapse Analytics 以及适用于大数据场景的 Azure Kubernetes 服务等工具。 

在我们开始之前，请务必了解认知服务主要用于两个不同的任务。 根据要执行的任务，你可以在不同的开发和部署选项中进行选择。 

* [用于预测和分析的开发选项](#development-options-for-prediction-and-analysis)
* [用于自定义和配置模型的工具](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>用于预测和分析的开发选项 

用于自定义和配置模型的工具与用于调用认知服务的工具不同。 大多数认知服务都允许你直接发送数据和接收见解，不需要进行任何自定义。 例如： 

* 可以将图像发送到计算机视觉服务，以检测字词和短语或统计帧中的人数
* 可以将音频文件发送到语音服务并获取听录，同时将语音转换为文本
* 可以将 PDF 发送到表单识别器服务，并检测表、单元格以及这些单元格中的文本，获得包含坐标和详细信息的 JSON 输出

Azure 提供了各种类型的工具，这些工具适用于不同类型的用户，其中许多工具可用于认知服务。 设计器驱动的工具是最容易使用的工具，可快速设置和自动运行，但在自定义方面可能有限制。 我们的 REST API 和客户端库为用户提供了更多控制和灵活性，但需要更多的工作量、时间和专业知识来构建解决方案。 如果你使用 REST API 和客户端库，则需要熟悉现代编程语言，例如 C#、Java、Python、JavaScript 或其他常用编程语言。 

让我们看看使用认知服务的各种方式。

### <a name="client-libraries-and-rest-apis"></a>客户端库和 REST API

使用认知服务客户端库和 REST API，你可以直接访问你的服务。 这些工具提供对认知服务及其基线模型的编程访问，并且在许多情况下允许以编程方式自定义模型和解决方案。 

* **目标用户**：开发人员和数据科学家
* **优点：** 提供最大灵活性，允许从任何语言和环境调用服务。 
* **UI**：不适用 - 仅限代码
* **订阅**：Azure 帐户 + 认知服务资源

若要详细了解可用的客户端库和 REST API，请使用我们的[认知服务概述](index.yml)选取服务，并开始学习关于视觉、决策、语言和语音的快速入门之一。

### <a name="cognitive-services-for-big-data"></a>适用于大数据的认知服务

借助适用于大数据的认知服务，可以将不断改进的智能模型直接嵌入 Apache Spark&trade; 和 SQL 计算中。 这些工具将开发人员从低级网络细节中解放出来，使他们可以专注于创建智能的分布式应用程序。 适用于大数据的认知服务支持以下平台和连接器：Azure Databricks、Azure Synapse、Azure Kubernetes 服务和数据连接器。

* **目标用户**：数据科学家和数据工程师
* 优点：适用于大数据的 Azure 认知服务让用户可以使用 Apache Spark 通过认知服务传输 TB 级数据&trade;。 可以轻松借助任何数据存储创建大规模的智能应用程序。
* **UI**：不适用 - 仅限代码
* **订阅**：Azure 帐户 + 认知服务资源

如果要了解有关认知服务大数据的详细信息，请参阅[概述](./big-data/cognitive-services-for-big-data.md)。 如果你已准备好开始构建，请尝试我们的 [Python](./big-data/samples-python.md) 或 [Scala](./big-data/samples-scala.md) 示例。

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions 和 Azure 服务 Web 作业

[Azure Functions](../azure-functions/index.yml) 和 [Azure 应用服务 Web 作业](../app-service/index.yml)都提供了针对开发人员设计的代码优先的集成服务，并且是基于 [Azure 应用服务](../app-service/index.yml)构建的。 这些产品提供了用于编写代码的无服务器基础结构。 在该代码中，你可以使用我们的客户端库和 REST REST API 来调用服务。 

* **目标用户**：开发人员和数据科学家
* **优点：** 无服务器计算服务，可用于运行事件触发的代码。 
* **UI**：是
* **订阅**：Azure 帐户 + 认知服务资源 + Azure Functions 订阅

### <a name="azure-logic-apps"></a>Azure 逻辑应用 

[Azure 逻辑应用](../logic-apps/index.yml)与 Power Automate 共享相同的工作流设计器和连接器，但提供了更高级的控制，包括与 Visual Studio 和 DevOps 的集成。 有了 Power Automate，就可以通过特定于服务的连接器轻松地与认知服务资源集成，这些连接器围绕 API 提供代理或包装器。 它们与 Power Automate 中提供的连接器相同。 

* **目标用户**：开发人员、集成人员、IT 专业人员、开发运营人员
* **优点：** 设计器优先（声明性）开发模型，在低代码解决方案中提供高级选项和集成
* **UI**：是
* **订阅**：Azure 帐户 + 认知服务资源 + 逻辑应用部署

### <a name="power-automate"></a>Power Automate 

Power Automate 是 [Power Platform](/power-platform/) 中的一项服务，可帮助你在应用和服务之间创建自动化工作流，无需编写代码。 我们提供了多个连接器，使你可以轻松地在 Power Automate 解决方案中与认知服务资源进行交互。 Power Automate 基于逻辑应用而构建。 

* **目标用户**：业务用户（分析人员）和 SharePoint 管理员
* **优点：** 只需从桌面记录鼠标点击、击键和复制粘贴步骤，便可自动执行重复的手动任务！
* **UI 工具**：是 - 仅限 UI
* **订阅**：Azure 帐户 + 认知服务资源 + Power Automate 订阅 + Office 365 订阅

### <a name="ai-builder"></a>AI Builder 

[AI Builder](/ai-builder/overview) 是一种 Microsoft Power Platform 功能，可以用来自动执行流程并预测结果以提高业务绩效。 AI Builder 通过点击式体验，在你的解决方案中引入了 AI 功能。 许多认知服务（例如表单识别器、文本分析和计算机视觉）已直接集成在这里，你不需要创建自己的认知服务。 

* **目标用户**：业务用户（分析人员）和 SharePoint 管理员
* **优点：** 统包式解决方案，通过点击体验引入 AI 功能。 无需编码或数据科学技能。
* **UI 工具**：是 - 仅限 UI
* **订阅**：AI Builder

### <a name="continuous-integration-and-deployment"></a>持续集成和持续部署

你可以使用 Azure DevOps 和 GitHub Actions 来管理你的部署。 在[下面的部分中](#continuous-integration-and-delivery-with-devops-and-github-actions)，我们提供了两个 CI/CD 集成示例，用于训练和部署语音识别和语言理解 ( LUIS) 服务的自定义模型。 

* **目标用户**：开发人员、数据科学家和数据工程师
* **优点：** 允许你以编程方式持续调整、更新和部署应用程序和模型。 当定期使用你的数据来改进和更新语音、视觉、语言和决策的模型时，这有明显的优势。 
* **UI 工具**：不适用 - 仅限代码 
* **订阅**：Azure 帐户 + 认知服务资源 + GitHub 帐户

## <a name="tools-to-customize-and-configure-models"></a>用于自定义和配置模型的工具

当你继续操作，使用认知服务构建应用程序或工作流时，你可能会发现需要自定义该模型以实现所需的性能。 我们的许多服务都允许你基于预建模型进行构建以满足你的特定业务需求。 对于我们的所有可自定义的服务，我们都提供了用于演练流程的 UI 驱动体验，以及用于代码驱动训练的 API。 例如：

* 你希望训练一个自定义语音识别模型，以小于 3% 的单词错误率 (WER) 来正确识别医学术语
* 你希望使用自定义视觉构建一个图像分类器，该分类器能够区分针叶树和落叶树
* 你希望使用你的个人语音数据构建自定义神经语音，以获得改进的自动化客户体验

用于训练和配置模型的工具与用于调用认知服务的工具不同。 许多情况下，支持自定义的认知服务提供了设计用来帮助你训练、评估和部署模型的门户和 UI 工具。 让我们快速看一下几个选项：<br><br>

| 构成要素 | 服务 | 自定义 UI | 快速入门 |
|--------|---------|------------------|------------|
| 影像 | 自定义视觉 | https://www.customvision.ai/ | [快速入门](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| 影像 | 表单识别器 | [示例标记工具](https://aka.ms/fott-2.1-ga) | [操作指南文章](./form-recognizer/label-tool.md?tabs=v2-0) |
| 决策 | 内容审查器 | https://contentmoderator.cognitive.microsoft.com/dashboard | [快速入门](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| 决策 | 个性化体验创建服务 | Azure 门户的个性化体验创建服务中提供 UI。 | [快速入门](./personalizer/quickstart-personalizer-sdk.md) |
| 语言 | 语言理解 (LUIS) | https://www.luis.ai/ | |
| 语言 | QnA Maker | https://www.qnamaker.ai/ | [快速入门](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| 语言 | 翻译/自定义翻译工具 | https://portal.customtranslator.azure.ai/ | [快速入门](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| 语音 | 自定义命令 | https://speech.microsoft.com/ | [快速入门](./speech-service/custom-commands.md) |
| 语音 | 自定义语音识别 | https://speech.microsoft.com/ | [快速入门](./speech-service/custom-speech-overview.md) |
| 语音 | 自定义语音 | https://speech.microsoft.com/ | [快速入门](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>使用 DevOps 和 GitHub Actions 进行持续集成和交付

语言理解和语音服务提供持续集成和持续部署解决方案，这些解决方案由 Azure DevOps 和 GitHub Actions 提供支持。 这些工具用于对自定义模型进行自动训练、测试和发布管理。 

* [自定义语音识别的 CI/CD](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [LUIS 的 CI/CD](./luis/luis-concept-devops-automation.md)

## <a name="on-premises-containers"></a>本地容器 

许多认知服务可以部署在容器中，供本地访问和使用。 借助这些容器，你能够灵活地将认知服务移至更接近你的数据的位置，以满足合规性、安全性或其他操作目的。 有关认知服务容器的完整列表，请参阅[认知服务的本地容器](./cognitive-services-container-support.md)。

## <a name="next-steps"></a>后续步骤
<!--
* Learn more about low code development options for Cognitive Services -->
* [创建认知服务资源并开始构建](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)
