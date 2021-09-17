---
title: 安装和设置 CLI (v2)
titleSuffix: Azure Machine Learning
description: 了解如何安装和设置用于机器学习的 Azure CLI 扩展。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 05/25/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 8034b9e6027f85d9dd5385fda18f81523830944f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772706"
---
# <a name="install-and-set-up-the-cli-v2"></a>安装和设置 CLI (v2)

[Azure CLI](/cli/azure/) 的 `ml` 扩展（预览版）是适用于 Azure 机器学习的增强型接口。 该扩展可让你从命令行训练和部署模型，并提供了在跟踪模型生命周期时加快数据科学纵向和横向扩展的功能。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

- 若要使用 CLI，必须拥有 Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。
- 若要从本地环境使用本文档中的 CLI 命令，需要使用 [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="installation"></a>安装

新的机器学习扩展需要 Azure CLI 版本 `>=2.15.0`。 请确保满足此要求：

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

如果不满足此要求，请[升级 Azure CLI](/cli/azure/update-azure-cli)。

检查已安装的 Azure CLI 扩展：

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_list":::

确保未安装任何有冲突的、使用 `ml` 命名空间的扩展，包括 `azure-cli-ml` 扩展：

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_remove":::

现在，请安装 `ml` 扩展：

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_install":::

运行 help 命令以验证安装，并查看可用的子命令：

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_verify":::

可将扩展升级到最新版本：

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_update":::

### <a name="installation-on-linux"></a>在 Linux 上安装

如果使用的是 Linux，安装所需 CLI 版本和机器学习扩展的最快方法是：

:::code language="bash" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_install_linux":::

有关详细信息，请参阅[安装适用于 Linux 的 Azure CLI](/cli/azure/install-azure-cli-linux)。

## <a name="set-up"></a>设置

登录名：

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

如果你可以访问多个 Azure 订阅，则可以设置活动的订阅：

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

如果它不存在，可以创建 Azure 资源组：

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_group_create":::

机器学习子命令需要 `--workspace/-w` 和 `--resource-group/-g` 参数。 为了避免反复键入这些值，请配置默认值：

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

> [!TIP]
> 大多数代码示例假设已设置默认工作区和资源组。 可以在命令行中替代它们。

现在创建机器学习工作区：

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_workspace_create":::

## <a name="next-steps"></a>后续步骤

- [使用机器学习 CLI 扩展（预览版）训练模型](how-to-train-cli.md)
- [安装 Visual Studio Code Azure 机器学习扩展](how-to-setup-vs-code.md)
- [使用 Azure 机器学习 Visual Studio Code 扩展训练图像分类 TensorFlow 模型](tutorial-train-deploy-image-classification-model-vscode.md)
