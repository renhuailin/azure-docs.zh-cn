---
title: " 更新 Web 服务"
titleSuffix: Azure Machine Learning
description: 了解如何刷新已在 Azure 机器学习中部署的 Web 服务。 可以更新设置，如模型、环境和入口脚本。
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: e95bc02cce2cc3ff736de44e9f7fb3d367c33e5c
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296342"
---
# <a name="update-a-deployed-web-service"></a>更新已部署的 Web 服务

本文介绍如何更新通过 Azure 机器学习部署的 Web 服务。

## <a name="prerequisites"></a>必备条件

本教程假定你已使用 Azure 机器学习部署了 Web 服务。 如果需要了解如何部署 Web 服务，请[执行这些步骤](how-to-deploy-and-where.md)。

## <a name="update-web-service"></a>更新 Web 服务

若要更新 Web 服务，请使用 `update` 方法。 你可以更新 Web 服务，以使用可以在推理配置中指定的新模型、新入口脚本或新依赖项。 有关详细信息，请参阅 [Webservice.update](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-) 的文档。

请参阅 [AKS 服务更新方法。](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

请参阅 [ACI 服务更新方法。](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> 创建模型的新版本时，必须手动更新要使用的每个服务。
>
> 不能使用 SDK 来更新从 Azure 机器学习设计器发布的 Web 服务。

**使用 SDK**

下面的代码演示如何使用 SDK 更新 Web 服务的模型、环境和入口脚本：

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**使用 CLI**

还可以使用 ML CLI 更新 Web 服务。 以下示例演示如何注册新模型，然后更新 Web 服务以使用新模型：

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 此示例使用 JSON 文档将模型信息从注册命令传递到更新命令。
>
> 若要更新服务以使用新的入口脚本或环境，请创建[推理配置文件](./reference-azure-machine-learning-cli.md#inference-configuration-schema)并使用 `ic` 参数指定它。

有关详细信息，请参阅 [az ml 服务更新](/cli/azure/ml(v1)/computetarget/create#az_ml_service_update)文档。

## <a name="next-steps"></a>后续步骤

* [排查部署失败问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)
* [如何使用自定义 Docker 映像部署模型](./how-to-deploy-custom-container.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [为模型部署创建事件警报和触发器](how-to-use-event-grid.md)