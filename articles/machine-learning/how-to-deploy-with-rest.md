---
title: 通过 REST API 使用托管联机终结点部署模型（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何通过 REST API 使用托管联机终结点部署模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 08/05/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: 97cfd7a5121094af9fc1663fcdddcd4c620b77cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742030"
---
# <a name="deploy-models-with-rest-preview"></a>通过 REST 部署模型（预览版）

了解如何使用 Azure 机器学习 REST API 部署模型（预览版）。

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

## <a name="set-endpoint-name"></a>设置终结点名称

> [!NOTE]
> 终结点名称在 Azure 区域级别需是唯一的。 例如，westus2 中只能有一个名为 my-endpoint 的终结点。

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-managed-online-endpoints"></a>Azure 机器学习托管联机终结点
通过托管联机终结点（预览版），无需创建和管理底层基础结构即可部署模型。 在本文中，你将创建一个联机终结点和部署，并通过调用它来对其进行验证。 但首先必须注册部署所需的资产，包括模型、代码和环境。

有多种方法可以创建 Azure 机器学习联机终结点，包括使用 [Azure CLI](how-to-deploy-managed-online-endpoints.md) 创建，以及在[工作室](how-to-use-managed-online-endpoint-studio.md)中以可视方式创建。 下面的示例使用 REST API 部署托管联机终结点。

## <a name="create-machine-learning-assets"></a>创建机器学习资产

首先设置 Azure 机器学习资产以配置作业。

在以下 REST API 调用中，我们使用了 `SUBSCRIPTION_ID`、`RESOURCE_GROUP`、`LOCATION` 和 `WORKSPACE` 作为占位符。 将占位符替换为自己的值。 

管理 REST 请求一个[服务主体身份验证令牌](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)。 请将 `TOKEN` 替换为你自己的值。 可使用以下命令检索此令牌：

```bash
TOKEN=$(az account get-access-token --query accessToken -o tsv)
```

服务提供商使用 `api-version` 参数来确保兼容性。 `api-version` 参数因服务而异。 当前的 Azure 机器学习 API 版本为 `2021-03-01-preview`。 将 API 版本设置为变量以适应将来的版本：

```bash
API_VERSION="2021-03-01-preview"
```

### <a name="get-storage-account-details"></a>获取存储帐户详细信息

若要注册模型和代码，首先需要将其上传到存储帐户。 数据存储中提供了存储帐户的详细信息。 此示例将获取工作区的默认数据存储和 Azure 存储帐户。 使用 GET 请求查询工作区，以获取包含信息的 JSON 文件。

可以使用工具 [jq](https://stedolan.github.io/jq/) 分析 JSON 结果并获取所需的值。 也可以使用 Azure 门户找到相同的信息：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_storage_details":::

获取存储密钥：

```bash
AZURE_STORAGE_KEY=$(az storage account keys list --account-name $AZURE_STORAGE_ACCOUNT | jq '.[0].value')
```

### <a name="upload--register-code"></a>上传和注册代码

获取数据存储后，可以上传评分脚本。 使用 Azure 存储 CLI 将 Blob 上传到默认容器中：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_code":::

> [!TIP]
> 也可以使用其他方法来上传，例如 Azure 门户或 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

上传代码后，可以使用 PUT 请求指定代码，并使用 `datastoreId` 引用数据存储：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>上传和注册模型

与代码类似，上传模型文件：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_model":::

现在注册模型：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_model":::

### <a name="create-environment"></a>创建环境
部署需要在具有所需依赖关系的环境中运行。 使用 PUT 请求创建环境。 使用 Microsoft Container Registry 中的 Docker 映像。 可以使用 `Docker` 配置 Docker 映像，并使用 `condaFile` 添加 Conda 依赖项。

在以下代码片段中，已将 Conda 环境 (YAML 文件) 的内容读入环境变量：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_environment":::

### <a name="create-endpoint"></a>创建终结点

创建联机终结点：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_endpoint":::

### <a name="create-deployment"></a>创建部署

在该终结点下创建部署：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_deployment":::

### <a name="invoke-the-endpoint-to-score-data-with-your-model"></a>调用终结点为模型数据评分

需要评分 uri 和访问令牌来调用终结点。 首先获取评分 uri：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_endpoint":::

获取终结点访问令牌：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_access_token":::

现在，使用 curl 调用该终结点：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="score_endpoint":::

### <a name="check-the-logs"></a>检查日志

检查部署日志：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_deployment_logs":::

### <a name="delete-the-endpoint"></a>删除终结点

如果你将来不再使用该部署，应使用以下命令将其删除（这会删除终结点和所有基础部署）：

:::code language="rest" source="~/azureml-examples-main/cli/deploy-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 Azure CLI](how-to-deploy-managed-online-endpoints.md) 部署模型。
* 了解如何[使用工作室](how-to-use-managed-online-endpoint-studio.md)部署模型。
* 了解如何[排查托管联机终结点的部署和评分问题（预览版）](how-to-troubleshoot-managed-online-endpoints.md)
* 了解如何[使用托管联机终结点和系统托管标识访问 Azure 资源（预览版）](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)
* 了解如何[监视联机终结点](how-to-monitor-online-endpoints.md)。
* 了解[联机终结点（预览版）的安全推出](how-to-safely-rollout-managed-endpoints.md)。
* [查看 Azure 机器学习托管联机终结点（预览版）的成本](how-to-view-online-endpoints-costs.md)。
* [托管联机终结点 SKU 列表（预览版）](reference-managed-online-endpoints-vm-sku-list.md)。
* 在[使用 Azure 机器学习管理和增加资源配额](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)中了解托管联机终结点的限制。