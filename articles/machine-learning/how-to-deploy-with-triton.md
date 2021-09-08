---
title: 借助 Triton 实现的高性能服务（预览）
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用 NVIDIA Triton 推理服务器部署模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 05/17/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: d1f45c5501395ff486101f7270212c8fb333aede
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436961"
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

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://azure.microsoft.com/free/)。
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

在 Azure 机器学习中注册模型时，`--model-path  -p` 参数的值必须为 Triton 模型存储库的父文件夹的名称。
在以上示例中，`--model-path` 是“模型”。

在此示例中，`--name  -n` 参数的值“`my_triton_models`”将作为 Azure 机器学习工作区已知的模型名称。 

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=register-model)]

有关详细信息，请参阅关于[模型类](/python/api/azureml-core/azureml.core.model.model)的文档。

---

### <a name="deploy-your-model"></a>部署模型

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

如果已有启用了 GPU 的 Azure Kubernetes 服务群集名为“aks-gpu”（通过 Azure 机器学习创建），则可以使用以下命令来部署模型。

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=deploy-webservice)]

---

请参阅[此文档以了解有关部署模型的详细信息](how-to-deploy-and-where.md)。

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

### <a name="call-into-your-deployed-model"></a>调入已部署的模型

首先，获取评分 URI 和持有者令牌。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=get-keys)]

---

然后，执行以下操作确保服务正在运行： 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
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
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=query-service)]

---


执行运行状况检查后，可以创建一个客户端，将数据发送到 Triton 以进行推理。 有关创建客户端的详细信息，请参阅 NVIDIA 文档中的[客户端示例](https://aka.ms/nvidia-client-examples)。 还可以参阅 [Triton GitHub 中的 Python 示例](https://aka.ms/nvidia-triton-docs)。

## <a name="clean-up-resources"></a>清理资源

如果你打算继续使用 Azure 机器学习工作区，但要删除已部署的服务，请使用以下选项之一：


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=delete-service)]

---

## <a name="how-to-use-azure-machine-learning-triton-inference-server-container-image"></a>如何使用 Azure 机器学习 Triton 推理服务器容器映像

了解如何通过新的 [CLI(v2)](/cli/azure/ml?view=azure-cli-latest&preserve-view=true) 使用 Azure 机器学习 Triton 推理服务器容器映像。 下面的示例使用[联机终结点和部署](concept-endpoints.md#what-are-online-endpoints-preview)概念。 

1. [部署单个 Triton 模型](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-triton-managed-online-endpoint.sh)。
1. [部署多个 Triton 模型](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-triton-multiple-models-online-endpoint.sh)。
1. [部署 Triton 系综模型](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-triton-ensemble-managed-online-endpoint.sh)。
1. 查看 [Triton 示例](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton)。

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
