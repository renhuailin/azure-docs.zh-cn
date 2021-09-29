---
title: 使用本地模型部署进行故障排除
titleSuffix: Azure Machine Learning
description: 尝试将本地模型部署作为排查模型部署错误的第一步。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: ca06d475544cb45045d751ede1abe743a3a012ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553435"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>使用本地模型部署进行故障排除

尝试将本地模型部署用作对 Azure 容器实例 (ACI) 或 Azure Kubernetes 服务 (AKS) 部署进行故障排除的第一步。  使用本地 Web 服务可以更轻松地发现和修复常见的 Azure 机器学习 Docker Web 服务部署错误。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。
* 选项 A（建议）- 在 Azure 机器学习计算实例上进行本地调试
   * 正在运行[计算实例](how-to-deploy-local-container-notebook-vm.md)的 Azure 机器学习工作区
* 选项 B - 在计算中进行本地调试
   * [Azure 机器学习 SDK](/python/api/overview/azure/ml/install)。
   * [Azure CLI](/cli/azure/install-azure-cli)。
   * [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
   * 在本地系统上安装有效的 Docker。 
   * 若要验证 Docker 安装，请使用终端或命令提示符中的命令 `docker run hello-world`。 有关安装 Docker 或排除 Dcoker 错误的详细信息，请参阅 [Docker 文档](https://docs.docker.com/)。
* 选项 C - 通过 Azure 机器学习推理 HTTP 服务器启用本地调试。
    * Azure 机器学习推理 HTTP 服务器[（预览版）](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)是一个 Python 包，可用于在本地开发环境中轻松验证入口脚本 (`score.py`)。 如果评分脚本出现问题，该服务器将返回一个错误。 它还将返回发生错误的位置。
    * 在持续集成和部署管道中创建验证入口时，也可以使用该服务器。 例如，使用候选脚本启动服务器，并针对本地终结点运行测试套件。

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

## <a name="debug-locally"></a>本地调试

可以在 [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) 存储库中找到示例[本地部署笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb)，以探索可运行的示例。

> [!WARNING]
> 生成方案不支持本地 Web 服务部署。

若要进行本地部署，请修改代码以使用 `LocalWebservice.deploy_configuration()` 创建部署配置。 然后使用 `Model.deploy()` 部署该服务。 以下示例将模型（包含在 model 变量中）部署为本地 Web 服务：

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

如果定义你自己的 conda 规范 YAML，请将版本大于等于 1.0.45 的 azureml-defaults 作为 pip 依赖项列出。 需要此包以将模型作为 Web 服务托管。

此时，你可以正常使用该服务。 以下代码演示了将数据发送到该服务的过程：

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

有关自定义 Python 环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。 

### <a name="update-the-service"></a>更新服务

在本地测试中，可能需要更新 `score.py` 文件以添加记录或尝试解决发现的任何问题。 若要重新加载对 `score.py` 文件的更改，请使用 `reload()`。 例如，以下代码重新加载服务的脚本，然后向其发送数据。 使用 `score.py` 文件对数据进行评分：

> [!IMPORTANT]
> `reload` 方法仅适用于本地部署。 有关将部署更新到其他计算目标的信息，请参阅[如何更新 webservice](how-to-deploy-update-web-service.md)。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 可从服务所使用的 `InferenceConfig` 对象指定的位置重新加载该脚本。

若要更改模型、Conda 依赖项或部署配置，请使用 [update()](/python/api/azureml-core/azureml.core.webservice%28class%29#update--args-)。 以下示例更新服务使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>删除服务

要删除服务，请使用 [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--)。

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> 检查 Docker 日志

可以通过服务对象打印详细的 Docker 引擎日志消息。 可以查看 ACI、AKS 和 Local 部署的日志。 以下示例演示如何打印日志。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

如果在日志中看到行 `Booting worker with pid: <pid>` 多次出现，则意味着没有足够的内存来启动辅助角色。
可以通过增加 `deployment_config` 中 `memory_gb` 的值来处理错误

## <a name="next-steps"></a>后续步骤

详细了解部署：

* [如何排查远程部署问题](how-to-troubleshoot-deployment.md)
* [Azure 机器学习推理 HTTP 服务器](how-to-inference-server-http.md)
* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练和部署模型](tutorial-train-models-with-aml.md)
* [如何在本地运行和调试试验](./how-to-debug-visual-studio-code.md)