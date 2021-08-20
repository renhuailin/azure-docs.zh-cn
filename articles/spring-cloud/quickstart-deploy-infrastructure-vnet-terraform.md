---
title: 快速入门 - 使用 Terraform 预配 Azure Spring Cloud
description: 本快速入门介绍如何使用 Terraform 将 Spring Cloud 群集部署到现有虚拟网络中。
author: aluna033
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ariel
ms.date: 06/15/2021
ms.openlocfilehash: d099e86f5a28aae145723b728e79ce3ee55f8250
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287572"
---
# <a name="quickstart-provision-azure-spring-cloud-using-terraform"></a>快速入门：使用 Terraform 预配 Azure Spring Cloud

本快速入门介绍如何使用 Terraform 将 Azure Spring Cloud 群集部署到现有虚拟网络中。

借助 Azure Spring Cloud，可以轻松地将 Spring Boot 微服务应用程序部署到 Azure，不需更改任何代码。 该服务管理 Spring Cloud 应用程序的基础结构，因此开发人员可以专注于其代码。 Azure Spring Cloud 可以通过以下方法提供生命周期管理：综合性监视和诊断、配置管理、服务发现、CI/CD 集成、蓝绿部署等。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果你没有订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Hashicorp Terraform](https://www.terraform.io/downloads.html)
* Azure Spring Cloud 群集的两个专用子网，一个用于服务运行时，另一个用于 Spring Boot 微服务应用程序。 有关子网和虚拟网络要求，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[虚拟网络要求](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)部分。
* 用于 Azure Spring Cloud 诊断设置的现有 Log Analytics 工作区，以及一个基于工作区的 Application Insights 资源。 有关详细信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)以及 [Azure Spring Cloud 中的 Application Insights Java 进程内代理](how-to-application-insights.md)。
* 你已确定供 Azure Spring Cloud 群集使用的三个内部无类别域际路由 (CIDR) 范围（每个范围至少为 /16）。 这些 CIDR 范围不可直接路由，只能在 Azure Spring Cloud 群集内部使用。 群集无法将 169.254.0.0/16、172.30.0.0/16、172.31.0.0/16 或 192.0.2.0/24 用作内部 Spring Cloud CIDR 范围，或包含在群集虚拟网络地址范围内的任何 IP 范围   。
* 已授予对虚拟网络的服务权限。 Azure Spring Cloud 资源提供程序要求对虚拟网络拥有“所有者”权限，以便为虚拟网络中专用的动态服务主体授予访问权限，从而进行进一步的部署和维护。 有关说明和详细信息，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[授予虚拟网络服务权限](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)部分。
* 如果使用 Azure 防火墙或网络虚拟设备 (NVA)，则还需要满足以下先决条件：
   * 网络和完全限定域名 (FQDN) 规则。 有关详细信息，请参阅[虚拟网络要求](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)。
   * 应用于每个服务运行时和 Spring Boot 微服务应用程序子网的唯一一个用户定义路由 (UDR)。 有关 UDR 的详细信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。 在部署 Spring Cloud 群集之前，应将 UDR 配置为具有 NVA 目标的 0.0.0.0/0 路由。 有关详细信息，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[自带路由表](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table)部分。

## <a name="review-the-configuration-file"></a>查看配置文件

本快速入门中使用的配置文件来自 [Azure Spring Cloud 参考体系结构](reference-architecture.md)。

