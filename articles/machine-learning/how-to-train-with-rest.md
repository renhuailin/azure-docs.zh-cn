---
title: 使用 REST 训练模型（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何使用 REST API 训练模型并创建作业。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: wenxwei
ms.author: wenxwei
ms.date: 05/25/2021
ms.reviewer: peterlu
ms.custom: devplatv2
ms.openlocfilehash: 89fb2c08aa76b88110bb5832f297d08d5891dda4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448304"
---
# <a name="train-models-with-rest-preview"></a>使用 REST 训练模型（预览版）

了解如何使用 Azure 机器学习 REST API 创建和管理训练作业（预览版）。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

REST API 使用标准 HTTP 谓词创建、检索、更新和删除资源。 REST API 适用于可发出 HTTP 请求的任何语言或工具。 REST 具有简单的结构，因此它是适合脚本编写环境和 MLOps 自动化的良好选择。

本文介绍如何使用新的 REST API 执行以下操作：

> [!div class="checklist"]
> * 创建机器学习资产
> * 创建基本训练作业 
> * 创建超参数优化扫描作业

## <a name="prerequisites"></a>先决条件

- 你对其拥有管理权限的 **Azure 订阅**。 如果没有此类订阅，请尝试注册[免费或付费的个人订阅](https://azure.microsoft.com/free/)。
- 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)。
- 工作区中的服务主体。 管理 REST 请求使用[服务主体身份验证](how-to-setup-authentication.md#use-service-principal-authentication)。
- 一个服务主体身份验证令牌。 请按照[检索服务主体身份验证令牌](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)中的步骤检索此令牌。 
- **curl** 实用工具。 在 [适用于 Linux 的 Windows 子系统](/windows/wsl/install-win10)或任何 UNIX 分发版中均已提供了 **curl** 程序。 在 PowerShell 中，**curl** 是 **Invoke-WebRequest** 的别名，并且 `curl -d "key=val" -X POST uri` 变成了 `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`。 

## <a name="azure-machine-learning-jobs"></a>Azure 机器学习作业
作业是用于指定计算作业的所有方面的资源。 它聚合了三个要素：

- 要运行什么？
- 如何运行？
- 在哪个位置运行？

提交 Azure 机器学习作业的方法有很多，包括使用 SDK、使用 Azure CLI 以及在工作室中以可视方式提交。 以下示例使用 REST API 提交 LightGBM 训练作业。

## <a name="create-machine-learning-assets"></a>创建机器学习资产

首先设置 Azure 机器学习资产以配置作业。

在以下 REST API 调用中，我们使用了 `$SUBSCRIPTION_ID`、`$RESOURCE_GROUP`、`$LOCATION` 和 `$WORKSPACE` 作为占位符。 将占位符替换为自己的值。 

管理 REST 请求一个[服务主体身份验证令牌](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)。 请将 `$TOKEN` 替换为你自己的值。 可使用以下命令检索此令牌：

```bash
TOKEN=$(az account get-access-token --query accessToken -o tsv)
```

服务提供商使用 `api-version` 参数来确保兼容性。 `api-version` 参数因服务而异。 当前的 Azure 机器学习 API 版本为 `2021-03-01-preview`。 将 API 版本设置为变量以适应将来的版本：

```bash
API_VERSION="2021-03-01-preview"
```

### <a name="compute"></a>计算

运行机器学习作业需要计算资源。 可以列出工作区的计算资源：

```bash
curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes?api-version=$API_VERSION" \
--header "Authorization: Bearer $TOKEN"
```

对于此示例，我们使用了名为 `cpu-cluster` 的现有计算群集。 将计算名称设置为变量以便于封装：

```bash
COMPUTE_NAME="cpu-cluster"
```

> [!TIP]
> 可以[使用 PUT 请求创建或覆盖命名计算资源](./how-to-manage-rest.md#create-and-modify-resources-using-put-and-post-requests)。 

### <a name="environment"></a>环境 

LightGBM 示例需要在 LightGBM 环境中运行。 使用 PUT 请求创建环境。 使用 Microsoft Container Registry 中的 Docker 映像。

可以使用 `Docker` 配置 Docker 映像，并使用 `condaFile` 添加 Conda 依赖项： 

:::code language="rest" source="~/azureml-examples-main/cli/train-rest.sh" id="create_environment":::

### <a name="datastore"></a>数据存储

训练作业需要针对数据运行，因此你需要指定数据存储。 此示例将获取工作区的默认数据存储和 Azure 存储帐户。 使用 GET 请求查询工作区，以返回包含信息的 JSON 文件。

可以使用工具 [jq](https://stedolan.github.io/jq/) 分析 JSON 结果并获取所需的值。 也可以使用Azure 门户找到相同的信息。

```bash
response=$(curl --location --request GET "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/datastores?api-version=$API_VERSION&isDefault=true" \
--header "Authorization: Bearer $TOKEN")

AZURE_STORAGE_ACCOUNT=$(echo $response | jq '.value[0].properties.contents.accountName')
AZUREML_DEFAULT_DATASTORE=$(echo $response | jq '.value[0].name')
AZUREML_DEFAULT_CONTAINER=$(echo $response | jq '.value[0].properties.contents.containerName')
AZURE_STORAGE_KEY=$(az storage account keys list --account-name $AZURE_STORAGE_ACCOUNT | jq '.[0].value')
```

### <a name="data"></a>数据

获取数据存储后，接下来可以创建数据集。 对于此示例，请使用通用数据集 `iris.csv`，并在 `path` 中指向该数据集。 

:::code language="rest" source="~/azureml-examples-main/cli/train-rest.sh" id="create_data":::

### <a name="code"></a>代码

获取数据集和数据存储后，接下来可以上传要在作业中运行的训练脚本。 使用 Azure 存储 CLI 将 Blob 上传到默认容器中。 也可以使用其他方法来上传，例如 Azure 门户或 Azure 存储资源管理器。


```bash
az storage blob upload-batch -d $AZUREML_DEFAULT_CONTAINER/src \
 -s jobs/train/lightgbm/iris/src --account-name $AZURE_STORAGE_ACCOUNT --account-key $AZURE_STORAGE_KEY
```

上传代码后，可以使用 PUT 请求指定代码，并使用 `datastoreId` 引用数据存储。 

:::code language="rest" source="~/azureml-examples-main/cli/train-rest.sh" id="create_code":::

## <a name="submit-a-training-job"></a>提交训练作业

资产就位后，可以运行 LightGBM 作业，它会输出已训练的模型和元数据。 需要使用以下信息来配置训练作业： 

- run_id：[可选] 作业的名称，必须在所有作业之间保持唯一。 除非在 YAML 文件中通过 `name` 字段或者在命令行中通过 `--name/-n` 指定了名称，否则系统会自动生成 GUID/UUID 并将其用作名称。
- jobType：作业类型。 对于基本训练作业，请使用 `Command`。
- codeId：训练脚本的路径。
- command：要执行的命令。 输入数据可写入到该命令中，并可以通过数据绑定引用这些数据。 
- environmentId：环境的路径。
- inputDataBindings：数据绑定可帮助你引用输入数据。 创建环境变量，绑定名称将添加到可在 `command` 中引用的 AZURE_ML_INPUT_。 若要创建数据绑定，需要添加作为 `dataId` 创建的数据的路径。 
- experimentName：[可选] 标记作业以帮助在 Azure 机器学习工作室中组织作业。 每个作业的运行记录将在工作室的“试验”选项卡中的相应试验下进行组织。如果省略此项，则标记将默认为创建作业时的工作目录的名称。
- compute：`target` 指定计算目标，它可以是计算的路径。 `instanceCount` 指定作业所需的实例数。

使用以下命令提交训练作业：

:::code language="rest" source="~/azureml-examples-main/cli/train-rest.sh" id="create_job":::

## <a name="submit-a-hyperparameter-sweep-job"></a>提交超参数扫描作业

Azure 机器学习还可让你有效优化训练超参数。 可以使用 REST API 创建超参数优化套件。 有关 Azure 机器学习的超参数优化选项的详细信息，请参阅[对模型进行超参数优化](how-to-tune-hyperparameters.md)。 指定超参数优化参数以配置扫描：

- jobType：作业类型。 对于扫描作业，此参数为 `Sweep`。 
- algorithm：采样算法 -“random”通常是很好的起始值。 有关选项的列表，请参阅扫描作业[架构](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)。 
- trial：要运行的每个试验的命令作业配置。 
- objective：`primaryMetric` 是优化指标，必须与训练代码中记录的指标的名称相匹配。 `goal` 指定方向（minimize 或 maximize）。 请参阅[架构](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)以查看完整的选项列表。 
- searchSpace：要扫描的超参数的字典。 键是超参数的名称，例如 `learning_rate`。 值是超参数分布。 请参阅[架构](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)以查看选项的列表。
- maxTotalTrials：要运行的各个试验的最大数目。
- maxConcurrentTrials：[可选] 在计算群集上并发运行的试验的最大数目。
- timeout：[可选] 运行扫描作业的最大分钟数。

若要通过相同的 LightGBM 示例创建扫描作业，请使用以下命令： 

:::code language="rest" source="~/azureml-examples-main/cli/train-rest.sh" id="create_a_sweep_job":::

## <a name="next-steps"></a>后续步骤

有了一个已训练的模型后，接下来请了解[如何部署模型](how-to-deploy-and-where.md)。
