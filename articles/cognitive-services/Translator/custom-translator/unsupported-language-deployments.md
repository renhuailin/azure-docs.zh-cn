---
title: 不支持的语言部署 - 自定义翻译器
titleSuffix: Azure Cognitive Services
description: 本文介绍了如何在 Azure 认知服务自定义翻译器中部署不受支持的语言对。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: ca2fab82dd08ea999186a430b78b6329c64ae816
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111409588"
---
# <a name="unsupported-language-deployments"></a>不支持的语言部署

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

随着 Microsoft Translator Hub 即将停用，Microsoft 将取消部署当前通过该 Hub 部署的所有模型。 很多人可能在该中心内部署了其语言对在自定义翻译器中不受支持的模型。  我们不希望这种情况下的用户没有可用来翻译其内容的资源。

我们现在提供了一个过程，允许你通过自定义翻译器来部署不受支持的模型。  可以通过此过程继续使用最新的 V3 API 来翻译内容。  这些模型会被托管，直到你选择取消部署它们或者语言对在自定义翻译器中变得可用为止。  本文介绍了部署其语言对不受支持的模型的过程。

## <a name="prerequisites"></a>先决条件

为了使你的模型成为可部署的候选模型，它们必须满足以下条件：
* 必须已使用迁移工具将包含该模型的项目从中心迁移到自定义翻译器。  
* 迁移发生时，模型必须处于已部署状态。  
* 模型的语言对必须是自定义翻译器中不受支持的语言对。  如果语言对中的某种语言可以翻译为英语或者从英语翻译过来，但该语言对本身不包含英语，则该语言对是不受支持的语言部署的候选者。  例如，法语到德语语言对的中心模型被认为是不受支持的语言对，即使法语到英语和英语到德语是受支持的语言对。

## <a name="process"></a>进程
从中心迁移可作为部署候选者的模型后，可通过以下方式找到它们：转到你的工作区的“设置”页，滚动到页尾，然后你会看到“不受支持的 Translator Hub 训练”部分。  仅当你的项目满足上述先决条件时，才会显示此部分。

![屏幕截图突出显示了“不受支持的 Translator Hub 训练”部分。](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

在“不受支持的 Translator Hub 训练”选择页中，“未请求的训练”选项卡包含符合部署条件的模型。  选择要部署的模型并提交请求。   在 4 月 30 日这个部署截止日期之前，你可以根据需要选择任意数量的模型进行部署。
 
![屏幕截图显示了“未请求的训练”选项卡。](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

在提交后，模型将不再显示在“未请求的训练”选项卡上，改为显示在“已请求的训练”选项卡上。你随时可以查看所请求的训练。

![如何从 Hub 迁移](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>后续步骤

在停用中心且取消部署所有模型后，你选择用于部署的模型会被保存。  你必须在 5 月 24 日之前提交部署不受支持的模型的请求。  我们将在 6 月 15 日部署这些模型，届时可以通过翻译器 V3 API 访问它们。  此外，在 7 月 1 日前，还可以通过 V2 API 访问它们。  

若要进一步了解与弃用该中心相关的重要日期，请查看[此处](https://www.microsoft.com/translator/business/hub/)。
部署后会产生正常的托管费用。  有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。  

与标准的自定义翻译器模型不同，中心模型仅在单个区域中可用，因此不会产生多区域托管费用。  在部署后，你随时可以通过已迁移的自定义翻译器项目取消部署你的中心模型。

## <a name="next-steps"></a>后续步骤

- [训练模型](how-to-train-model.md)。
- 通过 [Microsoft 文本翻译 API V3](../reference/v3-0-translate.md?tabs=curl) 开始使用已部署的自定义翻译模型。