```hcl
provider "azurerm" {
    features {} 
}

resource "azurerm_resource_group" "sc_corp_rg" {
    name      = var.resource_group_name
    location  = var.location
}

resource "azurerm_application_insights" "sc_app_insights" {
  name                = var.app_insights_name
  location            = var.location
  resource_group_name = var.resource_group_name
  application_type    = "web"
  depends_on = [azurerm_resource_group.sc_corp_rg]
}

resource "azurerm_spring_cloud_service" "sc" {
  name                = var.sc_service_name 
  resource_group_name = var.resource_group_name
  location            = var.location
  
  network {
    app_subnet_id                   = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.app_subnet_id}"
    service_runtime_subnet_id       = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.service_runtime_subnet_id}"
    cidr_ranges                     = var.sc_cidr
  }
  
  timeouts {
      create = "60m"
      delete = "2h"
  }
  
  depends_on = [azurerm_resource_group.sc_corp_rg]
  tags = var.tags
  
}

resource "azurerm_monitor_diagnostic_setting" "sc_diag" {
  name                        = "monitoring"
  target_resource_id          = azurerm_spring_cloud_service.sc.id
  log_analytics_workspace_id = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.OperationalInsights/workspaces/${var.sc_law_id}"

  log {
    category = "ApplicationConsole"
    enabled  = true

    retention_policy {
      enabled = false
    }
  }

  metric {
    category = "AllMetrics"

    retention_policy {
      enabled = false
    }
  }
}
```

## <a name="apply-the-configuration"></a>应用配置

若要应用配置，请执行以下步骤：

1. 将 [variables.tf](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/terraform/brownfield-deployment/variable.tf) 文件保存到本地，然后在编辑器中打开它。

1. 编辑该文件，在其中添加以下值：

   - 要部署到的 Azure 帐户的订阅 ID。

   - Azure Spring Cloud 可用区域（请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all)）中的某个部署位置。 需要以短格式输入位置名称。 若要获取此值，请使用以下命令生成 Azure 位置列表，然后查找所选区域的“名称”值。

      ```azurecli
      az account list-locations --output table
      ```

   - 要部署到的资源组的名称。

   - 为 Spring Cloud 部署选择的名称。

   - 将在其中部署资源的虚拟网络资源组的名称。

   - 分支虚拟网络的名称（例如 vnet-spoke）。

   - Spring Cloud 应用服务要使用的子网的名称（例如 snet-app）。

   - Spring Cloud 运行时服务要使用的子网的名称（例如 snet-runtime）。

   - Azure Log Analytics 工作区的名称。

   - Azure Spring Cloud 要使用的虚拟网络的 CIDR 范围（例如 XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16）。

   - 要在所有支持标记的资源上作为标记应用的键/值对。 有关详细信息，请参阅[使用标记对 Azure 资源和管理层次结构进行组织](../azure-resource-manager/management/tag-resources.md)。 

1. 运行以下命令以初始化 Terraform 模块：

   ```bash
   terraform init
   ```

1. 运行以下命令以创建 Terraform 部署计划：

   ```bash
   terraform plan -out=springcloud.plan
   ```

1. 运行以下命令以应用 Terraform 部署计划：

   ```bash
   terraform apply springcloud.plan
   ```

## <a name="review-deployed-resources"></a>查看已部署的资源

可以使用 Azure 门户来检查已部署的资源，也可以使用 Azure CLI 或 Azure PowerShell 脚本列出已部署的资源。

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。 如果不再需要本文中创建的资源，请使用以下命令将其删除。

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Terraform 将 Azure Spring Cloud 实例部署到了现有虚拟网络中，然后验证了部署。 若要详细了解 Azure Spring Cloud，请继续访问以下资源。

- 从以下位置部署以下示例应用程序之一：
   - [与 MySQL 集成的 Pet Clinic 应用](https://github.com/azure-samples/spring-petclinic-microservices)（带有 MySQL 后端的微服务）。
   - [简单的 Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI)。
- 在 Azure Spring Cloud 中使用[自定义域](tutorial-custom-domain.md)。
- 使用 [Azure 应用程序网关](expose-apps-gateway-azure-firewall.md)向 Internet 公开 Azure Spring Cloud 应用程序。
- 查看基于 [Microsoft Azure 架构良好的框架](/azure/architecture/framework/)的安全端到端 [Azure Spring Cloud 参考体系结构](reference-architecture.md)。