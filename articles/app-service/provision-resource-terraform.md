---
title: 使用 Terraform 模板创建应用服务应用
description: 使用 Terraform 模板只需要数秒即可在 Azure 应用服务中创建你的第一个应用，这是将应用部署到应用服务的诸多方式之一。
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.custom: subject-terraform
ms.openlocfilehash: 33aea508bd636ebb1b37cd2c9120d9fdb863460d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061920"
---
# <a name="create-app-service-app-using-a-terraform-template"></a>使用 Terraform 模板创建应用服务应用

通过使用 [Terraform](/azure/developer/terraform/) 将应用部署到云中，开始使用 [Azure 应用服务](overview.md)。 由于使用的是免费应用服务层，因此完成本快速入门不会产生费用。

使用 Terraform 可以在 Azure 中定义和创建完整的基础结构部署。 以用户可读格式生成 Terraform 模板，用于以一致且可重现的方式创建和配置 Azure 资源。 本文展示如何使用 Terraform 创建 Windows 应用。

## <a name="prerequisites"></a>先决条件

Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

配置 Terraform：如果尚未执行此操作，请使用以下选项之一配置 Terraform：

* [使用 Bash 在 Azure Cloud Shell 中配置 Terraform](/azure/developer/terraform/get-started-cloud-shell-bash?tabs=bash)
* [使用 PowerShell 在 Azure Cloud Shell 中配置 Terraform](/azure/developer/terraform/get-started-cloud-shell-powershell?tabs=bash)
* [使用 Bash 在 Windows 中配置 Terraform](/azure/developer/terraform/get-started-windows-bash?tabs=bash)
* [使用 PowerShell 在 Windows 中配置 Terraform](/azure/developer/terraform/get-started-windows-powershell?tabs=bash)

借助 Azure Terraform Visual Studio Code 扩展，可以通过编辑器使用 Terraform。 使用此扩展，你可以创作、测试和运行 Terraform 配置。 此扩展还支持资源图表可视化效果。 请参阅[本指南](/azure/developer/terraform/configure-vs-code-extension-for-terraform)，了解如何配置 Azure Terraform Visual Studio Code 扩展。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板如下所示。 它在 Windows 上部署应用服务计划和应用服务应用，并部署来自 [Azure 示例](https://github.com/Azure-Samples)存储库的示例 Node.js“Hello World”应用。

```hcl
# Configure the Azure provider
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 2.65"
    }
  }
  required_version = ">= 0.14.9"
}
provider "azurerm" {
  features {}
}
# Generate a random integer to create a globally unique name
resource "random_integer" "ri" {
  min = 10000
  max = 99999
}
# Create the resource group
resource "azurerm_resource_group" "rg" {
  name     = "myResourceGroup-${random_integer.ri.result}"
  location = "eastus"
}
# Create the Linux App Service Plan
resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "webapp-asp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku {
    tier = "Free"
    size = "F1"
  }
}
# Create the web app, pass in the App Service Plan ID, and deploy code from a public GitHub repo
resource "azurerm_app_service" "webapp" {
  name                = "webapp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
  source_control {
    repo_url           = "https://github.com/Azure-Samples/nodejs-docs-hello-world"
    branch             = "master"
    manual_integration = true
    use_mercurial      = false
  }
}
```

该模板中定义了三个 Azure 资源和一个订阅。 下面提供了 [Azure 提供程序 Terraform 注册表](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)的链接，以获取更多详细信息和使用情况信息：

* [Microsoft.Resources/resourcegroups](/azure/templates/microsoft.resources/resourcegroups?tabs=json)：创建一个资源组（如果尚不存在）。
  * [azurerm_resource_group](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group) 
* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms)：创建应用服务计划。
  * [azurerm_app_service_plan](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service_plan)
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites)：创建应用服务应用。
  * [azurerm_app_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service)
* [Microsoft.Web/sites/sourcecontrols](/azure/templates/microsoft.web/sites/sourcecontrols)：创建外部 git 部署配置。
  * [source_control](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service#source_control)

有关如何构造 Terraform 模板的详细信息，请参阅 [Terraform Learn 文档](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS)。

## <a name="implement-the-terraform-code"></a>实现 Terraform 代码

Terraform 提供了许多用于管理、构建、部署和更新基础结构的功能。 以下步骤将指导你完成部署和销毁资源的过程。 [Terraform Learn 文档](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS)和 [Azure 上的 Terraform 文档](/azure/developer/terraform/)提供了更多详细信息，如果 Terraform 是你的 Azure 基础结构策略的一部分，则应查看这些文档。

1. 创建用于测试和运行示例 Terraform 代码的目录，并将其设为当前目录。

    ```bash
    mkdir appservice_tf_quickstart
    cd appservice_tf_quickstart
    ```

1. 创建名为 `main.tf` 的文件并插入上述代码。

    ```bash
    code main.tf
    ```

1. 初始化 Terraform。

    ```bash
    terraform init
    ```

1. 创建 Terraform 计划。

    ```bash
    terraform plan
    ```

1. 预配 `main.tf` 配置文件中定义的资源（在提示符处输入 `yes` 确认操作）。

    ```bash
    terraform apply
    ```

## <a name="validate-the-deployment"></a>验证部署

1. 在 Azure 门户的主菜单上，选择“资源组”，并导航到使用上述模板创建的资源组。 它将命名为“myResourceGroup-”，后跟随机整数字符串。

1. 现在，你将看到 Terraform 创建的所有资源（应用服务和应用服务计划）。

1. 选择“应用服务”并导航到 url，验证是否已正确创建站点。 相反，你只需浏览到 `http://<app_name>.azurewebsites.net/`，其中应用名称为“webapp-”，后跟资源组中相同的随机整数字符串。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源，请[删除资源组](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)，或返回到终端/命令行并执行 `terraform destroy`，以删除与此快速入门关联的所有资源。

> [!NOTE]
> 可在[此处](/azure/app-service/samples-terraform)找到更多 Azure 应用服务 Terraform 示例。 可在[此处](https://github.com/hashicorp/terraform-provider-azurerm/tree/main/examples)找到所有 Azure 服务的更多 Terraform 示例。
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [详细了解如何在 Azure 中使用 Terraform](/azure/terraform)
> [!div class="nextstepaction"] 
> [针对 Azure 应用服务的 terraform 示例](/azure/app-service/samples-terraform)