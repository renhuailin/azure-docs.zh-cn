---
title: 使用 Terraform 管理工作区
titleSuffix: Azure Machine Learning
description: 了解如何使用 Terraform 管理 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: deeikele
author: denniseik
ms.date: 10/08/2021
ms.topic: how-to
ms.custom: ''
ms.openlocfilehash: e273c2f10ddbc21d12be9eb62e069d77a5b5d65e
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716241"
---
# <a name="manage-azure-machine-learning-workspaces-using-terraform-preview"></a>使用 Terraform（预览版）管理 Azure 机器学习工作区

本文介绍如何使用 Terraform 配置文件创建和管理 Azure 机器学习工作区。 使用 [Terraform](/azure/developer/terraform/) 基于模板的配置文件，能够以可重复、可预测的方式定义、创建和配置 Azure 资源。 Terraform 跟踪资源状态，并能够清理和销毁资源。 

Terraform 配置是定义部署所需资源的文档。 它还可以指定部署变量。 使用配置时，变量用于提供输入值。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://azure.microsoft.com/free/)。
* 已安装的 [Azure CLI](/cli/azure/) 版本。
* 配置 Terraform：遵循本文以及[配置 Terraform 和对 Azure 的访问权限](/azure/developer/terraform/get-started-cloud-shell)一文中的指导。

## <a name="declare-the-azure-provider"></a>声明 Azure 提供程序

创建声明 Azure 提供程序的 Terraform 配置文件：

1. 创建名为 `main.tf` 的新文件。 如果使用 Azure Cloud Shell，请使用 bash：

    ```bash
    code main.tf
    ```

1. 在编辑器中粘贴以下代码：

    main.tf：
    :::code language="terraform" source="~/terraform/quickstart/101-machine-learning/main.tf":::

1. 保存文件 ( **&lt;Ctrl>S**) 并退出编辑器 ( **&lt;Ctrl>Q**)。

## <a name="deploy-a-workspace"></a>部署工作区

可使用以下 Terraform 配置创建 Azure 机器学习工作区。 创建 Azure 机器学习工作区时，需要各种其他服务作为依赖项。 该模板还会[向工作区指定这些关联资源](/azure/machine-learning/concept-workspace#resources)。 根据需要，可选择使用创建具有公用或专用网络连接的资源的模板。

# <a name="public-network-connectivity"></a>[公用网络连接](#tab/publicworkspace)

Azure 中的某些资源需要全局唯一的名称。 使用以下模板部署资源之前，将 `name` 变量设置为唯一值。

variables.tf：
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/variables.tf":::

workspace.tf：
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/workspace.tf":::

# <a name="private-network-connectivity"></a>[专用网络连接](#tab/privateworkspace)

以下配置使用 Azure 专用链接终结点在隔离的网络环境中创建工作区。 包含[专用 DNS 区域](/azure/dns/private-dns-privatednszone)，以便可以在虚拟网络中解析域名。

Azure 中的某些资源需要全局唯一的名称。 使用以下模板部署资源之前，将 `resourceprefix` 变量设置为唯一值。

在将专用链接终结点同时用于 Azure 容器注册表和 Azure 机器学习时，无法使用 Azure 容器注册表任务生成[环境](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true)映像。 而可使用 Azure 机器学习计算群集生成映像。 要配置使用的群集名，请设置 [image_build_compute_name](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) 参数。 可使用 [public_network_access_enabled](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) 参数配置为[允许公开访问](/azure/machine-learning/how-to-configure-private-link?tabs=python#enable-public-access)具有专用链接终结点的工作区。

variables.tf：
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/variables.tf":::

workspace.tf：
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/workspace.tf":::

network.tf：
```terraform
# Virtual Network
resource "azurerm_virtual_network" "default" {
  name                = "vnet-${var.name}-${var.environment}"
  address_space       = var.vnet_address_space
  location            = azurerm_resource_group.default.location
  resource_group_name = azurerm_resource_group.default.name
}

resource "azurerm_subnet" "snet-training" {
  name                                           = "snet-training"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.training_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-aks" {
  name                                           = "snet-aks"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.aks_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-workspace" {
  name                                           = "snet-workspace"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.ml_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

# ...
# For full reference, see: https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/network.tf
```

有几个选项可以连接到专用链接终结点工作区。 要详细了解这些选项，请参阅[安全连接到工作区](/azure/machine-learning/how-to-secure-workspace-vnet#securely-connect-to-your-workspace)。

---

## <a name="troubleshooting"></a>疑难解答

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="current-limitations"></a>当前限制

* 目前无法将 Terraform 用于使用客户管理的加密密钥部署工作区。

## <a name="next-steps"></a>后续步骤

* 要详细了解 Azure 上的 Terraform 支持，请参阅 [Azure 文档上的 Terraform](/azure/developer/terraform/)。
* 要查找 Terraform 的“快速入门”模板示例，请参阅 [Azure Terraform 快速入门模板](https://github.com/Azure/terraform/tree/master/quickstart)：
  
  * [101：机器学习工作区和计算](https://github.com/Azure/terraform/tree/master/quickstart/101-machine-learning) - 开始使用 Azure ML 所需的最少资源集。
  * [201：用于网络隔离的机器学习工作区、计算和一组网络组件](https://github.com/Azure/terraform/tree/master/quickstart/201-machine-learning-moderately-secure) - 创建用于 HBI 数据的生产试点环境所需的所有资源。
  * [202：类似于 201，但具有引入现有网络组件的选项](https://github.com/Azure/terraform/tree/master/quickstart/202-machine-learning-moderately-secure-existing-VNet)。
  
* 有关 Terraform Azure 提供程序的详细信息，请参阅 [Terraform 注册表 Azure 资源管理器提供程序](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)。
* 要详细了解网络配置选项，请参阅[使用虚拟网络 (VNets) 保护 Azure 机器学习工作区资源](/azure/machine-learning/how-to-network-security-overview)。
* 有关替代 Azure 资源管理器基于模板的部署，请参阅[使用资源管理器模板和资源管理器 REST API 部署资源](../azure-resource-manager/templates/deploy-rest.md)。
