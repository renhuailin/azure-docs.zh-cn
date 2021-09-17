---
title: 使用批处理终结点进行批量评分
titleSuffix: Azure Machine Learning
description: 本文介绍如何创建批处理终结点，以便连续为大型数据批量评分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 8/11/2021
ms.custom: how-to, devplatv2
ms.openlocfilehash: b68ba3f0221aa97307e746d192de65b2915d2e4b
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419550"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>使用批处理终结点（预览版）进行批量评分

本文介绍如何使用批处理终结点（预览版）执行批量评分。 批处理终结点简化了承载用于批量评分的模型的过程，使你可以将工作重心放在机器学习而不是基础结构上。 创建批处理终结点后，可以使用 Azure CLI 或者在任何平台中使用 HTTP 库和 REST API 触发批量评分作业。 有关详细信息，请参阅[什么是 Azure 机器学习终结点（预览版）？](concept-endpoints.md)。

本文介绍如何执行以下任务：

> [!div class="checklist"]
> * 为 MLflow 模型创建一个附带无代码体验的批处理终结点
> * 检查批处理终结点详细信息
> * 使用 Azure CLI 启动批量评分作业
> * 监视批量评分作业执行进度并检查评分结果
> * 将新部署添加到批处理终结点
> * 使用 REST 启动批量评分作业

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

* 一个 Azure 订阅。如果你没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

* Azure 命令行接口 (CLI) 和 ML 扩展。

机器学习扩展需要 Azure CLI 版本 `>=2.15.0`。 请确保满足此要求：

```azurecli
az version
```

如果需要，请升级 Azure CLI：

```azurecli
az upgrade
```

