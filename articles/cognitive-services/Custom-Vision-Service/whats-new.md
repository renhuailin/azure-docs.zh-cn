---
title: 自定义视觉有哪些新增功能？
titleSuffix: Azure Cognitive Services
description: 本文包含有关自定义视觉的新闻。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.openlocfilehash: 677a5ff8214c8f43ec5dfe1d6d6e73b1d03b2650
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356955"
---
# <a name="whats-new-in-custom-vision"></a>自定义视觉中的新增功能

了解服务中的新增功能。 这些内容可能包括发布说明、视频、博客文章和其他类型的信息。 请将本页加入书签，以随时了解该服务的最新信息。


## <a name="october-2020"></a>2020 年 10 月 

### <a name="custom-base-model"></a>自定义基础模型

- 有些应用程序有大量的联合训练数据，但需要分别对它们的模型进行微调；这样可以使来自不同来源、差异较小的图像获得更好的性能。 在这种情况下，可以像往常一样使用大量的训练数据训练第一个模型。 然后使用请求正文中的 CustomBaseModelInfo 调用 3.4 公共预览 API 中的 TrainProject，以使用第一阶段训练的模型作为下游项目的基础模型。 如果源项目和下游目标项目具有相似的图像特征，则预期可以获得更好的性能。 

### <a name="new-domain-information"></a>新的域信息

- 自定义视觉 3.4 公共预览 API 中的从 GetDomains 返回的域信息现在包括支持的可导出平台、模型体系结构的简要描述以及压缩域的模型大小。

### <a name="training-divergence-feedback"></a>训练分歧反馈

- 现在，自定义视觉 3.4 公共预览 API 通过 GetIteration 调用返回 TrainingErrorDetails 。 对于失败的迭代，这显示了失败是否是由训练分歧引起的，这可以用更多更高质量的训练数据来弥补。

## <a name="july-2020"></a>2020 年 7 月

### <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

* 自定义视觉支持 Azure 基于角色的访问控制 (Azure RBAC)，这是用于管理对 Azure 资源的个人访问权限的授权系统。 若要了解如何管理对自定义视觉项目的访问权限，请参阅 [Azure 基于角色的访问控制](./role-based-access-control.md)。

### <a name="subset-training"></a>子集训练

* 训练对象检测项目时，可以选择只对应用的标记的一个子集进行训练。 如果你还没有应用足够多的特定标记，但是你确实有足够多的其他标记，则可能需要这样做。 按照 C# 或 Python 的[客户端库快速入门](./quickstarts/object-detection.md)了解详细信息。

### <a name="azure-storage-notifications"></a>Azure 存储通知

* 可以将自定义视觉项目与 Azure blob 存储队列集成，以获取项目训练/导出活动的推送通知和已发布模型的备份副本。 运行长时间的操作时，此功能有助于避免不断轮询服务来获取结果。 你可以改为将存储队列通知集成到工作流中。 有关详细信息，请参阅[存储集成](./storage-integration.md)指南。

### <a name="copy-and-move-projects"></a>复制和移动项目

* 你现在可以将项目从一个自定义视觉帐户复制到其他帐户。 你可能需要将项目从开发环境移至生产环境，或将项目备份到不同 Azure 区域中的帐户，以提高数据安全性。 有关详细信息，请参阅[复制和移动项目](./copy-move-projects.md)指南。

## <a name="september-2019"></a>2019 年 9 月

### <a name="suggested-tags"></a>建议的标记

* [自定义视觉网站](https://www.customvision.ai/)上的智能标记工具为训练图像生成建议的标记。 这使你可以在训练自定义视觉模型时更快地标记大量图像。 有关如何使用此功能的说明，请参阅[建议的标记](./suggested-tags.md)。

## <a name="cognitive-service-updates"></a>认知服务更新

[认知服务的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)