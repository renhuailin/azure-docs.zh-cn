---
title: 快速入门 - 使用 Bicep 预配 Azure Spring Cloud
description: 本快速入门介绍如何使用 Bicep 将 Spring Cloud 群集部署到现有虚拟网络中。
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ssarwa
ms.date: 08/06/2021
ms.openlocfilehash: eb9bf7de3b89dc39a110a87796a1704780e0d270
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122272345"
---
# <a name="quickstart-provision-azure-spring-cloud-using-bicep"></a>快速入门：使用 Bicep 预配 Azure Spring Cloud

本快速入门介绍如何使用 Bicep 模板将 Azure Spring Cloud 群集部署到现有虚拟网络中。

借助 Azure Spring Cloud，可以轻松地将 Spring Boot 微服务应用程序部署到 Azure，不需更改任何代码。 该服务管理 Spring Cloud 应用程序的基础结构，因此开发人员可以专注于其代码。 Azure Spring Cloud 可以通过以下方法提供生命周期管理：综合性监视和诊断、配置管理、服务发现、CI/CD 集成、蓝绿部署等。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果你没有订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure Spring Cloud 群集的两个专用子网，一个用于服务运行时，另一个用于 Spring Boot 微服务应用程序。 有关子网和虚拟网络要求，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[虚拟网络要求](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)部分。
* 用于 Azure Spring Cloud 诊断设置的现有 Log Analytics 工作区。 有关详细信息，请参阅[使用诊断设置分析日志和指标](diagnostic-services.md)。
* 你已确定供 Azure Spring Cloud 群集使用的三个内部无类别域际路由 (CIDR) 范围（每个范围至少为 /16）。 这些 CIDR 范围不可直接路由，只能在 Azure Spring Cloud 群集内部使用。 群集无法将 169.254.0.0/16、172.30.0.0/16、172.31.0.0/16 或 192.0.2.0/24 用作内部 Spring Cloud CIDR 范围，或包含在群集虚拟网络地址范围内的任何 IP 范围   。
* 已授予对虚拟网络的服务权限。 Azure Spring Cloud 资源提供程序要求对虚拟网络拥有“所有者”权限，以便为虚拟网络中专用的动态服务主体授予访问权限，从而进行进一步的部署和维护。 有关说明和详细信息，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[向服务授予虚拟网络权限](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)部分。
* 如果使用 Azure 防火墙或网络虚拟设备 (NVA)，则还需要满足以下先决条件：
   * 网络和完全限定域名 (FQDN) 规则。 有关详细信息，请参阅[虚拟网络要求](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)。
   * 应用于每个服务运行时和 Spring Boot 微服务应用程序子网的唯一一个用户定义路由 (UDR)。 有关 UDR 的详细信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。 在部署 Spring Cloud 群集之前，应将 UDR 配置为具有 NVA 目标的 0.0.0.0/0 路由。 有关详细信息，请参阅[在虚拟网络中部署 Azure Spring Cloud](how-to-deploy-in-azure-virtual-network.md) 的[自带路由表](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table)部分。
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="deploy-using-bicep"></a>使用 Bicep 进行部署

若要部署群集，请执行以下步骤：

