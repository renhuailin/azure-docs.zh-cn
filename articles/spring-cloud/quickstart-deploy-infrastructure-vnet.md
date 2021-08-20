---
title: 快速入门 - 使用 Azure 资源管理器模板（ARM 模板）预配 Azure Spring Cloud
description: 本快速入门介绍如何使用 ARM 模板将 Spring Cloud 群集部署到现有虚拟网络中。
services: azure-resource-manager
author: ryhud
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-java
ms.author: rhudson
ms.date: 05/27/2021
ms.openlocfilehash: 73c24f62e2c4333cb1e8ea826792626591aa1c68
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895879"
---
# <a name="quickstart-provision-azure-spring-cloud-using-an-arm-template"></a>快速入门：使用 ARM 模板预配 Azure Spring Cloud

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）将 Azure Spring Cloud 群集部署到现有虚拟网络中。

借助 Azure Spring Cloud，可以轻松地将 Spring Boot 微服务应用程序部署到 Azure，不需更改任何代码。 该服务管理 Spring Cloud 应用程序的基础结构，因此开发人员可以专注于其代码。 Azure Spring Cloud 可以通过以下方法提供生命周期管理：综合性监视和诊断、配置管理、服务发现、CI/CD 集成、蓝绿部署等。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure Spring Cloud 群集的两个专用子网，一个用于服务运行时，另一个用于 Spring Boot 微服务应用程序。 有关子网和虚拟网络要求，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[虚拟网络要求](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)部分。
* 用于 Azure Spring Cloud 诊断设置的现有 Log Analytics 工作区，以及一个基于工作区的 Application Insights 资源。 有关详细信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)以及 [Azure Spring Cloud 中的 Application Insights Java 进程内代理](how-to-application-insights.md)。
* 你已确定供 Azure Spring Cloud 群集使用的三个内部无类别域际路由 (CIDR) 范围（每个范围至少为 /16）。 这些 CIDR 范围不可直接路由，只能在 Azure Spring Cloud 群集内部使用。 群集无法将 169.254.0.0/16、172.30.0.0/16、172.31.0.0/16 或 192.0.2.0/24 用作内部 Spring Cloud CIDR 范围，或包含在群集虚拟网络地址范围内的任何 IP 范围   。
* 已授予对虚拟网络的服务权限。 Azure Spring Cloud 资源提供程序要求对虚拟网络拥有“所有者”权限，以便为虚拟网络中专用的动态服务主体授予访问权限，从而进行进一步的部署和维护。 有关说明和详细信息，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[授予虚拟网络服务权限](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)部分。
* 如果使用 Azure 防火墙或网络虚拟设备 (NVA)，则还需要满足以下先决条件：
   * 网络和完全限定域名 (FQDN) 规则。 有关详细信息，请参阅[虚拟网络要求](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)。
   * 应用于每个服务运行时和 Spring Boot 微服务应用程序子网的唯一一个用户定义路由 (UDR)。 有关 UDR 的详细信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。 在部署 Spring Cloud 群集之前，应将 UDR 配置为具有 NVA 目标的 0.0.0.0/0 路由。 有关详细信息，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[自带路由表](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table)部分。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure Spring Cloud 参考体系结构](reference-architecture.md)。

:::code language="json" source="~/azure-spring-cloud-reference-architecture/ARM/brownfield-deployment/azuredeploy.json":::

该模板中定义了两个 Azure 资源：

* [Microsoft.AppPlatform/Spring](/azure/templates/microsoft.appplatform/spring)：创建 Azure Spring Cloud 实例。
* [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)：创建 Application Insights 工作区。

对于 Azure CLI 和 Terraform 部署，请参阅 GitHub 上的 [Azure Spring Cloud 参考体系结构](https://github.com/Azure/azure-spring-cloud-reference-architecture)存储库。

## <a name="deploy-the-template"></a>部署模板

若要部署模板，请执行以下步骤：

1. 选择下图登录到 Azure 并打开一个模板。 模板将在现有的虚拟网络中创建一个 Azure Spring Cloud 实例，并在现有的 Azure Monitor Log Analytics 工作区中创建一个基于工作区的 Application Insights 实例。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

2. 为以下字段输入值：

- 资源组：选择“新建”，为资源组输入唯一的名称，然后选择“确定”   。
- springCloudInstanceName：输入 Azure Spring Cloud 资源的名称。
- appInsightsName：输入 Azure Spring Cloud 的 Application Insights 实例的名称。
- laWorkspaceResourceId：输入现有 Log Analytics 工作区的资源 ID（例如 /subscriptions/<your subscription>/resourcegroups/<your log analytics resource group>/providers/Microsoft.OperationalInsights/workspaces/<your log analytics workspace name>）。
- springCloudAppSubnetID：输入 Azure Spring Cloud 应用子网的 resourceID。
- springCloudRuntimeSubnetID：输入 Azure Spring Cloud 运行时子网的 resourceID。
- springCloudServiceCidrs：以 CIDR 格式输入 IP 地址范围（总共 3 个）的逗号分隔列表。 这些 IP 范围将保留下来，用于托管底层的 Azure Spring Cloud 基础结构。 这 3 个范围应该至少是 /16 的未使用 IP 范围，且不能与网络中使用的任何可路由子网 IP 范围重叠。
- tags：输入任何自定义标记。

3. 选择“审阅 + 创建”，然后选择“创建” 。

## <a name="review-deployed-resources"></a>查看已部署的资源

可以使用 Azure 门户来检查已部署的资源，也可以使用 Azure CLI 或 Azure PowerShell 脚本列出已部署的资源。

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。 如果不再需要资源组，可以将其删除，这将删除资源组中的资源。 若要使用 Azure CLI 或 Azure PowerShell 删除资源组，请使用以下命令：

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```
---

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 ARM 模板将一个 Azure Spring Cloud 实例部署到了现有虚拟网络中，然后验证了部署。 若要详细了解 Azure Spring Cloud 和 Azure 资源管理器，请继续参阅以下资源。

- 从以下位置部署下列示例应用程序之一：
   - [与 MySQL 集成的 Pet Clinic 应用](https://github.com/azure-samples/spring-petclinic-microservices)（带有 MySQL 后端的微服务）。
   - [简单的 Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI)。
- 在 Azure Spring Cloud 中使用[自定义域](tutorial-custom-domain.md)。
- 使用 [Azure 应用程序网关](expose-apps-gateway-azure-firewall.md)向 Internet 公开 Azure Spring Cloud 应用程序。
- 查看基于 [Microsoft Azure 架构良好的框架](/azure/architecture/framework/)的安全端到端 [Azure Spring Cloud 参考体系结构](reference-architecture.md)。
- 了解有关 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 的详细信息。
