---
title: 如何部署机器学习模型
titleSuffix: Azure Machine Learning
description: 了解如何以及在哪里部署机器学习模型。 部署到 Azure 容器实例、Azure Kubernetes 服务 和 FPGA。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 04/21/2021
ms.topic: how-to
ms.custom: devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2, contperf-fy21q4
adobe-target: true
ms.openlocfilehash: 22161ee49c2527b92b11b4c1fe197fa06d48fcb9
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122661979"
---
# <a name="deploy-machine-learning-models-to-azure"></a>将机器学习模型部署到 Azure 

了解如何将机器学习或深度学习模型作为 Web 服务部署在 Azure 云中。

> [!TIP]
> 托管联机终结点（预览版）使你无需创建和管理底层基础结构即可部署已训练的模型。 有关详细信息，请参阅[使用托管联机终结点（预览版）部署机器学习模型并为其评分](how-to-deploy-managed-online-endpoints.md)。

无论你在何处部署模型，工作流都是类似的：

1. 注册模型
1. 准备入口脚本
1. 准备推理配置
1. 在本地部署模型以确保一切正常运作
1. 选择计算目标
1. 将模型重新部署到云端
1. 测试生成的 Web 服务

若要详细了解机器学习部署工作流中涉及的概念，请参阅[使用 Azure 机器学习来管理、部署和监视模型](concept-model-management-and-deployment.md)。

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="prerequisites"></a>先决条件

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
- 模型。 如果没有已训练的模型，则可以使用[此教程](https://aka.ms/azml-deploy-cloud)中提供的模型和依赖项文件。
- [机器学习服务的 Azure 命令行界面 (CLI) 扩展](reference-azure-machine-learning-cli.md)。
- 可运行 Docker 的计算机，如[计算实例](how-to-create-manage-compute-instance.md)。

# <a name="python"></a>[Python](#tab/python)

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
- 模型。 如果没有已训练的模型，则可以使用[此教程](https://aka.ms/azml-deploy-cloud)中提供的模型和依赖项文件。
- [适用于 Python 的 Azure 机器学习软件开发工具包 (SDK)](/python/api/overview/azure/ml/intro)。
- 可运行 Docker 的计算机，如[计算实例](how-to-create-manage-compute-instance.md)。
---

## <a name="connect-to-your-workspace"></a>连接到工作区

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

应做事项

```azurecli-interactive
az login
az account set -s <my subscription>
az ml workspace list --resource-group=<my resource group>
```

以便查看有权访问的工作区。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace(subscription_id="<subscription_id>",
               resource_group="<resource_group>",
               workspace_name="<workspace_name>")
```

若要详细了解如何使用 SDK 连接到工作区，请参阅[用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro#workspace) 文档。


---

## <a name="register-your-model"></a><a id="registermodel"></a> 注册模型

已部署的机器学习服务会遇到的典型情况是需要以下组件：
    
 + 表示要部署的特定模型的资源（例如：pytorch 模型文件）
 + 你将在服务中运行的代码，根据给定输入执行模型

Azure 机器学习允许你将部署分成两个单独的部分以便保留相同代码，但只限更新模型。 我们将上传模型与代码“分开”的这种机制定义为“注册模型”。

当你注册模型时，我们会将该模型上传到云端（位于工作区的默认存储帐户中），然后将其装载到运行 Web 服务的相同计算机中。

以下示例演示如何注册模型。

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-a-local-file"></a>通过本地文件注册模型

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=register-model-from-local-file-code)]

将 `-p` 设为要注册的文件夹或文件的路径。

有关 `az ml model register` 的详细信息，请参阅[参考文档](/cli/azure/ext/azure-cli-ml/ml/model)。

### <a name="register-a-model-from-an-azure-ml-training-run"></a>通过 Azure ML 训练运行注册一个模型

```azurecli-interactive
az ml model register -n bidaf_onnx --asset-path outputs/model.onnx --experiment-name myexperiment --run-id myrunid --tag area=qna
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path` 参数表示模型的云位置。 本示例使用的是单个文件的路径。 若要在模型注册中包含多个文件，请将 `--asset-path` 设置为包含文件的文件夹的路径。

有关 `az ml model register` 的详细信息，请参阅[参考文档](/cli/azure/ml/model)。

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-a-local-file"></a>通过本地文件注册模型

可以通过提供模型的本地路径来注册模型。 可以提供本地计算机上文件夹或单个文件的路径。
<!-- pyhton nb call -->
[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=register-model-from-local-file-code)]


若要在模型注册中包含多个文件，请将 `model_path` 设置为包含文件的文件夹的路径。

有关详细信息，请参阅关于[模型类](/python/api/azureml-core/azureml.core.model.model)的文档。


### <a name="register-a-model-from-an-azure-ml-training-run"></a>通过 Azure ML 训练运行注册一个模型

  使用 SDK 训练模型时，可以接收 [Run](/python/api/azureml-core/azureml.core.run.run) 对象或 [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) 对象，具体取决于模型的训练方式。 每个对象都可用于注册通过实验运行创建的模型。

  + 通过 `azureml.core.Run` 对象注册模型：
 
    ```python
    model = run.register_model(model_name='bidaf_onnx',
                               tags={'area': 'qna'},
                               model_path='outputs/model.onnx')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` 参数表示模型的云位置。 本示例使用的是单个文件的路径。 若要在模型注册中包含多个文件，请将 `model_path` 设置为包含文件的文件夹的路径。 有关详细信息，请参阅 [Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) 文档。

  + 通过 `azureml.train.automl.run.AutoMLRun` 对象注册模型：

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'qna'})

        print(run.model_id)
    ```

    在此示例中，未指定 `metric` 和 `iteration` 参数，因此将注册具有最佳主要指标的迭代。 不会使用模型名称，而是使用从运行返回的 `model_id` 值。

    有关详细信息，请参阅 [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 文档。

    若要从 `AutoMLRun` 部署已注册的模型，建议通过 [Azure 机器学习工作室中的一键式部署按钮](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)来执行此操作。 

---

## <a name="define-a-dummy-entry-script"></a>定义虚拟入口脚本

[!INCLUDE [write entry script](../../includes/machine-learning-dummy-entry-script.md)]


## <a name="define-an-inference-configuration"></a>定义推理配置

推理配置描述了初始化 Web 服务时要使用的 Docker 容器和文件。 在部署 Web 服务时，源目录（包括子目录）中的所有文件都将经过压缩并上传到云端。

下方的推理配置指定机器学习部署将使用 `./source_dir` 目录中的 `echo_score.py` 文件来处理传入的请求，并将搭配使用 Docker 映像与 `project_environment` 环境中指定的 Python 包。

创建项目环境时，可以使用任何 [Azure 机器学习的推理特选环境](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference)作为基础 Docker 映像。 我们会在顶层安装所需的依赖项，并将生成的 Docker 映像存储在与工作区关联的存储库中。

> [!NOTE]
> Azure 机器学习[推理源目录](/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py#constructor&preserve-view=true)上传不遵循 .gitignore 或 .amlignore

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

最小推理配置可以编写为：

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/dummyinferenceconfig.json":::

使用 `dummyinferenceconfig.json` 名称保存此文件。


有关推理配置的更详细讨论，[请参阅此文](./reference-azure-machine-learning-cli.md#inference-configuration-schema)。 

# <a name="python"></a>[Python](#tab/python)

以下示例演示了如何使用上文定义的虚拟评分脚本创建不含 pip 依赖项的最小环境。

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=inference-configuration-code)]

有关环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。

有关推理配置的详细信息，请参阅 [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) 类文档。

---


## <a name="define-a-deployment-configuration"></a>定义部署配置

部署配置指定了为运行 Web 服务所需预留的内存容量和核心数量，以及底层 Web 服务的配置详情。 例如，可以使用部署配置来指定服务需要 2GB 内存、2 个 CPU 核心和 1 个 GPU 核心，并且想要启用自动缩放。

适用于部署配置的选项因所选计算目标而异。 在本地部署中，只能指定将在哪个端口上提供 Web 服务。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

有关详细信息，请参阅[此参考](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

# <a name="python"></a>[Python](#tab/python)

若要创建本地部署配置，请执行以下操作：

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deployment-configuration-code)]

---

## <a name="deploy-your-machine-learning-model"></a>部署机器学习模型

现在已准备好部署模型。 

[!INCLUDE [aml-deploy-service](../../includes/machine-learning-deploy-service.md)]


## <a name="call-into-your-model"></a>调用模型

我们来检查一下是否已成功部署回显模型。 你应该能够执行简单的运行情况探知请求以及评分请求：

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
<!-- cli nb call -->

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=call-into-model-code)]

