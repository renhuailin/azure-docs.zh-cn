---
title: 使用托管联机终结点部署 ML 模型
titleSuffix: Azure Machine Learning
description: 了解如何将机器学习模型部署为由 Azure 自动管理的 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 05/13/2021
ms.topic: how-to
ms.custom: how-to
ms.openlocfilehash: 85b587dcaed162a0372f03240f9c4cb33d7507c1
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969055"
---
# <a name="deploy-and-score-a-machine-learning-model-with-a-managed-online-endpoint-preview"></a>使用托管联机终结点（预览版）部署机器学习模型并为其评分

托管联机终结点（预览版）使你无需创建和管理底层基础结构即可部署模型。 在本文中，你将首先在本地计算机上部署一个模型以调试任何错误，然后在 Azure 中部署并测试该模型。 你还将了解如何查看日志和监视服务级别协议 (SLA)。 你将从模型着手，最后会使用一个可缩放的 HTTPS/REST 终结点执行联机/实时评分。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

* 若要使用 Azure 机器学习，必须有一个 Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 必须安装并配置 Azure CLI 和 ML 扩展。 有关详细信息，请参阅[安装、设置和使用 2.0 CLI（预览版）](how-to-configure-cli.md)。 

* 必须有一个 Azure 资源组，你（或者你使用的服务主体）在此资源组中需要拥有 `Contributor` 访问权限。 如果已按照上述文章配置了 ML 扩展，那么你就有了此类资源组。 

* 必须有一个 Azure 机器学习工作区。 如果已按照上述文章配置了 ML 扩展，那么你就有了此类工作区。

* 如果尚未为 Azure CLI 指定默认设置，则应保存默认设置。 为了避免必须反复传入值，请运行：

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>

