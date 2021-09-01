---
title: Azure Percept 示例 AI 模型
description: 详细了解可用于原型和部署的 AI 模型
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 78eb20b0229faef9c067b4bab475759badccd19e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221275"
---
# <a name="azure-percept-sample-ai-models"></a>Azure Percept 示例 AI 模型

借助 Azure Percept，可以开发 AI 模型，并将这些模型直接从 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 部署到 [Azure Percept DK](./overview-azure-percept-dk.md)。 模型部署利用 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)和 [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview)。

## <a name="sample-ai-models"></a>示例 AI 模型

Azure Percept Studio 包含以下应用程序的示例模型：

- 人员检测
- 车辆检测
- 常规物体检测
- 产品现货检测

使用预先训练的模型，不需要编码或训练数据收集。 只需[将所需模型从门户部署](./how-to-deploy-model.md)到 Azure Percept DK，然后打开开发工具包的[视频流](./how-to-view-video-stream.md)即可看到模型的实际推理效果。 [模型推理遥测](./how-to-view-telemetry.md)也可以通过 [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) 工具访问。

## <a name="reference-solutions"></a>参考解决方案

[人员计数参考解决方案](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app)也可用。 此参考解决方案是一种开放源代码 AI 应用程序，它使用用户定义的区域进入/退出事件来提供基于边缘的人员计数。 本地边缘设备的视频和 AI 输出流出到 [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/)，其中用户界面作为 Azure 网站运行。 AI 推理由开放源代码的人员检测 AI 模型提供。

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="空间分析预生成解决方案 gif。":::

## <a name="custom-no-code-solutions"></a>自定义无代码解决方案

通过 Azure Percept Studio，可以开发自定义[视觉](./tutorial-nocode-vision.md)和[语音](./tutorial-no-code-speech.md)解决方案，而无需编码。

对于自定义视觉解决方案，对象检测和分类 AI 模型都是可用的。 只需上传并标记训练图像，如果需要，可以直接使用 Azure Percept DK 的 Azure Percept Vision SoM 拍摄。 模型训练和评估可以在[自定义视觉](https://www.customvision.ai/)中轻松执行，这是 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/#overview)的一部分。

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

对于自定义语音解决方案，语音助理模板目前可用于以下应用程序：

- 酒店餐饮：配备了语音控制型智能设备的酒店房间。
- 医疗：配备了语音控制型智能设备的保健护理设施。
- 库存：配备了语音控制型智能设备的库存中心。
- 汽车：配备了语音控制型智能设备的汽车中心。

预生成的语音助理关键字和命令可以直接通过门户获得。 自定义关键字和命令可以在 [Speech Studio](https://speech.microsoft.com/) 中创建和训练，这也是 Azure 认知服务的一部分。

## <a name="advanced-development"></a>高级开发

有关以下方面的最新指南、教程和示例，请参阅 [Azure Percept DK 高级开发 GitHub](https://github.com/microsoft/azure-percept-advanced-development)：

- 将自定义 AI 模型部署到 Azure Percept DK
- 使用迁移学习更新受支持的模型
- 及其他信息
