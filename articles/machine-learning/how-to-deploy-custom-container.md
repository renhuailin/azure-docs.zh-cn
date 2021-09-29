---
title: 将自定义容器部署为托管联机终结点
titleSuffix: Azure Machine Learning
description: 了解如何通过自定义容器在 Azure 机器学习中使用开源服务器
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 06/16/2021
ms.topic: how-to
ms.custom: deploy, devplatv2
ms.openlocfilehash: b5438ccfcd84a9574e7e65eb9d0342f947f4d03b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599461"
---
# <a name="deploy-a-tensorflow-model-served-with-tf-serving-using-a-custom-container-in-a-managed-online-endpoint-preview"></a>在托管联机终结点中使用自定义容器部署通过 TF 服务提供服务的 TensorFlow 模型（预览版）

了解如何在 Azure 机器学习中将自定义容器部署为托管联机终结点。

自定义容器部署可以使用其他 Web 服务器，而不使用 Azure 机器学习所用的默认 Python Flask 服务器。 这些部署的用户仍可利用 Azure 机器学习的内置监视、缩放、警报和身份验证。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!WARNING]
> Microsoft 可能无法帮助解决由自定义映像引起的问题。 如果遇到问题，请使用默认映像或 Microsoft 提供的映像之一，了解该问题是否特定于你的映像。

## <a name="prerequisites"></a>先决条件

* 安装并配置 Azure CLI 和 ML 扩展。 有关详细信息，请参阅[安装、设置和使用 CLI (v2)（预览版）](how-to-configure-cli.md)。 

* 必须有一个 Azure 资源组，你（或者你使用的服务主体）在此资源组中需要拥有 `Contributor` 访问权限。 如果已按照上述文章配置了 ML 扩展，那么你就有了此类资源组。 

* 必须有一个 Azure 机器学习工作区。 如果已按照上述文章配置了 ML 扩展，那么你就有了此类工作区。

* 如果尚未为 Azure CLI 指定默认设置，则应保存默认设置。 为了避免必须反复传入值，请运行：

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>

* To deploy locally, you must have [Docker engine](https://docs.docker.com/engine/install/) running locally. This step is **highly recommended**. It will help you debug issues.

## Download source code

To follow along with this tutorial, download the source code below.

```azurecli-interactive
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

## <a name="initialize-environment-variables"></a>初始化环境变量

定义环境变量：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="initialize_variables":::

## <a name="download-a-tensorflow-model"></a>下载 TensorFlow 模型

下载并解压缩一个将输入除以 2 并将结果加 2 的模型：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="download_and_unzip_model":::

## <a name="run-a-tf-serving-image-locally-to-test-that-it-works"></a>在本地运行 TF 服务映像以测试其工作状态

使用 Docker 在本地运行该映像以进行测试：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="run_image_locally_for_testing":::

### <a name="check-that-you-can-send-liveness-and-scoring-requests-to-the-image"></a>检查是否可以向该映像发送活动性和评分请求

首先，检查容器是否处于“活动”状态，即，该容器中的进程是否仍在运行。 应会收到“200 (正常)”响应。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="check_liveness_locally":::

然后，检查是否可以获取有关未标记的数据的预测：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="check_scoring_locally":::

### <a name="stop-the-image"></a>停止映像

完成本地测试后，停止映像：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="stop_image":::

## <a name="create-a-yaml-file-for-your-endpoint"></a>为终结点创建 YAML 文件

可以使用 YAML 配置云部署。 查看此终结点的示例 YAML：

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/custom-container/tfserving-endpoint.yml":::

在此 YAML 中，需要注意几个重要概念：

### <a name="readiness-route-vs-liveness-route"></a>就绪性路由与活动性路由

HTTP 服务器可以选择性地为活动性和就绪性定义路径 。 活动性路由用于检查服务器是否正在运行。 就绪性路由用于检查服务器是否已准备好执行某项工作。 在机器学习推理中，在加载模型之前，服务器可对活动性请求做出“200 正常”响应。 只有在模型已载入内存后，服务器才能对就绪性请求做出“200 正常”响应。

有关活动性探测和就绪性探测的详细信息，请查看 [Kubernetes 文档](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。

请注意，此部署对活动性和就绪性使用相同的路径，因为 TF 服务仅定义活动性路由。

### <a name="locating-the-mounted-model"></a>定位已装载的模型

将某个模型部署为实时终结点时，Azure 机器学习会将该模型装载到终结点。 通过装载模型，无需创建新的 Docker 映像即可部署该模型的新版本。 默认情况下，使用名称 foo 和版本 1 注册的模型将定位到已部署的容器内部的以下路径：`/var/azureml-app/azureml-models/foo/1` 

例如，如果本地计算机上具有以下目录结构：

```
azureml-examples
  cli
    endpoints
      online
        custom-container
          half_plus_two
          tfserving-endpoint.yml    
```     

并且 `tfserving-endpoint.yml` 包含：

```
model:
    name: tfserving-mounted
    version: 1
    local_path: ./half_plus_two
```

则模型将定位到终结点中的以下位置：

```
var 
  azureml-app
    azureml-models
      tfserving-endpoint
        1
          half_plus_two
```

### <a name="create-the-endpoint"></a>创建终结点

了解 YAML 的构造方式后，接下来请创建终结点。 此命令可能需要几分钟才能完成。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="create_endpoint":::

### <a name="invoke-the-endpoint"></a>调用终结点

部署完成后，看看能否向部署的终结点发出评分请求。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="invoke_endpoint":::

### <a name="delete-endpoint-and-model"></a>删除终结点和模型

使用终结点成功评分后，可以删除终结点：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="delete_endpoint_and_model":::

## <a name="next-steps"></a>后续步骤

- [联机终结点的安全推出（预览版）](how-to-safely-rollout-managed-endpoints.md)
- [排查托管联机终结点部署问题](how-to-troubleshoot-managed-online-endpoints.md)
- [Torch Serve 示例](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-torchserve.sh)