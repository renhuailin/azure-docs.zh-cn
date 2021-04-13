---
title: 针对在创作自定义命令应用程序时发生的错误进行调试（预览）
titleSuffix: Azure Cognitive Services
description: 本文介绍如何针对在创作自定义命令应用程序时发生的错误进行调试。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: aeb90e8e064c44f4d17f920261ed58310f0e55f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025695"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>针对在创作自定义命令应用程序时发生的错误进行调试

本文介绍如何针对在创作自定义命令应用程序时出现的错误进行调试。 

## <a name="errors-when-creating-an-application"></a>创建应用程序时出错
创建自定义命令应用程序时，自定义命令也会在 [LUIS](https://www.luis.ai/) 中创建一个应用程序。 

[LUIS 限制为在每个创作资源中创建的应用程序不超过 500 个](../luis/luis-limits.md)。 如果所用创作资源包含的应用程序已达到 500 个，创建 LUIS 应用程序可能会失败。 

确保所选 LUIS 创作资源包含的应用程序少于 500 个。 如果超过此限制，可以创建新的 LUIS 创作资源、切换到另一个创作资源，或者尝试清理 LUIS 应用程序。  

## <a name="errors-when-deleting-an-application"></a>删除应用程序时出错
### <a name="cant-delete-luis-application"></a>无法删除 LUIS 应用程序
删除某个自定义命令应用程序时，自定义命令还可能尝试删除与该自定义命令应用程序关联的 LUIS 应用程序。

如果删除 LUIS 应用程序失败，请转到你的 [LUIS](https://www.luis.ai/) 帐户以手动将其删除。

### <a name="toomanyrequests"></a>TooManyRequests
尝试一次性删除大量应用程序时，可能会出现“TooManyRequests”错误。 这些错误表示删除请求受到 Azure 的限制。 

请刷新页面，然后尝试删除更少的应用程序。

## <a name="errors-when-modifying-an-application"></a>修改应用程序时出错

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>无法删除某个参数或 Web 终结点
不允许删除正在使用的参数。 删除任何语音响应、示例句子、条件和操作中对该参数的任何引用，然后重试。

### <a name="cant-delete-a-web-endpoint"></a>无法删除某个 Web 终结点
不允许删除正在使用的 Web 终结点。 在删除某个 Web 终结点之前，请删除使用此 Web 终结点的任何“调用 Web 终结点”操作。

## <a name="errors-when-training-an-application"></a>训练应用程序时出错
### <a name="built-in-intents"></a>内置意向
LUIS 包含内置的“是/否意向”。 只在示例句子中包含“是”、“否”会导致训练失败。 

| 关键字 | 变体 | 
| ------- | --------- | 
| 是 | 当然、好的 |
| 否 | 不是、不会 | 

### <a name="common-sample-sentences"></a>常用示例句子
自定义命令不允许在不同的命令之间共享常用示例句子。 如果一个命令中的某些示例句子已在另一个命令中定义，则应用程序训练可能会失败。 

确保未在不同的命令之间共享常用示例句子。 

有关在不同的命令之间均衡分配示例句子的最佳做法，请参阅 [LUIS 最佳做法](../luis/luis-concept-best-practices.md)。

### <a name="empty-sample-sentences"></a>空示例句子
需要为每个命令至少提供一个示例句子。

### <a name="undefined-parameter-in-sample-sentences"></a>示例句子中未定义的参数
在示例句子中使用了一个或多个参数，但这些参数未定义。

### <a name="training-takes-too-long"></a>训练时间太长
LUIS 训练旨在通过少量示例快速学习。 请不要添加太多的示例句子。 

如果你有许多类似的示例句子，请定义一个参数，将这些句子抽象成一种模式，然后将该模式添加到示例句子。

例如，可为以下示例句子定义参数 {vehicle}，并仅将“预订 {vehicle}”添加到示例句子。

| 示例句子 | 模式 | 
| ------- | ------- | 
| 预订汽车 | 预订 {vehicle} | 
| 预订航班 | 预订 {vehicle} |
| 预订出租车 | 预订 {vehicle} |

有关 LUIS 训练的最佳做法，请参阅 [LUIS 最佳做法](../luis/luis-concept-best-practices.md)。

## <a name="cant-update-luis-key"></a>无法更新 LUIS 密钥
### <a name="reassign-to-e0-authoring-resource"></a>重新分配到 E0 创作资源
LUIS 不支持将 LUIS 应用程序重新分配到 E0 创作资源。

如果需要将创作资源从 F0 更改为 E0，或更改为其他 E0 资源，请重新创建应用程序。 

若要快速导出现有应用程序并将其导入新应用程序，请参阅[使用 Azure DevOps 进行持续部署](./how-to-custom-commands-deploy-cicd.md)。

### <a name="save-button-is-disabled"></a>“保存”按钮已禁用
如果你从未向应用程序分配过 LUIS 预测资源，则当你在未添加预测资源的情况下尝试更改创作资源时，“保存”按钮将会禁用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看 GitHub 上的示例](https://aka.ms/speech/cc-samples)