# <a name="python"></a>[Python](#tab/python)
<!-- python nb call -->
[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-into-model-code)]

---

## <a name="define-an-entry-script"></a>定义入口脚本

现在可以实际加载模型了。 首先，修改入口脚本：


:::code language="python" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/source_dir/score.py":::

将此文件保存为 `source_dir` 内的 `score.py`。

请注意，应使用 `AZUREML_MODEL_DIR` 环境变量定位已注册的模型。 现在你已添加一些 pip 包。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/inferenceconfig.json":::

将此文件保存为 `inferenceconfig.json` 

# <a name="python"></a>[Python](#tab/python)

```python
env = Environment(name='myenv')
python_packages = ['nltk', 'numpy', 'onnxruntime']
for package in python_packages:
    env.python.conda_dependencies.add_pip_package(package)

inf_config = InferenceConfig(environment=env, source_directory='./source_dir', entry_script='./score.py')
```

有关详细信息，请参阅关于 [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice)[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 和 [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice) 的文档。

---
Deploy your service again: null
---

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

将 `bidaf_onnx:1` 替换为模型的名称及其版本号。

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=re-deploy-model-code)]

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-print-logs)]

有关详细信息，请参阅[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)和[Webservice](/python/api/azureml-core/azureml.core.webservice.webservice)文档。

