---
title: 如何在本地运行和部署
titleSuffix: Azure Machine Learning
description: 了解如何在本地计算机上运行训练的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: fcea7c162e5c978a7c8ff1b42e09ffe0afb98f3c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549913"
---
# <a name="deploy-on-your-local-machines-models-trained-with-azure-machine-learning"></a>在本地计算机上部署 Azure 机器学习培训的模型

本文介绍如何使用本地计算机作为训练或部署在 Azure 机器学习中创建的模型的目标。 Azure 机器学习的灵活性使它可以与大多数 Python 机器学习框架一起使用。 机器学习解决方案通常具有复杂的依赖项，这可能比较困难。 本文将向您展示如何权衡总体控制与易用性。

本地部署的一些方案包括：

* 在项目的早期快速循环访问数据、脚本和模型
* 稍后阶段中的调试和故障排除
* 用户托管硬件上的最终部署

## <a name="prerequisites"></a>必备条件

- Azure 机器学习工作区。 有关详细信息，请参阅 [创建 Azure 机器学习工作区](how-to-manage-workspace.md)
- 模型和环境。 如果没有训练的模型，则可以使用[本教程](tutorial-train-models-with-aml.md)中提供的模型和依赖项文件
- [适用于 Python 的 Azure 机器学习软件开发工具包 (SDK) ](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
- Conda 管理器（例如 Anaconda 或 miniconda) ），如果要镜像 Azure 机器学习的包依赖关系
- Docker，若要使用容器化版本的 Azure 机器学习环境

## <a name="prepare-your-local-machine"></a>准备本地计算机

本地运行 Azure 机器学习模型的最可靠方法是使用 Docker 映像。 Docker 映像提供了一个独立的容器化体验，它在 Azure 执行环境中重复，但硬件问题除外。 有关安装和配置 Docker 以实现开发方案的详细信息，请参阅 [Windows 上的 docker 远程开发概述](/windows/dev-environment/docker/overview)。

