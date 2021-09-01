---
title: 使用托管联机终结点（预览版）部署机器学习模型
titleSuffix: Azure Machine Learning
description: 了解如何将机器学习模型部署为由 Azure 自动管理的 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 0a4117e17ddddbb1fcb083a927fc35abae8b2eb6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751961"
---
# <a name="deploy-and-score-a-machine-learning-model-by-using-a-managed-online-endpoint-preview"></a>使用托管联机终结点（预览版）部署机器学习模型并为其评分

了解如何使用托管联机终结点（预览版）部署模型，因此无需创建和管理底层基础结构。 首先在本地计算机上部署一个模型以调试任何错误，然后在 Azure 中部署并测试该模型。 

你还将了解如何查看日志和监视服务级别协议 (SLA)。 你将从模型着手，最后会使用一个可缩放的 HTTPS/REST 终结点执行联机和实时评分。 

有关详细信息，请参阅[什么是 Azure 机器学习终结点（预览版）？](concept-endpoints.md)。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

* 若要使用 Azure 机器学习，必须有一个 Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

* 安装并配置 Azure CLI 和 Azure CLI 的 `ml` 扩展。 有关详细信息，请参阅[安装、设置和使用 2.0 CLI（预览版）](how-to-configure-cli.md)。 

* 必须有一个 Azure 资源组，且你（或者你使用的服务主体）必须对此拥有参与者访问权限。 在[安装、设置和使用 2.0 CLI（预览版）](how-to-configure-cli.md)中创建资源组。 

* 必须有一个 Azure 机器学习工作区。 在[安装、设置和使用 2.0 CLI（预览版）](how-to-configure-cli.md)中创建工作区。

