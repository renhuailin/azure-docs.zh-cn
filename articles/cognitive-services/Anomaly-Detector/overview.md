---
title: 什么是异常检测器 API？
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 算法对时序数据应用异常情况检测。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: 异常检测, 机器学习, 算法
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 30764913ae92670a9034f709f9bb3f50a309374f
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2021
ms.locfileid: "113296876"
---
# <a name="what-is-the-anomaly-detector-univariate-api"></a>什么是异常检测器单变量 API？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

使用异常检测器 API，无需了解机器学习方面的知识，就能监视和检测时序数据中的异常。 异常检测器 API 算法通过自动标识最佳适配模型并将其应用到数据来进行自适应，并且不限行业、场景或数据量。 使用时序数据，此 API 可以确定异常检测的边界、预期的值，以及哪些数据点异常。

![检测服务请求中的模式更改](./media/anomaly_detection2.png)

使用异常检测器不需要以前在机器学习方面有任何经验，你可以使用 RESTful API 轻松地将服务集成到应用程序和进程中。

本文档包含以下类型的文章：
* [快速入门](./Quickstarts/client-libraries.md)是分步说明，可按照其调用服务，并在短时间内获得结果。 
* [操作指南](./how-to/identify-anomalies.md)包含以更具体的方式或自定义方式使用服务的说明。
* [概念性文章](./concepts/anomaly-detection-best-practices.md)对服务的功能和特性进行了深入说明。
* [教程](./tutorials/batch-anomaly-detection-powerbi.md)是较长的指南，向你演示了如何在更广泛的业务解决方案中使用此服务作为组件。

## <a name="features"></a>功能

可以使用异常检测器自动检测时序数据中的异常以及实时出现的异常。

|Feature  |说明  |
|---------|---------|
|实时检测异常。 | 检测流式传输数据中的异常，方法是：使用以前见过的数据点来确定最近的数据点是否异常。 此操作使用发送的数据点生成一个模型，然后确定目标点是否异常。 每生成一个新数据点就调用该 API，这样就可以在创建数据时监视数据。 |
|以批的形式检测整个数据集中的异常。 | 使用时序来检测数据中可能存在的任何异常。 此操作使用整个时序数据生成一个模型，每个点使用同一模型进行分析。         |
|以批的形式检测整个数据集中的更改点。 | 使用时序来检测数据中存在的趋势更改点。 此操作使用整个时序数据生成一个模型，每个点使用同一模型进行分析。    |
| 获取数据的其他信息。 | 获取有关数据的有用详细信息以及任何观察到的异常，包括预期的值、异常边界和位置。 |
| 调整异常检测边界。 | 异常检测器 API 自动创建异常检测的边界。 调整这些边界，以便提高或降低 API 对数据异常的敏感度，并更好地拟合数据。 |

## <a name="demo"></a>演示

查看此[交互式演示](https://aka.ms/adDemo)以了解异常检测器的工作原理。
若要运行演示，需要创建一个异常检测器资源，并获取 API 密钥和终结点。

## <a name="notebook"></a>笔记本

若要了解如何调用异常检测器API，请试用此 [Notebook](https://aka.ms/adNotebook)。 此 Jupyter Notebook 演示如何发送 API 请求和直观显示结果。

若要运行 Notebook，应获取有效的异常检测器 API 订阅密钥和 API 终结点。 在笔记本中，将有效的异常检测器 API 订阅密钥添加到 `subscription_key` 变量，并将 `endpoint` 变量更改为你的终结点。

## <a name="workflow"></a>工作流

异常检测器 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

注册后：

1. 获取时序数据并将其转换为有效的 JSON 格式。 在准备数据时使用[最佳做法](concepts/anomaly-detection-best-practices.md)，以便获取最佳结果。
1. 向包含你的数据的异常检测器 API 发送请求。
1. 通过分析返回的 JSON 消息处理 API 响应。

## <a name="algorithms"></a>算法

* 有关所使用的算法的信息，请参阅以下技术博客：
    * [Azure 异常检测器 API 简介](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Azure 异常检测器中的 SR-CNN 算法概述](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

可以阅读论文 [Microsoft 的时序异常检测服务](https://arxiv.org/abs/1906.03821)（KDD 2019 接受），详细了解 Microsoft 开发的 SR-CNN 算法。

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>服务可用性和冗余性

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>异常检测器服务是否可在区域内复原？

是。 默认情况下，异常探测器服务可在区域内复原。

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>如何将异常探测器服务配置为可在区域内复原？

客户无需配置即可启用区域复原能力。 异常探测器资源默认提供区域内复原能力，由服务自身进行管理。

## <a name="deploy-on-premises-using-docker-containers"></a>使用 Docker 容器进行本地部署

[使用异常检测器容器](anomaly-detector-container-howto.md)在本地部署 API 功能。 借助 Docker 容器，你可使服务更靠近数据，以满足符合性、安全性或其他操作目的。

## <a name="join-the-anomaly-detector-community"></a>加入“异常检测器”社区

* 加入 [Microsoft Teams 中的异常检测器顾问组](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>后续步骤

* [快速入门：使用异常检测器检测时序数据中的异常](quickstarts/client-libraries.md)
* 异常检测器 API [在线演示](https://github.com/Azure-Samples/AnomalyDetector/tree/master/ipython-notebook)
* 异常检测器 [REST API 参考](https://aka.ms/anomaly-detector-rest-api-ref)