---
title: 预生成的 Docker 映像
titleSuffix: Azure Machine Learning
description: Azure 机器学习中用于推理（评分）的预生成 Docker 映像
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 4851e5cdfc5e50febf7d60ab4d7eaee2c95da5b4
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866299"
---
# <a name="prebuilt-docker-images-for-inference-preview"></a>用于推理的预生成 Docker 映像（预览版）

通过 Azure 机器学习部署模型时，将使用用于推理的预生成 Docker 容器映像[（预览版）](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。  这些映像是使用流行的机器学习框架和 Python 包预生成的。 你还可以使用以下方法之一扩展包，以添加其他包：

* [添加 Python 包](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [使用预生成的推理映像作为新 Dockerfile 的基础](how-to-extend-prebuilt-docker-image-inference.md)。 使用此方法可以安装 Python 包和 apt 包。

## <a name="why-should-i-use-prebuilt-images"></a>为何要使用预生成的映像？

* 降低模型部署延迟。
* 提高模型部署成功率。
* 避免在模型部署期间进行不必要的映像生成。
* 只需在映像/容器中拥有依赖关系和访问权限。 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>用于推理的预生成 Docker 映像列表 

* 所有 docker 映像都以非根用户身份运行。
* 我们建议对 docker 映像使用 `latest` 标记。 用于推理的预生成 docker 映像会发布到 Microsoft 容器注册表 (MCR)。若要查询可用标记的列表，请按照[其 GitHub 存储库的说明](https://github.com/microsoft/ContainerRegistry#browsing-mcr-content)进行操作。

### <a name="tensorflow"></a>TensorFlow

框架版本 | CPU/GPU | 预安装的包 | MCR 路径 | 特选环境
 --- | --- | --- | --- | --- |
 1.15 | CPU | pandas==0.25.1 </br> numpy=1.20.1 | `mcr.microsoft.com/azureml/tensorflow-1.15-ubuntu18.04-py37-cpu-inference:latest`  | AzureML-tensorflow-1.15-ubuntu18.04-py37-cpu-inference | 
2.4 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cpu-inference |
2.4 | GPU | numpy >= 1.16.0 </br> pandas~=1.1.x </br> CUDA==11.0.3 </br> CuDNN==8.0.5.39 | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference |

### <a name="pytorch"></a>PyTorch

框架版本 | CPU/GPU | 预安装的包 | MCR 路径 | 特选环境
 --- | --- | --- | --- | --- |
 1.6 | CPU | numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.6-ubuntu18.04-py37-cpu-inference |
1.7 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/pytorch-1.7-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.7-ubuntu18.04-py37-cpu-inference |

### <a name="scikit-learn"></a>SciKit-Learn

框架版本 | CPU/GPU | 预安装的包 | MCR 路径 | 特选环境
 --- | --- | --- | --- | --- |
0.24.1  | CPU | scikit-learn==0.24.1 </br> numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/sklearn-0.24.1-ubuntu18.04-py37-cpu-inference:latest` | AzureML-sklearn-0.24.1-ubuntu18.04-py37-cpu-inference |

### <a name="onnx-runtime"></a>ONNX 运行时

框架版本 | CPU/GPU | 预安装的包 | MCR 路径 | 特选环境
 --- | --- | --- | --- | --- |
1.6 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/onnxruntime-1.6-ubuntu18.04-py37-cpu-inference:latest` |AzureML-onnxruntime-1.6-ubuntu18.04-py37-cpu-inference |

### <a name="xgboost"></a>XGBoost

框架版本 | CPU/GPU | 预安装的包 | MCR 路径 | 特选环境
 --- | --- | --- | --- | --- |
0.9 | CPU | scikit-learn==0.23.2 </br> numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/xgboost-0.9-ubuntu18.04-py37-cpu-inference:latest` | AzureML-xgboost-0.9-ubuntu18.04-py37-cpu-inference | 

### <a name="no-framework"></a>无框架

框架版本 | CPU/GPU | 预安装的包 | MCR 路径 | 特选环境
 --- | --- | --- | --- | --- |
NA | CPU | NA | `mcr.microsoft.com/azureml/minimal-ubuntu18.04-py37-cpu-inference:latest` | AzureML-minimal-ubuntu18.04-py37-cpu-inference  |

## <a name="next-steps"></a>后续步骤

* [将 Python 包添加到预生成的映像](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [使用预生成的包作为新 Dockerfile 的基础](how-to-extend-prebuilt-docker-image-inference.md)。