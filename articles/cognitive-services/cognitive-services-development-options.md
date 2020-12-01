---
title: Azure 认知服务开发选项
description: 了解如何将 Azure 认知服务用于不同的开发和部署选项，例如客户端库、REST Api、逻辑应用、电源自动、Azure Functions、Azure App Service、Azure Databricks 等。
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: a952bfa45515b9d35549d03d18fd94103679344d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349463"
---
# <a name="cognitive-services-development-options"></a>认知服务开发选项

本文档提供了用于帮助你开始使用 Azure 认知服务的开发和部署选项的高级概述。

Azure 认知服务是基于云的 AI 服务，使开发人员无需深入了解机器学习即可在其应用程序和产品中构建智能。 对于认知服务，你可以访问由 Microsoft 生成、培训和更新的 AI 功能或模型，以便在你的应用程序中使用。 在许多情况下，你还可以选择根据业务需求自定义模型。 

认知服务分为四类：决策、语言、语音和视觉。 通常，你可以通过 REST Api、客户端库和自定义工具 (访问这些服务，如 Microsoft 提供的命令行界面) 。 但是，这只是成功的一个路径。 通过 Azure，你还可以访问多个开发选项，例如：

* 自动化和集成工具，例如逻辑应用和 Power Automate。
* 部署选项，例如 Azure Functions 和应用服务。 
* 用于安全访问的认知服务 Docker 容器。
* Apache Spark、Azure Databricks、Azure Synapse Analytics 以及适用于大数据场景的 Azure Kubernetes 服务等工具。 

在开始之前，请务必了解认知服务主要用于两个不同的任务。 根据要执行的任务，您可以选择不同的开发和部署选项。 

