---
title: 语言理解 (LUIS) 概述
description: 语言理解 (LUIS) - 一种基于云的 API 服务，将机器学习用于对话式自然语言，来预测含义和提取信息。
keywords: Azure, artificial intelligence, ai, natural language processing, nlp, natural language understanding, nlu, LUIS, conversational AI, ai chatbot, nlp ai, azure luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 05/17/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d48d80170e3f1b9db83e12b34610a8b6736f42ca
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110095817"
---
# <a name="what-is-language-understanding-luis"></a>什么是语言理解 (LUIS)？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

语言理解 (LUIS) 是一种基于云的对话式 AI 服务，可在用户对话的自然语言文本中应用自定义机器学习智能，以便预测整体含义并提炼出相关的详细信息。 LUIS 通过其[自定义门户](https://www.luis.ai)、[API][endpoint-apis] 和 [SDK 客户端库](client-libraries-rest-api.md)提供访问权限。

对于新手用户，请按照以下步骤[登录 LUIS 门户](sign-in-luis-portal.md "登录到 LUIS 门户") 要开始使用，可以尝试 LUIS [预生成域应用](luis-get-started-create-app.md)。

本文档包含以下文章类型：  

* [**快速入门**](luis-get-started-create-app.md)介绍了入门说明，指导您完成向服务发出请求。  
* [**操作指南**](luis-how-to-start-new-app.md)包含以更具体的方式或自定义方式使用服务的说明。  
* [**概念**](artificial-intelligence.md)对服务的功能和特性进行了深入说明。  
* [**教程**](tutorial-intents-only.md)是较长的指南，向您演示了如何在更广泛的业务解决方案中使用该服务作为组件。  

## <a name="what-does-luis-offer"></a>LUIS 提供的功能 

* **简单**：LUIS 无需你具备任何内行 AI 专业知识或任何机器学习先验知识。 只需单击几下即可生成自己的对话式 AI 应用程序。 你可通过使用我们的[快速入门](luis-get-started-create-app.md)之一来生成你的自定义应用程序，也可使用我们的[预生成域](luis-get-started-create-app.md)应用之一。
* **安全性、隐私保护和合规性**：由 Azure 基础结构提供支持，LUIS 提供企业级安全性、隐私保护和合规性。 你的数据仍为你所用；你可以随时删除你的数据。 你的数据在存储时进行了加密。 在[此处](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy)了解详细信息。
* **集成**：可轻松将 LUIS 应用与其他 Microsoft 服务（如 [Microsoft Bot Framework](/composer/tutorial/tutorial-luis)、[QnA Maker](../QnAMaker/choose-natural-language-processing-service.md) 和 [语音服务](../speech-service/get-started-intent-recognition.md)）集成。


## <a name="luis-scenarios"></a>LUIS 方案
* [生成企业级聊天机器人](/azure/architecture/reference-architectures/ai/conversational-bot)：本参考体系结构介绍如何使用 Azure Bot Framework 生成企业级聊天机器人 (chatbot)。
* [商务聊天机器人](/azure/architecture/solution-ideas/articles/commerce-chatbot)：使用 Azure 机器人服务和语言理解服务，开发人员可以为各种方案（例如银行、旅游和娱乐）创建聊天界面。
* [使用语音助理控制 IoT 设备](/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant)：使用可通过 Internet 访问的所有设备（从联网电视或冰箱到联网电厂中的设备，不一而足）创建无缝对话接口。


## <a name="application-development-life-cycle"></a>应用程序开发生命周期

![LUIS 应用开发生命周期](./media/luis-overview/luis-dev-lifecycle.png "LUIS 应用程序开发生命周期")

-   **计划**：标识用户可能使用你的应用程序的方案。 定义需要识别的操作和相关信息。
-   **生成**：使用创作资源来开发应用。 首先定义[意向](luis-concept-intent.md)和[实体](luis-concept-entity-types.md)。 然后，为每个意向添加训练[言语](luis-concept-utterance.md)。 
-   **测试和改进**：开始使用其他言语测试模型，以了解应用的行为方式，并决定是否需要任何改进。 可遵循以下[最佳做法](luis-concept-best-practices.md)来改进应用程序。 
-   **发布**：部署应用以进行预测，并使用预测资源查询终结点。 如需详细了解如何创作和预测资源，请参阅[此处](luis-how-to-azure-subscription.md)。 
-   **连接**：连接其他服务，例如 [Microsoft Bot Framework](/composer/tutorial/tutorial-luis)、[QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)和 [语音服务](../speech-service/get-started-intent-recognition.md)。 
-   **优化**：[检查终结点言语](luis-concept-review-endpoint-utterances.md)，以利用真实的示例改进应用程序

如需详细了解如何规划和生成应用程序，请参阅[此处](luis-how-plan-your-app.md)。

## <a name="next-steps"></a>后续步骤

* 服务和文档的[新增内容](whats-new.md "新增功能")
* [生成 LUIS 应用](tutorial-intents-only.md)
* [API 参考][endpoint-apis]
* [最佳实践](luis-concept-best-practices.md)
* 面向 LUIS 的[开发人员资源](developer-reference-resource.md "开发人员资源")。
* 使用[意图](luis-concept-intent.md "意向")和[实体](luis-concept-entity-types.md "实体")[计划应用](luis-how-plan-your-app.md "规划应用")。

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/