1. 创建包含以下内容的 azuredeploy.bicep 文件：

   ```Bicep
   @description('The instance name of the Azure Spring Cloud resource')
   param springCloudInstanceName string

   @description('The name of the Application Insights instance for Azure Spring Cloud')
   param appInsightsName string

   @description('The resource ID of the existing Log Analytics workspace. This will be used for both diagnostics logs and Application    Insights')
   param laWorkspaceResourceId string

   @description('The resourceID of the Azure Spring Cloud App Subnet')
   param springCloudAppSubnetID string

   @description('The resourceID of the Azure Spring Cloud Runtime Subnet')
   param springCloudRuntimeSubnetID string

   @description('Comma-separated list of IP address ranges in CIDR format. The IP ranges are reserved to host underlying Azure Spring Cloud    infrastructure, which should be 3 at least /16 unused IP ranges, must not overlap with any Subnet IP ranges')
   param springCloudServiceCidrs string = '10.0.0.0/16,10.2.0.0/16,10.3.0.1/16'

   @description('The tags that will be associated to the Resources')
   param tags object = {
     environment: 'lab'
   }

   var springCloudSkuName = 'S0'
   var springCloudSkuTier = 'Standard'
   var location = resourceGroup().location

   resource appInsights 'Microsoft.Insights/components@2020-02-02-preview' = {
     name: appInsightsName
     location: location
     kind: 'web'
     tags: tags
     properties: {
       Application_Type: 'web'
       Flow_Type: 'Bluefield'
       Request_Source: 'rest'
       WorkspaceResourceId: laWorkspaceResourceId
     }
   }

   resource springCloudInstance 'Microsoft.AppPlatform/Spring@2020-07-01' = {
     name: springCloudInstanceName
     location: location
     tags: tags
     sku: {
       name: springCloudSkuName
       tier: springCloudSkuTier
     }
     properties: {
       networkProfile: {
         serviceCidr: springCloudServiceCidrs
         serviceRuntimeSubnetId: springCloudRuntimeSubnetID
         appSubnetId: springCloudAppSubnetID
       }
     }
   }

   resource springCloudMonitoringSettings 'Microsoft.AppPlatform/Spring/monitoringSettings@2020-07-01' = {
     name: '${springCloudInstance.name}/default'
     properties: {
       traceEnabled: true
       appInsightsInstrumentationKey: appInsights.properties.InstrumentationKey
     }
   }

   resource springCloudDiagnostics 'microsoft.insights/diagnosticSettings@2017-05-01-preview' = {
     name: 'monitoring'
     scope: springCloudInstance
     properties: {
       workspaceId: laWorkspaceResourceId
       logs: [
         {
           category: 'ApplicationConsole'
           enabled: true
           retentionPolicy: {
             days: 30
             enabled: false
           }
         }
       ]
     }
   }
   ```

1. 打开 Bash 窗口并运行以下 Azure CLI 命令，将 \<value> 占位符替换为以下值：

   - resource-group：用于部署 Spring Cloud 实例的资源组名称。
   - springCloudInstanceName：Azure Spring Cloud 资源的名称。
   - appInsightsName：Azure Spring Cloud 的 Application Insights 实例名称。
   - laWorkspaceResourceId：现有 Log Analytics 工作区的资源 ID（例如，/   subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/   Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>。）
   - springCloudAppSubnetID：Azure Spring Cloud 应用子网的 resourceID。
   - springCloudRuntimeSubnetID：Azure Spring Cloud 运行时子网的 resourceID。
   - springCloudServiceCidrs：CIDR 格式的 IP 地址范围（总共 3 个）的逗号分隔列表。 这些 IP 范围将保留下来，用于托管底层的 Azure Spring Cloud 基础结构。 这 3 个范围应该至少是 /16 的未使用 IP 范围，且不能与网络中使用的任何可路由子网 IP 范围重叠。

   ```azurecli
   az deployment group create \
       --resource-group <value> \
       --name initial \
       --template-file azuredeploy.bicep \
       --parameters \
           springCloudInstanceName=<value> \
           appInsightsName=<value> \
           laWorkspaceResourceId=<value> \
           springCloudAppSubnetID=<value> \
           springCloudRuntimeSubnetID=<value> \
           springCloudServiceCidrs=<value>
   ```

   此命令使用 Bicep 模板在现有虚拟网络中创建 Azure Spring Cloud 实例。 该命令还会在现有 Azure Monitor Log Analytics 工作区中创建基于工作区的 Application Insights 实例。

## <a name="review-deployed-resources"></a>查看已部署的资源

可以使用 Azure 门户来检查已部署的资源，也可以使用 Azure CLI 或 Azure PowerShell 脚本列出已部署的资源。

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。 如果不再需要资源组，可以将其删除，这将删除资源组中的资源。 若要使用 Azure CLI 删除资源组，请使用以下命令：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Bicep 将 Azure Spring Cloud 实例部署到了现有虚拟网络中，然后验证了部署。 若要详细了解 Azure Spring Cloud，请继续访问以下资源。

- 从以下位置部署以下示例应用程序之一：
   - [与 MySQL 集成的 Pet Clinic 应用](https://github.com/azure-samples/spring-petclinic-microservices)（带有 MySQL 后端的微服务）。
   - [简单的 Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI)。
- 在 Azure Spring Cloud 中使用[自定义域](tutorial-custom-domain.md)。
- 使用 [Azure 应用程序网关](expose-apps-gateway-azure-firewall.md)向 Internet 公开 Azure Spring Cloud 应用程序。
- 查看基于 [Microsoft Azure 架构良好的框架](/azure/architecture/framework/)的安全端到端 [Azure Spring Cloud 参考体系结构](reference-architecture.md)。