* [Optional] To deploy locally, you must have [Docker engine](https://docs.docker.com/engine/install/) running locally. This step is **highly recommended**. It will help you debug issues.

## Prepare your system

To follow along with the article, clone the samples repository, and navigate to the right directory by running the following commands:

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

设置终结点名称（请将下面的 `YOUR_ENDPOINT_NAME` 重命名为唯一名称）。 以下命令适用于 Unix 环境：

```azurecli
export ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

如果使用 Windows 操作系统，请改用此命令：`set ENDPOINT_NAME=YOUR_ENDPOINT_NAME`。

> [!NOTE]
> 终结点名称在 Azure 区域级别需是唯一的。 例如，`westus2` 中只能有一个名为 `my-endpoint` 的终结点。 

## <a name="define-the-endpoint-configuration"></a>定义终结点配置

在联机终结点上部署模型时需要的输入包括：

- 模型文件（或者已在工作区中注册的模型的名称和版本）。 本示例中有一个执行回归的 `scikit-learn` 模型。
- 为模型评分所需的代码。 本例中有一个 `score.py` 文件。
- 运行模型的环境（如你所见，该环境可以是包含 conda 依赖项的 Docker 映像，也可以是 Dockerfile）。
- 用于指定实例类型和缩放容量的设置。

以下代码片段显示了用于捕获上述所有信息的 `endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml` 文件： 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml":::

> [!Note]
> 可以[参考](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)一个为托管联机终结点完全指定的示例 YAML

下面是有关终结点 YAML 格式的参考信息。 若要了解如何指定这些特性，请参阅本文中的 YAML 示例，或前面说明中提到的完全指定的 YAML 示例。 有关托管终结点相关限制的详细信息，请参阅[管理和提高 Azure 机器学习资源的配额](how-to-manage-quotas.md)。

| 密钥 | 说明 |
| --- | --- |
| $schema    | [可选] YAML 架构。 可以在浏览器中查看上述示例中的架构，以查看 YAML 文件的所有可用选项。|
| name       | 终结点的名称。 在 Azure 区域级别需是唯一的。|
| traffic | 从终结点转移到每个部署的流量百分比。 流量值的总和需是 100 |
| auth_mode | 使用 `key` 可执行基于密钥的身份验证，使用 `aml_token` 可执行基于 Azure 机器学习令牌的身份验证。 `key` 不会过期，但 `aml_token` 会过期。 使用 `az ml endpoint get-credentials` 命令获取最新的令牌。 |
| deployments | 包含要在终结点中创建的部署的列表。 在本例中，只有一个名为 `blue` 的部署。 有关多个部署的详细信息，请参阅[联机终结点的安全推出（预览版）](how-to-safely-rollout-managed-endpoints.md)|

`deployments` 的特性：

| 密钥 | 说明 |
| --- | --- |
| name  | 部署的名称 |
| 模型 | 在此示例中，我们指定了内联模型属性：`name`、`version` 和 `local_path`。 模型文件将自动上传和注册。 内联规范的缺点是，如果要更新模型文件，则必须手动递增版本。 如需相关的最佳做法，请参阅以下部分中的“提示”。 |
| code_configuration.code.local_path | 包含用于为模型评分的所有 Python 源代码的目录。 支持嵌套的目录/包。 |
| code_configuration.scoring_script | 上述评分目录中的 Python 文件。 此 Python 代码必须具有 `init()` 函数和 `run()` 函数。 在创建或更新模型后将调用 `init()` 函数（可以使用该函数在内存中缓存模型，等等）。 每次调用终结点时，都将调用 `run()` 函数来执行实际评分/预测。 |
| 环境 | 包含用于承载模型和代码的环境的详细信息。 在此示例中，我们具有包含 `name`、`version` 和 `path` 的内联定义。 在此示例中，`environment.docker.image` 将用作映像，`conda_file` 依赖项将在此映像之上安装。 有关详细信息，请参阅以下部分中的“提示”。 |
| instance_type | 用于承载部署实例的 VM SKU。 有关详细信息，请参阅[托管联机终结点支持的 VM SKU](reference-managed-online-endpoints-vm-sku-list.md)。 |
| scale_settings.scale_type | 目前，此值必须是 `manual`。 若要在创建终结点和部署后进行纵向扩展或缩减，请更新 YAML 中的 `instance_count` 并运行命令 `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>`。|
| scale_settings.instance_count | 部署中的实例数。 请根据预期的工作负载确定值。 为实现高可用性，Microsoft 建议将此值至少设置为 `3`。 |

有关 YAML 架构的详细信息，请参阅[联机终结点 YAML 参考](reference-online-endpoint-yaml.md)文档。

> [!Note]
> 若要使用Azure Kubernetes 服务 (AKS) 而不是托管终结点作为计算目标，请运行以下命令：
> 1. [使用 Azure 机器学习工作室](how-to-create-attach-compute-studio.md#whats-a-compute-target)创建 AKS 群集并将其作为计算目标附加到 Azure 机器学习工作区
> 2. 使用此[终结点 YAML](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) 而不是上述托管终结点 YAML，将 AKS 指定为目标。 需要编辑 YAML，以将 `target` 的值更改为已注册的计算目标的名称。
> 本文中的命令（可选的 SLA 监视和 Log Analytics 集成命令除外）可以在托管终结点和 AKS 终结点之间换用。

### <a name="registering-your-model-and-environment-separately"></a>单独注册模型和环境

 在此示例中，我们将指定要从中上传文件的内联模型和环境属性：`name`、`version` 和 `local_path`。 在幕后，CLI 会上传文件，并自动注册模型和环境。 适用于生产环境的最佳做法是单独注册模型和环境，并在 YAML 中指定已注册的名称和版本。 格式为 `model: azureml:my-model:1` 或 `environment: azureml:my-env:1`。

 若要执行注册，可以将 `model` 和 `environment` 的 YAML 定义提取到单独的 YAML 文件中，并使用命令 `az ml model create` 和 `az ml environment create`。 若要详细了解这些命令，请运行 `az ml model create -h` 和 `az ml environment create -h`。

### <a name="using-different-cpu--gpu-instance-types"></a>使用不同的 CPU 和 GPU 实例类型

上述 YAML 使用常规用途类型 (`Standard_F2s_v2`) 和非 GPU Docker 映像（请参阅 YAML 中的 `image` 特性）。 对于 GPU 计算，应选择 GPU 计算类型 SKU 和 GPU Docker 映像。

可以在[托管联机终结点支持的 VM SKU](reference-managed-online-endpoints-vm-sku-list.md) 中查看支持的常规用途类型和 GPU 实例类型。 在 [Azure 机器学习基础映像](https://github.com/Azure/AzureML-Containers)中可以找到 Azure ML CPU 和 GPU 基础映像列表。

### <a name="using-more-than-one-model"></a>使用多个模型

目前，在 YAML 中只能为每个部署指定一个模型。 如果你有多个模型，可通过以下方式避开此限制：注册模型时，将所有模型（作为文件或子目录）复制到用于注册的文件夹中。 在评分脚本中，可以使用环境变量 `AZUREML_MODEL_DIR` 获取模型根文件夹的路径；基础目录结构会被保留。

## <a name="understand-the-scoring-script"></a>了解评分脚本

> [!Tip]
> 托管联机终结点的评分脚本格式与早期版本的 CLI 和 Python SDK 中使用的格式相同。

如前面的 YAML 中所述，`code_configuration.scoring_script` 必须有一个 `init()` 函数和一个 `run()` 函数。 此示例使用该 [score.py 文件](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py)。 初始化/启动容器时，将调用函数 `init()`。 此初始化通常在创建或更新部署后立即发生。 在此处编写逻辑以执行全局初始化操作，例如在内存中缓存模型（如本示例中所执行的那样）。 每次调用终结点时，都将调用 `run()` 函数，该函数应执行实际的评分/预测。 在该示例中，我们将从 JSON 输入提取数据，调用 `scikit-learn` 模型的 `predict()` 方法，并返回结果。

## <a name="deploy-and-debug-locally-using-local-endpoints"></a>使用本地终结点在本地部署和调试

为了节省调试时间，强烈建议在本地对终结点进行测试运行。

> [!Note]
> * 若要在本地部署，必须已安装 [Docker 引擎](https://docs.docker.com/engine/install/)
> * Docker 引擎必须正在运行。 一般情况下，启动系统时会启动引擎。 如果它不启动，可以[参阅此文进行故障排除](https://docs.docker.com/config/daemon/#start-the-daemon-manually)。

> [!Important]
> 在本地部署终结点的目的是在部署到 Azure 之前验证和调试代码与配置。 本地部署具有以下限制：
> - 本地终结点不支持流量规则、身份验证、缩放设置或探测设置。 
> - 本地终结点仅支持每个终结点一个部署。 也就是说，在本地部署中，不能使用对 Azure 机器学习工作区中已注册的模型或环境的引用。 

### <a name="deploy-the-model-locally"></a>在本地部署模型

若要在本地部署模型，请运行以下命令：

```azurecli
az ml endpoint create --local -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

`--local` 标志指示 CLI 在 Docker 环境中部署终结点。

>[!NOTE]
>如果使用 Windows 操作系统，请在此处和后续命令中使用 `%ENDPOINT_NAME%` 而不是 `$ENDPOINT_NAME`

### <a name="check-if-the-local-deployment-succeeded"></a>检查本地部署是否成功

在日志中检查模型是否成功部署：

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

### <a name="invoke-the-local-endpoint-to-score-data-with-your-model"></a>调用本地终结点为模型数据评分

使用便捷命令 `invoke` 并传递 JSON 文件中存储的查询参数，调用终结点来为模型评分：

```azurecli
az ml endpoint invoke --local -n $ENDPOINT_NAME --request-file endpoints/online/model-1/sample-request.json
```

如果你要使用 REST 客户端（例如 curl），则需要评分 URI。 可以使用命令 `az ml endpoint show --local -n $ENDPOINT_NAME` 获取该 URI。 在返回的数据中，可以看到名为 `scoring_uri` 的特性。 

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>在日志中查看 invoke 操作的输出

在示例 `score.py` 中，`run()` 方法将一些输出记录到控制台。 可以再次使用 `get-logs` 命令查看此输出：

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>将托管联机终结点部署到 Azure 

### <a name="deploy-to-azure"></a>部署到 Azure

若要将 YAML 配置部署到云，请运行以下命令：

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="deploy" :::

此项部署最多可能需要约 15 分钟，具体取决于是否是首次生成基础环境/映像。 以后使用同一环境进行部署将更快完成。

> [!Tip]
> 如果你不希望阻塞 CLI 控制台，可以将 `--no-wait` 标志添加到命令中。 但是，这会停止以交互方式显示部署状态。

> [!Tip]
> 使用[排查托管联机终结点部署问题](how-to-troubleshoot-managed-online-endpoints.md)调试错误。

### <a name="check-the-status-of-the-deployment"></a>检查部署的状态

对于终结点和部署，`show` 命令均包含 `provisioning_status`：

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

可以使用 `list` 命令以表格格式列出工作区中的所有终结点：

```azurecli
az ml endpoint list --output table
```

### <a name="check-if-the-cloud-deployment-succeeded"></a>检查云部署是否成功

在日志中检查模型是否成功部署：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

默认情况下，日志是从推理服务器拉取的。 如果你要查看存储初始化表达式（用于将模型和代码等资产装载到容器）的日志，请添加标志 `--container storage-initializer`。

### <a name="invoke-the-endpoint-to-score-data-with-your-model"></a>调用终结点为模型数据评分

可以使用 `invoke` 命令或你选择的 REST 客户端来调用终结点并为一些数据评分： 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

可以再次使用前面所示的 `get-logs` 命令来查看调用日志。

若要使用 REST 客户端，需要 `scoring_uri` 和身份验证密钥/令牌。 `show` 命令的输出中提供了 `scoring_uri`：
 
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_scoring_uri" :::

请注意我们如何使用 `--query` 来筛选特性，以便仅显示所需的特性。 可以在[查询 Azure CLI 命令输出](/cli/azure/query-azure-cli)中详细了解 `--query`。

使用 `get-credentials` 命令检索所需的凭据：

```azurecli
az ml endpoint get-credentials -n $ENDPOINT_NAME
```

### <a name="optional-update-the-deployment"></a>[可选] 更新部署

如果你要更新代码、模型、环境或缩放设置，请更新 YAML 文件并运行 `az ml endpoint update` 命令。 

>[!IMPORTANT]
> 在单个 `update` 命令中只能修改一个方面（流量、缩放设置、代码、模型或环境）。 

若要了解 `update` 的工作原理，请执行以下操作：

1. 打开 `online/model-1/onlinescoring/score.py`文件。
1. 更改 `init()` 函数的最后一行：在 `logging.info("Init complete")` 后面，添加 `logging.info("Updated successfully")`。 
1. 保存文件
1. 运行以下命令：
```azurecli
az ml endpoint update -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

> [!IMPORTANT]
> 使用 YAML 进行更新是声明性操作。 也就是说，YAML 中的更改将反映在基础资源 Azure 资源管理器资源（终结点和部署）中。 此方法有助于 [GitOps](https://www.atlassian.com/git/tutorials/gitops)：对终结点/部署的所有更改都将通过 YAML 进行（甚至包括 `instance_count`）。 副作用是，如果从 YAML 中删除部署，然后使用该文件运行 `az ml endpoint update`，则会删除此部署。 可以使用 `--set ` 标志进行更新，而无需使用 YAML，如下面的“提示”中所述。

5. 由于修改了在创建或更新终结点时运行的 `init()` 函数，因此日志中会包含消息 `Updated successfully`。 运行以下命令检索日志：
```azurecli
az ml endpoint get-logs -n $ENDPOINT_NAME --deployment blue
```

如果由于无法解决的问题而需要删除并重新创建部署（这种情况很少见），请使用：

```azurecli
az ml endpoint delete -n $ENDPOINT_NAME --deployment blue
```

`update` 命令也适用于本地终结点。 将同一个 `az ml endpoint update` 命令与标志 `--local` 配合使用。

> [!Tip]
> 使用 `az ml endpoint update` 命令时，可以使用 [Azure CLI 中的 `--set` 参数](/cli/azure/use-cli-effectively#generic-update-arguments)来替代 YAML 中的特性，或者将该参数用于设置特定的特性，而无需传递 YAML 文件。 在开发/测试方案中，为单个特性使用 `--set` 特别有用。 例如，若要纵向扩展第一个部署的 `instance_count`，可以使用标志 `--set deployments[0].scale_settings.instance_count=2`。 但是，由于 YAML 未更新，此方法对 [GitOps](https://www.atlassian.com/git/tutorials/gitops) 没有帮助。

### <a name="optional-monitor-sla-using-azure-monitor"></a>[可选] 使用 Azure Monitor 监视 SLA

可以按照[监视托管联机终结点](how-to-monitor-online-endpoints.md)中的说明查看指标并基于 SLA 设置警报。

### <a name="optional-integrate-with-log-analytics"></a>[可选] 与 Log Analytics 集成

`get-logs` 命令只提供自动选择的实例最近发出的几百行日志。 但是，Log Analytics 提供一种用于持久存储和分析日志的方式。 首先，请按照[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace)中的步骤创建一个 Log Analytics 工作区。

然后在 Azure 门户中执行以下操作：

1. 转到资源组
1. 选择你的终结点
1. 选择 ARM 资源页
1. 选择“诊断设置”
1. 选择“添加设置”：允许将控制台日志发送到 log analytics 工作区

请注意，最长可能需要一小时才能连接日志。 在这段时间过后，发送一些评分请求，然后使用以下步骤检查日志：

1. 打开 Log Analytics 工作区 
1. 在左侧导航区域中选择“日志”
1. 关闭自动打开的“查询”弹出窗口
1. 双击“AmlOnlineEndpointConsoleLog”
1. 选择“运行”

## <a name="delete-the-endpoint-and-deployment"></a>删除终结点和部署

如果你将来不再使用该部署，应使用以下命令将其删除（这会删除终结点和所有基础部署）：

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>后续步骤

- [联机终结点的安全推出（预览版）](how-to-safely-rollout-managed-endpoints.md)
- [排查托管联机终结点部署问题](how-to-troubleshoot-managed-online-endpoints.md)