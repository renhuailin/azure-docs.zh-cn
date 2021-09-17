---
title: 快速入门 - 使用 Azure CLI 预配 Azure Spring Cloud
description: 本快速入门介绍如何使用 Azure CLI 将 Spring Cloud 群集部署到现有虚拟网络中。
services: azure-cli
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-java
ms.author: vramasubbu
ms.date: 06/15/2021
ms.openlocfilehash: a8dd727b0f0f999f1b0b8e6c9b6ed5977779f5ff
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271370"
---
# <a name="quickstart-provision-azure-spring-cloud-using-azure-cli"></a>快速入门 - 使用 Azure CLI 预配 Azure Spring Cloud

本快速入门介绍如何使用 Azure CLI 将 Azure Spring Cloud 群集部署到现有虚拟网络中。

借助 Azure Spring Cloud，可以轻松地将 Spring Boot 微服务应用程序部署到 Azure，不需更改任何代码。 该服务管理 Spring Cloud 应用程序的基础结构，因此开发人员可以专注于其代码。 Azure Spring Cloud 可以通过以下方法提供生命周期管理：综合性监视和诊断、配置管理、服务发现、CI/CD 集成、蓝绿部署等。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果你没有订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure Spring Cloud 群集的两个专用子网，一个用于服务运行时，另一个用于 Spring Boot 微服务应用程序。 有关子网和虚拟网络要求，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[虚拟网络要求](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)部分。
* 用于 Azure Spring Cloud 诊断设置的现有 Log Analytics 工作区，以及一个基于工作区的 Application Insights 资源。 有关详细信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)以及 [Azure Spring Cloud 中的 Application Insights Java 进程内代理](how-to-application-insights.md)。
* 你已确定供 Azure Spring Cloud 群集使用的三个内部无类别域际路由 (CIDR) 范围（每个范围至少为 /16）。 这些 CIDR 范围不可直接路由，只能在 Azure Spring Cloud 群集内部使用。 群集无法将 169.254.0.0/16、172.30.0.0/16、172.31.0.0/16 或 192.0.2.0/24 用作内部 Spring Cloud CIDR 范围，或包含在群集虚拟网络地址范围内的任何 IP 范围   。
* 已授予对虚拟网络的服务权限。 Azure Spring Cloud 资源提供程序要求对虚拟网络拥有“所有者”权限，以便为虚拟网络中专用的动态服务主体授予访问权限，从而进行进一步的部署和维护。 有关说明和详细信息，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[向服务授予虚拟网络权限](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)部分。
* 如果使用 Azure 防火墙或网络虚拟设备 (NVA)，则还需要满足以下先决条件：
   * 网络和完全限定域名 (FQDN) 规则。 有关详细信息，请参阅[虚拟网络要求](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)。
   * 应用于每个服务运行时和 Spring Boot 微服务应用程序子网的唯一一个用户定义路由 (UDR)。 有关 UDR 的详细信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。 在部署 Spring Cloud 群集之前，应将 UDR 配置为具有 NVA 目标的 0.0.0.0/0 路由。 有关详细信息，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[自带路由表](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table)部分。
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="review-the-azure-cli-deployment-script"></a>查看 Azure CLI 部署脚本

本快速入门中使用的部署脚本来自 [Azure Spring Cloud 参考体系结构](reference-architecture.md)。

```azurecli
#!/bin/bash

echo "Enter Azure Subscription ID: "
read subscription
subscription=$subscription

echo "Enter Azure region for resource deployment: "
read region
location=$region

echo "Enter Azure Spring cloud Resource Group Name: "
read azurespringcloudrg
azurespringcloud_resource_group_name=$azurespringcloudrg

echo "Enter Azure Spring cloud VNet Resource Group Name: "
read azurespringcloudvnetrg
azurespringcloud_vnet_resource_group_name=$azurespringcloudvnetrg

echo "Enter Azure Spring cloud Spoke VNet : "
read azurespringcloudappspokevnet
azurespringcloudappspokevnet=$azurespringcloudappspokevnet

echo "Enter Azure Spring cloud App SubNet : "
read azurespringcloudappsubnet
azurespringcloud_app_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudappsubnet

echo "Enter Azure Spring cloud Service SubNet : "
read azurespringcloudservicesubnet
azurespringcloud_service_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudservicesubnet

echo "Enter Azure Log Analytics Workspace Resource Group Name: "
read loganalyticsrg
loganalyticsrg=$loganalyticsrg

echo "Enter Log Analytics Workspace Resource ID: "
read workspace
workspaceID='/subscriptions/'$subscription'/resourcegroups/'$loganalyticsrg'/providers/microsoft.operationalinsights/workspaces/'$workspace

echo "Enter Reserved CIDR Ranges for Azure Spring Cloud: "
read reservedcidrrange
reservedcidrrange=$reservedcidrrange

echo "Enter key=value pair used for tagging Azure Resources (space separated for multiple tags): "
read tag
tags=$tag

randomstring=$(LC_ALL=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 13 | head -n 1)
azurespringcloud_service='spring-'$randomstring #Name of unique Spring Cloud resource
azurespringcloud_appinsights=$azurespringcloud_service
azurespringcloud_resourceid='/subscriptions/'$subscription'/resourceGroups/'$azurespringcloud_resource_group_name'/providers/Microsoft.AppPlatform/Spring/'$azurespringcloud_service

# Create Application Insights
az monitor app-insights component create \
    --app ${azurespringcloud_service} \
    --location ${location} \
    --kind web \
    -g ${azurespringcloudrg} \
    --application-type web \
    --workspace ${workspaceID}

# Create Azure Spring Cloud Instance
az spring-cloud create \
   -n ${azurespringcloud_service} \
   -g ${azurespringcloudrg} \
   -l ${location} \
   --enable-java-agent true \
   --app-insights ${azurespringcloud_service} \
   --sku Standard \
   --app-subnet ${azurespringcloud_app_subnet_name} \
   --service-runtime-subnet ${azurespringcloud_service_subnet_name} \
   --reserved-cidr-range ${reservedcidrrange} \
   --tags ${tags}

# Update diagnostic setting for Azure Spring Cloud instance
az monitor diagnostic-settings create  \
   --name monitoring \
   --resource ${azurespringcloud_resourceid} \
   --logs    '[{"category": "ApplicationConsole","enabled": true}]' \
   --workspace  ${workspaceID}
```

