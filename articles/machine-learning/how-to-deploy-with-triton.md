---
title: 借助 Triton 实现的高性能服务（预览）
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用 NVIDIA Triton 推理服务器部署模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 971a6474b3e48f70c1e4e96a784bf1d92709cf71
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885211"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>利用 Triton 推理服务器实现的高性能服务（预览） 

了解如何使用 [NVIDIA Triton 推理服务器](https://aka.ms/nvidia-triton-docs)提高用于模型推理的 Web 服务的性能。

用于部署推理模型的一种方法是使用 Web 服务。 例如，Azure Kubernetes 服务或 Azure 容器实例的部署。 默认情况下，Azure 机器学习使用单线程、常规用途的 Web 框架来实现 Web 服务部署。

Triton 是针对推理进行了优化的框架。 它提供更好的 GPU 利用率和更具成本效益的推理。 在服务器端，它对传入的请求进行批处理，并提交这些批处理以进行推理。 通过批处理能够更好地利用 GPU 资源，这是 Triton 性能的关键部分。

> [!IMPORTANT]
> 使用 Triton 从 Azure 机器学习进行部署这一功能目前处于预览状态。 客户支持可能不会涵盖预览版功能。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> 本文档中的代码片段用于说明目的，可能不会显示完整的解决方案。 有关工作示例代码，请参阅 [Azure 机器学习中 Triton 的端到端示例](https://aka.ms/triton-aml-sample)。

> [!NOTE]
> [NVIDIA Triton 推理服务器](https://aka.ms/nvidia-triton-docs)是 Azure 机器学习中集成的开源第三方软件。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。
* 熟悉[如何使用 Azure 机器学习部署模型以及部署到的位置](how-to-deploy-and-where.md)。
* [适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/) 或 [Azure CLI](/cli/azure/) 和[机器学习扩展](reference-azure-machine-learning-cli.md)。
* 用于本地测试的 Docker 的工作安装。 有关安装和验证 Docker 的信息，请参阅 docker 文档中的[方向和设置](https://docs.docker.com/get-started/)。

## <a name="architectural-overview"></a>体系结构概述

在尝试将 Triton 用于自己的模型之前，请务必了解它如何处理 Azure 机器学习，并将其与默认部署进行比较。

不使用 Triton 的默认部署

* 已启用多个 [Gunicorn](https://gunicorn.org/) 辅助角色来并发处理传入请求。
* 这些辅助角色执行预处理、调用模型并执行后期处理。 
* 客户端使用 Azure ML 评分 URI。 例如，`https://myservice.azureml.net/score`。

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="正常的非 triton 部署体系结构图":::

**直接使用 Triton 进行部署**

* 系统将请求直接发送到 Triton 服务器。
* Triton 对请求进行批处理，以最大程度利用 GPU。
* 客户端使用 Triton URI 发出请求。 例如，`https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`。

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="仅使用 Triton 进行 Inferenceconfig 部署，无 Python 中间件":::

**使用 Triton 的推理配置部署**

* 已启用多个 [Gunicorn](https://gunicorn.org/) 辅助角色来并发处理传入请求。
* 请求会转发到“Triton 服务器”。 
* Triton 对请求进行批处理，以最大程度利用 GPU。
* 客户端使用 Azure ML 评分 URI 发出请求。 例如，`https://myservice.azureml.net/score`。

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="使用 Triton 和 Python 中间件进行部署":::

将 Triton 用于模型部署的工作流为：

1. 直接使用 Triton 为模型服务。
1. 验证是否可以将请求发送到部署了 Triton 的模型。
1. （可选）为服务器端预处理和后期处理创建一个 Python 中间件层

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>在不进行 Python 预处理和后期处理的情况下部署 Triton

首先，请按照以下步骤验证 Triton 推理服务器是否可以为模型提供服务。

### <a name="optional-define-a-model-config-file"></a>（可选）定义模型配置文件

模型配置文件告知 Triton 预期输入是多少以及这些输入将是什么维度。 有关创建配置文件的详细信息，请参阅 NVIDIA 文档中的[模型配置](https://aka.ms/nvidia-triton-docs)。

> [!TIP]
> 启动 Triton 推理服务器时，我们使用 `--strict-model-config=false` 选项，这意味着你无需为 ONNX 或 TensorFlow 模型提供 `config.pbtxt` 文件。
> 
> 有关此选项的详细信息，请参阅 NVIDIA 文档中的[生成的模型配置](https://aka.ms/nvidia-triton-docs)。

### <a name="use-the-correct-directory-structure"></a>使用正确的目录结构

向 Azure 机器学习注册模型时，可以注册单独的文件或目录结构。 若要使用 Triton，模型注册必须针对包含名为 `triton` 的目录的目录结构。 此目录的一般结构为：

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> 此目录结构是一个 Triton 模型存储库，你的模型使用 Triton 时需要这个结构。 有关详细信息，请参阅 NVIDIA 文档中的 [Triton 模型存储库](https://aka.ms/nvidia-triton-docs)。

### <a name="register-your-triton-model"></a>注册 Triton 模型

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

有关 `az ml model register` 的详细信息，请参阅[参考文档](/cli/azure/ml/model)。

在 Azure 机器学习中注册模型时，`--model-path  -p` 参数的值必须为 Triton 的父文件夹的名称。  
在以上示例中，`--model-path` 是“模型”。

在此示例中，`--name  -n` 参数的值“my_triton_model”将作为 Azure 机器学习工作区已知的模型名称。 

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
有关详细信息，请参阅关于[模型类](/python/api/azureml-core/azureml.core.model.model)的文档。

---

### <a name="deploy-your-model"></a>部署模型

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

如果已有启用了 GPU 的 Azure Kubernetes 服务群集名为“aks-gpu”（通过 Azure 机器学习创建），则可以使用以下命令来部署模型。

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

请参阅[此文档以了解有关部署模型的详细信息](how-to-deploy-and-where.md)。

### <a name="call-into-your-deployed-model"></a>调入已部署的模型

首先，获取评分 URI 和持有者令牌。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

然后，执行以下操作确保服务正在运行： 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

此命令返回如下信息。 请注意 `200 OK`；此状态意味着 Web 服务器正在运行。

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

执行运行状况检查后，可以创建一个客户端，将数据发送到 Triton 以进行推理。 有关创建客户端的详细信息，请参阅 NVIDIA 文档中的[客户端示例](https://aka.ms/nvidia-client-examples)。 还可以参阅 [Triton GitHub 中的 Python 示例](https://aka.ms/nvidia-triton-docs)。

此时，如果不想将 Python 预处理和后期处理添加到已部署的 Webservice，则操作可能已经完成了。 如果要添加此预处理逻辑和后期处理逻辑，请继续阅读。

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>（可选）使用 Python 入口脚本重新部署，以便进行预处理和后期处理

验证 Triton 能够为模型提供服务之后，可以通过定义入口脚本来添加预处理和后处理代码。 此文件的名称为 `score.py`。 有关入口脚本的详细信息，请参阅[定义入口脚本](how-to-deploy-and-where.md#define-an-entry-script)。

这两个主要步骤旨在初始化 `init()` 方法中的 Triton HTTP 客户端，并在 `run()` 函数中调用该客户端。

### <a name="initialize-the-triton-client"></a>初始化 Triton 客户端

请在 `score.py` 文件中包含如下所示的代码。 Azure 机器学习中的 Triton 预期在端口 8000 的 localhost 上寻址。 在本例中，localhost 位于此部署的 Docker 映像中，而不在本地计算机的端口上：

> [!TIP]
> 特选 `AzureML-Triton` 环境中包含了 `tritonhttpclient` pip 包，因此无需将该包指定为 pip 依赖项。

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>修改评分脚本以调用 Triton

下面的示例演示如何动态请求模型的元数据：

> [!TIP]
> 可以使用 Triton 客户端的 `.get_model_metadata` 方法动态请求已通过 Triton 加载的模型的元数据。 参阅[示例笔记本](https://aka.ms/triton-aml-sample)，获取其用法的示例。

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>使用推理配置重新部署

通过推理配置，你可以使用入口脚本，以及通过 Python SDK 或 Azure CLI 完成的 Azure 机器学习部署过程。

> [!IMPORTANT]
> 必须指定 `AzureML-Triton` [特选环境](./resource-curated-environments.md)。
>
> Python 代码示例将 `AzureML-Triton` 克隆到另一个名为 `My-Triton` 的环境中。 Azure CLI 代码也使用此环境。 有关克隆环境的详细信息，请参阅 [Environment.Clone()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) 引用。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> 有关创建推理配置的详细信息，请参阅[推理配置架构](./reference-azure-machine-learning-cli.md#inference-configuration-schema)。

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

部署完成后将显示评分 URI。 对于此本地部署，则为 `http://localhost:6789/score`。 如果部署到云，则可以使用 [az ml service show](/cli/azure/ml/service#az_ml_service_show) CLI 命令来获取评分 URI。

有关如何创建将推理请求发送到评分 URI 的客户端的信息，请参阅[使用部署为 Web 服务的模型](how-to-consume-web-service.md)。

### <a name="setting-the-number-of-workers"></a>设置辅助角色数

若要在部署中设置辅助角色数，请设置环境变量 `WORKER_COUNT`。 假设有一个名为 `env` 的 [Environment](/python/api/azureml-core/azureml.core.environment.environment) 对象，可以执行以下操作：

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

这会告知 Azure ML 启动指定数目的辅助角色。


## <a name="clean-up-resources"></a>清理资源

如果你打算继续使用 Azure 机器学习工作区，但要删除已部署的服务，请使用以下选项之一：


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---
## <a name="troubleshoot"></a>疑难解答

* [对失败的部署进行故障排除](how-to-troubleshoot-deployment.md)，了解如何排查和解决在部署模型时可能遇到的常见错误。

* 如果部署日志显示“TritonServer 启动失败”，请参阅 [Nvidia 的开源文档](https://github.com/triton-inference-server/server)。

## <a name="next-steps"></a>后续步骤

* [请参阅 Azure 机器学习中 Triton 的端到端示例](https://aka.ms/aml-triton-sample)
* 查看 [Triton 客户端示例](https://aka.ms/nvidia-client-examples)
* 阅读 [Triton 推理服务器文档](https://aka.ms/nvidia-triton-docs)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