* [用于预测和分析的开发选项](#development-options-for-prediction-and-analysis)
* [用于自定义和配置模型的工具](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>用于预测和分析的开发选项 

用于自定义和配置模型的工具与用于调用认知服务的工具不同。 最常见的是，大多数认知服务都允许您在没有任何自定义的情况下发送数据和接收见解。 例如： 

* 你可以将图像发送到计算机视觉服务，以检测字词和短语或统计帧中的人数
* 可以将音频文件发送到语音服务并获取转录并将语音转换为文本
* 可以将 PDF 发送到表单识别器服务，并在这些单元中检测表、单元格和文本，并获得包含坐标和详细信息的 JSON 输出

Azure 提供各种不同类型的工具，这些工具适用于不同类型的用户，其中的许多工具可用于认知服务。 设计器驱动的工具是最易于使用的工具，可快速设置和自动执行，但在自定义时可能会受到限制。 REST Api 和客户端库为用户提供了更多控制和灵活性，但需要更多的工作量、时间和专业知识来构建解决方案。 如果你使用 REST Api 和客户端库，则需要熟悉 c #、Java、Python、JavaScript 或其他常用编程语言等新式编程语言。 

让我们看看使用认知服务的不同方式。

### <a name="client-libraries-and-rest-apis"></a>客户端库和 REST API

认知服务客户端库和 REST Api 使你能够直接访问你的服务。 这些工具提供对认知服务、其基线模型的编程访问，并且在许多情况下允许以编程方式自定义模型和解决方案。 

* **目标用户 ()**：开发人员和数据科学家
* **优势**：提供从任何语言和环境调用服务的最大灵活性。 
* **UI**：仅限暂缺
* **) 订阅 (**： Azure 帐户 + 认知服务资源

如果你想要了解有关可用客户端库和 REST Api 的详细信息，请使用我们的 [认知服务概述](index.yml) 来选择并提供服务，并开始使用我们的一个快速入门教程来实现视觉、决策、语言和语音。

### <a name="cognitive-services-for-big-data"></a>适用于大数据的认知服务

借助适用于大数据的认知服务，可以将不断改进的智能模型直接嵌入 Apache Spark&trade; 和 SQL 计算中。 这些工具将开发人员从低级网络细节中解放出来，使他们可以专注于创建智能的分布式应用程序。 适用于大数据的认知服务支持以下平台和连接器： Azure Databricks、Azure Synapse、Azure Kubernetes 服务和数据连接器。

* **目标用户 ()**：数据科学家和数据工程师
* **优势**：适用于大数据的 Azure 认知服务使用户可以使用 Apache Spark 通过认知服务通道 tb 的数据 &trade; 。 可以轻松地通过任何数据存储创建大规模的智能应用程序。
* **UI**：仅限暂缺
* **) 订阅 (**： Azure 帐户 + 认知服务资源

如果要了解有关认知服务的大数据的详细信息，请参阅 [概述](./big-data/cognitive-services-for-big-data.md)。 如果你已准备好开始构建，请尝试我们的 [Python](./big-data/samples-python.md) 或 [Scala](./big-data/samples-scala.md) 示例。

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions 和 Azure 服务 Web 作业

[Azure Functions](../azure-functions/index.yml) 和 [Azure App Service Web 作业](../app-service/index.yml) 都提供专为开发人员设计的代码优先的集成服务，并且是在 [Azure 应用服务](../app-service/index.yml)上构建的。 这些产品提供用于编写代码的无服务器基础结构。 在该代码中，你可以使用我们的客户端库和 REST Api 对我们的服务进行调用。 

* **目标用户 ()**：开发人员和数据科学家
* **优势**：无服务器计算服务，允许你运行事件触发的代码。 
* **UI**：是
* **) 订阅 (**： Azure 帐户 + 认知服务资源 + Azure Functions 订阅

### <a name="azure-logic-apps"></a>Azure 逻辑应用 

[Azure 逻辑应用](../logic-apps/index.yml) 与电源自动化共享相同的工作流设计器和连接器，但提供了更高级和更多控制，包括与 Visual Studio 和 DevOps 的集成。 利用电源自动，可以通过特定于服务的连接器（提供 Api 或围绕 Api 的包装器）轻松地与认知服务资源集成。 它们与电源自动中的可用连接器相同。 

* **目标用户 ()**：开发人员、集成者、IT 专业人员、DevOps
* **优势**：设计器优先 (声明性) 开发模型，在低代码解决方案中提供高级选项和集成
* **UI**：是
* **) 订阅 (**： Azure 帐户 + 认知服务资源 + 逻辑应用部署

### <a name="power-automate"></a>Power Automate 

Power 自动功能是 [Power 平台](/power-platform/) 中的一项服务，可帮助你在应用和服务之间创建自动化工作流，而无需编写代码。 我们提供了多个连接器，使你可以轻松地在 Power 自动化解决方案中与认知服务资源进行交互。 Power Automate 基于逻辑应用而构建。 

* **目标用户 ()**：业务用户 (分析人员) 和 Sharepoint 管理员
* **优点**：只需记录鼠标单击、击键和从桌面复制粘贴步骤，就能自动执行重复的手动任务！
* **Ui 工具**：是-仅限 ui
* **订阅 ()**： Azure 帐户 + 认知服务资源 + 电源自动执行订阅 + Office 365 订阅

### <a name="ai-builder"></a>AI Builder 

[AI Builder](/ai-builder/overview) 是一种 Microsoft 电源平台功能，可用于通过自动执行流程和预测结果来提高业务绩效。 AI 生成器通过点击体验将 AI 功能带入您的解决方案。 许多认知服务（如窗体识别器、文本分析和计算机视觉）已直接集成在这里，无需创建自己的认知服务。 

* **目标用户 ()**：业务用户 (分析人员) 和 Sharepoint 管理员
* **优势**：一个全包式解决方案，它通过点击体验实现了 AI 的强大功能。 无需编码或数据科学技能。
* **Ui 工具**：是-仅限 ui
* **订阅 ()**： AI 生成器

### <a name="continuous-integration-and-deployment"></a>持续集成和持续部署

你可以使用 Azure DevOps 和 GitHub 操作来管理你的部署。 在下面讨论的 [部分](#continuous-integration-and-delivery-with-devops-and-github-actions) 中，我们提供了两个 CI/CD 集成示例，用于为语音和语言理解 (LUIS) 服务定型和部署自定义模型。 

* **目标用户 ()**：开发人员、数据科学家和数据工程师
* **优势**：允许你以编程方式持续调整、更新和部署应用程序和模型。 如果经常使用您的数据来改善和更新语音、视觉、语言和决策的模型，则存在明显的好处。 
* **UI 工具**：仅适用于非代码 
* **) 订阅 (**： Azure 帐户 + 认知服务资源 + GitHub 帐户

## <a name="tools-to-customize-and-configure-models"></a>用于自定义和配置模型的工具

当你在使用认知服务构建应用程序或工作流时，你可能会发现需要自定义该模型以实现所需的性能。 许多服务都允许您根据自己的特定业务需求在预建模型之上构建。 对于所有可自定义的服务，我们都提供 UI 驱动的体验，用于完成过程，并提供代码驱动定型的 Api。 例如：

* 你需要训练自定义语音模型，以便使用字错误率正确识别医学术语， (WER) 低于3%
* 想要生成一个带有自定义视觉的图像分类器，它可以说明 coniferous 和落叶树之间的差异
* 您希望使用您的个人语音数据构建自定义的神经语音，以获得改进的自动化客户体验

用于定型和配置模型的工具不同于用于调用认知服务的工具。 在许多情况下，支持自定义的认知服务提供门户和 UI 工具，旨在帮助你训练、评估和部署模型。 让我们快速看一下几个选项：<br><br>

| 构成要素 | 服务 | 自定义 UI | 快速入门 |
|--------|---------|------------------|------------|
| 影像 | 自定义视觉 | https://www.customvision.ai/ | [快速入门](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| 影像 | 表单识别器 | 示例标记工具 | [快速入门](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| 决策 | 内容审查器 | https://contentmoderator.cognitive.microsoft.com/dashboard | [快速入门](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| 决策 | 指标顾问 | https://metricsadvisor.azurewebsites.net/  | [快速入门](./metrics-advisor/quickstarts/web-portal.md) |
| 决策 | 个性化体验创建服务 | 在 Personalizer 资源下的 Azure 门户中提供 UI。 | [快速入门](./personalizer/quickstart-personalizer-sdk.md) |
| 语言 | 语言理解 (LUIS) | https://www.luis.ai/ | |
| 语言 | QnA Maker | https://www.qnamaker.ai/ | [快速入门](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| 语言 | 翻译人员/自定义转换器 | https://portal.customtranslator.azure.ai/ | [快速入门](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| 语音 | 自定义命令 | https://speech.microsoft.com/ | [快速入门](./speech-service/custom-commands.md) |
| 语音 | 自定义语音识别 | https://speech.microsoft.com/ | [快速入门](./speech-service/custom-speech-overview.md) |
| 语音 | 自定义语音 | https://speech.microsoft.com/ | [快速入门](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>与 DevOps 和 GitHub 操作进行持续集成和交付

语言理解和语音服务提供持续集成和持续部署解决方案，这些解决方案由 Azure DevOps 和 GitHub 操作提供支持。 这些工具可用于对自定义模型进行自动定型、测试和发布管理。 

* [自定义语音识别的 CI/CD](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [LUIS 的 CI/CD](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>本地容器 

许多认知服务可以部署在容器中，以供本地访问和使用。 借助这些容器，你能够灵活地将认知服务移至更接近你的数据的位置，以满足合规性、安全性或其他操作目的。 有关认知服务容器的完整列表，请参阅 [认知服务的本地容器](./cognitive-services-container-support.md)。

## <a name="next-steps"></a>后续步骤
<!--
* Learn more about low code development options for Cognitive Services -->
* [创建认知服务资源并开始构建](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)