* 如果尚未为 Azure CLI 指定默认设置，则应保存默认设置。 若要避免多次传入订阅、工作区和资源组的值，请运行此代码：

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* （可选）若要在本地部署，必须在本地计算机上[安装 Docker 引擎](https://docs.docker.com/engine/install/)。 强烈建议选择此选项，以便更轻松地调试问题。

## <a name="prepare-your-system"></a>准备你的系统

若要按照本文的说明进行操作，请先克隆示例存储库 (azureml-examples)。 然后，运行以下代码，以转至示例目录：

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

若要设置终结点名称，请根据你的操作系统，选择以下命令之一（将 `YOUR_ENDPOINT_NAME` 替换为唯一名称）。

若是 Unix，运行此命令：

```azurecli
export ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

若是 Windows，运行此命令：

```azurecli
set ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

> [!NOTE]
> Azure 区域租户中的终结点名称必须唯一。 例如，在 Azure westus2 区域中，只能有一个名为 `my-endpoint` 的终结点。 

## <a name="define-the-endpoint-configuration"></a>定义终结点配置

在联机终结点上部署模型时所需的特定输入：

- 模型文件（或者已在工作区中注册的模型的名称和版本）。 本示例中有一个执行回归的 scikit-learn 模型。
- 为模型评分所需的代码。 在这种情况下，我们会用到 score.py 文件。
- 运行模型的环境。 正如你所见，环境可能是具有 Conda 依赖项的 Docker 映像，也可能是 Dockerfile。
- 用于指定实例类型和缩放容量的设置。

以下代码片段显示了 endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml 文件，该文件可捕获所有必需的输入： 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml":::

> [!NOTE]
> 有关 YAML 的完整说明，请参阅[托管联机终结点（预览版）的 YAML 参考资料](reference-yaml-endpoint-managed-online.md)。

下表描述了 YAML 格式的终结点的参考资料。 若要了解如何指定这些属性，请参阅[准备系统](#prepare-your-system)或[联机终结点 YAML 参考](reference-yaml-endpoint-managed-online.md)中的 YAML 示例。 有关托管终结点相关限制的信息，请参阅[管理和提高 Azure 机器学习资源的配额](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)。

| 键 | 说明 |
| --- | --- |
| `$schema`    | （可选）YAML 架构。 如要查看 YAML 文件的所有可用选项，可在浏览器中查看上述示例中的架构。|
| `name`       | 终结点的名称。 在 Azure 区域中必须具有唯一性。|
| `traffic` | 从终结点转移到每个部署的流量百分比。 流量值之和必须是 100。 |
| `auth_mode` | 使用 `key`（用于执行基于密钥的身份验证），使用 `aml_token`（用于执行基于 Azure 机器学习令牌的身份验证）。 `key` 不会过期，但 `aml_token` 会过期。 （使用 `az ml endpoint get-credentials` 命令获取最新的令牌。） |
| `deployments` | 要在终结点中创建的部署的列表。 在本例中，只有一个名为 `blue` 的部署。 有关多个部署的详细信息，请参阅[联机终结点的安全推出（预览版）](how-to-safely-rollout-managed-endpoints.md)。|

下表描述了 `deployments` 的属性：

| 键 | 说明 |
| --- | --- |
| `name`  | 部署的名称。 |
| `model` | 在此示例中，我们指定了内联模型属性：`name`、`version` 和 `local_path`。 将自动上传和注册模型文件。 内联规范的缺点是，如果要更新模型文件，则必须手动递增版本。 有关相关的最佳实践，请参阅下一节中的提示。 |
| `code_configuration.code.local_path` | 包含用于为模型评分的所有 Python 源代码的目录。 可以使用嵌套目录和包。 |
| `code_configuration.scoring_script` | `code_configuration.code.local_path` 评分目录中的 Python 文件。 此 Python 代码必须具有 `init()` 函数和 `run()` 函数。 在创建或更新模型后将调用 `init()` 函数（例如，可以使用该函数在内存中缓存模型）。 每次调用终结点时，都将调用 `run()` 函数来执行实际评分和预测。 |
| `environment` | 包含用于承载模型和代码的环境的详细信息。 在此示例中，我们具有包含 `name`、`version` 和 `path` 的内联定义。 我们将为映像使用 `environment.docker.image`。 将在映像之上安装 `conda_file` 依赖项。 有关详细信息，请参阅下一节中的提示。 |
| `instance_type` | 用于承载部署实例的 VM SKU。 有关详细信息，请参阅[托管联机终结点支持的 VM SKU](reference-managed-online-endpoints-vm-sku-list.md)。 |
| `scale_settings.scale_type` | 目前，此值必须是 `manual`。 若要在创建终结点和部署后进行纵向扩展或缩减，请更新 YAML 中的 `instance_count` 并运行命令 `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>`。|
| `scale_settings.instance_count` | 部署中的实例数。 请根据预期的工作负载确定值。 为实现高可用性，建议至少将 `scale_settings.instance_count` 设置为 `3`。 |

有关 YAML 架构的详细信息，请参阅[联机终结点 YAML 参考](reference-yaml-endpoint-managed-online.md)。

> [!NOTE]
> 若要使用 Azure Kubernetes 服务 (AKS) 而不是托管终结点作为计算目标，请运行以下命令：
> 1. 使用 [Azure 机器学习工作室](how-to-create-attach-compute-studio.md#whats-a-compute-target)创建 AKS 群集并将其作为计算目标附加到 Azure 机器学习工作区。
> 1. 使用[终结点 YAML](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) 而不是托管终结点 YAML，将 AKS 指定为目标。 需要编辑 YAML，以将 `target` 的值更改为已注册的计算目标的名称。
>
> 本文中使用的所有命令（除了可选 SLA 监视和 Azure Log Analytics 集成）可以与托管终结点或 AKS 终结点一起使用。

### <a name="register-your-model-and-environment-separately"></a>单独注册模型和环境

在此示例中，我们将指定模型和内联环境属性：`name`、`version` 和 `local_path`（从何处上传文件）。 CLI 会自动上传文件并注册模型和环境。 适用于生产的最佳做法是注册模型和环境，并在 YAML 中指定已注册的名称和版本。 使用格式 `model: azureml:my-model:1` 或 `environment: azureml:my-env:1`。

若要执行注册，可以将 `model` 和 `environment` 的 YAML 定义提取到单独的 YAML 文件中，并使用命令 `az ml model create` 和 `az ml environment create`。 若要详细了解这些命令，请运行 `az ml model create -h` 和 `az ml environment create -h`。

### <a name="use-different-cpu-and-gpu-instance-types"></a>使用不同的 CPU 和 GPU 实例类型

上述 YAML 使用常规用途类型 (`Standard_F2s_v2`) 和非 GPU Docker 映像（请参阅 YAML 中的 `image` 特性）。 对于 GPU 计算，应选择 GPU 计算类型 SKU 和 GPU Docker 映像。

可以在[托管联机终结点支持的 VM SKU](reference-managed-online-endpoints-vm-sku-list.md) 中查看支持的常规用途类型和 GPU 实例类型。 有关 Azure 机器学习 CPU 和 GPU 基础映像的列表，请参阅 [Azure 机器学习基础映像](https://github.com/Azure/AzureML-Containers)。

### <a name="use-more-than-one-model"></a>使用多个模型

目前，在 YAML 中只能为每个部署指定一个模型。 如果你有多个模型，注册模型时，将所有模型（作为文件或子目录）复制到用于注册的文件夹中。 在评分脚本中，使用环境变量 `AZUREML_MODEL_DIR` 获取模型根文件夹的路径。 保留基础目录结构。

## <a name="understand-the-scoring-script"></a>了解评分脚本

> [!TIP]
> 托管联机终结点的评分脚本格式与早期版本的 CLI 和 Python SDK 中使用的格式相同。

如之前所述，`code_configuration.scoring_script` 必须具有一个 `init()` 函数和一个 `run()` 函数。 此示例使用 [score.py 文件](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py)。 初始化或启动容器时，将调用函数 `init()`。 初始化通常在创建或更新部署后立即发生。 在此处编写逻辑以执行全局初始化操作，例如在内存中高速缓存模型（如本示例中的执行）。 每次调用终结点时，都将调用 `run()` 函数，该函数应执行实际的评分和预测。 在该示例中，我们将从 JSON 输入提取数据，调用模型的 `predict()` 方法，并返回结果。

## <a name="deploy-and-debug-locally-by-using-local-endpoints"></a>使用本地终结点在本地部署和调试

为了节省调试时间，我们强烈建议在本地对终结点进行测试运行。

> [!NOTE]
> * 若要在本地部署，必须安装 [Docker 引擎](https://docs.docker.com/engine/install/)。
> * Docker 引擎必须运行。 Docker 引擎通常在计算机启动时启动。 如果它不启动，可以[对 Docker 引擎进行故障排除](https://docs.docker.com/config/daemon/#start-the-daemon-manually)。

> [!IMPORTANT]
> 在本地部署终结点的目的是在部署到 Azure 之前验证和调试代码与配置。 本地部署具有以下限制：
> - 本地终结点不支持流量规则、身份验证、缩放设置或探测设置。 
> - 本地终结点仅支持每个终结点一个部署。 也就是说，在本地部署中，不能使用对 Azure 机器学习工作区中已注册的模型或环境的引用。 

### <a name="deploy-the-model-locally"></a>在本地部署模型

在本地部署模型：

```azurecli
az ml endpoint create --local -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

> [!NOTE]
> 如果使用 Windows 操作系统，请在此处和后续命令中使用 `%ENDPOINT_NAME%` 而不是 `$ENDPOINT_NAME`

`--local` 标志指示 CLI 在 Docker 环境中部署终结点。

### <a name="verify-the-local-deployment-succeeded"></a>验证本地部署是否成功

检查日志，查看模型是否已部署且未出错：

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

### <a name="invoke-the-local-endpoint-to-score-data-by-using-your-model"></a>调用本地终结点以使用模型为数据评分

使用便捷命令 `invoke` 并传递 JSON 文件中存储的查询参数，调用终结点来为模型评分：

```azurecli
az ml endpoint invoke --local -n $ENDPOINT_NAME --request-file endpoints/online/model-1/sample-request.json
```

如果要使用 REST 客户端（例如 curl），必须具有评分 URI。 如要获取评分 URI，需要运行 `az ml endpoint show --local -n $ENDPOINT_NAME`。 在返回的数据中，找到 `scoring_uri` 属性。 

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>在日志中查看 invoke 操作的输出

在示例 score sp 文件中，`run()` 方法将一些输出记录到控制台。 可以再次使用 `get-logs` 命令查看此输出：

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>将托管联机终结点部署到 Azure

接下来，将托管联机终结点部署到 Azure。

### <a name="deploy-to-azure"></a>部署到 Azure

若要将 YAML 配置部署到云，请运行以下代码：

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="deploy" :::

此项部署最多可能需要约 15 分钟，具体取决于是否为首次构建基础环境或映像。 使用同一环境的后续部署将更快完成处理。

> [!TIP]
> * 如果你不希望阻塞 CLI 控制台，可以将 `--no-wait` 标志添加到命令中。 但是，这会停止以交互方式显示部署状态。
>
> * 使用[排查托管联机终结点部署问题（预览版）](how-to-troubleshoot-managed-online-endpoints.md)调试错误。

### <a name="check-the-status-of-the-deployment"></a>检查部署的状态

对于终结点和部署，`show` 命令包含 `provisioning_status` 中的信息：

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

可以使用 `list` 命令以表格格式列出工作区中的所有终结点：

```azurecli
az ml endpoint list --output table
```

### <a name="check-the-status-of-the-cloud-deployment"></a>检查云部署的状态

检查日志，查看模型是否已部署且未出错：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

默认情况下，日志是从推理服务器拉取的。 若要查看存储初始值（将模型和代码等资产装载到容器）中的日志，请添加 `--container storage-initializer` 标志。

### <a name="invoke-the-endpoint-to-score-data-by-using-your-model"></a>调用终结点，以使用模型为数据评分

可以使用 `invoke` 命令或你选择的 REST 客户端来调用终结点并为一些数据评分： 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

若要查看调用日志，请再次运行 `get-logs`。

若要使用 REST 客户端，必须具有 `scoring_uri` 的值以及身份验证密钥或令牌。 `show` 命令的输出中提供了 `scoring_uri` 值：
 
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_scoring_uri" :::

我们使用 `--query` 标志来筛选特性，以便仅显示所需的特性。 可以在[查询 Azure CLI 命令输出](/cli/azure/query-azure-cli)中详细了解 `--query`。

使用 `get-credentials` 命令检索所需的凭据：

```azurecli
az ml endpoint get-credentials -n $ENDPOINT_NAME
```

### <a name="optional-update-the-deployment"></a>（可选）更新部署

如果你要更新代码、模型、环境或缩放设置，请更新 YAML 文件，然后运行 `az ml endpoint update` 命令。 

> [!IMPORTANT]
> 在单个 `update` 命令中只能修改一个方面（流量、缩放设置、代码、模型或环境）。 

若要了解 `update` 的工作原理，请执行以下操作：

1. 打开 online/model-1/onlinescoring/score.py 文件。
1. 更改 `init()` 函数的最后一行：在 `logging.info("Init complete")` 后面，添加 `logging.info("Updated successfully")`。 
1. 保存文件。
1. 运行以下命令：

    ```azurecli
    az ml endpoint update -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
    ```

    > [!IMPORTANT]
    > 使用 YAML 进行更新是声明性的。 也就是说，YAML 中的更改反映在基础资源 Azure 资源管理器资源（终结点和部署）中。 声明性方法有助于 [GitOps](https://www.atlassian.com/git/tutorials/gitops)：对终结点和部署的所有更改都将通过 YAML 进行（甚至包括 `instance_count`）。 结果是，如果从 YAML 中删除部署，然后使用该文件运行 `az ml endpoint update`，则会删除此部署。 无需使用 YAML 即可使用 `--set` 标志进行更新。
    
1. 由于修改了 `init()` 函数（`init()` 在创建或更新终结点时运行），因此日志中会包含消息 `Updated successfully`。 运行以下命令检索日志：

    ```azurecli
    az ml endpoint get-logs -n $ENDPOINT_NAME --deployment blue
    ```

如果由于无法解决的问题而需要删除并重新创建部署（这种情况很少见），请运行：

```azurecli
az ml endpoint delete -n $ENDPOINT_NAME --deployment blue
```

`update` 命令也适用于本地终结点。 将同一个 `az ml endpoint update` 命令与标志 `--local` 配合使用。

> [!TIP]
> 使用 `az ml endpoint update` 命令时，可以使用 [`--set`Azure CLI 中的参数](/cli/azure/use-cli-effectively#generic-update-arguments)来替代 YAML 中的特性，或者将该参数用于设置特定的特性，而无需传递 YAML 文件。 在开发和测试方案中，为单个特性使用 `--set`，这种做法特别有用。 例如，若要纵向扩展第一个部署的 `instance_count` 值，可以使用标志 `--set deployments[0].scale_settings.instance_count=2`。 但是，由于 YAML 未更新，此方法对 [GitOps](https://www.atlassian.com/git/tutorials/gitops) 没有帮助。

### <a name="optional-monitor-sla-by-using-azure-monitor"></a>（可选）使用 Azure Monitor 监视 SLA

若要查看指标并基于 SLA 设置警报，请完成[监视托管联机终结点](how-to-monitor-online-endpoints.md)中所述的步骤。

### <a name="optional-integrate-with-log-analytics"></a>（可选）与 Log Analytics 集成

`get-logs` 命令只提供自动选择的实例最近发出的几百行日志。 但是，Log Analytics 提供一种用于持久存储和分析日志的方式。 

首先，请按照[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace)中的步骤创建一个 Log Analytics 工作区。

然后在 Azure 门户中执行以下操作：

1. 转至资源组。
1. 选择终结点。
1. 选择 ARM 资源页。
1. 选择“诊断设置”。
1. 选择添加设置。
1. 选择允许将控制台日志发送至 Log Analytics 工作区。

日志最多可能需要一小时才能进行连接。 一个小时后，发送一些评分请求，然后使用以下步骤检查日志：

1. 打开 Log Analytics 工作区。 
1. 在左侧菜单中，选择日志。
1. 关闭自动打开的查询对话。
1. 双击 AmlOnlineEndpointConsoleLog。
1. 选择“运行”。

## <a name="delete-the-endpoint-and-the-deployment"></a>删除终结点和部署

如果你将来不再使用该部署，应运行以下代码将其删除（这会删除终结点和所有基础部署）：

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请查看下列文章：

- [通过 REST 部署模型（预览版）](how-to-deploy-with-rest.md)
- [在工作室中创建和使用托管联机终结点（预览版）](how-to-use-managed-online-endpoint-studio.md)
- [联机终结点的安全推出（预览版）](how-to-safely-rollout-managed-endpoints.md)
- [使用批处理终结点（预览版）进行批量评分](how-to-use-batch-endpoint.md)
- [查看 Azure 机器学习托管联机终结点（预览版）的成本](how-to-view-online-endpoints-costs.md)
- [教程：使用托管联机终结点和系统托管标识访问 Azure 资源（预览版）](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)
- [排查托管联机终结点部署问题](how-to-troubleshoot-managed-online-endpoints.md)
