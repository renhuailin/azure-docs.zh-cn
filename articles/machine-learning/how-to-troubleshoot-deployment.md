---
title: 远程模型部署故障排除
titleSuffix: Azure Machine Learning
description: 了解如何规避、解决及排查 Azure Kubernetes 服务和 Azure 容器实例的一些常见 Docker 部署错误。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 0e3163ad1cdc3f75bb40975c57af277339e2a62d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620305"
---
# <a name="troubleshooting-remote-model-deployment"></a>远程模型部署故障排除 

了解如何使用 Azure 机器学习排查、解决或规避在将模型部署到 Azure 容器实例 (ACI) 和 Azure Kubernetes 服务 (AKS) 时可能遇到的常见错误。

> [!NOTE]
> 如果要将模型部署到 Azure Kubernetes 服务 (AKS)，建议为该群集启用 [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md)。 这将帮助你了解总体群集运行状况和资源使用情况。 以下资源也可能有用：
>
> * [检查影响 AKS 群集的资源运行状况事件](../aks/aks-resource-health.md)
> * [Azure Kubernetes 服务诊断](../aks/concepts-diagnostics.md)
>
> 如果尝试将模型部署到运行不正常或重载的群集，应该会遇到问题。 如果需要帮助排查 AKS 群集问题，请联系 AKS 支持。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。
* [Azure 机器学习 SDK](/python/api/overview/azure/ml/install)。
* [Azure CLI](/cli/azure/install-azure-cli)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>机器学习模型的 Docker 部署步骤

在 Azure 机器学习中将模型部署到非本地计算时，会发生以下情况：

1. 你在 InferenceConfig 的 Environments 对象中指定的 Dockerfile 将与源目录的内容一起发送到云
1. 如果以前生成的映像在容器注册表中不可用，则云中会生成新的 Docker 映像，该映像会存储在工作区的默认容器注册表中。
1. 容器注册表中的 Docker 映像将下载到计算目标。
1. 工作区的默认 Blob 存储会装载到计算目标，使你可以访问已注册的模型
1. Web 服务器通过运行入口脚本的 `init()` 函数进行初始化
1. 已部署的模型收到请求时，`run()` 函数会处理该请求

使用本地部署时的主要区别是，容器映像在本地计算机上生成，因此需要为本地部署安装 Docker。

了解这些高级步骤会有助于了解发生错误的位置。

## <a name="get-deployment-logs"></a>获取部署日志

