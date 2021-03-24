---
title: 使用本地模型部署进行故障排除
titleSuffix: Azure Machine Learning
description: 尝试将本地模型部署作为排查模型部署错误的第一步。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 69ac47296cb4624de6cdf05ddb3e72973751f631
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519616"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>使用本地模型部署进行故障排除

尝试将本地模型部署用作对 Azure 容器实例 (ACI) 或 Azure Kubernetes 服务 (AKS) 部署进行故障排除的第一步。  使用本地 Web 服务可以更轻松地发现和修复常见的 Azure 机器学习 Docker Web 服务部署错误。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
* 选项 A（建议）- 在 Azure 机器学习计算实例上进行本地调试
   * 正在运行[计算实例](how-to-deploy-local-container-notebook-vm.md)的 Azure 机器学习工作区
* 选项 B - 在计算中进行本地调试
   * [Azure 机器学习 SDK](/python/api/overview/azure/ml/install)。
   * [Azure CLI](/cli/azure/install-azure-cli)。
   * [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
   * 在本地系统上安装有效的 Docker。 
   * 若要验证 Docker 安装，请使用终端或命令提示符中的命令 `docker run hello-world`。 有关安装 Docker 或排除 Dcoker 错误的详细信息，请参阅 [Docker 文档](https://docs.docker.com/)。

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
* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练和部署模型](tutorial-train-models-with-aml.md)
* [如何在本地运行和调试试验](./how-to-debug-visual-studio-code.md)