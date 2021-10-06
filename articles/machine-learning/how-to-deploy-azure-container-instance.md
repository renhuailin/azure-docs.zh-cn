---
title: 如何将模型部署到 Azure 容器实例
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 容器实例将 Azure 机器学习模型部署为 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.custom: deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/20/2021
ms.openlocfilehash: a42c87124490d666427b408912d470d85df3b399
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429692"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>将模型部署到 Azure 容器实例

了解如何使用 Azure 机器学习将模型部署为 Azure 容器实例 (ACI) 上的 Web 服务。 如果你符合以下情况，可使用 Azure 容器实例：

- 不愿意管理自己的 Kubernetes 群集
- 可以接受只拥有服务的单个副本，这可能会影响运行时间

有关 ACI 的配额和区域可用性的信息，请参阅 [Azure 容器实例的配额和区域可用性](../container-instances/container-instances-quotas.md)文章。

> [!IMPORTANT]
> 强烈建议在部署到 Web 服务之前先进行本地调试。有关详细信息，请参阅[本地调试](./how-to-troubleshoot-deployment-local.md)
>
> 还可参阅 Azure 机器学习 - [部署到本地笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- 工作区中注册的机器学习模型。 如果尚未注册模型，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro) 或 [Azure 机器学习 Visual Studio Code 扩展](how-to-setup-vs-code.md)。

- 本文中的 Python 代码片段假设设置了以下变量：

    * `ws` - 设置为工作区。
    * `model` - 设置为注册的模型。
    * `inference_config` - 设置为模型的推理配置。

    有关如何设置这些变量的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- 本文中的 CLI 片段假设已创建 `inferenceconfig.json` 文档。 有关如何创建此文档的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="limitations"></a>限制

* 在虚拟网络中使用 Azure 容器实例时，虚拟网络必须与 Azure 机器学习工作区位于同一资源组中。
* 在虚拟网络中使用 Azure 容器实例时，你的工作区的 Azure 容器注册表 (ACR) 不能也在该虚拟网络中。

有关详细信息，请参阅[如何通过虚拟网络保护推理](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)。

## <a name="deploy-to-aci"></a>部署到 ACI

要将模型部署到 Azure 容器实例，请创建一个描述所需计算资源的部署配置。 例如，核心数和内存。 此外，还需要一个推理配置，描述托管模型和 Web 服务所需的环境。 有关如何创建推理配置的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

> [!NOTE]
> * ACI 仅适用于大小不到 1 GB 的小模型。 
> * 建议使用单节点 AKS 对较大的模型进行开发测试。
> * 要部署的模型数仅限每个部署（每个容器）1,000 个模型。 

### <a name="using-the-sdk"></a>使用 SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-azure-cli"></a>使用 Azure CLI

要使用 CLI 进行部署，请使用以下命令。 将 `mymodel:1` 替换为注册的模型的名称和版本。 将 `myservice` 替换为要赋予此服务的名称：

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

有关详细信息，请参阅 [az ml model deploy](/cli/azure/ml/model#az_ml_model_deploy) 参考文档。 

## <a name="using-vs-code"></a>使用 VS Code

请参阅[如何在 VS Code 中管理资源](how-to-manage-resources-vscode.md)。

> [!IMPORTANT]
> 无需事先创建 ACI 容器即可进行测试。 将据需要创建 ACI 容器。

> [!IMPORTANT]
> 我们会将经哈希处理的工作区 ID 追加到所有已创建的基础 ACI 资源。同一工作区中的所有 ACI 名称将具有相同的后缀。 Azure 机器学习服务名称仍然是客户提供的同一“service_name”，所有面向 Azure 机器学习 SDK API 的用户都不需要任何更改。 我们不会对所创建的基础资源的名称提供任何保证。

## <a name="next-steps"></a>后续步骤

* [如何使用自定义 Docker 映像部署模型](./how-to-deploy-custom-container.md)
* [部署疑难解答](how-to-troubleshoot-deployment.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)