调试错误的第一步是获取部署日志。 首先，请按照[此处的说明](how-to-deploy-and-where.md#connect-to-your-workspace)连接到工作区。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

若要从已部署的 Web 服务获取日志，请执行以下命令：

```azurecli
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


假设有一个名为 `ws` 的 `azureml.core.Workspace` 类型的对象，可以执行以下操作：

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>本地调试

如果将模型部署到 ACI 或 AKS 时遇到问题，请将其部署为本地 Web 服务。 使用本地 Web 服务可简化解决问题的过程。 若要在本地对部署进行故障排除，请参阅[本地故障排除文章](./how-to-troubleshoot-deployment-local.md)。

## <a name="azure-machine-learning-inference-http-server"></a>Azure 机器学习推理 HTTP 服务器

在本地推理服务器中可以快速调试入口脚本 (`score.py`)。 如果基础评分脚本存在 bug，该服务器将无法初始化或者为模型提供服务。 相反，它会引发异常并指出发生问题的位置。 [详细了解 Azure 机器学习推理 HTTP 服务器](how-to-inference-server-http.md)

1. 从 [pypi](https://pypi.org/) 源安装 `azureml-inference-server-http` 包：

    ```bash
    python -m pip install azureml-inference-server-http
    ```

2. 启动服务器并将 `score.py` 设置为入口脚本：

    ```bash
    azmlinfsrv --entry_script score.py
    ```

3. 使用 `curl` 将评分请求发送到服务器：

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

## <a name="container-cannot-be-scheduled"></a>无法计划容器

将服务部署到 Azure Kubernetes Service 计算目标时，Azure 机器学习将尝试使用请求的资源量来计划服务。 如果在 5 分钟后，群集中没有具有相应可用资源量的节点，则部署将失败。 失败消息为 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00`。 可通过添加更多节点、更改节点的 SKU 或更改服务的资源要求来解决此错误。 

该错误消息通常会指示你更需要哪一种资源 - 例如，如果看到一条指示“`0/3 nodes are available: 3 Insufficient nvidia.com/gpu`”的错误消息，则意味着该服务需要 GPU，且群集中有三个节点没有可用的 GPU。 如果使用的是 GPU SKU，则可以通过添加更多节点来解决此问题；如果使用的不是 GPU SKU，则可以通过切换到启用 GPU 的 SKU，或将环境更改为不需要 GPU 来解决此问题。  

## <a name="service-launch-fails"></a>服务启动失败

成功生成映像后，系统会尝试使用部署配置启动容器。 作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误。

利用[检查 Docker 日志](how-to-troubleshoot-deployment-local.md#dockerlog)文章中的信息。

## <a name="function-fails-get_model_path"></a>函数故障：get_model_path()

通常情况下，在评分脚本的 `init()` 函数中，会调用 [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) 来查找容器中的模型文件或模型文件的文件夹。 如果找不到模型文件或文件夹，函数将失败。 调试此错误的最简单方法是在容器 shell 中运行以下 Python 代码：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

该示例将打印容器中的本地路径（相对于 `/var/azureml-app`），其中评分脚本有望找到模型文件或文件夹。 然后可以验证文件或文件夹是否确实位于其应该在的位置。

将日志记录级别设置为 DEBUG 可能会导致记录其他信息，这可能有助于识别故障。

## <a name="function-fails-runinput_data"></a>函数故障：run(input_data)

如果服务部署成功，但在向评分终结点发布数据时崩溃，可在 `run(input_data)` 函数中添加错误捕获语句，以便转而返回详细的错误消息。 例如：

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**注意**：通过 `run(input_data)` 调用返回错误消息应仅用于调试目的。 出于安全原因，不应在生产环境中按此方法返回错误消息。

## <a name="http-status-code-502"></a>HTTP 状态代码 502

502 状态代码指示服务在 score.py 文件的 `run()` 方法中引发了异常或已崩溃。 使用本文中的信息调试该文件。

## <a name="http-status-code-503"></a>HTTP 状态代码 503

Azure Kubernetes 服务部署支持自动缩放，这允许添加副本以支持额外的负载。 自动缩放程序旨在处理负载中的逐步更改。 如果每秒收到大量请求，客户端可能会收到 HTTP 状态代码 503。 即使自动缩放器反应迅速，但 AKS 仍需要大量时间来创建其他容器。

纵向扩展/缩减决策取决于当前容器副本的利用率。 处于繁忙状态（正在处理请求）的副本数除以当前副本的总数为当前利用率。 如果此数字超出 `autoscale_target_utilization`，则会创建更多的副本。 如果它较低，则会减少副本。 添加副本的决策是迫切而迅速的（大约 1 秒）。 删除副本的决策是保守的（大约 1 分钟）。 默认情况下，自动缩放目标利用率设置为 70%，这意味着服务可以处理高达 30% 的大量每秒请求数 (RPS) 。

有两种方法可以帮助防止 503 状态代码：

> [!TIP]
> 这两种方法可以单独使用，也可以结合使用。

* 更改自动缩放创建新副本的利用率。 可以通过将 `autoscale_target_utilization` 设置为较低的值来调整利用率目标。

    > [!IMPORTANT]
    > 该更改不会导致更快创建副本。 而会以较低的利用率阈值创建副本。 可以在利用率达到 30% 时，通过将值改为 30% 来创建副本，而不是等待该服务的利用率达到 70% 时再创建。
    
    如果 Web 服务已在使用当前最大副本，然后你仍能看见 503 状态代码，则请增加 `autoscale_max_replicas` 值以增加副本的最大数量。

* 更改副本最小数量。 增加最小副本可提供一个更大的池来处理传入峰值。

    若要增加副本的最小数量，请将 `autoscale_min_replicas` 设置为更大的值。 可以使用以下代码计算所需的副本，将值替换为项目指定的值：

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 如果收到请求高峰大于新的最小副本可以处理的数量，则可能会再次收到 503 代码。 例如，服务流量增加时，可能需要增加最小副本数据。

有关设置 `autoscale_target_utilization`、`autoscale_max_replicas` 和 `autoscale_min_replicas` 的详细信息，请参阅 [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) 模块参考。

## <a name="http-status-code-504"></a>HTTP 状态代码 504

504 状态代码指示请求已超时。默认超时值为 1 分钟。

可以通过修改 score.py 删除不必要的调用来增加超时值或尝试加快服务速度。 如果这些操作不能解决问题，请使用本文中的信息调试 score.py 文件。 代码可能处于无响应状态或无限循环。

## <a name="other-error-messages"></a>其他错误消息

对以下错误采取以下操作：

|错误  | 解决方法  |
|---------|---------|
| 409 冲突错误| 如有操作正在进行，则同一 Web 服务上的任何新操作都将响应 409 冲突错误。 例如，如果创建或更新 Web 服务操作正在进行，那么触发新的删除操作将会引发错误。 |
|部署 Web 服务时映像生成失败     |  将“pynacl==1.2.1”作为 pip 依赖项添加到 Conda 文件以进行映像配置       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   请将部署中使用的 VM 的 SKU 更改为具有更多内存的 SKU。 |
|FPGA 失败     |  你将无法在 FPGA 上部署模型，直到已请求并获得 FPGA 配额批准为止。 若要请求访问权限，请填写配额请求表单： https://aka.ms/aml-real-time-ai       |


## <a name="advanced-debugging"></a>高级调试

你可能需要以交互方式调试模型部署中包含的 Python 代码。 例如，如果输入脚本失败，并且无法通过其他记录确定原因。 通过使用 Visual Studio Code 和 debugpy，可以附加到在 Docker 容器中运行的代码。

有关详细信息，请访问[在 VS Code 指南中进行交互式调试](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)。

## <a name="model-deployment-user-forum"></a>[模型部署用户论坛](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>后续步骤

详细了解部署：

* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练和部署模型](tutorial-train-models-with-aml.md)
* [如何在本地运行和调试试验](./how-to-debug-visual-studio-code.md)