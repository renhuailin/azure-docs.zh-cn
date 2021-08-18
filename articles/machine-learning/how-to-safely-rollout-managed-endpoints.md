---
title: 联机终结点的安全推出
titleSuffix: Azure Machine Learning
description: 在不造成中断的情况下推出较新版本的 ML 模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 11fa5a3057f50a354058141ed688d8c55b4345d8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739138"
---
# <a name="safe-rollout-for-online-endpoints-preview"></a>联机终结点的安全推出（预览版）

在生产环境中已部署了一个现有模型，你现在想要部署该模型的新版本。 如何在不造成任何中断的情况下推出新的 ML 模型？ 合理的回答之一是蓝绿部署，在此方法中，会先将更改推出到一小部分用户/请求者，然后再全面推出，从而将新版本的 Web 服务引入到生产环境。 本文假设你使用的是联机终结点；有关详细信息，请参阅[什么是 Azure 机器学习终结点（预览版）？](concept-endpoints.md)。

本文内容：

> [!div class="checklist"]
> * 部署一个名为“blue”（蓝）的新联机终结点，该终结点为模型版本 1 提供服务
> * 扩展此部署，使其能够处理更多请求
> * 将模型版本 2 部署到名为“green”（绿）的终结点，该终结点不接受实时流量
> * 在隔离环境中测试 green 部署 
> * 将 10% 的实时流量发送到 green 部署
> * 将所有实时流量完全切换到 green 部署
> * 删除现已不再使用的 v1 blue 部署

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

* 若要使用 Azure 机器学习，必须有一个 Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

* 必须安装并配置 Azure CLI 和 ML 扩展。 有关详细信息，请参阅[安装、设置和使用 2.0 CLI（预览版）](how-to-configure-cli.md)。 

* 必须有一个 Azure 资源组，你（或者你使用的服务主体）在此资源组中需要拥有 `Contributor` 访问权限。 如果已按照上述文章配置了 ML 扩展，那么你就有了此类资源组。 

* 必须有一个 Azure 机器学习工作区。 如果已按照上述文章配置了 ML 扩展，那么你就有了此类工作区。

* 如果尚未为 Azure CLI 指定默认设置，则应保存默认设置。 为了避免必须反复传入值，请运行：

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

* 一个现有的托管终结点。 本文假设你的部署与[使用托管联机终结点（预览版）部署机器学习模型并为其评分](how-to-deploy-managed-online-endpoints.md)中所述相符。

* 如果尚未设置环境变量 $ENDPOINT_NAME，现在请设置：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="set_endpoint_name":::

* （建议）克隆示例存储库，并切换到该存储库的 `cli/` 目录： 

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples/cli
```

本教程中的命令位于文件 `deploy-declarative-safe-rollout-online-endpoints.sh` 中，YAML 配置文件位于 `endpoints/online/managed/canary-declarative-flow/` 子目录中。

## <a name="confirm-your-existing-deployment-is-created"></a>确认已创建现有部署

可以运行以下命令来查看现有部署的状态： 

```azurecli
az ml endpoint show --name $ENDPOINT_NAME 
```

应会看到由 `$ENDPOINT_NAME` 标识的终结点，以及名为 `blue` 的部署。 

## <a name="scale-your-existing-deployment-to-handle-more-traffic"></a>扩展现有部署以处理更多流量

在按[使用托管联机终结点（预览版）部署机器学习模型并为其评分](how-to-deploy-managed-online-endpoints.md)所述进行部署时，你已将 `instance_count` 设置为值 `1`。 为了处理更多流量，第二个版本的 YAML 文件 (`2-scale-blue.yml`) 将值更改为 `2`：

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/2-scale-blue.yml" range="29":::

使用以下命令更新部署：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="scale_blue" :::

> [!IMPORTANT]
> 使用 YAML 进行更新是声明性操作。 也就是说，YAML 中的更改将反映在基础资源 Azure 资源管理器资源（终结点和部署）中。 此方法有助于 [GitOps](https://www.atlassian.com/git/tutorials/gitops)：对终结点/部署的所有更改都将通过 YAML 进行（甚至包括 `instance_count`）。 副作用是，如果从 YAML 中删除部署，然后使用该文件运行 `az ml endpoint update`，则会删除此部署。 

## <a name="deploy-a-new-model-but-send-it-no-traffic-yet"></a>部署新模型，但暂时不要向其发送任何流量

若要部署新模型，请在配置文件的 `deployments` 节中添加一个新节，但在 `traffic` 节中指定应该接收 0% 的流量。 文件 `3-create-green.yml` 包含此更改：

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/3-create-green.yml" range="7,35-56":::

更新部署： 

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="create_green" :::

### <a name="test-the-new-deployment"></a>测试新部署

配置指定了将 0% 的流量发送到刚刚创建的 `green` 部署。 若要测试该部署，可以通过指定 `--deployment` 名称来直接调用它：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="test_green" :::

如果要使用 REST 客户端直接调用部署而不经历流量规则处理过程，请设置以下 HTTP 头：`azureml-model-deployment: <deployment-name>`。

## <a name="test-the-new-deployment-with-a-small-percentage-of-live-traffic"></a>使用较小百分比的实时流量测试新部署

测试 `green` 部署后，`4-flight-green.yml` 文件将演示如何通过修改配置文件中的 `traffic` 配置来提供某个百分比的流量：

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/4-flight-green.yml" range="5-7":::

除了突出显示的行以外，配置文件中的其他内容保持不变。 使用以下命令更新部署：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_10pct_traffic" :::

现在，`green` 部署将接收 10% 的请求。 

## <a name="send-all-traffic-to-your-new-deployment"></a>将所有流量发送到新部署

如果你对 `green` 部署完全满意，请将所有流量切换到该部署。 以下代码片段只显示了配置文件中的相关代码，其他代码保持不变：

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/5-full-green.yml" range="5-7":::

更新部署： 

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_100pct_traffic" :::

## <a name="remove-the-old-deployment"></a>删除旧部署

通过删除旧的 `blue` 部署来完成切换到新模型。 最终的配置文件如下所示：

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/6-delete-blue.yml" :::

使用以下命令更新部署：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_blue" :::

## <a name="delete-the-endpoint-and-deployment"></a>删除终结点和部署

如果你今后不再使用该部署，应使用以下命令将其删除：

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_endpoint" :::


## <a name="next-steps"></a>后续步骤
- [通过 REST 部署模型（预览版）](how-to-deploy-with-rest.md)
- [在工作室中创建和使用托管联机终结点（预览版）](how-to-use-managed-online-endpoint-studio.md)
- [教程：使用托管联机终结点和系统托管标识访问 Azure 资源（预览版）](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)
- [监视托管联机终结点（预览版）](how-to-monitor-online-endpoints.md)
- [管理和增大 Azure 机器学习资源的配额](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
- [查看 Azure 机器学习托管联机终结点（预览版）的成本](how-to-view-online-endpoints-costs.md)
- [托管联机终结点 SKU 列表（预览版）](reference-managed-online-endpoints-vm-sku-list.md)
- [排查托管联机终结点的部署和评分问题（预览版）](how-to-troubleshoot-managed-online-endpoints.md)
- [托管联机终结点（预览版）YAML 参考](reference-yaml-endpoint-managed-online.md)