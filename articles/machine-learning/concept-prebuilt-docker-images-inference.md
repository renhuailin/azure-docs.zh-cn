---
title: 预生成的 Docker 映像
titleSuffix: Azure Machine Learning
description: Azure 机器学习中用于推理（评分）的预生成 Docker 映像
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 10/07/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, curated environments
ms.openlocfilehash: 04ca2582360f0a4463dd806664a84d020b0c58a7
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712174"
---
# <a name="prebuilt-docker-images-for-inference"></a>用于推理的预生成 Docker 映像

通过 Azure 机器学习部署模型时，将使用用于推理的预生成 Docker 容器映像。  这些映像是使用流行的机器学习框架和 Python 包预生成的。 你还可以使用以下方法之一扩展包，以添加其他包：

* [添加 Python 包](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [使用预生成的推理映像作为新 Dockerfile 的基础](how-to-extend-prebuilt-docker-image-inference.md)。 使用此方法可以安装 Python 包和 apt 包。

## <a name="why-should-i-use-prebuilt-images"></a>为何要使用预生成的映像？

* 降低模型部署延迟。
* 提高模型部署成功率。
* 避免在模型部署期间进行不必要的映像生成。
* 只需在映像/容器中拥有依赖关系和访问权限。 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>用于推理的预生成 Docker 映像列表 

[!INCLUDE [list-of-inference-prebuilt-docker-images](../../includes/aml-inference-list-prebuilt-docker-images.md)]

## <a name="next-steps"></a>后续步骤

* [将 Python 包添加到预生成的映像](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [使用预生成的包作为新 Dockerfile 的基础](how-to-extend-prebuilt-docker-image-inference.md)。