## <a name="deploy-the-cluster"></a>部署群集

若要使用 Azure CLI 脚本部署 Azure Spring Cloud 群集，请执行以下步骤：

1. 使用以下命令登录到 Azure：

   ```azurecli
   az login
   ```

   登录后，此命令将输出有关你有权访问的所有订阅的信息。 记下要使用的订阅的名称和 ID。

1. 设置目标订阅。

   ```azurecli
   az account set --subscription "<your subscription name>"
   ```

1. 注册 Azure Spring Cloud 资源提供程序。

   ```azurecli
   az provider register --namespace 'Microsoft.AppPlatform'
   ```

1. 将所需的扩展添加到 Azure CLI。

   ```azurecli
   az extension add --name spring-cloud
   ```

1. 从 Azure Spring Cloud 可用的区域中选择一个部署位置，如[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all)中所示。

1. 使用以下命令生成 Azure 位置列表。 记下在上一步中选择的区域的简短 Name 值。

   ```azurecli
   az account list-locations --output table
   ```

1. 创建要将资源部署到其中的资源组。

   ```azurecli
   az group create --name <your-resource-group-name> --location <location-name>
   ```

1. 将 [deploySpringCloud.sh](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/CLI/brownfield-deployment/deploySpringCloud.sh) Bash 脚本保存在本地，然后从 Bash 提示符执行它。

   ```azurecli
   ./deploySpringCloud.sh
   ```

1. 在脚本提示时输入以下值：

   - 之前保存的 Azure 订阅 ID。
   - 之前保存的 Azure 位置名称。
   - 之前创建的资源组的名称。
   - 将在其中部署资源的虚拟网络资源组的名称。
   - 分支虚拟网络的名称（例如 vnet-spoke）。
   - Spring Cloud 应用服务要使用的子网的名称（例如 snet-app）。
   - Spring Cloud 运行时服务要使用的子网的名称（例如 snet-runtime）。
   - 要用于存储诊断日志的 Azure Log Analytics 工作区的资源组的名称。
   - Azure Log Analytics 工作区的名称（例如 la-cb5sqq6574o2a）。
   - Azure Spring Cloud 要使用的虚拟网络的 CIDR 范围（例如 XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16）。
   - 要在所有支持标记的资源上作为标记应用的键/值对。 有关详细信息，请参阅[使用标记对 Azure 资源和管理层次结构进行组织](../azure-resource-manager/management/tag-resources.md)。 使用空格分隔列表来应用多个标记（例如 environment=Dev BusinessUnit=finance）。

提供此信息后，脚本将创建和部署 Azure 资源。

## <a name="review-deployed-resources"></a>查看已部署的资源

可以使用 Azure 门户来检查已部署的资源，也可以使用 Azure CLI 列出已部署的资源。

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。 如果不再需要资源组，可以将其删除，这将删除资源组中的资源。 若要使用 Azure CLI 删除资源组，请使用以下命令：

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 Azure CLI 将 Azure Spring Cloud 实例部署到现有虚拟网络中，然后验证了部署。 若要详细了解 Azure Spring Cloud，请继续访问以下资源。

- 从以下位置部署以下示例应用程序之一：
   - [与 MySQL 集成的 Pet Clinic 应用](https://github.com/azure-samples/spring-petclinic-microservices)（带有 MySQL 后端的微服务）。
   - [简单的 Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI)。
- 在 Azure Spring Cloud 中使用[自定义域](tutorial-custom-domain.md)。
- 使用 [Azure 应用程序网关](expose-apps-gateway-azure-firewall.md)向 Internet 公开 Azure Spring Cloud 应用程序。
- 查看基于 [Microsoft Azure 架构良好的框架](/azure/architecture/framework/)的安全端到端 [Azure Spring Cloud 参考体系结构](reference-architecture.md)。