---
然后，确保可以向该服务发送 post 请求：

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=send-post-request-code)]

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=send-post-request-code)]

---

## <a name="choose-a-compute-target"></a>选择计算目标

选择计算目标时，请参阅下方的关系图。

[![如何选择计算目标](./media/how-to-deploy-and-where/how-to-choose-target.png)](././media/how-to-deploy-and-where/how-to-choose-target.png#lightbox)

[!INCLUDE [aml-deploy-target](../../includes/aml-compute-target-deploy.md)]

## <a name="re-deploy-to-cloud"></a>重新部署到云端

一旦确认服务可在本地工作并选取了远程计算目标，便可准备部署到云端了。 

更改部署配置，使其与所选的计算目标相对应（本例中为 Azure 容器实例）：

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

适用于部署配置的选项因所选计算目标而异。

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/re-deploymentconfig.json":::

将此文件保存为 `re-deploymentconfig.json`。

有关详细信息，请参阅[此参考](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-on-cloud-code)]

---

再次部署服务：


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

将 `bidaf_onnx:1` 替换为模型的名称及其版本号。



[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=deploy-model-on-cloud-code)]

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-print-logs)]

有关详细信息，请参阅[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)和[Webservice](/python/api/azureml-core/azureml.core.webservice.webservice)文档。

---


## <a name="call-your-remote-webservice"></a>调用远程 Web 服务

远程部署时，可能需启用密钥身份验证。 下方示例介绍了如何使用 Python 获取服务密钥以发出推理请求。

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-web-service-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-webservice-print-logs)]



请参阅[使用 Web 服务的客户端应用程序](how-to-consume-web-service.md)主题文章，了解使用其他语言的更多示例客户端。

### <a name="understanding-service-state"></a>了解服务状态

在模型部署期间，当模型完全部署时，你可能会看到服务状态发生更改。

下表描述了各种服务状态：

| Webservice 状态 | 说明 | 最终状态？
| ----- | ----- | ----- |
| 正在转换 | 此服务正在进行部署。 | 否 |
| 不正常 | 此服务已部署，但当前无法访问。  | 否 |
| 不可安排 | 由于缺少资源，此时无法部署此服务。 | 否 |
| 已失败 | 由于出现错误或崩溃，服务未能部署。 | 是 |
| 正常 | 服务正常，终结点可用。 | 是 |

> [!TIP]
> 在部署时，会从 Azure 容器注册表 (ACR) 生成并加载用于计算目标的 Docker 映像。 在默认情况下，Azure 机器学习会创建一个使用“基本”服务层级的 ACR。 将工作区的 ACR 更改为“标准”或“高级”层级可能会减少生成映像并将其部署到计算目标所花费的时间。 有关详细信息，请参阅 [Azure 容器注册表服务层级](../container-registry/container-registry-skus.md)。

> [!NOTE]
> 如果要将模型部署到 Azure Kubernetes 服务 (AKS)，建议为该群集启用 [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md)。 这将帮助你了解总体群集运行状况和资源使用情况。 以下资源也可能有用：
>
> * [检查影响 AKS 群集的资源运行状况事件](../aks/aks-resource-health.md)
> * [Azure Kubernetes 服务诊断](../aks/concepts-diagnostics.md)
>
> 如果尝试将模型部署到运行不正常或重载的群集，应该会遇到问题。 如果需要帮助排查 AKS 群集问题，请联系 AKS 支持。

## <a name="delete-resources"></a>删除资源

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-resource-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-your-resource-code)]

若要删除已部署的 webservice，请使用 `az ml service delete <name of webservice>`。

若要从工作区中删除已注册的模型，请使用 `az ml model delete <model id>`

详细了解如何[删除 webservice](/cli/azure/ml(v1)/computetarget/create#az_ml_service_delete) 和[删除模型](/cli/azure/ml/model#az_ml_model_delete)。

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=delete-resource-code)]

若要删除已部署的 Web 服务，请使用 `service.delete()`。
若要删除已注册的模型，请使用 `model.delete()`。

有关详细信息，请参阅关于 [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) 和 [Model.delete()](/python/api/azureml-core/azureml.core.model.model#delete--) 的文档。

---

## <a name="next-steps"></a>后续步骤

* [排查部署失败问题](how-to-troubleshoot-deployment.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [在 Azure 机器学习工作室中运行自动化 ML 的一键式部署](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为模型部署创建事件警报和触发器](how-to-use-event-grid.md)