> [!NOTE]
>
> `az upgrade` 命令是在 2.11.0 版本中添加的，不能在 2.11.0 之前的版本中使用。 可以按照[安装 Azure CLI](/cli/azure/update-azure-cli) 中重新安装以更新旧版本。
>
> 默认情况下，此命令还将更新所有已安装的扩展。 有关更多 `az upgrade` 选项，请参阅[命令参考页面](/cli/azure/reference-index#az_upgrade)。

添加并配置 Azure ML 扩展：

```azurecli
az extension add -n ml
```

有关配置 ML 扩展的详细信息，请参阅[安装、设置和使用 CLI (v2)（预览版）](how-to-configure-cli.md)。

* 示例存储库

克隆 [AzureML 示例存储库](https://github.com/Azure/azureml-examples)。 本文将使用 `/cli/endpoints/batch` 中的资产。

## <a name="create-a-compute-target"></a>创建计算目标

批量评分仅在云计算资源上运行，而不在本地运行。 云计算资源称为“计算目标”。 计算目标是可在其中运行批量评分工作流的可重用虚拟计算机。

运行以下代码以创建一个常规用途 [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py&preserve-view=true) 目标。 若要详细了解计算目标，请参阅[什么是 Azure 机器学习中的计算目标？](./concept-compute-target.md)。

```azurecli
az ml compute create --name cpu-cluster --type amlcompute --min-instances 0 --max-instances 5
```

## <a name="create-a-batch-endpoint"></a>创建批处理终结点

如果使用的是 MLflow 模型，则可以使用无代码批处理终结点创建方式。 也就是说，你无需准备评分脚本和环境，系统会自动生成这两者。 有关详细信息，请参阅[使用 MLflow 和 Azure 机器学习训练和跟踪 ML 模型（预览版）](how-to-use-mlflow.md)。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_batch_endpoint" :::

下面是定义 MLFlow 批处理终结点的 YAML 文件：

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/create-batch-endpoint.yml":::

| 密钥 | 说明 |
| --- | ----------- |
| $schema | [可选] YAML 架构。 可以在浏览器中查看上述示例中的架构，以查看批处理终结点 YAML 文件的所有可用选项。 |
| name | 批处理终结点的名称，在整个区域中必须唯一。 `name` 值将用作评分 URI 的一部分。 该值必须以英文字符开头，后接数字、字符和 `-` 符号的混合形式，并以数字或字符结尾。 它的长度必须至少为 3 个字符。 用于验证的正则表达式为：`^[a-zA-Z][-a-zA-Z0-9]+[a-zA-Z0-9]$`|
| 类型 | 终结点的类型。 使用 `batch` 表示批处理终结点。 |
| auth_mode | 使用 `aad_token` 表示基于 Azure 令牌的身份验证。 |
| traffic | 路由到此部署的流量百分比。 对于批处理终结点，`traffic` 的有效值仅为 `0` 或 `100`。 流量值为 `100` 的部署处于活动状态。 调用后，所有数据将发送到活动部署。 |
| deployments | 要在批处理终结点中创建的部署列表。 该示例只有一个名为 `autolog-deployment` 的部署。 |

部署特性：

| 密钥 | 说明 |
| --- | ----------- |
| name | 部署的名称。 |
| 模型 | 用于批量评分的模型。 使用 `name`、`version` 和 `local_path` 从本地计算机上传模型。 使用 `azureml:` 前缀引用工作区中的现有模型资源。 例如，`azureml: autolog:1` 将指向名为 `autolog` 的模型的版本 1。 |
| compute.target | 计算目标。 使用 `azureml:` 前缀引用工作区中的现有计算资源。 例如，`azureml:cpu-cluster` 将指向名为 `cpu-cluster` 的计算目标。 |
| compute.instance_count | 用于批量评分的计算节点数。 默认值为 `1`。|
| mini_batch_size | [可选] `scoring_script` 可以在一次 `run()` 调用中处理的文件数。 默认值为 `10`。 |
| output_file_name | [可选] 批量评分输出文件的名称。 默认值为 `predictions.csv`。 |
| retry_settings.max_retries | [可选] 失败的 `scoring_script` `run()` 的最大尝试次数。 默认值为 `3`。 |
| retry_settings.timeout | [可选] `scoring_script` `run()` 的超时，以秒为单位。 默认值为 `30`。 |
| error_threshold | [可选] 应忽略的文件失败次数。 如果整个输入的错误计数超出此值，则作业将终止。 错误阈值适用于整个输入，而不适用于发送给 `run()` 方法的单个微型批处理。 默认值为 `-1`，指定允许失败任意次，而不会终止运行。 | 
| logging_level | [可选] 日志详细程度。 值（以详细程度递增的顺序排列）为：WARNING、INFO 和 DEBUG。 默认值为 INFO。 |

## <a name="check-batch-endpoint-details"></a>检查批处理终结点详细信息

创建批处理终结点后，可以使用 `show` 查看详细信息。 使用 [`--query parameter`](/cli/azure/query-azure-cli) 只会获取返回数据中的特定特性。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

## <a name="start-a-batch-scoring-job-using-the-azure-cli"></a>使用 Azure CLI 启动批量评分作业

批量评分工作负载以脱机作业的形式运行。 批量评分旨在处理大型数据。 输入将在计算群集上并行处理。 将为节点上的进程分配数据分区。 包含多个进程的单个节点将有多个分区并行运行。 默认情况下，批量评分将评分输出存储在 Blob 存储中。 可以通过传入数据输入内容来使用 Azure CLI 启动批量评分作业。 还可以配置输出位置并覆盖某些设置以获得最佳性能。

### <a name="start-a-batch-scoring-job-with-different-input-options"></a>使用不同的输入选项启动批量评分作业

可以使用三个选项指定数据输入。

选项 1：已注册的数据

使用 `--input-data` 传入 AML 注册的数据。

> [!NOTE]
> 在预览期，仅支持 FileDataset。 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-data azureml:<dataName>:<dataVersion>
```

选项 2：云中的数据

使用 `--input-datastore` 指定 AML 注册的数据存储，并使用 `--input-path` 指定数据存储中的相对路径。

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-datastore azureml:<datastoreName> --input-path <relativePath>
```

如果数据公开可用，请使用 `--input-path` 指定公共路径。

如果使用提供的示例，则可以运行以下命令启动批量评分作业。

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv
```

选项 3：本地存储的数据

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-local-path <localPath>
```

### <a name="configure-the-output-location"></a>配置输出位置

默认情况下，批量评分结果存储在工作区的默认 Blob 存储中按作业名称（系统生成的 GUID）命名的某个文件夹内。 可以在启动批量评分作业时配置评分输出的存储位置。 使用 `--output-datastore` 配置任何已注册的数据存储，使用 `--output-path` 配置相对路径。 使用 `--set output_file_name` 配置新的输出文件名。

> [!IMPORTANT]
> 必须使用唯一的输出位置。 如果输出文件已存在，批量评分作业将失败。 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --output-datastore azureml:workspaceblobstore --output-path mypath --set output_file_name=mypredictions.csv
```

### <a name="overwrite-settings"></a>覆盖设置

可以在启动批量评分作业时覆盖某些设置，以充分利用计算资源并提高性能： 

* 如果使用不同大小的输入数据，可使用 `--mini-batch-size` 覆盖 `mini_batch_size`。 
* 如果此作业需要不同的计算资源，可使用 `--instance-count` 覆盖 `instance_count`。 
* 使用 `--set` 可覆盖其他设置，包括 `max_retries`、`timeout` 和 `error_threshold`。

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --set retry_settings.max_retries=1
```
:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_with_new_settings" :::

## <a name="check-batch-scoring-job-execution-progress"></a>检查批量评分作业执行进度

批量评分作业通常会花费一段时间来处理整个输入集。 可以在 Azure 机器学习工作室中监视作业进度。 `invoke` 的响应中以 `interactionEndpoints.Studio.endpoint` 值的形式提供了工作室链接。

还可以使用 Azure CLI 检查作业详细信息及状态。

从 invoke 响应中获取作业名称。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job" :::

使用 `job show` 检查批量评分作业的详细信息和状态。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_job_status" :::

使用 `job stream` 流式传输作业日志。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="stream_job_logs_to_console" :::


## <a name="check-batch-scoring-results"></a>检查批量评分结果

若要查看评分结果，请执行以下操作：

1. 转到工作室
1. 导航到“试验”
1. 导航到终结点的运行（`endpoint invoke` 响应中的 `interactionEndpoints.Studio.endpoint` 值）
1. 在运行图中，单击 `batchscoring` 步骤
1. 选择“输出 + 日志”选项卡，然后选择“显示数据输出” 
1. 选择“查看输出”图标:::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="显示如何查看数据输出位置的工作室屏幕截图" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::
1. 在弹出的面板上复制路径，然后选择“打开数据存储”链接。
1. 在出现的“Blob 存储”页上，将上面的路径粘贴到搜索框中。 在文件夹中可以看到评分输出。
:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.gif" alt-text="显示如何打开评分文件夹和评分输出的屏幕截图" lightbox="media/how-to-use-batch-endpoint/scoring-view.gif":::

## <a name="add-a-deployment-to-the-batch-endpoint"></a>将部署添加到批处理终结点

一个批处理终结点可以包含多个部署。 每个部署承载一个要批量评分的模型。 

### <a name="add-a-new-deployment"></a>添加新部署

使用以下命令将新部署添加到现有的批处理终结点。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" range="65" :::

此示例使用非 MLflow 模型。 使用非 MLflow 时，需要在 YAML 文件中指定环境和评分脚本：

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/add-deployment.yml" :::

非 MLflow 模型的其他部署特性：

| 密钥 | 说明 |
| --- | ----------- |
| code_configuration.code.local_path | 包含用于为模型评分的所有源代码的目录。 |
| code_configuration.scoring_script | 上述目录中的 Python 文件。 此文件必须具有 `init()` 函数和 `run()` 函数。 对于任何成本较高的或者一般性的准备工作，请使用 `init()` 函数。 例如，使用它将模型加载到全局对象。 此函数将在进程开始时调用一次。 使用 `run(mini_batch)` 为每个项评分；`mini_batch` 值是文件路径列表。 `run()` 方法应返回 pandas `DataFrame` 或数组。 这些返回的元素将追加到通用输出文件。 每个返回的输出元素指示输入微型批中的输入元素的一次成功运行。 请确保运行结果中包含足够的数据，以便将输入映射到特定的运行输出结果。 运行输出将写入输出文件中，并且不保证按顺序写入，因此你应使用输出中的某个键将其映射到正确的输入。 |
| 环境 | 计算目标上用于为模型评分的环境。 可以通过指定名称、版本和路径来以内联方式定义环境。 使用 `conda_file` 包含要安装在 `docker.image` 之上的依赖项。 使用 `azureml:` 前缀引用现有环境。 例如，`azureml: mnist-env:1` 将指向名为 `mnist-env` 的环境的版本 1。 |

若要查看部署详细信息，请运行：

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

### <a name="activate-the-new-deployment"></a>激活新部署

对于批量推理，必须将 100% 的查询发送到所需的部署。 若要将新建的部署设置为目标，请使用：

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="switch_traffic" :::

如果再次检查部署详细信息，你将看到更改：

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

现在，可以使用此新部署调用批量评分作业：

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_with_new_settings" :::

## <a name="start-a-batch-scoring-job-using-rest"></a>使用 REST 启动批量评分作业

批处理终结点具有用于访问 REST 的评分 URI。 REST 可让你在任何平台上使用任何 HTTP 库来启动批量评分作业。

1. 获取 `scoring_uri`：  

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="get_scoring_uri" :::

2. 获取访问令牌：

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="get_token" :::


3. 使用 `scoring_uri`、访问令牌和 JSON 数据发布 (POST) 请求并启动批量评分作业：

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_rest":::


## <a name="clean-up-resources"></a>清理资源

如果不打算使用已创建的资源，请删除它们，以免产生任何费用：

1. 在 Azure 门户的左侧菜单中选择“资源组”。
1. 在资源组列表中，选择创建的资源组。
1. 选择“删除资源组”。
1. 输入资源组名称。 然后选择“删除”。

还可保留资源组，但请删除单个工作区。 显示工作区属性，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何创建和调用批处理终结点，以便为大量数据评分。 有关 Azure 机器学习的详细信息，请参阅下述其他文章：

* [排查批处理终结点问题](how-to-troubleshoot-batch-endpoints.md)
* [使用托管联机终结点（预览版）部署机器学习模型并为其评分](how-to-deploy-managed-online-endpoints.md)
