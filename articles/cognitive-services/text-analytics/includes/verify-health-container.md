---
title: 验证运行状况容器实例
titleSuffix: Azure Cognitive Services
description: 了解如何验证运行状况容器实例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009841"
---
### <a name="verify-that-a-container-is-running"></a>验证容器是否正在运行

1. 选择“概述”选项卡，并复制 IP 地址。
1. 打开新的浏览器选项卡，并输入 IP 地址。 例如，输入 `http://<IP-address>:5000 (http://55.55.55.55:5000`)。 此时将显示容器的主页，让你知道该容器正在运行。

    ![查看容器主页，以验证它是否处于运行状态](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 选择“服务 API 说明”链接，以转到该容器的 Swagger 页。

1. 选择任意一个 **POST** API，然后选择“试用”。此时将显示参数，其中包括示例输入。

还可以使用多个 URL 来验证容器是否正在运行。

|请求|用途|
|--|--|
|`http://localhost:5000/`|容器提供主页。|
|`http://localhost:5000/ready`|使用 GET 对此 URL 进行请求，可以验证容器是否已准备好接受针对模型的查询。 此请求可用于 Kubernetes [运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。|
|`http://localhost:5000/status`|使用 GET 对此 URL 进行请求（像 /ready 终结点一样），可以验证容器是否正在运行，而不会引起针对模型的查询。 此请求可用于 Kubernetes [运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。|
|`http://localhost:5000/swagger`|通过此 URL，容器提供了有关终结点和 `Try it now` 功能的整套文档。 使用此功能可以将设置输入到基于 Web 的 HTML 表单并进行查询，而无需编写任何代码。 查询返回后，将提供示例 CURL 命令，用于演示所需的 HTTP 标头和正文格式。 |
|`http://localhost:5000/demo`| 通过浏览器对此功能进行请求后，此功能针对输入文本示例的查询或你提供的查询所产生的结果提供交互式可视化效果。  |

使用此请求 URL (`http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`) 将查询提交到容器。
