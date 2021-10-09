---
title: 新增功能 - 语言理解 (LUIS)
description: 本文会经常更新有关 Azure 认知服务语言理解 API 的新闻。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: a42798f5af63d9ec43760511a749861a955518cf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643073"
---
# <a name="whats-new-in-language-understanding"></a>语言理解中的新增功能

了解服务中的新增功能。 这些项包括发布说明、视频、博客文章和其他类型的信息。 请将本页加入书签，以随时了解该服务的最新信息。

## <a name="release-notes"></a>发行说明

### <a name="august-2021"></a>2021 年 8 月
* 挪威东部[发布区域](luis-reference-regions.md#publishing-to-europe).
* 美国西部 3 [发布区域](luis-reference-regions.md#other-publishing-regions)。

### <a name="april-2021"></a>2021 年 4 月

* 瑞士北部[创作区域](luis-reference-regions.md#publishing-to-europe)。

### <a name="january-2021"></a>2021 年 1 月

* V3 预测 API 现支持[必应拼写检查 API](luis-tutorial-bing-spellcheck.md)。
* 区域门户（au.luis.ai 和 eu.luis.ai）已合并为一个门户和 URL。 如果你使用的是这些门户之一，则将自动重定向到 luis.ai。

### <a name="december-2020"></a>2020 年 12 月

* 所有 LUIS 用户都必须[迁移到 LUIS 创作资源](luis-migration-authoring.md)
* 新的[评估终结点](luis-how-to-batch-test.md#batch-testing-using-the-rest-api)，你可通过它们使用 REST API 提交批处理测试，并获得意向和实体的准确性结果。 随 v3.0-preview LUIS 终结点开始提供。

### <a name="june-2020"></a>2020 年 6 月

* [预览版 3.0 创作](luis-migration-authoring-entities.md) SDK -
    * 版本 3.2.0-preview.3 - [.NET - NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * 版本 4.0.0-preview.3 - [JS - NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* 通过 LUIS 应用 DevOps 实践
    * 概念
        * [LUIS 的 DevOps 实践](luis-concept-devops-sourcecontrol.md)
        * [LUIS DevOps 持续集成和持续交付工作流](luis-concept-devops-automation.md)
        * [LUIS DevOps 测试](luis-concept-devops-testing.md)
    * 操作说明
        * [使用 GitHub Actions 将 DevOps 应用于 LUIS 应用开发](./luis-concept-devops-automation.md)
    * [完整代码 GitHub 存储库](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>2020 年 5 月 - //Build

* 发布为正式版 (GA)：
    * [语言理解容器](luis-container-howto.md)
    * 预览门户已升级为[当前门户](https://www.luis.ai)，[以前的](https://previous.luis.ai)门户仍然可用
    * 新的机器学习实体创建和标记体验
    * [升级流程](migrate-from-composite-entity.md)，从复合实体和简单实体升级为机器学习实体
    * [设置](how-to-application-settings-portal.md)对规范化单词变体的支持
* 预览创作 API 更改
    * 适用于嵌套机器学习实体的应用架构 7.x
    * [迁移到所需功能](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* 为开发人员提供的新资源
    * [持续集成工具](developer-reference-resource.md#continuous-integration-tools)
    * 研讨会 - [使用 LUIS 了解自然语言理解 (NLU) 的最佳实践](developer-reference-resource.md#workshops)
* [客户托管密钥](./encrypt-data-at-rest.md) - 通过使用自己的密钥加密所有在 LUIS 中使用的数据
* [AI 展示](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding)（视频）- 查看 LUIS 的新功能



### <a name="march-2020"></a>2020 年 3 月

* 现在，将对此服务的所有 HTTP 请求强制执行 TLS 1.2。 有关详细信息，请参阅 [Azure 认知服务安全性](../cognitive-services-security.md)。

### <a name="november-4-2019---ignite"></a>2019 年 11 月 4 日 - Ignite

* 视频 - [使用 LUIS 和 Azure 认知服务的高级自然语言理解 (NLU) 模型 | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* 提高开发人员工作效率
    * [预测终结点 V3](luis-migration-api-v3.md)的正式版。
    * 能够以 `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) 格式导入和导出应用。 这样，有助于执行有效的 CI/CD 进程。
* 语言扩展
    * [阿拉伯语和印地语](luis-language-support.md)为公共预览版。
* 预生成的模型
    * [预生成的域](luis-reference-prebuilt-domains.md)现在已为正式版 (GA)
    * V3 中不支持日语[预生成实体](luis-reference-prebuilt-entities.md#japanese-entity-support) - 年龄、货币、数字和百分比。
    * 从 V2 起，意大利语[预生成实体](luis-reference-prebuilt-entities.md#italian-entity-support) - 年龄、货币、维度、数字和百分比解析已更改。
* [preview.luis.ai 门户](https://preview.luis.ai)中的增强用户体验 - 改进了标记体验，从而可以生成和调试复杂的模型。 尝试预览门户教程：
    * [仅限意图](tutorial-intents-only.md)
    * [可分解的机器学习实体](tutorial-machine-learned-entity.md)
* 高级语言理解功能 - 轻松[生成复杂的语言模型](luis-concept-entity-types.md)。
* 在模型级别定义机器学习功能，并允许将模型用作其他模型的信号，例如，将实体用作意图和其他实体的功能。
* 全新扩展的[限制](luis-limits.md) - 对短语列表和总短语有更高的上限，全新模型作为功能限制
* 从深层次体系结构格式文本中提取信息，使聊天应用程序的功能更强大。

    ![机器学习实体图像](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019 年 9 月 3 日

* Azure 创作资源 - [立即迁移](luis-migration-authoring.md)。
    * 每个 Azure 资源有 500 个应用
    * 每个应用 100 个版本
* 预构建实体的土耳其语支持
* datetimeV2 的意大利语支持

### <a name="july-23-2019"></a>2019 年 7 月 23 日

* 将 [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) 更新为 1.2.3
    * 意大利语中的年龄、温度、维度和货币识别器。
    * 改进了英语中的假期识别，以正确计算基于感恩节的日期。
    * 改进了法语日期/时间，以减少非日期和非时间实体的误报。
    * 在英文的日期范围中支持日历/学校/会计年度和首字母缩写。
    * 改进了中文和日语的 PhoneNumber 识别。
    * 改进了对英语 NumberRange 的支持。
    * 性能改进。

### <a name="june-24-2019"></a>2019 年 6 月 24 日

* [序号 V2 预生成实体](luis-reference-prebuilt-ordinal-v2.md)，以支持排序，如下一个、上一个和最后一个。 仅限英语区域性。

### <a name="may-6-2019---build-conference"></a>2019 年 5 月 6 日 - //Build 会议

以下功能是在 Build 2019 大会上发布的：

* [V3 API 预览版迁移指南](luis-migration-api-v3.md)
* [改进的分析仪表板](luis-how-to-use-dashboard.md)
* [改进的预生成域](luis-reference-prebuilt-domains.md)
* [动态列表实体](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [外部实体](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>博客

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>视频

### <a name="2019-ignite-videos"></a>2019 Ignite 视频

[使用 LUIS 和 Azure 认知服务的高级自然语言理解 (NLU) 模型 | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Build 视频

[如何使用 Azure 对话 AI 来扩展下一代业务](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>服务更新

[认知服务的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
