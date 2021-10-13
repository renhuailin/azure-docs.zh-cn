---
title: ONNX 模型：优化推理
titleSuffix: Azure Machine Learning
description: 了解如何使用 Open Neural Network Exchange (ONNX) 来帮助优化机器学习模型的推理。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 348589b133c126f7190955087cc6068e7af90062
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614035"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 和 Azure 机器学习：创建和加速 ML 模型

了解如何使用 [Open Neural Network Exchange](https://onnx.ai) (ONNX) 来帮助优化机器学习模型的推理。 推理或模型评分是将部署的模型用于预测（通常针对生产数据）的阶段。 

优化用于推理（或模型评分）的机器学习模型非常困难，因为需要调整模型和推理库，充分利用硬件功能。 如果想要在不同类型的平台（云/Edge、CPU/GPU 等）上获得最佳性能，实现起来会异常困难，因为每个平台都有不同的功能和特性。 如果模型来自需要在各种平台上运行的多种框架，会极大增加复杂性。 优化框架和硬件的所有不同组合非常耗时。 这就需要一种解决方案，在首选框架中训练一次后能在云或 Edge 上的任意位置运行。 此时 ONNX 便派上了用场。

Microsoft 和合作伙伴社区创建了 ONNX 作为表示机器学习模型的开放标准。 [许多框架](https://onnx.ai/supported-tools)（包括 TensorFlow、PyTorch、SciKit-Learn、Keras、Chainer、MXNet、MATLAB 和 SparkML）中的模型都可以导出或转换为标准 ONNX 格式。 模型采用 ONNX 格式后，可在各种平台和设备上运行。

[ONNX 运行时](https://onnxruntime.ai)是一种用于将 ONNX 模型部署到生产环境的高性能推理引擎。 它针对云和 Edge 进行了优化，适用于 Linux、Windows 和 Mac。 它使用 C++ 编写，还包含 C、Python、C#、Java 和 Javascript (Node.js) API，可在各种环境中使用。 ONNX 运行时同时支持 DNN 和传统 ML 模型，并与不同硬件上的加速器（例如，NVidia GPU 上的 TensorRT、Intel 处理器上的 OpenVINO、Windows 上的 DirectML 等）集成。 通过使用 ONNX 运行时，可以从大量的生产级优化、测试和不断改进中受益。

ONNX 运行时用于大规模 Microsoft 服务，如必应、Office 和 Azure 认知服务。 性能提升取决于许多因素，但这些 Microsoft 服务的 CPU 平均起来可实现 2 倍的性能提升。 除了 Azure 机器学习服务外，ONNX 运行时还在支持机器学习工作负荷的其他产品中运行，包括：
+ Windows:该运行时作为 [Windows 机器学习](/windows/ai/windows-ml/)的一部分内置于 Windows 中，在数亿台设备上运行。 
+ Azure SQL 产品系列：针对 [Azure SQL Edge](../azure-sql-edge/onnx-overview.md) 和 [Azure SQL 托管实例](../azure-sql/managed-instance/machine-learning-services-overview.md)中的数据运行本机评分。
+ ML.NET：[在 ML.NET 中运行 ONNX 模型](/dotnet/machine-learning/tutorials/object-detection-onnx)。


[![ONNX 流程图，其中显示了训练、转换器和部署](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>获取 ONNX 模型

可通过以下几种方式获取 ONNX 模型：
+ 通过 Azure 机器学习（参见本文底部的示例）或[自动机器学习功能](concept-automated-ml.md#automl--onnx)训练新的 ONNX 模型
+ 将现有模型从其他格式转换为 ONNX（请参阅 [教程](https://github.com/onnx/tutorials)） 
+ 从 [ONNX 模型 Zoo](https://github.com/onnx/models) 获取预先定型的 ONNX 模型
+ 从 [Azure 自定义影像服务](../cognitive-services/custom-vision-service/index.yml)生成自定义 ONNX 模型 

许多模型（包括图像分类、对象检测和文本处理）都可以表示为 ONNX 模型。 如果遇到无法成功转换的模型的问题，请在所用的相应转换器的 GitHub 中提出问题。 可以继续使用现有的格式模型，直到问题得到解决。

## <a name="deploy-onnx-models-in-azure"></a>在 Azure 中部署 ONNX 模型

使用 Azure 机器学习，可以部署、管理和监视 ONNX 模型。 使用标准[部署工作流](concept-model-management-and-deployment.md)和 ONNX 运行时，可以创建在云中托管的 REST 终结点。 请参阅本文末尾的 Jupyter 笔记本示例，亲自试用。 

### <a name="install-and-use-onnx-runtime-with-python"></a>安装 ONNX 运行时并与 Python 配合使用

ONNX 运行时的Python 包可用于 [PyPi.org](https://pypi.org)（[CPU](https://pypi.org/project/onnxruntime) 和 [GPU](https://pypi.org/project/onnxruntime-gpu)）。 安装之前，请阅读[系统要求](https://github.com/Microsoft/onnxruntime#system-requirements)。 

 若要安装适用于 Python 的 ONNX 运行时，请使用以下命令之一： 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

若要在 Python 脚本中调用 ONNX 运行时，请使用：    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

模型附带的文档通常会指示有关使用模型的输入和输出。 还可使用可视化工具（如 [Netron](https://github.com/lutzroeder/Netron)）查看模型。 ONNX 运行时还可以查询模型元数据、输入和输出：    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

若要推理模型，请使用 `run`，并传入要返回的输出列表（如果需要所有输出，则保留为空）和输入值的映射。 结果是输出列表。  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

有关完整的 Python API 参考，请参阅 [ONNX 运行时参考文档](https://onnxruntime.ai/docs/api/python-api.html)。  

## <a name="examples"></a>示例
有关创建和部署 ONNX 模型的示例 Python 笔记本，请参阅 [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx)。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

有关其他语言的使用示例，请参阅 [ONNX 运行时 GitHub](https://github.com/microsoft/onnxruntime/tree/master/samples)。

## <a name="more-info"></a>更多信息

详细了解 ONNX 或参与项目：
+ [ONNX 项目网站](https://onnx.ai)
+ [GitHub 上的 ONNX 代码](https://github.com/onnx/onnx)

详细了解 ONNX 运行时或参与项目：
+ [ONNX 运行时项目网站](https://onnxruntime.ai)
+ [ONNX 运行时 GitHub 存储库](https://github.com/Microsoft/onnxruntime)
