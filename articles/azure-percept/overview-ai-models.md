---
title: Azure Percept AI 模型
description: 了解有关可用于原型制作和部署的 AI 模型的详细信息
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: e61fb00b4ddd309124c11d1749f448c742d660d0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662064"
---
# <a name="azure-percept-ai-models"></a>Azure Percept AI 模型

Azure Percept 可让你通过 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)直接开发 AI 模型并将其部署到 AZURE Percept 深色。 模型部署利用 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) 和 [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview)。

## <a name="sample-ai-models"></a>AI 模型示例

Azure Percept Studio 包含以下应用程序的示例模型：

- 人脸检测
- 人员检测
- 车辆检测
- 常规对象检测
- 产品-保质期检测

对于预先训练的模型，不需要进行编码或定型数据收集。 只需在门户中将所需的模型部署到 Azure Percept 深色，并打开 devkit 的视频流即可查看模型推断。 还可以通过 [Azure IoT 浏览器](https://github.com/Azure/azure-iot-explorer/releases) 工具访问模型推断遥测。

## <a name="pre-built-solutions"></a>预先生成的解决方案

还提供了 [用于人员检测的空间分析预构建解决方案](https://github.com/george-moore/Santa-Cruz-AI-App) 。 预生成的解决方案是一种开源 AI 应用程序，可提供基于边缘的人员，并使用用户定义的区域入口/出口事件进行计数。 从本地边缘设备进行视频和 AI 输出的出口为 [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/)，用户界面作为 Azure 网站运行。 AI 推断由开源 AI 模型提供，用于人员检测。

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="空间分析预生成的解决方案 gif。":::

## <a name="custom-no-code-solutions"></a>自定义无代码解决方案

通过 Azure Percept Studio，可以开发自定义 [视觉](./tutorial-nocode-vision.md) 和语音解决方案，无需编码。

对于自定义远景解决方案，可以使用对象检测和分类 AI 模型。 只需上传和标记定型映像，如果需要，可以直接通过 Azure Percept 深色的 Azure Percept 视觉 SoM 来执行。 可以轻松地在 [自定义视觉](https://www.customvision.ai/)（这是 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/#overview)的一部分）中执行模型训练和评估。

对于自定义语音解决方案，语音助手模板当前适用于以下应用程序：

- 宾馆：配备有语音控制的智能设备的酒店房间。
- 医疗保健：配备有语音控制的智能设备的工具。
- 清单：预配了语音控制智能设备的清单集线器。
- 汽车：汽车中心配备了语音控制的智能设备。

预先生成的语音助手关键字和命令可通过门户直接使用。 自定义关键字和命令可在 [Speech Studio](https://speech.microsoft.com/)中创建和训练，这也是 Azure 认知服务的一部分。

## <a name="advanced-development"></a>高级开发

对于高级开发人员，可用的 [Jupyter 笔记本](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) 使用预先训练的 TensorFlow 模型 (MobileNetSSDV2Lite) 在 Python 中使用自定义数据集进行对象检测。 该笔记本通过 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/#product-overview) 利用远程计算实例，可以使用 AzureML 门户在云中运行，也可以在 [Visual Studio Code](https://code.visualstudio.com/)本地运行。

其中还包含一些有用的 Python [脚本](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts) ，用于管理数据集和 [开发工具包安装程序](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md)，开发人员可以使用它来安装和配置开发高级 AI 解决方案所需的所有工具。