虽然可以将调试程序附加到在 Docker 中运行的进程 (请参阅 [附加到正在运行的容器](https://code.visualstudio.com/docs/remote/attach-container)) ，你可能更愿意调试和循环访问 Python 代码，而不涉及 Docker。 在此方案中，重要的是，您的本地计算机使用的库与您在 Azure 机器学习中运行试验时使用的相同。 为了管理 Python 依赖项，Azure 使用 [conda](https://docs.conda.io/)。 尽管可以使用其他包管理器重新创建环境，但在本地计算机上安装和配置 conda 是同步的最简单方法。 

## <a name="prepare-your-entry-script"></a>准备输入脚本

即使使用 Docker 来管理模型和依赖项，Python 计分脚本也必须是本地的。 脚本必须有两种方法：

- `init()`不采用任何参数且不返回任何内容的方法 
- 一个 `run()` 方法，该方法采用 json 格式的字符串并返回一个 json 可序列化的对象。

该方法的参数 `run()` 将采用以下格式： 

```json
{
    "data": <model-specific-data-structure>
}
```

从方法返回的对象 `run()` 必须实现 `toJSON() -> string` 。

下面的示例演示如何加载已注册的 scikit-learn 模型并使用 numpy 数据对其进行评分。 此示例基于 [本教程](tutorial-train-models-with-aml.md)的模型和依赖关系：

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

有关更高级的示例，包括自动 Swagger 架构生成和如何对二进制数据进行评分 (例如，图像) 数据，请阅读 [高级条目脚本创作](how-to-deploy-advanced-entry-script.md)。 

## <a name="deploy-as-a-local-web-service-using-docker"></a>使用 Docker 部署为本地 web 服务

复制 Azure 机器学习使用的环境的最简单方法是使用 Docker 部署 web 服务。 在本地计算机上运行 Docker 后，你将：

1. 连接到在其中注册了模型的 Azure 机器学习工作区
1. 创建 `Model` 表示模型的对象
1. 创建一个 `Environment` 包含依赖项的对象，并定义将在其中运行代码的软件环境
1. 创建一个 `InferenceConfig` 对象，该对象将条目脚本和 `Environment`
1. 创建 `DeploymentConfiguration` 子类的对象 `LocalWebserviceDeploymentConfiguration`
1. 使用 `Model.deploy()` 创建 `Webservice` 对象。 此方法下载 Docker 映像，并将其与 `Model` 、 `InferenceConfig` 和关联 `DeploymentConfiguration`
1. 激活 `Webservice``Webservice.wait_for_deployment()`

下面的代码演示了这些步骤：

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

对的调用 `Model.deploy()` 可能需要几分钟的时间。 最初部署后，使用 `update()` 方法比从头开始更有效。 请参阅 [更新已部署的 web 服务](how-to-deploy-update-web-service.md)。

### <a name="test-your-local-deployment"></a>测试本地部署

运行以前的部署脚本时，它将输出可将数据发布到的 URI，以便 (例如 `http://localhost:6789/score`) 。 下面的示例演示了使用本地部署的模型对示例数据进行评分的脚本 `"sklearn-mnist-local"` 。 如果经过适当定型，则该模型 `normalized_pixel_values` 将推断为 "2"。 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>直接下载并运行模型

当使用 Docker 将模型部署为 web 服务时，最常见的方法是，你可能还想要使用本地 Python 脚本直接运行代码。 需要两个重要元素： 

- 模型本身
- 模型依赖的依赖项 

下载模型可以完成：  

- 从门户中，选择 "**模型**" 选项卡，选择所需的模型，并从 **详细信息** 页中选择 "**下载**"
- 在命令行中，使用 `az ml model download` (参阅 [模型下载引用](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false)) 
- 使用 Python SDK 时 `Model.download()` (参阅 [模型 API 参考](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false)) 

Azure 模型是一个或多个序列化的 Python 对象，打包为 Python pickle 文件 (**. model.pkl** 扩展) 。 Pickle 文件的内容取决于 ML 库或用于定型模型的技术。 例如，通过教程中的模型，可以通过以下方式加载模型：

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

依赖关系始终是非常棘手的，尤其是在机器学习中，在这种情况下，可能会有一令人眼花缭乱的特定版本要求。 通过使用类的方法，可以在本地计算机上重新创建 Azure 机器学习环境，该环境可以是完整的 conda 环境，也可以是 Docker 映像 `Environment` `build_local()` 。 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda env
```

如果将 `build_local()` 的参数设置 `useDocker` 为 `True` ，则该函数将创建 Docker 映像，而不是 conda 环境。 如果需要更多的控制，则可以使用 `Environment` 的 `save_to_directory()` 方法，该方法写入定义文件的 **conda_dependencies docker-compose.override.yml** 和 **azureml_environment.js** ，您可以对这些文件进行微调，并将其用作扩展的基础。 

`Environment`类有许多其他方法可以在计算硬件、Azure 工作区和 Docker 映像中同步环境。 有关详细信息，请参阅[ `Environment` API 参考](/python/api/azureml-core/azureml.core.environment(class))。

下载并解决了该模型的依赖关系之后，在执行评分、微调模型、使用传输学习等操作时，没有 Azure 定义的限制。 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>将重新训练模型上传到 Azure 机器学习

如果你具有本地定型或重新训练的模型，则可以将其注册到 Azure。 注册后，你可以继续使用 Azure 计算进行优化，或使用 azure 设施（如 [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) 或 [Triton 推理服务器 (预览版) ](how-to-deploy-with-triton.md)来部署它。

若要与 Azure 机器学习的 Python SDK 一起使用，必须将模型存储为 pickle 格式的序列化 Python 对象 (**model.pkl** 文件) ，并且必须实现 `predict(data)` 返回 JSON 可序列化对象的方法。 例如，你可以使用以下内容存储一个本地训练的 scikit-learn 糖尿病模型： 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

若要使模型在 Azure 中可用，可以使用类的 `register()` 方法 `Model` ：

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

然后，可以在 Azure 机器学习的 " **模型** " 选项卡中找到新注册的模型：

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="显示上传模型的 Azure 机器学习模型 &quot;选项卡的屏幕截图。":::

有关上传和更新模型和环境的详细信息，请参阅 [注册模型和使用高级使用情况本地部署](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)。

## <a name="next-steps"></a>后续步骤

- 有关管理环境的详细信息，请参阅 [Create & use software 环境 in Azure 机器学习](how-to-use-environments.md)
- 若要了解如何访问数据存储中的数据，请参阅 [连接到 Azure 上的存储服务](how-to-access-data.md)