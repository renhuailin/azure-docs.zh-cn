---
title: 安装、设置和使用 2.0 CLI
titleSuffix: Azure Machine Learning
description: 了解如何安装、设置和使用适用于 Azure 机器学习的 CLI 扩展。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 05/25/2021
ms.reviewer: laobri
ms.openlocfilehash: e9a5562f665ee2d52a39f296fb39d9a7139be6cb
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382605"
---
# <a name="install-set-up-and-use-the-20-cli-preview"></a>安装、设置和使用 2.0 CLI（预览版）

[Azure CLI](/cli/azure/) 的 `ml` 扩展（预览版）是适用于 Azure 机器学习的下一代接口。 该扩展可让你从命令行训练和部署模型，并提供了在跟踪模型生命周期时加快数据科学纵向和横向扩展的功能。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

- 若要使用 CLI，必须拥有 Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
- 若要从本地环境使用本文档中的 CLI 命令，需要使用 [Azure CLI](/cli/azure/install-azure-cli)。

    > [!TIP]
    > 如果使用的是 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)，则可通过浏览器访问 CLI（它位于云端）。

## <a name="installation"></a>安装

新的机器学习扩展需要 Azure CLI 版本 `>=2.15.0`。 请确保满足此要求：

```azurecli
az version
```

如果不满足此要求，请[升级 Azure CLI](/cli/azure/update-azure-cli)。

检查已安装的 Azure CLI 扩展：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/how-to-configure-cli.sh" id="az_extension_list":::

确保未安装任何有冲突的、使用 `ml` 命名空间的扩展，包括 `azure-cli-ml` 扩展：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/how-to-configure-cli.sh" id="az_extension_remove":::

现在，请安装 `ml` 扩展：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/setup.sh" id="az_ml_install":::

运行 help 命令以验证安装，并查看可用的子命令：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/how-to-configure-cli.sh" id="az_ml_verify":::

可将扩展升级到最新版本：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/how-to-configure-cli.sh" id="az_ml_update":::

## <a name="set-up"></a>设置

登录名：

```azurecli
az login
```

如果你可以访问多个 Azure 订阅，则可以设置活动的订阅：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/how-to-configure-cli.sh" id="az_account_set":::

如果它不存在，可以创建 Azure 资源组：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/setup.sh" id="az_group_create":::

同样对机器学习工作区执行该操作：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/setup.sh" id="az_ml_workspace_create":::

机器学习子命令需要 `--workspace/-w` 和 `--resource-group/-g` 参数。 为了避免反复键入这些值，请配置默认值：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/setup.sh" id="az_configure_defaults":::

> [!TIP]
> 大多数代码示例假设已设置默认工作区和资源组。 可以在命令行中替代它们。

## <a name="hello-world"></a>Hello world

若要继续操作，请克隆示例存储库并切换到 `cli` 子目录：

```azurecli-interactive
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

若要通过 Python 在本地运行 hello world，请查看 `jobs` 子目录中的示例：

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/hello-world.yml":::

> [!IMPORTANT]
> 需要在本地安装和运行 [Docker](https://docker.io)。

提交作业，将日志流式传输到控制台输出，并在 Azure 机器学习工作室中打开运行：

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/how-to-configure-cli.sh" id="hello_world":::

> [!IMPORTANT]
> 首次运行可能需要花费几分钟时间，因为将在本地拉取 Docker 映像，并且要运行 Azure ML 作业。 后续运行将使用本地缓存的映像，因此可以更快地完成。

## <a name="next-steps"></a>后续步骤

- [使用机器学习 CLI 扩展（预览版）训练模型](how-to-train-cli.md)
- [使用 Azure 机器学习 Visual Studio Code 扩展训练图像分类 TensorFlow 模型](tutorial-train-deploy-image-classification-model-vscode.md)
