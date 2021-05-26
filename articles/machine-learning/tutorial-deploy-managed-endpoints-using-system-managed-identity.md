---
title: 使用托管联机终结点访问 Azure 资源
titleSuffix: Azure Machine Learning
description: 使用托管联机终结点和系统分配的托管标识安全访问用于机器学习模型部署的 Azure 资源。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 05/25/2021
ms.topic: tutorial
ms.custom: tutorial
ms.openlocfilehash: 6e8f176df9a87f10af89b71d35b1d5577a223424
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382176"
---
# <a name="tutorial-access-azure-resources-with-a-managed-online-endpoint-and-system-managed-identity-preview"></a>教程：使用托管联机终结点和系统托管标识访问 Azure 资源（预览版）

本教程介绍如何使用托管联机终结点和系统分配的托管标识从评分脚本安全访问 Azure 资源。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]
本教程将演示如何使用 Azure CLI 及其 ML 扩展执行以下操作：

> [!div class="checklist"]
> * 设置供 Azure CLI 使用的默认值
> * 配置要与托管联机终结点配合使用的变量
> * 创建 Blob 存储帐户和 Blob 容器 
> * 创建托管联机终结点
> * 为系统分配的托管标识授予对存储的访问权限
> * 创建与托管终结点关联的部署


## <a name="prerequisites"></a>先决条件

* 若要使用 Azure 机器学习，必须有一个 Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 必须安装并配置 Azure CLI 和 ML 扩展。 有关详细信息，请参阅[安装、设置和使用 2.0 CLI（预览版）](how-to-configure-cli.md)。 

* 必须有一个 Azure 资源组，你（或者你使用的服务主体）在此资源组中需要拥有 `Contributor` 访问权限。 如果已按照上述文章配置了 ML 扩展，那么你就有了此类资源组。 

* 必须有一个 Azure 机器学习工作区。 如果已按照上述文章配置了 ML 扩展，那么你就有了此类工作区。

* 一个已训练的、可供评分和部署的机器学习模型。

## <a name="set-the-defaults-for-azure-cli"></a>为 Azure CLI 设置默认值

为确保在本教程中使用正确的资源，请为要使用的 Azure 订阅 ID、Azure 机器学习工作区和资源组设置默认值。 这样可以避免每次调用 Azure CLI 命令时都要重复传入值。 

> [!IMPORTANT]
> 确保你的用户帐户在资源组中分配有“用户访问管理员”角色。 

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

## <a name="define-the-configuration-yaml-file-for-your-deployment"></a>为部署定义配置 YAML 文件

若要部署托管终结点，首先需要在 YAML 文件中定义终结点的配置。

以下代码示例创建一个托管终结点，其中：  
* 显示了 `endpoints/online/managed/managed-identities/` 目录中的 YAML 文件。
* 定义了用于引用该终结点的名称 `my-sai-endpoint`
* 指定了用于访问该终结点的授权类型 `auth-mode: key`
* 指定了要创建的终结点类型是 `online` 终结点。 
* 指出了该终结点关联了一个名为 `blue` 的部署
* 配置部署详细信息，例如，要部署的模型，以及要使用的环境和评分脚本。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::


## <a name="configure-variables-for-your-deployment"></a>为部署配置变量

为工作区、工作区位置以及要创建的终结点配置变量名称。 以下代码将这些值作为终结点中的环境变量导出：

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

接下来，指定 Blob 存储帐户、Blob 容器和文件的名称。 这些变量名称在此处定义，并在下一部分所述的 `az storage account create` 和 `az storage container create` 命令中引用。

以下代码将这些值作为环境变量导出：

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::


导出这些变量后，在本地创建一个文本文件。 部署终结点后，评分脚本将使用在创建终结点时生成的系统分配的托管标识来访问此文本文件。

## <a name="create-blob-storage-and-container"></a>创建 Blob 存储和容器

对于此示例，请创建一个 Blob 存储帐户和 Blob 容器，然后将前面创建的文本文件上传到该 Blob 容器。 

首先创建存储帐户。 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

接下来，在该存储帐户中创建 Blob 容器。 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

然后，将文本文件上传到该 Blob 容器。 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

## <a name="create-a-managed-online-endpoint"></a>创建托管联机终结点

以下代码在不指定部署的情况下创建托管联机终结点。 本教程稍后将创建部署。

创建托管终结点时，默认将为该终结点创建系统分配的托管标识。

>[!IMPORTANT]
> 系统分配的托管标识是不可变的，一旦创建便不可更改。

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

使用以下代码检查终结点的状态。 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::


## <a name="give-storage-permission-to-system-assigned-managed-identity"></a>向系统分配的托管标识授予存储权限

可以授予托管终结点通过其系统分配的托管标识访问你的存储的权限。 

检索为终结点创建的系统分配的托管标识。 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

在此处，可以授予系统分配的托管标识访问你的存储的权限。

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

## <a name="scoring-script-to-access-azure-resource"></a>用于访问 Azure 资源的评分脚本

请参考以下评分脚本，了解如何使用系统分配的托管标识令牌访问 Azure 资源。 在此方案中，Azure 资源是在前面部分中创建的存储帐户。 

:::code language="python" source="~/azureml-examples-main/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-using-your-configuration"></a>使用配置创建部署

创建与托管终结点关联的部署。

此项部署可能需要大约 8-14 分钟，具体取决于是否是首次生成基础环境/映像。 以后使用同一环境进行部署将更快完成。

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

检查部署的状态。 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

> [!NOTE]
> 评分脚本中的 init 方法使用系统分配的托管标识令牌从你的存储帐户读取文件。 

若要检查 init 方法输出，请使用以下代码查看部署日志。 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::

此命令完成后，即已在 Azure 机器学习工作区中注册模型、环境和终结点。

### <a name="confirm-your-endpoint-deployed-successfully"></a>确认终结点是否部署成功

部署终结点后，请确认其操作。 推理的详细信息因模型而异。 在本教程中，JSON 查询参数如下所示： 

:::code language="json" source="~/azureml-examples-main/cli/endpoints/online/model-1/sample-request.json" :::

若要调用终结点，请运行： 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::


## <a name="delete-the-endpoint-and-storage-account"></a>删除终结点和存储帐户

如果你不打算继续使用部署的终结点和存储，请将其删除以降低成本。 删除终结点时，也会删除其所有关联的部署。 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

## <a name="next-steps"></a>后续步骤

在这篇 Azure 机器学习教程中，你使用机器学习 CLI 执行了以下任务：

> [!div class="checklist"]
> * 设置供 Azure CLI 使用的默认值
> * 配置要与终结点配合使用的变量
> * 创建 Blob 存储帐户和 Blob 容器 
> * 创建托管终结点
> * 为系统分配的托管标识授予对存储的访问权限
> * 创建与托管终结点关联的部署

* 要详细了解如何使用 CLI，请参阅[将 CLI 扩展用于 Azure 机器学习](reference-azure-machine-learning-cli.md)。
* 若要将 JSON 查询细化为仅返回特定数据，请参阅[查询 Azure CLI 命令输出](/cli/azure/query-azure-cli)。
