---
title: Azure Percept Vision AI 模块
description: azureeyemodule 概述，该模块负责在 Azure Percept DK 上运行 AI 视觉工作负载。
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: overview
ms.date: 08/09/2021
ms.custom: template-overview
ms.openlocfilehash: b09c5eb0dc2fa977c544d7da8178408dadcd08a6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222372"
---
# <a name="azure-percept-vision-ai-module"></a>Azure Percept Vision AI 模块

Azureeyemodule 是负责在 Azure Percept DK 上运行 AI 视觉工作负载的边缘模块的名称。 它是 Azure IoT 边缘模块套件的一部分，在[设置体验](./quickstart-percept-dk-set-up.md)期间部署到 Azure Percept DK。 本文概述了该模块及其体系结构。

## <a name="architecture"></a>体系结构

:::image type="content" source="media/azureeyemodule-overview/azureeyemodule-architecture.png" alt-text="显示 azureeyemodule 体系结构的关系图。":::

Azure Percept DK 上的 Azure Percept Workload 是在 azureeyemodule docker 容器内运行的 C++ 应用程序。 它使用 OpenCV GAPI 进行图像处理和模型执行。 Azureeyemodule 作为在 Azure Percept DK 上运行的 Azure IoT 模块套件的一部分，在 Mariner 操作系统上运行。

Azure Percept Workload 旨在接收图像以及输出图像和消息。 输出图像可能标有绘图，例如边界框、分段掩码、接点、标签等。 输出消息是推理结果的 JSON 流，可由下游任务引入和使用。
结果作为 RTSP 流提供，可在设备的端口 8554 上使用。 结果还会传送到设备上运行的另一个模块（在端口 3000 上运行），该模块为包装在 HTTP 服务器中的 RTSP 流提供服务。 无论采用哪种方式，都只能在本地网络上查看结果。

> [!CAUTION]
> 对于 RTSP 源，没有任何加密或身份验证。 通过在 Web 浏览器或 RTSP 媒体播放器中键入正确的地址，本地网络上的任何人都可以查看 Azure Percept Vision 所看到的确切内容。

Azure Percept Workload 支持最终用户可利用的多项功能：
- 一种适用于常见计算机视觉用例（例如对象分类和常见对象检测）的无代码解决方案。
- 一种高级解决方案，开发人员可以将自己的（可能是级联的）训练模型带到设备中并运行它，并且可能会将结果传递到他们自己创建的在设备上运行的另一个 IoT 模块。
- 一种重新训练循环，用于定期从设备抓取图像，在云中重新训练模型，然后将新训练的模型推回设备。 借助设备的功能动态更新和交换模型。

## <a name="ai-workload-details"></a>AI 工作负载详细信息
Workload 应用程序是 Azure Percept 高级开发 [github 存储库](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app)中的开源项目，由许多小型 C++ 模块组成，其中一些比较重要的模块包括：
- [main.cpp](https://github.com/microsoft/azure-percept-advanced-development/blob/main/azureeyemodule/app/main.cpp)：设置所有内容，然后运行主循环。
- [iot](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/iot)：此文件夹包含用于处理 Azure IoT Edge 中心的传入和传出消息的模块以及孪生更新方法。
- [model](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/model)：此文件夹包含用于计算机视觉模型的类层次结构的模块。
- [kernels](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/kernels)：此文件夹包含用于 G-API 内核、操作和 C++ 包装器函数的模块。

开发人员可以使用此 Workload 应用程序生成自定义模块或自定义当前的 azureeyemodule。 

## <a name="next-steps"></a>后续步骤

- 你已详细了解 azureeyemodule 和 Azure Percept Workload，接下来按照[这些教程](https://github.com/microsoft/azure-percept-advanced-development/blob/main/tutorials/README.md)之一尝试使用自己的模型或管道
- 或者，使用现成的[机器学习笔记本](https://github.com/microsoft/azure-percept-advanced-development/tree/main/machine-learning-notebooks)之一尝试迁移学习

